<template>
  <div class="main">
    <!-- title -->
    <h1>todos</h1>
    <div class="mainList">
      <!-- input -->
      <div class="input-view">
        <div
          v-if="list.length !== 0"
          class="arrow"
          :class="{ active: showListIsAllOver }"
          @click="allOver"
        >
          ❯
        </div>
        <input
          class="input"
          type="text"
          v-model.trim="newThing"
          placeholder="What needs to be done?"
          @keyup.enter="submit()"
        />
      </div>

      <!-- list -->
      <ul>
        <li v-for="item in showList" :key="item.id" @click.stop="over(item)">
          <div v-if="!item.isEdit" class="li-view">
            <!-- <input
              :id="item.id"
              :checked="item.isOver === ViewTypeMap.Completed"
              class="check"
              type="checkbox"
            /> -->
            <div class="check">
              {{ item.isOver === ViewTypeMap.Completed ? "✔" : "" }}
            </div>
            <label
              class="cont"
              :class="{ isOver: item.isOver === ViewTypeMap.Completed }"
              :for="item.id"
              @dblclick.self="dbChange(item.id)"
            >
              {{ item.cont }}
            </label>
            <span @click.stop="del(item.id)">×</span>
          </div>
          <input
            v-show="item.isEdit"
            ref="input"
            class="edit"
            v-model="item.cont"
            type="text"
            @blur="inputIsBlur(item.id)"
            @keyup.enter="inputIsBlur(item.id)"
          />
        </li>
      </ul>
      <div v-if="list.length !== 0" class="list-bottom">
        <!-- 未做数 -->
        <div class="item-num">{{ activeNum }} items left</div>
        <div class="btn-list">
          <!-- 所有 -->
          <div
            v-for="item in btnList"
            :key="item.name"
            class="btn"
            :class="{ active: item.name === viewType }"
            @click="switchBtn(item.name)"
          >
            {{ item.name }}
          </div>
        </div>
        <!-- 清除 -->
        <div v-show="clearShow" class="clear-btn" @click="clearCompleted">
          Clear completed
        </div>
      </div>
    </div>
    <!-- 列表 -->
    <!-- footer -->
    <footer>
      <p>Double-click to edit a todo</p>
      <p>Written by Evan You</p>
      <p>Part of TodoMVC</p>
    </footer>
  </div>
</template>

<script>
const ViewTypeMap = {
  All: "All",
  Completed: "Completed",
  Active: "Active",
};
export default {
  name: "todo",
  data() {
    return {
      ViewTypeMap,
      status: 1, // all:1  active:2 completed:3
      list: [],
      viewType: ViewTypeMap.All,
      btnList: [
        {
          name: "All",
          isSel: false,
        },
        {
          name: "Active",
          isSel: false,
        },
        {
          name: "Completed",
          isSel: false,
        },
      ],
      newThing: "",
    };
  },
  computed: {
    showList() {
      return this.list.filter((item) => {
        return this.viewType === "All" || item.isOver === this.viewType;
      });
    },

    activeNum() {
      return this.list.filter((item) => {
        return item.isOver === "Active";
      }).length;
    },

    showListIsAllOver() {
      return !this.showList.length
        ? false
        : !this.list.some((item) => item.isOver === ViewTypeMap.Active);
    },
    clearShow() {
      return this.list.some((item) => item.isOver === ViewTypeMap.Completed);
    },
  },
  methods: {
    submit() {
      if (this.newThing === "") {
        return;
      }
      this.list.push({
        id: Math.random().toString(36).slice(-8),
        cont: this.newThing,
        isOver: ViewTypeMap.Active,
        isEdit: false,
      });
      this.newThing = "";
    },
    switchBtn(val) {
      this.viewType = val;
      this.btnList.forEach((item) => {
        item.isSel = item.name === val ? true : false;
      });
    },
    over(item) {
      item.isOver =
        item.isOver === ViewTypeMap.Active
          ? ViewTypeMap.Completed
          : ViewTypeMap.Active;
    },
    allOver() {
      this.list.every((item) => item.isOver === ViewTypeMap.Completed)
        ? this.list.forEach((item) => {
            item.isOver = ViewTypeMap.Active;
          })
        : this.list.forEach((item) => {
            item.isOver = ViewTypeMap.Completed;
          });
    },
    dbChange(id) {
      this.list.forEach((item, index) => {
        if (item.id === id) {
          item.isEdit = true;
          this.$nextTick(() => {
            this.$refs.input[index].focus();
          });
        }
      });
    },
    del(id) {
      this.list.forEach((item, index, arr) => {
        item.id === id && arr.splice(index, 1);
      });
    },
    inputIsBlur(id) {
      this.list.forEach((item) => {
        item.id === id && (item.isEdit = false);
      });
    },
    clearCompleted() {
      this.list = this.list.filter((item) => {
        return item.isOver === ViewTypeMap.Active;
      });
    },
  },
};
</script>

<style lang="less" scoped>
.main {
  display: flex;
  flex-direction: column;
  background-color: #f5f5f5;
  align-items: center;
  h1 {
    font-size: 100px;
    font-weight: 100;
    color: #ead7d7;
  }
  .mainList {
    box-shadow: 0px 8px 16px 0px rgba(0, 0, 0, 0.2);
    .input-view {
      width: 550px;
      position: relative;
      display: flex;
      align-items: center;
      .arrow {
        position: absolute;
        left: 0;
        padding: 10px 27px;
        font-size: 22px;
        color: #e6e6e6;
        transform: rotate(90deg);
        cursor: pointer;
        &:hover {
          color: #737373;
        }
      }
      .active {
        color: #737373;
      }
      .input {
        width: 550px;
        background-color: white !important;
        padding: 16px 16px 16px 60px;
        font-size: 24px;
        line-height: 1.4em;
        outline: none;
        cursor: text;
        box-shadow: inset 0 -2px 1px rgba(0, 0, 0, 0.03);
      }
      .input::-webkit-input-placeholder {
        font-size: 24px;
        font-weight: 300;
        font-style: italic;
        color: #e6e6e6;
        line-height: 1.4em !important;
      }
    }
    ul {
      border-top: 1px solid #e6e6e6;
      width: 550px;
      height: auto;
      list-style: none;
      li {
        background-color: #fff;
        font-size: 24px;
        border-bottom: 1px solid #ededed;
        .li-view {
          display: flex;
          // display: none;
          align-items: center;
          position: relative;
          padding: 15px 15px 15px 60px;
          &:hover span {
            display: block;
          }
          .check {
            cursor: pointer;
            position: absolute;
            left: 17px;
            width: 30px;
            height: 30px;
            border-radius: 50%;
            line-height: 30px;
            text-align: center;
            font-size: 18px;
            color: #5dc2af;
            border: 2px solid #efefef;
            &:hover {
              border: 1px solid #5dc2af;
            }
          }
          .cont {
            max-width: 430px;
            min-height: 1.4em;
            word-break: break-all;
            padding: 15px 30px 15px 60px;
            display: block;
            line-height: 1.4em;
            transition: color 0.4s;
          }
          .isOver {
            color: #d9d9d9;
            text-decoration: line-through;
          }
          span {
            display: none;
            position: absolute;
            right: 10px;
            width: 40px;
            height: 40px;
            font-size: 30px;
            line-height: 40px;
            text-align: center;
            color: #cc9a9a;
            transition: color 0.2s ease-out;
            cursor: pointer;
          }
        }
        .edit {
          display: block;
          width: 490px;
          padding: 12px 16px;
          border: 1px solid #000;
          font-size: 24px;
          line-height: 1.4em;
          margin: 0 15px 0 60px;
        }
      }
    }
    .list-bottom {
      display: flex;
      align-items: center;
      justify-content: center;
      position: relative;
      border-top: 2px solid #e6e6e6;
      width: 550px;
      height: 40px;
      color: #777;
      padding: 0 10px;
      text-align: center;
      border-top: 1px solid #e6e6e6;
      // line-height: 50px;
      background-color: #fff;
      box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2), 0 8px 0 -3px #f6f6f6,
        0 9px 1px -3px rgba(0, 0, 0, 0.2), 0 16px 0 -6px #f6f6f6,
        0 17px 2px -6px rgba(0, 0, 0, 0.2);
      .item-num {
        position: absolute;
        left: 10px;
      }
      .btn-list {
        position: relative;
        display: flex;
        align-items: center;
        justify-content: space-between;
        .btn {
          padding: 3px 7px;
          border-radius: 3px;
          margin: 0 3px;
          box-sizing: border-box;
          cursor: pointer;
          border: 1px solid #fff;
        }
        .btn:hover {
          border: 1px solid rgba(175, 47, 47, 0.2);
        }
        .active {
          border: 1px solid rgba(175, 47, 47, 0.2);
        }
      }
      .clear-btn {
        position: absolute;
        right: 10px;
        cursor: pointer;
        &:hover {
          text-decoration: underline;
        }
      }
    }
  }
  footer {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 65px auto 0;
    color: #bfbfbf;
    font-size: 10px;
    text-shadow: 0 1px 0 rgb(255 255 255 / 50%);
    text-align: center;
    p {
      display: block;
      margin-bottom: 10px;
    }
  }
}
</style>