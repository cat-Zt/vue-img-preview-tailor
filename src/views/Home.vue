<template>
  <div class="home">
    <h2 class="title">
      图片裁剪、预览及上传
    </h2>
    <div class="className">
      <div>
        <input type="file" accept="image/*" @change="onChange" />
      </div>
    </div>
    <!-- 展示 -->
    <div class="className">
      <div>
        <img
          id="scream"
          :src="dataURL"
          alt=""
          ref="imageRef"
          style="border: 2px dashed #79D281; width: 400px; height: 300px;"
        />
      </div>
      <!-- canvas 图片 -->
      <div @mousedown="handleMouseDown" @mousemove="handleMouseMove" @mouseup="handleMouseUp">
        <div style="position: relative">
          <canvas
            id="myCanvas"
            width="300px"
            height="300px"
            style="border: 2px dashed #632B21;"
          ></canvas>
          <div class="canvas11"></div>
        </div>
        <div>
          <button type="button" @click="bigChange">
            变大
          </button>
          <button type="button" @click="smallChange">
            变小
          </button>
          <button type="button" @click="comfig">
            剪切
          </button>
        </div>
      </div>
      <div>
        <canvas id="configCanvas" width="300px" height="300px"></canvas>
      </div>
      <div>
        <button type="button" @click="upload">
            上传
        </button>
      </div>
    </div>
  </div>
</template>

<script>
/*eslint-disable */
export default {
  name: "Home",
  data() {
    return {
      file: null, // 选中的文件
      dataURL: "", // 选中的文件的原始的base64字符串
      times: 1, // 放大倍数
      startX: 0, // 鼠标按下时的x坐标
      startY: 0, // 鼠标按下时的y坐标
      startDrag: false, // 是否在拖拽过程中
      lastX: 0, // 上一次最后的x坐标
      lastY: 0, // 上一次最后的y坐标
      configurl: ""
    };
  },
  methods: {
    onChange(event) {
      const that = this;
      let file = event.target.files[0];
      let fileReader = new FileReader();
      // 一个文件上传的回调
      fileReader.onload = event => {
        // console.log(file, event.target.result)
        that.file = file;
        that.dataURL = event.target.result;
        that.$nextTick(()=> {
          that.drawImage();
        })
        // this.imageRef.current.onload = () => this.drawImage();
      };
      // 开始读取指定的Blob中的内容。一旦完成，result属性中将包含一个data: URL格式的Base64字符串以表示所读取文件的内容
      fileReader.readAsDataURL(file);
    },
    drawImage(left = this.lastX, top = this.lastY) {
      console.log(left, "left", top, "top");
      const that = this;
      var c = document.getElementById("myCanvas");
      var ctx = c.getContext("2d");
      // var img = document.getElementById("scream");
      var img = this.$refs.imageRef;
      console.log(img, 'img')
      img.onload = function(){
        let imageWidth = img.width;
        let imageHeight = img.height;
        console.log(imageWidth, imageHeight)
        // clearRect() 方法清空给定矩形内的指定像素。
        ctx.clearRect(0, 0, c.width, c.height);
        if (imageWidth > imageHeight) {
          let scale = c.width / c.height;
          imageWidth = c.width * that.times;
          imageHeight = imageHeight * scale * that.times;
        } else {
          let scale = c.height / c.width;
          imageHeight = c.height * that.times;
          imageWidth = imageWidth * scale * that.times;
        }
        ctx.drawImage(
          img,
          (c.width - imageWidth) / 2 + left,
          (c.height - imageHeight) / 2 + top,
          imageWidth,
          imageHeight
        );
      }
      // console.log(ctx, "left", img, "top");
      
    },
    drawImage111(left = this.lastX, top = this.lastY) {
      console.log(left, "left", top, "top");
      const that = this;
      var c = document.getElementById("myCanvas");
      var ctx = c.getContext("2d");
      // var img = document.getElementById("scream");
      var img = this.$refs.imageRef;
      console.log(img, 'img')
      let imageWidth = img.width;
        let imageHeight = img.height;
        console.log(imageWidth, imageHeight)
        // clearRect() 方法清空给定矩形内的指定像素。
        ctx.clearRect(0, 0, c.width, c.height);
        if (imageWidth > imageHeight) {
          let scale = c.width / c.height;
          imageWidth = c.width * that.times;
          imageHeight = imageHeight * scale * that.times;
        } else {
          let scale = c.height / c.width;
          imageHeight = c.height * that.times;
          imageWidth = imageWidth * scale * that.times;
        }
        ctx.drawImage(
          img,
          (c.width - imageWidth) / 2 + left,
          (c.height - imageHeight) / 2 + top,
          imageWidth,
          imageHeight
        );
      // console.log(ctx, "left", img, "top");
      
    },
    handleMouseDown(event) {
      this.startX = event.clientX;
      this.startY = event.clientY;
      this.startDrag = true;
    },
    handleMouseMove(event) {
      if (this.startDrag) {
        this.drawImage111(
          event.clientX - this.startX + this.lastX,
          event.clientY - this.startY + this.lastY
        );
      }
    },
    handleMouseUp(event) {
      this.lastX = event.clientX - this.startX + this.lastX;
      this.lastY = event.clientY - this.startY + this.lastY;
      this.startDrag = false;
    },
    bigChange() {
      this.times = this.times + 0.1;
      this.drawImage111();
    },
    smallChange() {
      this.times = this.times - 0.1;
      this.drawImage111()
    },
    comfig() {
      var canvas = document.getElementById("myCanvas");
      let fc = canvas.getContext("2d");
      // x 开始复制的左上角位置的 x 坐标。 y 开始复制的左上角位置的 y 坐标。 width 将要复制的矩形区域的宽度。height 将要复制的矩形区域的高度。
      const imageData = fc.getImageData(100, 100, 100, 100);
      var c = document.getElementById("configCanvas");
      var ctx = c.getContext("2d");
      ctx.putImageData(imageData, 0, 0);
    },
    upload() {
      var c = document.getElementById("configCanvas");
      const url = c.toDataURL();
      console.log(url)
      let bytes = atob(url.split(",")[1]);
      console.log("bytes", bytes);
      // ArrayBuffer 对象用来表示通用的、固定长度的原始二进制数据缓冲区。它是一个字节数组，通常在其他语言中称为“byte array”。
      let arrayBuffer = new ArrayBuffer(bytes.length);
      // Uint8Array 数组类型表示一个8位无符号整型数组，创建时内容被初始化为0。创建完后，可以以对象的方式或使用数组下标索引的方式引用数组中的元素。
      let uInt8Array = new Uint8Array();
      for (let i = 0; i < bytes.length; i++) {
        uInt8Array[i] = bytes.charCodeAt[i];
      }
      let blob = new Blob([arrayBuffer], { type: "image/png" });
      let xhr = new XMLHttpRequest();
      let formData = new FormData();
      formData.append("avatar", blob);
      xhr.open("POST", "/upload", true);
      xhr.send(formData);
    }
  }
};
</script>
<style lang="less">
.home {
  margin-top: 10vh;
}
.title {
  color: #79d281;
  margin-left: 300px;
}
.className {
  display: flex;
  justify-content: space-around;
  margin-bottom: 20px;
}
.canvas11 {
  width: 100px;
  height: 100px;
  background-color: blue;
  opacity: 0.3;
  position: absolute;
  left: 100px;
  top: 100px;
}
</style>
