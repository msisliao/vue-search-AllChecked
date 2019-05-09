## 演示地址，打开啊、搜索啊、随便点啊
<http://msisliao.github.io/demo/dist/index.html#/>

### 懒得点地址，gif已在来的路上~~[额😓，体积有点庞大，建议还是看demo吧]
![gif](https://raw.githubusercontent.com/msisliao/vue-search-AllChecked/master/static/aa.gif)


- 安装vue-cli 
- 安装elementUI `npm i element-ui -S`
- 在main.js 引入elementUI


```javascript
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
Vue.use(ElementUI)
```

## demo功能概览

> * 默认没有全选，搜索时支持全选与取消全选，
> * 将选择的数据添加到已选中，已选删除时改变当前搜索列表的状态与全选按钮的状态
> * 全选时全部追加到已选，取消全选时从已选中删除当前搜索的列表


## 功能列表

1、搜索时展示相应的数据列表，支持全选与取消全选，（默认展示所有数据时不支持全选）

```javascript
    datas() {
      // 每次搜索的数据 根据下拉菜单的值的变化
      if (this.value !== "") {
        return this.listItem.list.filter(item => {
          return item.BrandNames.includes(this.value);
        });
      } else {
        return this.listItem.list; // 没有搜索的关键词时展示全部数据
      }
    },
```

2、搜索的下拉菜单去重

```javascript
    filDatas() {
      // 利用reduce 下拉菜单去重
      var obj = {};
      return this.listItem.list.reduce(function(item, next) {
        obj[next.BrandNames] ? "" : (obj[next.BrandNames] = true && item.push(next));
        return item;
      }, []);
    }

```



3、当前界面全选时添加到已选中，当前界面取消全选时，从已选的数据删除当前搜索出来的列表数据，

```javascript
    // 每次搜索列表的全选 与 取消全选
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

```

4、列表选中时添加到已选，全部选中时改变全选状态(变取消全选)

```javascript
// 监听当前搜索列表的勾选数据
    ckarr: function() {
      if (this.value !== "") {
        this.ckarr.length == this.datas.length ? this.allck = true  : this.allck = false; //如果已选等于当前搜索列表 改变全选状态
      }
    }

```

5、在已选中操作删除时，如果删除的是当前搜索的列表,当前全选改变状态，如果删除的非当前搜索列表，当前全选状态不变(这里有点绕)

```javascript
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
```
