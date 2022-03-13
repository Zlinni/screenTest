<template>
  <div class="alertBox" v-show="isStart">
    <p>{{ text1 }}</p>
    <p>{{ text2 }}</p>
    <input type="submit" value="继续" class="continue" @click="showBtn" />

  </div>
</template>

<script>
export default {
  data() {
    return {
      isStart: false,
      text1: "请恢复到原始坐姿，准备下次测试。",
      text2: "请主持人继续点击按钮。",


    };
  },


  methods: {
    changeStart(data) {
      this.isStart = data;
    },
    showBtn() {
      this.isStart = false;
      if (this.text2) {
        this.$bus.$emit("alertIsClick", true);
      } else {
        this.$bus.$emit("endIsClick", true);
      }
    },
    changeText(text) {
      this.text1 = text;
      this.text2 = undefined;
      this.isStart = true;
    },

  },
  mounted() {
    this.$bus.$off("isShow");
    this.$bus.$on("isShow", this.changeStart);
    this.$bus.$on("exit", this.changeText);
  },
  beforeDestroy() {},
};
</script>

<style lang="less">
.alertBox {
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  margin: auto;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 520px;
  height: 230px;
  background-color: var(--box-color);
  color: var(--text-color);
  border-radius: 20px;
  p:nth-child(2) {
    margin-bottom: 30px;
  }
  p {
    font-size: 20px;
  }
  .continue {
    cursor: pointer;
  }
}
</style>
