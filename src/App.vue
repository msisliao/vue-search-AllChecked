<template>
    <div class='tpbox'>
        <el-select v-model="values" filterable placeholder="请选择" size="mini" clearable >
            <el-option v-for="item in filDatas" :key="item.BrandID" :label="item.BrandNames" :value="item.BrandNames" :value-key='item.BrandID'>
            </el-option>
        </el-select>
        <!-- 搜索的列表 -->
        <div v-if="values!=='' && values!==null ">
            <p class='ck-btn-box'>
                <el-button size="mini" @click="ckAll">{{allck?'取消全选':'全选'}}</el-button>
            </p>
            <ul>
                <li v-for="item in datas" :key="item.BrandID">
                    <span>AA{{item.BrandTypeName}}</span>
                    <span>BB{{item.BrandCName}}</span>
                    <span>CC{{item.BrandName}}</span>
                    <span>
                        <el-checkbox v-model="item.ck" @change="handItem(item)">{{item.BrandNames}}</el-checkbox>
                    </span>
                </li>
            </ul>

        </div>
        <!-- 默认列表 -->
        <ul v-else>
            <li v-for="item in datas" :key="item.BrandID">
                <span>AA{{item.BrandTypeName}}</span>
                <span>BB{{item.BrandCName}}</span>
                <span>CC{{item.BrandName}}</span>
                <span>
                    <el-checkbox v-model="item.ck" @change="handItem(item)">{{item.BrandNames}}</el-checkbox>
                </span>
            </li>
        </ul>
        <p class='checked-box' v-if="this.arr.length>0">
            已选：
            <span @click="clearAll" class='clearll-txt'>清空</span>
            <el-tag v-for="tag in this.arr" :key="tag.BrandID" closable @close="handleClose(tag)" :disable-transitions=true>
                {{tag.BrandName}} / {{tag.BrandNames}}
            </el-tag>
        </p>
    </div>
</template>
<script>
export default {
  data() {
    return {
      allck: false, //控制全选 当没有任何操作时每次默认为 true
      ckarr: [], //每次搜索出来点击了复选框
      arr: [], //点击了input的数据 存放所有的已选
      values: "",
      listItem:{
        list: [
          {
            BrandTypeName: "大类1 建材/家居 ", //品牌正常
            BrandTypeID: 1,
            BrandCName: "中类1 建筑材料",
            BrandCID: 1,
            BrandName: "小类1 水泥",
            BransID: 1,
            BrandNames: "红水泥",
            BrandID: 1,
            ck: false
          },
          {
            BrandTypeName: "大类1 建材/家居 ", //品牌在多个小类里
            BrandTypeID: 1,
            BrandCName: "中类2 家私定制",
            BrandCID: 2,
            BrandName: "小类2 电饭煲",
            BransID: 2,
            BrandNames: "松下",
            BrandID: 2,
            ck: false
          },
          {
            BrandTypeName: "大类1 建材/家居 ",
            BrandTypeID: 1,
            BrandCName: "中类2 家私定制",
            BrandCID: 2,
            BrandName: "小类3 电压力锅",
            BransID: 3,
            BrandNames: "松下",
            BrandID: 3,
            ck: false
          },
          {
            BrandTypeName: "大类1 建材/家居 ", //品牌在多个中类小类里
            BrandTypeID: 1,
            BrandCName: "中类2 高档家具",
            BrandCID: 3,
            BrandName: "小类2 家具类",
            BransID: 4,
            BrandNames: "品牌",
            BrandID: 4,
            ck: false
          },
          {
            BrandTypeName: "大类1 建材/家居 ",
            BrandTypeID: 1,
            BrandCName: "中类2 豪华家具",
            BrandCID: 4,
            BrandName: "小类3 厨具类",
            BransID: 5,
            BrandNames: "品牌2",
            BrandID: 5,
            ck: false
          },
          {
            BrandTypeName: "大类1 装修/房产 ",
            BrandTypeID: 2,
            BrandCName: "中类2 豪华家具",
            BrandCID: 5,
            BrandName: "小类3 沙发类",
            BransID: 6,
            BrandNames: "品牌3",
            BrandID: 6,
            ck: false
          }
        ]
      }
    };
  },
  computed: {
    datas(){
      if(!this.values){
        return this.listItem.list
      }
      //每次搜索的数据
      if (this.values !== "") {
        return this.listItem.list.filter(item => {
          return item.BrandNames.includes(this.values);
        });
      } 
    },
    filDatas() {
      //select下拉菜单去重 相同名字的子选项会存放在多个类别里
      var obj = {};
      return this.listItem.list.reduce(function(item, next) {
        obj[next.BrandNames] ? "" : (obj[next.BrandNames] = true && item.push(next));
        return item;
      }, []);
    }
  },
  watch: {
    //   监听每次搜索时的数据变化
    datas: function(ary) {
      //搜索数据变化时 如果搜的结果全部是已选 第二次搜这个关键词就变成 取消选择
      if (this.values !== "") {
        this.allck = false; //默认每次搜索时是全选状态 需判断之前是否全选中的 有的话就是取消全选
         ary.every( item => { item.ck ? !this.allck : this.allck  });
        // 将当前搜索列表的已选拿出来
        this.ckarr = this.datas.filter(item => {
          if (item.ck) {  return item; }
        });
      }
    },
    // 监听每次搜索列表的数据是否全部为选中 判断已选的数据是不是等于当前搜索列表的数据
    ckarr: function() {
      if (this.values !== "") {
        this.ckarr.length == this.datas.length ? this.allck = true  : this.allck = false; //如果已选等于当前搜索列表 改变全选状态
      }
    },

  },
  methods: {
    //   数据列表的复选框点击
    handItem(item) {
      if (item.ck) {
        this.arr.push(item); //arr是所有复选框的数据 存放在已选中
        this.ckarr.push(item); //ckarr是每次搜索列表点了复选框的数据 当取消全选时 在已选的大数组中删除 ckarr的数据
      } else {
        this.arr.splice(this.arr.indexOf(item), 1);
        this.ckarr.splice(this.arr.indexOf(item), 1);
      }
    },
   //   已选中的 单个删除
    handleClose(tag) {
      this.arr.splice(this.arr.indexOf(tag), 1); // 点哪删哪
      this.ckarr.forEach(items => {   // 判断删除的是否是当前搜索列表的数据 是的话改变全选状态
        if (items.BrandID == tag.BrandID) {
          this.ckarr.splice(this.ckarr.indexOf(tag), 1);
        }
      });
      this.listItem.list.forEach(items => {       // 删除已选时改变数据列表状态
        if (items.BrandID == tag.BrandID) { items.ck = false; }
      });
    },
    // 清空操作
    clearAll() {
      this.listItem.list.forEach(item => { item.ck = false; }); // 数据列表状态恢复
      this.arr = this.ckarr = []; //已选全部清空 当前搜索列表存放的已选全部清空
      this.allck = false; //全选状态恢复
      this.values='' //回到默认数据    
    },
    // 每次搜索列表的全选
    ckAll() {
      this.allck = !this.allck;  //点击全选 变 取消选择
      let arrys = []; //存放复选框为取消状态的数据
      if (this.allck) { // 将当前搜索的列表数据追加到已选中
        this.datas.forEach(item => {
          item.ck = true; 
          if (!this.arr.includes(item)) { // 追加复选框为false的数据
            this.arr.push(item);
            this.ckarr.push(item);
          }
        });
      } else {
        this.datas.forEach(item => {  item.ck = false; }); //当前搜索的数据列表复选框设为取消状态
        arrys = this.datas.filter(item => {  return !item.ck;  });   //把复选框为false的数据 拿出来
        this.datas.forEach(items => {  //已选那里删除当前搜索列表复选框为false的数据
          arrys.forEach(item => {
            if (item.BrandID == items.BrandID) { this.arr.splice(this.arr.indexOf(item), 1);}
          });
        });
        this.ckarr = []; //当前搜索列表为复选框的数据清空
      }
    },
  }
};
</script>
<style  scoped>
.tpbox {
  background: #fff;
  padding: 30px;
  height: 500px;
}
  ul {
    margin-top: 15px;
  }
  li {
    justify-content: space-around;
    display: flex;
    line-height: 50px;
    color: #666;
    border-bottom: 1px solid #eee;

  }
      span {
      flex: 1;
      text-align: left;
      padding-left: 10px;
    }
  .checked-box {
    margin-top: 20px;
    
  }
  .el-tag {
      margin-left: 10px;
    }
  .clearll-txt {
    color: red;
    cursor: pointer;
  }
  .ck-btn-box {
    margin-top: 30px;
  }

</style>



