<template>
  <div class="areaBox" v-show="isShow">
    <div id="areaList"></div>
    <div class="excelBox" v-show="isEnd">
      <p class="excelText">
        {{myHeader}}
      </p>
      <download-excel
        class="export-btn"
        :data="tableData"
        :fields="jsonFields"
        type="xls"
        :header="myHeader"
        name="结果列表.xls"
      >
        导出
      </download-excel>
    </div>
  </div>
</template>

<script>
import JsonExcel from "vue-json-excel";

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
      col: "",
      row: "",
      w: "",
      h: "",
      jsonFields: {
        //导出Excel表格的表头设置
        区域: "area",
        点击结果: "result",
        "点击时长（ms）": "time",
      },
      tableData: [],
      isEnd: false,
      startTime: "",
      endTime: "",
      correct: 0,
      myHeader:''
    };
  },
  components: {
    DownloadExcel: JsonExcel,
  },
  computed: {},
  methods: {
    getMsg([col, row, w, h, btnSide]) {
      this.startTime = new Date();
      this.isShow = true;
      this.col = parseInt(col);
      this.row = parseInt(row);
      this.w = w;
      this.h = h;
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
        // areaBtn.innerText = i;
        areaBtn.style.visibility = "hidden";
        areaBtn.style.border = "0";
        areaBtn.style.borderRadius = "5px";
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
      if (this.ran <= this.sum) {
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
      } else {
        this.$bus.$emit("isShow", false);
      }
    },
    randomShow() {
      let areaBtn = document.getElementsByClassName("areaBtn");
      let visBtn = areaBtn[this.randomArr[this.ran]];
      if (this.ran === this.sum) {
        this.$bus.$emit("exit", "测试完成，感谢参与");
        return;
      } else {
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
      return;
    },
    getMap(isEnd) {
      if (isEnd) {
        console.log(this.map);
        let areaItem = document.getElementsByClassName("areaItem");
        let areaBtn = document.getElementsByClassName("areaBtn");

        for (let i = 0; i < this.sum; i++) {
          let pText = document.createElement("p");
          pText.style.color = "#fff";
          pText.className = "pText";
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
        let pText2 = document.getElementsByClassName("pText");
        let textMap = new Map();
        let col = 1;
        let row = 1;
        let mark;
        for (let i = 0; i < this.sum; i++) {
          col = (i % this.col) + 1;
          let area = `${row}-${col}`;
          if (areaItem[i].style.backgroundColor === "rgb(127, 19, 19)") {
            mark = 0;
          } else if (areaItem[i].style.backgroundColor === "rgb(0, 53, 44)") {
            mark = 1;
            this.correct++;
          } else {
            mark = "";
          }
          textMap.set(area, [pText2[i].innerText, mark]);
          console.log(textMap);
          console.log(col, this.col, row);
          if (col === this.col) {
            row++;
          }
        }
        for (let item of textMap) {
          let obj = {
            area: item[0],
            result: item[1][1],
            time: item[1][0],
          };
          this.tableData.push(obj);
        }
        this.isEnd = true;
        this.endTime = new Date();
        this.timeSelect();
      }
    },
    timeSelect() {
      let now = new Date();
      let year_month_day =
        now.getFullYear() +
        "年" +
        (now.getMonth() + 1) +
        "月" +
        now.getDate() +
        "日";
      let hour_min =
        this.startTime.getHours() +
        ":" +
        this.startTime.getMinutes() +'-'+
        this.endTime.getHours() +
        ":" +
        this.endTime.getMinutes();
      console.log("年月日", year_month_day);
      console.log("时分", hour_min);
      console.log("行列", this.col + "x" + this.row);
      console.log("分辨率", this.w + "x" + this.h + "px");
      console.log("正确率", (this.correct / this.sum) * 100 + "%");
      this.myHeader = year_month_day+' | '+hour_min+' | '+this.col + "x" + this.row+" | "+this.w + "x" + this.h + "px"+' | '+'正确率'+(this.correct / this.sum) * 100 + "%";
      console.log(this.myHeader)
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

<style lang='less'>
#areaList {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  flex-wrap: wrap;
  width: 100%;
  height: 100%;
  box-sizing: border-box;
  .areaItem {
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
.excelBox {
  position: absolute;
  top: 0;
  left: -300px;
  margin-left: 50%;
  width: 600px;
  height: 50px;
  display: flex;
  justify-content: space-around;
  align-items: center;
  background-color: var(--box-color);
  border-radius: 0 0 10px 10px;
  .excelText {
    font-size: 16px;
    color: var(--text-color);
  }
  .export-btn {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 60px;
    height: 30px;
    border-radius: 10px;
    background-color: #1c1c1e;
    color: #0b7eb3;
    cursor: pointer;
  }
}
</style>