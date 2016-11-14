title: 学习html踩过的坑

categories: 前端

---

### 问题列表及解决方法

1. input标签不能设置before，after伪类。因为input不能包含子标签，如果需要添加，外面包裹一个div，在div上添加即可；

2. table和tr 标签添加圆角border-radius样式无效，如需设置可以[参考这里](http://stackoverflow.com/questions/4094126/how-to-add-border-radius-on-table-row)；

3. 使用form上传file文件，需要给form设置enctype="multipart/form-data"，不然无法成功上传文件的。

4. ie浏览器会把长整型数字转换为科学计数法，所以如果需要显示，必须转换为字符串形式。（类似：22222222+''转换）

5. Ie和edge浏览器，css样式中如果top，left没有设置会默认为auto，所以如果需要计算，要先把auto转换为0，然后再计算。

6. a标签不能嵌套，不然html渲染会错乱。

7. input设置固定宽度后，设置了padding-top bottom后。在ie和火狐浏览器下会显示不正常。chrome正常。解决方法是取消width限制，或者不用padding。


### 参考资料
1. [How to add border radius on table row](http://stackoverflow.com/questions/4094126/how-to-add-border-radius-on-table-row)

