<script >
export default {
  name: 'App',
  data() {
    return {
      imageArr: [],
      imageSrc: [],
      mouse: {
        current: {
          x: 0,
          y: 0
        },
        previous: {
          x: 0,
          y: 0
        },
        down: false
      },
      rect:{
        startX:0,
        startY:0,
        w:0,
        h:0
      },
      img:null
    }
  },
  computed: {
    currentMouse: function () {
      var c = document.getElementById("canvas");
      var rect = c.getBoundingClientRect();
      
      return {
        x: this.mouse.current.x - rect.left,
        y: this.mouse.current.y - rect.top
      }
    }
  },
  methods: {
    fileupload(event) {
      console.log(event.target.files)
      if (!event.target.files) {
        return 
      }
      for (let i = 0; i < event.target.files.length; i++) {
        this.imageArr.push(event.target.files[i])
      }
      this.displayImages()
      this.imageArr = []
    },
    displayImages() {
      this.imageArr.forEach((image, index) => {
        this.imageSrc.push(URL.createObjectURL(image))
      })
      console.log(this.imageSrc)
    },
    deleteImage(index) {
      this.imageArr.splice(index, 1)
      this.displayImages()
    },
    draw: function (event) {
       if (this.mouse.down ) {
          var c = document.getElementById("canvas");
         var ctx = c.getContext("2d");
         ctx.drawImage(this.img,0,0);  // ctx.clearRect(0,0,800,800);
         ctx.setLineDash([6]);
         ctx.strokeRect(this.rect.startX, this.rect.startY, this.rect.w, this.rect.h);
       }
    },
    handleMouseDown: function (event) {
      this.mouse.down = true;
      this.mouse.current = {
        x: event.pageX,
        y: event.pageY
      }
        this.rect.startX = this.mouse.current.x;
        this.rect.startY = this.mouse.current.y;
    },
    handleMouseUp: function () {
      this.mouse.down = false;
    },
    handleMouseMove: function (event) {

      this.mouse.current = {
        x: event.pageX,
        y: event.pageY
      }
      if (this.mouse.down) {
        this.rect.w = this.mouse.current.x - this.rect.startX;
        this.rect.h = this.mouse.current.y - this.rect.startY ;
        // ctx.clearRect(0,0,canvas.width,canvas.height);
        this.draw(event)
      }
    }
  },
       ready: function () {   
                    var c = document.getElementById("canvas");
                    var ctx = c.getContext("2d");
                    ctx.translate(0.5, 0.5);
                    ctx.imageSmoothingEnabled= false;
                    // var img = document.getElementById("imageRef");
                    // ctx.drawImage(img);
                     var img = new Image
                     img.onload = function(){
                       ctx.drawImage(img, this.x, this.y)
                       c.width = img.width
                       c.height = img.height
                    }
                   img.src = this.imagesrc
                   this.img = img
                   c.width = img.width
                   c.height = img.height  
      }
}
</script>

<template>
  <div class="header">
    <div class="logo">
      <img src="./assets/LabelAI.png" alt="Logo">
      <div>LabelAI</div>
    </div>
  </div>
  <div class="mainbody">
    <div class="sidecol">
      <div class="siderow">
        <div class="sidetitle">Images</div>
        <label class="noimage">
          <div class="abtn" v-if="imageSrc.length != 0">
            <input ref="fileinput" type="file" accept="image/*" @change="this.fileupload($event)" multiple>Add More</div>
        </label>
      </div>
      <div class="smallcontainer">
        <label v-if="imageSrc.length == 0" class="noimage">
          <input ref="fileinput" type="file" accept="image/*" @change="this.fileupload($event)" multiple>
          <div class="icon">
            <img src="./assets/photo_library_FILL0_wght400_GRAD0_opsz48.svg" alt="Icon">
          </div>
          <div class="temptext"><b>Drop images</b></div>
          <div class="temptext">or</div>
          <div class="temptext"><b>Click here to select them</b></div>
        </label>
        <div v-if="imageSrc.length != 0" class="imageshow" >
          <template v-for="(imgsrc, index) in imageSrc">
            <img :src="imgsrc" alt="img" @click="">
          </template>
        </div>
      </div>
      <div class="siderow">
        <div class="sidetitle">Labeled Images</div>
        <div class="abtn">Export All</div>
      </div>
      <div class="smallcontainer">

      </div>
    </div>
    <div class="maincol">
      <div class="toprow">
        <div class="counter">Image 1/1</div>
        <div class="innerrow">
          <div class="abtn">Previous</div>
          <div class="imgtitle">image1_tester.png</div>
          <div class="abtn">Next</div>
        </div>
      </div>
      <div class="toprow">
        <div class="bbtn">
          <img src="./assets/input_FILL0_wght400_GRAD0_opsz48.svg" alt="add">
        </div>
        <div class="deletebtn" @click="">
          <img src="./assets/delete_FILL0_wght400_GRAD0_opsz48.svg" alt="add">
        </div>
      </div>
      <div class="imagebody">
        <canvas id="canvas" v-on:mousedown="handleMouseDown" v-on:mouseup="handleMouseUp" v-on:mousemove="handleMouseMove" width="800px" height="800px"></canvas>
      </div>
    </div>
    <div class="sidecol2">
      <div class="sidetitle">Bounding Boxes</div>
      <div class="sidelist"></div>
      <div class="siderow">
        <div class="abtn">
          <img src="./assets/add_FILL0_wght400_GRAD0_opsz48.svg" alt="add">
        </div>
        <div class="innerrow">
          <div class="sidetitle">Labels</div>
        </div>
      </div>
      <div class="sidelist"></div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.header {
  .logo {
    display: flex;
    justify-content: center;
    align-items: center;
    img {
      width: 2rem;
    }
    div {
      padding-left: 1vw;
      font-family: 'Mulish', sans-serif;
      font-weight: 500;
      letter-spacing: 2px;
      font-size: 1.5rem;
      display: flex;
      align-items: center;
      color: #68696e;
    }
  }
}
.mainbody {
  height: 94vh;
  display: flex;
  flex-direction: row;
  .sidecol {
    padding: 1vw;
    width: 16vw;
    border: #000000 1px solid;
    border-radius: 5px;
    box-shadow: 0 10px 30px 0 rgba(0, 0, 0, 0.15);
    .sidetitle {
      }
    .smallcontainer {
      // background-color: #e0e0e0;
      height: 35vh;
      padding: 1vh;
      margin-block: 2vh;;
      // border: #000000 1px solid;
      border-radius: 5px;
      overflow-y: auto;
      .imageshow {
        img {
          width: 5vw;
          height: 5vw;
          padding: 0.5vw;
          // background-color: #F8F8F8;
          background-color: #e0e0e0;
          border-radius: 10px;
          margin: 0.5vw;
          cursor: pointer;
          &:hover {
            opacity: 0.5;
          }
        }
      }
      .noimage {
        display: flex;
        align-items: center;
        justify-content: center;
        flex-direction: column;
        padding-block: 3vh;
        border: #000000 1px dashed;
        border-radius: 10px;
        cursor: pointer;
        &:hover {
          border: #000000 1px solid;
        }
        input[type="file"] {
            display: none;
        }
        .icon {
          margin-bottom: 1vh;
          img {
            width: 2vw;
          }
        }
        .temptext {
          font-size: 0.8rem;
          text-align: center;
        }
      }
    }
    .siderow {
      display: flex;
      flex-direction: row;
      align-items: center;
      justify-content: space-between;
      .abtn {
        padding: 1vh;
        border: #000000 1px solid;
        border-radius: 5px;
        cursor: pointer;
        &:hover {
          border: #7689BD 1px solid;
          background-color: #7689BD;
          color: #ffffff;
        }
        input[type="file"] {
            display: none;
        }
      }
    }
  }
  .maincol {
    margin-inline: 0.5vw;
    width: 62vw;
    border: #000000 1px solid;
    border-radius: 5px;
    box-shadow: 0 10px 30px 0 rgba(0, 0, 0, 0.15);
    .toprow {
      padding: 0.5vh;
      border-bottom: #000000 1px solid;
      display: flex;
      align-items: center;
      flex-direction: row;
      .bbtn {
        margin-left: 1vw;
        display: flex;
        justify-content: center;
        align-items: center;
        width: 1.7rem;
        height: 1.7rem;
        padding-inline: 0.5vw;
        border-radius: 5px;
        border: #000000 1px solid;
        background-color: #f2f2f2;
        img {
          width: 1.6rem;
        }
      }
      .deletebtn {
        margin-left: 0.5vw;
        display: flex;
        justify-content: center;
        align-items: center;
        padding-inline: 0.5vw;
        height: 1.7rem;
        border-radius: 5px;
        border: #F8F8F8 1px solid;
        cursor: pointer;
        img {
          width: 1.6rem;
        }
        &:hover {
          border: #000000 1px solid;
          filter: invert(77%) sepia(17%) saturate(1883%) hue-rotate(307deg) brightness(110%) contrast(92%);
        }
      }
      .counter {
        position: absolute;
        padding-left: 1vw;
        font-size: 0.9rem;
      }
      .innerrow {
        width: 62vw;
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: row;
        .abtn {
          padding: 1vh;
          margin-inline: 3vw;
          border: #000000 1px solid;
          border-radius: 5px;
          cursor: pointer;
          &:hover {
            border: #76b2bd 1px solid;
            background-color: #76b2bd;
            color: #ffffff;
          }
        }
      }
    }
  }
  .sidecol2 {
    width: 16vw;
    border: #000000 1px solid;
    border-radius: 5px;
    box-shadow: 0 10px 30px 0 rgba(0, 0, 0, 0.15);
    .sidetitle {
      text-align: center;
      padding-block: 1vh;
    }
    .sidelist {
      height: 42vh;
    }
    .siderow {
      border-top: #000000 1px solid;
      .abtn {
        position: absolute;
        padding: 0.7vh;
        margin: 0.2vh;
        height: 1rem;
        cursor: pointer;
        &:hover {
          border: #000000 1px solid;
          border-radius: 5px;
        }
        img {
          width: 1rem;
        }
      }
    }
  }
}
</style>
