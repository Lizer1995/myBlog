---

title: jQuery操作复选框选中状态，prop()和attr()的区别和使用方法
date: 2017-11-12 20:39:14

tags:

 前端
categories:
 前端
---
今天写代码遇到一个小小的问题，情况是这样的：我的页面中有两个复选框，我想实现选中一个复选框的同时另一个复选框也被选中，取消选中时亦然。一开始我用的是attr()和removeAttr()的方法，代码如下：
``` javascript
<input type="checkbox" id="checkbox1" />
<input type="checkbox" id="checkbox2" />
<script>
    $("#checkbox1").click(function(){
        var bol = $('#checkbox1').is(':checked');
        if(bol){
            $('#checkbox2').attr("checked",'check');
        }else{
            $('#checkbox2').removeAttr("checked");
        }
    })
    $("#checkbox2").click(function(){
        var bol = $('#checkbox2').is(':checked');
        if(bol){
            $('#checkbox1').attr("checked",'check');
        }else{
            $('#checkbox1').removeAttr("checked");
        }
    })
</script>
```
后来发现这个方法不行，会有Bug：第一次我选中复选框A，复选框B可以同时自动被选中，取消选中复选框A复选框B也同时取消，好，到这里是没问题的；但如果我这样选择：选中复选框A，复选框B同时自动选中，再点击复选框B来取消选中两个复选框，就没有用了，以后再怎么选中A或选中B，另一个都不为所动。
然后我试着换了个方法，使用prop()来替代attr()，代码如下：
```javascript
<input type="checkbox" id="checkbox1" />
<input type="checkbox" id="checkbox2" />
<script>
    $("#checkbox1").click(function(){
        var bol = $('#checkbox1').is(':checked');
        if(bol){
            $('#checkbox2').prop("checked",true);
        }else{
            $('#checkbox2').prop("checked",false);
        }
    })
    $("#checkbox2").click(function(){
        var bol = $('#checkbox2').is(':checked');
        if(bol){
            $('#checkbox1').prop("checked",true);
        }else{
            $('#checkbox1').prop("checked",false);
        }
    })
</script>
```
完全没问题，无论什么时候A与B的选中状态都是同步的：

![image.png](/img/4853241-885d46e82a739b3b.png)

![image.png](/img/4853241-407375939f788682.png)
这是什么原因呢？到网上查了一下，原理是这样的：
"attr"是"attribute"的缩写，表示HTML文档节点的属性，例如：
```javascript
<input type="checkbox" id="checkbox1" class="mycheckbox"  checked="checked" />
```
这里面id、class、checked都是input（HTML文档节点）的属性；"prop"是"property"的缩写，表示JS对象的属性，例如：
```javascript
var student = {name:"张三",age:"18",id:"001"}
```
这里面name、age、id都是student（JS对象）的属性。
在jQuery 1.6之前，只有attr()方法没有prop()方法，1.6及之后的版本增加了prop()方法，所以使用1.6及之后版本的jQuery，操作文档节点的属性和JS对象的属性需要分别使用其对应的方法。
jQuery认为：attribute的checked、selected、disabled表示的是标签初始状态的值，所以我在使用`attr("checked",'check')`方法时，仅仅是为`input`增加了一个checked的属性，即只设定了该复选框的初始状态为选中状态，而没有改变该复选框的实时选中状态，所以会出现上述Bug。只有property的值才能表示该对象的实时属性，故在我这种情况下使用`prop("checked",true)`才是正确的。
最后记录几个使用attr()和prop()时的细节问题：
* 对于表单元素的checked、selected、disabled等属性，在jQuery 1.6之前，attr()获取这些属性的返回值为Boolean类型，如果被选中(或禁用)就返回true，否则返回false。但是从1.6开始，使用attr()获取这些属性的返回值为String类型，如果被选中(或禁用)就返回"checked"、"selected"或"disabled"，如果不需要这个属性了，应使用removeAttr()将其移除。
* 1.6及之后请使用prop()来设定表单的实时状态（如：checked、selected、disabled），值为true或false。
* 1.6及之后attr()设置的值只能是String型的，而prop()设置的值可以是任意数据类型。