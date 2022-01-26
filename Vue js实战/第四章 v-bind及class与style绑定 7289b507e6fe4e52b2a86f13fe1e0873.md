# 第四章 v-bind及class与style绑定

DOM元素经常动态绑定一些class类名活style样式

## 4.1 了解v-bind指令

第二章提到了v-bind的基本用法和语法糖，主要用法时动态更新HTML元素上的属性，示例：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>回顾第二章v-bind</title>
</head>

<body>
    <div id="app">
        <a v-bind:href="url">链接</a>
        <img v-bind:src="imgUrl">
        <!--缩写为-->
        <br>
        <a :href="url">链接</a>
        <img :src="imgUrl">
    </div>
    <script src="https://unpkg.com/vue/dist/vue.min.js"></script>
    <script>
        var app = new Vue({
            el: '#app',
            data: {
                url: 'https://www.baidu.com',
                imgUrl: 'https://www.baidu.com/img/PCfb_5bf082d29588c07f842ccde3f97243ea.png'
            }
        })
    </script>
</body>

</html>
```

链接的href属性和图片的src属性都被动态设置了，当数据变化时，就会重新渲染。

在数据绑定中，最常见的两个需求就是元素的样式名称class和内联样式style的动态绑定。它们也是HTML的属性，因此可以使用v-bind指令，我们只需要用v-bind计算出表达式最终的字符串就可以。不过有时候表达式的逻辑较复杂，使用字符串拼接方法较难阅读和维护，所以[Vue](https://so.csdn.net/so/search?q=Vue&spm=1001.2101.3001.7020).js增强了对class和style的绑定。

## 4.2 绑定class的几种方式

### 4.2.1 对象语法

给`v-bind:class`设置一个对象，可以动态地切换`class`，例如：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>绑定active</title>
</head>
<body>
    <div id="app">
        <div :class="{ 'active' : isActive }"></div>
    </div>
    <script src="https://unpkg.com/vue/dist/vue.min.js"></script>
    <script>
      var app = new Vue({
        el: "#app",
        data: {
          isActive: true
        }
    })
    </script>
</body>
</html>
```

提示：

上面的`:class`等同于`v-bind:class`，是一个语法糖，如不特殊说明，后面都将使用语法糖写法，可以回顾第2.3章节。

上面示例中，类名active依赖于数据isActive。当其为true时，<div>会拥有类名active，为false时则没有。所以上例最终渲染完的结果是：

```html
<div id="app">
    <div class="active"></div>
</div>
```

对象也可以传入多个属性，来动态切换class。另外，:class可以与普通class共存，例如

```html
<div id="app">
    <div :class="{'active':isActive,'error':isError}"></div>
</div>
<script>
var app = new Vue({
    el:'#app',
    data:{
        isActive:true,
        isError:false
    }
})
</script>
```

:class 内的表达式每项为真，对应的类名就会加载，渲染结果为：

`<div class="static active"></div>`

当数据isActive或isError变化时，对应的class类名也会更新。比如当isError为true时，渲染的结果为：

`<div class="static active error"></div>`

当:class的表达式过长或逻辑复杂时，还可以绑定一个计算属性，这是一种很友好和常见的用法，一般当条件多于两个时，都可以使用data或computed，例如使用计算属性：

```html
<div id="app">
    <div :class="classes"></div>
</div>

<script>
var app = new Vue({
    el:'#app',
    data:{
        isActive:true,
        isError:null
    },
    computed:{
        classes(){
            return {
                active:this.isActive && !this.error,
                'text-fail':this.error && this.error.type ==='fail'
            }
        }
    }
})
</script>
```

除了计算属性，也可以直接绑定一个Object类型的数据，或者使用类似计算属性的methods.

### 4.2.2 数组语法

数组方法当需要应用多个class，可以使用数组语法，给:class 绑定一个数组，应用一个class列表

```html
<div id="app">
    <div :class="[atvieCls,errorCls]"></div>
</div>
<script>
var app = new Vue({
    el:'#app',
    data:{
        atvieCls:'active',
        errorCls:'error'
    }
})
</script>

最终渲染结果为：<div class="active error"></div>
```

也可以使用三元表达式来根据条件切换class,如

```html
<div id="app">
    <div :class="[isActive ? activeCls : '',errorCls]"></div>
</div>
<script>
var app = new Vue({
    el:'#app',
    data:{
        isActive:true,
        atvieCls:'active',
        errorCls:'error'
    }
})
</script>
```

样式error会始终应用，当数据isActive为真时，样式active才会被应用。class有多个条件时候，这样写较为反锁，可以在数组语法中使用对象语法

```html
<div id="app">
    <div :class="[{'active':isActive},errorCls]"></div>
</div>
<script>
var app = new Vue({
    el:'#app',
    data:{
        isActive:true,
        errorCls:'error'
    }
})
</script>
```

### 4.2.3 在组件上使用（第7章结束后再看）

如果直接在自定义组件上使用class或者:class，样式规则会直接应用到这个组件的根元素上，例如声明一个简单组件

```html
Vue.component('my-component',{
	template:'<p class="article">test</p>'
})
```

在调用这个组件时，应用上面介绍的对象语法或者数组语法绑定class，以对象语法为例

```html
<div id="app">
    <my-content :class="{'active':isActive}"></my-content>
</div>
<script>
var app = new Vue({
    el:'#app',
    data:{
        isActive:true
    }
})
</script>

渲染结果：
<p class="article active">test</p>
```

这种用法仅仅使用于自定义组件的最外层的一个根元素，否则无效，当不满足这种条件或需要给具体的子元素设置类名时候，应当使用组件的props来传递

## **4.3** 绑定内联样式

使用v-bind:style(即:style)可以给元素绑定内联样式。方法与:class类似，也有对象语法和数组语法，看起来很像直接在元素上写CSS：

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>示例</title>
</head>
<body>
  <div id="app">
    <div :style="{ 'color': color, 'fontSize': fontSize + 'px' }"></div>
  </div>
  <script src="https://unpkg.com/vue/dist/vue.min.js"></script>
  <script>
    var app = new Vue({
      el: '#app',
      data: {
        color: 'red',
        fontSize: 14
      },
    });
  </script>
</body>
</html>
```

CSS属性名称使用驼峰命名(camelCase)和短横分隔命名(kebab-case)。

渲染后的结果为：`<div style="color: red; font-size: 14px;">文本</div>`

大多数情况下，直接写一长串的样式不便于阅读和维护，所以一般写在data或computed里。

以data为例改写上面的示例：

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>示例</title>
</head>
<body>
  <div id="app">
    <div :style="styles"></div>
  </div>
  <script src="https://unpkg.com/vue/dist/vue.min.js"></script>
  <script>
    var app = new Vue({
      el: '#app',
      styles: {
        color: 'red',
        fontSize: 14 + 'px'
      },
    });
  </script>
</body>
</html>
```

应用多个样式对象时,可以使用数组语法:`<div :style="[styleA, styleB]">文本</div>`

在实际业务中, :style的数组语法并不常用,因为往往可以写在一个对象里面; 而较为常用的应用是计算属性.

另外,使用:style时,Vue.js会自动给特殊的CSS属性名称增加前缀,比如transform。