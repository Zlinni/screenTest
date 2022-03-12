<template>
  <div class="inputBox" v-show="isStart">
    <h1>请设置实验参数</h1>
    <div class="textBox">
      测试横向分辨率<input
        type="number"
        oninput="if(value.length>4)value=value.slice(0,4)"
        v-model="widthMedia"
      />px
    </div>
    <div class="textBox">
      测试纵向分辨率<input
        type="number"
        oninput="if(value.length>4)value=value.slice(0,4)"
        v-model="heightMedia"
      />px
    </div>
    <div class="textBox">
      测试按钮长宽为<input
        type="number"
        oninput="if(value.length>4)value=value.slice(0,4)"
        v-model="btnSide"
      />px
    </div>
    <div class="textBox">
      屏幕横向等分为<input type="number" v-model="col" />列
    </div>
    <div class="textBox">
      屏幕纵向等分为<input type="number" v-model="row" />行
    </div>
    <div class="area">
      <div class="borderBox"></div>
      共{{ col * row }}个分区
    </div>
    <input
      type="submit"
      value="开始实验"
      id="submit"
      :disabled="subShow"
      @click="startTest"
    />
  </div>
</template>

<script>
export default {
  data() {
    return {
      widthMedia: 1920,
      heightMedia: 1080,
      btnSide: 25,
      col: 1,
      row: 2,
      isStart: true,
    };
  },
  computed: {
    subShow() {
      if (
        this.widthMedia &&
        this.heightMedia &&
        this.btnSide &&
        this.col &&
        this.row
      ) {
        return false;
      } else {
        return true;
      }
    },
  },
  methods: {
    startTest() {
      window.resizeTo(this.widthMedia, this.heightMedia);
      let body = document.querySelector("body");
      body.style.width = this.widthMedia + "px";
      body.style.height = this.heightMedia + "px";
      this.isStart = false;
      this.$bus.$emit("areaData", [this.col,this.row,this.widthMedia,this.heightMedia,this.btnSide]);
    },

  },
};
</script>
<style scoped lang="less">
.inputBox {
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  margin: auto;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: flex-start;
  padding: 30px;
  width: 396px;
  height: 410px;
  background-color: var(--box-color);
  border-radius: 20px;
  color: var(--text-color);
  .textBox {
    display: flex;
    justify-content: space-evenly;
    align-items: center;
    width: 200px;
    input[type="number"] {
      width: 40px;
      height: 15px;
      background-color: transparent;
      border: 1px solid grey;
      border-radius: 5px;
      color: var(--text-color);
      text-align: center;
    }
  }
  input[type="number"] {
    -moz-appearance: textfield;
  }
  input[type="number"]::-webkit-inner-spin-button,
  input[type="number"]::-webkit-outer-spin-button {
    -webkit-appearance: none;
    margin: 0;
  }
  .area {
    position: absolute;
    top: 63%;
    left: 52%;
    display: flex;
    justify-content: center;
    align-items: center;
    color: grey;
    .borderBox {
      margin-right: 10px;
      width: 30px;
      height: 60px;
      border: 1px solid grey;
      border-left: 0;
    }
  }

}
</style>
