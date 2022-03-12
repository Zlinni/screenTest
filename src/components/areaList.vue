<template>
  <div class="areaBox" v-show="isShow">
    <div id="areaList"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isShow: false,
      ran: 0,
      sum: "",
      randomArr: [],
      visBtn: "",
      timeOutEvent: "",
      record: "",
      map: new Map(),
      colorMap: new Map(),
    };
  },
  methods: {
    getMsg([col, row, w, h, btnSide]) {
      this.isShow = true;
      this.sum = col * row;
      let areaWidth = (w - col * 2) / col;
      let areaHeight = (h - row * 2) / row;
      console.log(col, row, w, h);
      var areaList = document.getElementById("areaList");
      console.log(areaList);
      for (let i = 0; i < this.sum; i++) {
        let areaItem = document.createElement("div");
        areaItem.className = "areaItem";
        areaItem.style.width = areaWidth + "px";
        areaItem.style.height = areaHeight + "px";
        areaItem.style.backgroundColor = "grey";
        areaItem.style.border = "1px solid black";
        areaList.appendChild(areaItem);
      }

      this.randomBtn(col, row, btnSide);
    },
    randomBtn(col, row, size) {
      size = parseInt(size);
      let areaItem = document.getElementsByClassName("areaItem");
      for (let i = 0; i < this.sum; i++) {
        let areaBtn = document.createElement("button");
        areaBtn.className = "areaBtn";
        areaBtn.style.width = size + "px";
        areaBtn.style.height = size + "px";
        areaBtn.innerText = i;
        areaBtn.style.visibility = "hidden";
        areaItem[i].appendChild(areaBtn);
      }
      for (let i = 0; i < this.sum; i++) {
        this.randomArr[i] = i;
      }
      this.randomArr = this.shuffle(this.randomArr);
      setTimeout(() => {
        this.record = new Date();
        this.randomShow();
      }, Math.floor(Math.random() * 3000 + 1));
    },
    shuffle(arr) {
      var result = [],
        random;
      while (arr.length > 0) {
        random = Math.floor(Math.random() * arr.length);
        result.push(arr[random]);
        arr.splice(random, 1);
      }
      return result;
    },
    setBtn(e) {
      e.stopPropagation();
      this.ran++;
      this.$bus.$emit("isShow", true);
      this.$bus.$off("alertIsClick");
      this.$bus.$on("alertIsClick", (data) => {
        if (data) {
          let second = Math.floor(Math.random() * 3000 + 1);
          setTimeout(() => {
            this.record = new Date();
            this.randomShow();
          }, second);
        }
      });
    },
    randomShow() {
      let areaBtn = document.getElementsByClassName("areaBtn");
      let visBtn = areaBtn[this.randomArr[this.ran]];
      if (this.ran === this.sum) {
        this.$bus.$emit("exit2", "测试完成，感谢参与");
        return;
      }else{
        visBtn.style.visibility = "visible";
      }
      let areaList = document.getElementById("areaList");
      areaList.onmousedown = () => {
        this.timeOutEvent = setTimeout(() => {
          visBtn.style.visibility = "hidden";
          this.$bus.$emit("exit", "测试完成，感谢参与");
          this.timeOutEvent = 0;
        }, 5000);
      };
      areaList.onmouseup = (e) => {
        if (this.timeOutEvent != 0) {
          clearInterval(this.timeOutEvent);
          let now = new Date();
          let res = now - this.record;
          this.map.set(this.randomArr[this.ran], res);
          console.log("其他是", this.map.get(this.randomArr[this.ran]));
          visBtn.style.visibility = "hidden";
          this.colorMap.set(this.randomArr[this.ran], "#7f1313");
          this.setBtn(e);
        }
      };
      visBtn.onmousedown = (e) => {
        e.stopPropagation();
      };
      visBtn.onmouseup = (e) => {
        let now = new Date();
        let res = now - this.record;
        this.map.set(this.randomArr[this.ran], res);
        console.log("按钮地方是", this.map.get(this.randomArr[this.ran]));
        visBtn.style.visibility = "hidden";
        this.colorMap.set(this.randomArr[this.ran], "#00352c");
        this.setBtn(e);
      };
    },
    getMap(isEnd) {
      if (isEnd) {
        console.log(this.map);
        let areaItem = document.getElementsByClassName("areaItem");
        let areaBtn = document.getElementsByClassName("areaBtn");

        for (let i = 0; i < this.sum; i++) {
          let pText = document.createElement("p");
          pText.style.color = "#fff";
          if (!this.map.has(i)) {
            pText.innerText = "未测试";
            areaItem[i].style.backgroundColor = "#000";
          } else {
            pText.innerText = this.map.get(i) + "ms";
          }
          areaItem[i].appendChild(pText);
          areaItem[i].style.backgroundColor = this.colorMap.get(i);
          areaBtn[i].style.visibility = "hidden";
        }
      }
    },
  },

  mounted() {
    this.$bus.$on("areaData", this.getMsg);
    this.$bus.$on("endIsClick", this.getMap);
  },
  beforeDestroy() {
    this.$bus.$off("areaData");
    this.$bus.$off("isShow");
  },
};
</script>

<style>
#areaList {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  flex-wrap: wrap;
  width: 100%;
  height: 100%;
  box-sizing: border-box;
}
.areaItem {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>