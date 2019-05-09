# vue-search-AllChecked
# 基于vue2实现的搜索与全选

- 安装vue-cli 
- 安装elementUI `npm i element-ui -S`
- 在main.js 引入elementUI

```
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
Vue.use(ElementUI)
```


## 功能列表

1、搜索的下拉菜单去重

2、搜索时展示相应的数据列表，支持全选与取消全选，全选时添加到已选中

3、当前界面取消全选时，从已选的数据删除当前搜索出来的列表数据

4、列表选中时添加到已选，全部选中时改变全选状态(变取消全选)

5、在已选中操作删除时，如果删除的是当前搜索的列表,当前全选改变状态，如果删除的非当前搜索列表，当前全选状态不变(这里有点绕)
