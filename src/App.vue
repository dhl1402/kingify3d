<template>
  <div class="root">
    <div class="panel">
      <button class="btn" @click="addText">Add text</button>
      <input
        class="input-add-image"
        id="add-image"
        type="file"
        ref="file"
        accept=".png, .jpg, .jpeg"
        @change="uploadImage"
      />
      <button class="btn">
        <label for="add-image">Add image</label>
      </button>
      <div class="album">
        <h3>Album</h3>
        <img
          class="img"
          v-for="(img, index) in imgs"
          :key="index"
          :src="img"
          @click="selectImage(img)"
        />
      </div>
    </div>
    <div class="canvas-container">
      <canvas ref="canvas" height="600" width="600" />
      <img
        src="./assets/drop-your-design-here.png"
        for="add-image"
        v-if="isBlank"
        @click="openFileDialog"
      />
    </div>
  </div>
</template>

<script>
import { fabric } from "fabric";
import maskImg from "./assets/mask.png";
import img1 from "./assets/img1.jpg";
import img2 from "./assets/img2.jpg";
import img3 from "./assets/img3.jpg";
import img4 from "./assets/img4.jpg";
import img5 from "./assets/img5.jpg";
import img6 from "./assets/img6.jpg";

export default {
  name: "App",
  data() {
    return {
      canvas: null,
      mask: null,
      imgs: [img1, img2, img3, img4, img5, img6],
    };
  },
  computed: {
    isBlank() {
      if (!this.canvas) {
        return true;
      }
      return this.canvas.getObjects().length <= 1;
    },
  },
  mounted() {
    this.canvas = new fabric.Canvas(this.$refs.canvas, {
      preserveObjectStacking: true,
      selection: false,
    });

    fabric.util.loadImage(maskImg, (img) => {
      this.mask = img;
      this.mask = new fabric.Image(img);
      this.mask
        .scaleToWidth(this.canvas.width)
        .set({ left: 0, top: 0, selectable: false, evented: false });
      this.canvas.add(this.mask);
    });
  },
  methods: {
    openFileDialog() {
      this.$refs.file.click();
    },
    uploadImage() {
      const file = this.$refs.file.files[0];
      this.$refs.file.value = "";
      const reader = new FileReader();
      reader.onload = ({ target: { result } }) => {
        fabric.Image.fromURL(result, (img) => this.addImage(img));
      };
      reader.readAsDataURL(file);
    },
    selectImage(img) {
      fabric.util.loadImage(img, (imgData) => this.addImage(new fabric.Image(imgData)));
    },
    addImage(img) {
      img.scaleToWidth(this.canvas.width - 100);
      this.canvas.add(img);
      this.canvas.centerObject(img);
      this.canvas.setActiveObject(img);
      this.canvas.bringToFront(this.mask);
    },
    addText() {
      const text = new fabric.IText("Kingify", { fill: "#000" });
      this.canvas.add(text);
      this.canvas.centerObject(text);
      this.canvas.setActiveObject(text);
      this.canvas.bringToFront(this.mask);
    },
  },
};
</script>

<style scoped>
.root {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
.panel {
  display: flex;
  flex-direction: column;
  padding: 0 12px;
}
.btn {
  margin-bottom: 12px;
}
.input-add-image {
  display: none;
}
.album {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.album h3 {
  margin-bottom: 10px;
}
.img {
  height: 70px;
  width: 100px;
  margin: 6px 0;
  object-fit: cover;
}
.img:last-of-type {
  margin-bottom: 0;
}
.canvas-container {
  position: relative;
  border: 1px solid #ccc;
}
.canvas-container img {
  position: absolute;
  margin-left: auto;
  margin-right: auto;
  left: 0;
  right: 0;
  top: 180px;
  cursor: pointer;
}
</style>
