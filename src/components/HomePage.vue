<template>
  <div class="my-5">
    <div class="row">
      <div class="col-12 text-center">
        <p class="fw-bold h4">
          શ્રી આરાધધામ મામાપીરની જગ્યા ગૌશાળા-અન્નક્ષેત્ર સુખપર-વાવડી
        </p>
        <p class="fw-bold h4">
          શ્રી મહંત શ્રી અવધેશાનંદ ભારતીજી મહારાજ
        </p>
      </div>
    </div>
    <div class="row">
      <div class="col-md-6 col-12">
        <div class="d-flex justify-content-md-end justify-content-center">
          <canvas ref="myCanvas" height="1400" width="1200" style="width: 350px; height: 450px;"></canvas>
        </div>
      </div>
      <div
        class="col-md-6 col-12 "
      >
        

        <!-- <div>
        <v-avatar size="350px" class="mt-5" style="border: 2px solid black;">
          <v-img :src="avatarImage"></v-img>
        </v-avatar>
      </div> -->
      <div class="d-flex justify-content-md-center justify-content-center bg-dark mx-5 mx-sm-0 col-md-6 text-center rounded-3 my-3">
          <div class="">  <input
          ref="filePickerField"
          type="file"
          accept="image/*"
          @change="launchCropper"
          hidden
        />
          <button type="button" class="btn text-white btn-md" @click="$refs.filePickerField.click()"
          ><i class="bi bi-cloud-arrow-up fs-3"></i> 
          <br>
          <span class="fw-bold">
            Upload Profile Pic
          </span>
          <br> 
          <span class="text-muted">

            Click here to upload profile pic. You can repeat this step to choose another profile pic.
          </span>
          </button
          >
        </div>
        </div>

        <div class="d-flex justify-content-md-center justify-content-center bg-dark mx-5 mx-sm-0 col-md-6 text-white text-center rounded-3 my-3">
          <div class="my-3">
            <label for="exampleFormControlInput1" class="form-label fw-bold ">Name</label>
            <input type="text" @keyup="writeText()" v-model="form.name" class="form-control rounded-pill" id="exampleFormControlInput1" placeholder="Your Name">
          </div>
        </div>
        <div class="d-flex justify-content-md-center justify-content-center mx-5 mx-sm-0 col-md-6 text-white text-center rounded-3 my-3">
          <div class="my-3">
            <button @click="downloadImage()" type="button" class="btn btn-dark rounded-pill px-5"><i class="bi bi-cloud-arrow-down-fill fs-5"></i> Download</button>
          </div>
        </div>
        <image-cropper-dialog
          ref="cropperDialog"
          :chosenImage="chosenImage"
          @onReset="$refs.filePickerField.value = null"
          @onCrop="
            (croppedImage) => {
              avatarImage = croppedImage;
              croppedImageFun(avatarImage);
            }
          "
        />
      </div>
    </div>
  </div>
</template>

<script>
import ImageCropperDialog from "./ImageCropperDialog.vue";

export default {
  name: "HomePage",
  components: {
    ImageCropperDialog,
  },

  data() {
    return {
      form:{
        name:null        
      },
      avatarImage: null,
      chosenImage: null,
      bannerImage: require("../assets/main.png"),
    };
  },
  mounted() {
    const canvas = this.$refs.myCanvas;
    const ctx = canvas.getContext("2d");
    const img = new Image();

    img.src = this.bannerImage;

    img.onload = () => {
      ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
    };
  },
  methods: {
    async launchCropper(event) {
      if (!event) return;
      var file = event.target.files[0];
      this.chosenImage = await this.toBase64(file);
      this.$refs.cropperDialog.initCropper(file.type);
    },

    async toBase64(file) {
      return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = () => resolve(reader.result);
        reader.onerror = (error) => reject(error);
      });
    },

    async croppedImageFun(croppedImage) {
      this.avatarImage = croppedImage;
      this.drawNow();
    },
    writeText(){
      this.drawNow();
    },
    async drawNow(){
      const myFont = new FontFace('Noto Sans Gujarati', "https://fonts.googleapis.com/css2?family=Noto+Sans+Gujarati:wght@600&display=swap");
      myFont.load().then(async (font) => {
            document.fonts.add(font);
      });
      const canvas = this.$refs.myCanvas;
      // canvas.style.letterSpacing = '5px';
      const ctx = canvas.getContext("2d");
      if (this.avatarImage != null) {
        const img = new Image();

        img.src = this.avatarImage;

        img.onload = async () => {
         await ctx.drawImage(img, 90, 855, 380, 360);
        };
      }

      const img2 = new Image();

      img2.src = this.bannerImage;

      img2.onload = async () => {
        await ctx.drawImage(img2, 0, 0, canvas.width, canvas.height);

        if (this.form.name != null) {  
          ctx.font = `600 40px Noto Sans Gujarati`;
           // Font size and type
          ctx.fillStyle = "white"; // Fill color
          ctx.strokeStyle = "white"; // Stroke color
          ctx.lineWidth = 2; // Stroke width
          const textWidth = ctx.measureText(this.form.name).width;
          // console.log(textWidth);
          const x = 300 - (textWidth / 2);
          // Write text on the canvas
          ctx.fillText(this.form.name, x, 1275);
          // ctx.strokeText(this.form.name, x, 940);
        }
        
      };

    },
    downloadImage() {
      if (this.form.name == null || this.form.name.trim()=="") {
          alert('please enter the name');
      }else if(this.avatarImage == null){
        alert('please upload the image');
      }else{
        const link = document.createElement('a');
        link.download = 'download.png';
        if (this.form.name != null && this.form.name.trim()!="") {
          link.download = this.form.name+'.png'
        }
        link.href = this.$refs.myCanvas.toDataURL('image/png');
        link.click();
      }
    },
  },
};
</script>
