# 项目简介
公司项目一直采用的是React全家桶开发的。近期公司让做一款项目，由于不是很急打算用Vue开发试试尝尝鲜。由于接口、UI都还没完事，自己先借这个git项目先熟悉熟悉Vue，把Vue中常用的知识点总结下方便自己的同时也方便他人。本项目从Vue基础语法入手，逐层递进，让您更好的掌握Vue各个基础知识点。包括Vue中的CSS动画原理、Vue中使用animate.css库、Vue中同时使用过渡与动画效果、Vue中使用Velocity.js库、Vue中多组件和列表过度 、Vue中动画的封装、vuex等等

### 下载源码
```
git clone https://github.com/fangfeiyue/vue-qu-na.git
```
### 技术选型
- 版本控制：git
- 开发工具：vscode
- 前端：Vue2.5
- 模块化方案：ES6 + Webpack
- 前后端分离方式：完全分离，纯静态方式
### 项目文件目录大概说明
- image文件主要放了一些readme文件需要的图片
- setting_up梳理项目前期的一些知识点，方便初学Vue的同学学习
- project是项目文件
## 项目前期知识准备
### MVVM模式

传统开发中前端通常使用MVP设计模式

![MVP](https://github.com/fangfeiyue/vue-qu-na/blob/master/image/chuantong.png)

在这种设计模式下我们通常把代码分为三层，M层是数据层，V层是是视图层一般指的是数据展示，P层是控制层,下面是一个简单的MVP模式对应的前端代码

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>TodoList</title>
    <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.js"></script>
</head>
<body>
     <div>
         <input id="input" type="text">
         <button id="btn">提交</button>
         <ul id="list">
         </ul>
     </div>
     <script>
         function Page(){
              
         }
         $.extend(Page.prototype, {
             init: function(){
                 this.bindEvents();
             },
             bindEvents: function(){
                 var btn = $('#btn');
                 btn.on('click', $.proxy(this.handleBtnClick, this));
             },
             handleBtnClick: function(){
                 var inputElem = $('#input');
                 var inputValue = inputElem.val();
                 var ulElem = $('#list');
                 ulElem.append('<li>' + inputValue + '</li>');
             }
         });

         var page = new Page();
         page.init();
     </script>
</body>
</html>
```
这个例子因为没有请求接口，所以弱化了M数据层。div里面是V层，script标签里的操作Dom的是P层，当我们点击页面按钮提交时会执行P层的代码，更新页面。P层相当于M和V层的中转站，P层中的很大一份代码都是在做DOM的操作。而前端存在大量操作DOM的行为会严重影响页面性能。

下图是vue中MVVM模式的示意图

![MVP](https://github.com/fangfeiyue/vue-qu-na/blob/master/image/mvvm.png)

MVVM模式中也有视图层V和数据层M，但是没有P层取而代之的ViewModel层，从上图我们也可以看出VM层是vue自己带的，由此可以看出在使用MVVM进行开发Dom的操作被极大的简化了代码量会得到显著的减少，我们只需要关注M层和V层，更多是关注的M层就可以了。

### 组件初识

Vue中组件简单来说可以分为以下两种
- 全局组件
- 局部组件。

下面我们就来写了个小demo来看看这两种组件如何使用。

先来用Vue写一个阉割版todolist，这个只能实现添加功能，代码如下：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>TodoList</title>
    <script src="./vue.js"></script>
</head>
<body>
    <div id="app">
        <input type="text" v-model="inputValue">
        <button v-on:click="handleBtnClick">提交</button>
        <ul>
            <li v-for="item in list">{{item}}</li>
        </ul>
    </div>
    <script>
        var app = new Vue({
            el: "#app",
            data: {
                list:[],
                inputValue: ''
            },
            methods: {
                handleBtnClick: function(){
                    this.list.push(this.inputValue);
                    this.inputValue = '';
                }
            }
        });
    </script>
</body>
</html>
```
运行上述代码就实现了只带添加功能的TodoList，从运行结果看，添加的内容我们可以分装成一个组件也就是`ul`中的`li`标签。

全局组件的运用，在`script`标签中添加如下代码
```
Vue.component("TodoItem", {
    props: ['content'],
    template: "<li>{{content}}</li>"
});
```
上述代码中我们定义了一个Vue的全局组件`TodoItem`,下一步`<todo-item></todo-item>`替换`<li></li>`,注意我们定义的组件在使用的时候，字母要全部小写，大写字母转为小写后要用`-`连接，如`TodoItem`在使用时写为`todo-item`。

上述为全局组件的使用，下面我们试试局部组件。

在`script`标签中添加如下代码
```
var TodoItem = {
    props: ['content'],
    template: "<li>{{content}}</li>"
};
```
注意，此时我们还不能直接使用局部组件，局部组件要在new Vue()中通过components注册后方可使用，代码如下:
```
var app = new Vue({
    ...
    components: {
        TodoItem: TodoItem 
    }
    ...
});
```
注册完成后就可以像全局组件一样使用局部组件了。

`注意：v-bind可以缩写为:，v-on可以缩写为@。`

### 子组件向父组件传值初探

上面我们只实现了简单的添加todo，如果我们已经完成了某了todo，点击对应的todo需要删掉它，该怎么实现呢？首先我们用的是Vue，就不需要特别关注dom的操作，我们只需要处理数据即可，当我们点击某项todo，只要删除list数组中选中的项即可，数组中的数据发生了变化，页面也会跟随着变化。

首先我们分别修改html部分和js部分的代码
```
// html
<todo-list
    :index="index"
    @delete="handleItemDelete"  
>
</todo-list>

// js
var TodoList = {
    props: ['content', 'index'],
    template: "<li @click='handleItemClick'>{{content}}</li>",
    methods: {
        handleItemClick: function(){
            this.$emit("delete", this.index);
        }
    }
};

var app = new Vue({
    ...
    methods: {
        ...
        handleItemDelete: function(index){
            // 删除数组中指定索引的元素
            this.list.splice(index, 1);
        }
    }
});
```

`$emit`方法可以向外触发事件。
### 生命周期

生命周期函数就是Vue实例在某一个时间点会自动执行的函数.

![生命周期图示](https://github.com/fangfeiyue/vue-qu-na/blob/master/image/lifecycle.png)

### Vue的模板语法初探
v-text、v-html、{{}}都可以现实变量中的内容，区别在于，如果变量内容是被hmtl元素包裹的话，v-html会输出html语法中的样式，v-text和{{}}只输出普通的文本.
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        {{message}}
        <div v-text="message"></div>
        <div v-html="message"></div>
    </div>
    <script src="../vue.js"></script>
    <script>
        var app = new Vue({
            el:'#app',
            data: {
                message: '<h1>hell world</h1>'
            }
        });
    </script>
</body>
</html>
```

### 计算属性,方法与侦听器

- 计算属性 `computed` 这个属性有一个重要的特性就是内置缓存，当它里面用于计算的属性的值都没有发生变化的时候，这个属性执行一次就不再执行了，除非参与计算的属性的值发生了变化。

- 方法属性 `methods`
- 监听属性 `watch`

下面我们分别用这三个属性计算出一个完整的名字显示到页面上
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        {{fullName}}
    </div>
    <script src="../vue.js"></script>
    <script>
        var app = new Vue({
            el: "#app",
            data: {
                firstName: 'Fang',
                lastName: 'Feiyue'
            },
            // 计算属性
            computed: {
                fullName: function(){
                    console.log("计算了一次");
                    return this.firstName + this.lastName;
                }
            },
            // 方法
            methods: {
                fullName: function(){
                    return this.firstName + this.lastName;
                }
            },
            // 监听器
            watch: {
                firstName: function(){
                    this.fullName = this.firstName + this.lastName;
                },
                lastName: function(){
                    this.fullName = this.firstName + this.lastName;
                }
            }
        });
    </script>
</body>
</html>
```
综合对比三种方法，如果用methods的话，每个数据变化都会执行一遍，没有这个必要，我们只需要firtName和lastName变化的时候执行即可，如果用监听器的话虽然firstName和lastName的值在程序运行过程中不变的话里面的方法执行一次就不再执行了，但代码明显多很多，还要监听多个变量，由此看来使用计算属性的话更能适合这种方式。

### 计算属性的 getter和setter

还是上面的计算全名的例子，我们将代码修改如下
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        {{fullName}}
    </div>
    <script src="../vue.js"></script>
    <script>
        var app = new Vue({
            el: '#app',
            data: {
                firstName: 'fang',
                lastName: 'feiyue'
            },
            computed: {
                fullName: {
                    get: function(){
                        return this.firstName + this.lastName;
                    },
                    set: function(value){
                        var arr = value.split(' ');
                        this.firstName = arr[0];
                        this.lastName = arr[1];
                    }
                }
            }
        });
    </script>
</body>
</html>
```
对比过代码后，我们发现在`computed`属性中我们把之前fullName方法改成了对象，并且在这个对象中新增了两个方法一个是get一个是set。当我们获取fullName值时，会执行get方法，set方法在我们改变fullNmae值的时候回执行，将fullName改变前的值当做传入set方法中。这个例子中，我们在set方法中又重新操作了firstName和lastName，这两种值的变化会重新引起computed执行，从而更新fullName的值。

### Vue中的样式绑定

1. 类的使用

方式一,对象方式：
```
// html文件
<div 
    @click="handleDivClick"
    :class="{activated: isActivated}"
>
    hello world
</div>

// js文件
var app = new Vue({
    el: '#app',
    data: {
        isActivated: false,
    },
    methods: {
        handleDivClick: function(){
            this.isActivated = !this.isActivated;
        }
    }
});
```

方式二，数组方式：
```
// html
<div 
    @click="handleDivClick"
    :class="[activated]"
>
    hello world
</div>

// js文件
var app = new Vue({
    el: '#app',
    data: {
        activated: ''
    },
    methods: {
        handleDivClick: function(){
            this.activated = this.activated  ? '' : 'activated';
        }
    }
});
```
既然是class既然给的是一个数组，数组中肯定可以包含多个元素，即元素可以有多个类名

2. style的使用

对象方式
```
// html
<div 
    @click="handleDivClick"
    :style="styleObj"
>
    hello world
</div>

// js
var app = new Vue({
    el: '#app',
    data: {
        styleObj: {
            color: ''
        }
    },
    methods: {
        handleDivClick: function(){
            this.styleObj.color = this.styleObj.color ? '' : 'red';
        }
    }
});
```

数组方式
```
<div 
    @click="handleDivClick"
    :style="[styleObj, {fontSize:'12px'}]"
>
    hello world
</div>
```
### Vue中的条件渲染

1. v-show和v-if都能控制页面元素的显示，v-if对应的值为false时元素不会被渲染到Dom结构中，v-show对应的值为false时，元素会被渲染到Dom结构中只不过它的display为none。当我们频繁的去操作一个元素显示和隐藏时用v-show更合适，它不会频繁的将这个元素从Dom中删除再添加,性能要高些。
```
<div id="app">
        <div  v-if="show">{{message}}</div>
        <div  v-show="show">{{message}}</div>
</div>
<script src="../vue.js"></script>
<script>
    var app = new Vue({
        el: '#app',
        data: {
            show: false,
            message: 'hello world'
        }
    });
</script>
```

2. v-if、v-else的使用
```
<div id="app">
    <div  v-if="show">{{message}}</div>
    <div v-else>I love doudou</div>
</div>
<script src="../vue.js"></script>
<script>
    var app = new Vue({
        el: '#app',
        data: {
            show: false,
            message: 'hello world'
        }
    });
</script>
```

`注意：v-if和v-else一起使用时中间不能有其他元素阻隔他俩，一定要在一起写`

3. v-if、v-else的使用
```
<div id="app">
    <div  v-if="show=='if'">if</div>
    <div  v-else-if="show=='else if'">else if</div>
    <div v-else>else</div>
</div>
<script src="../vue.js"></script>
<script>
    var app = new Vue({
        el: '#app',
        data: {
            show: false,
            message: 'hello world'
        }
    });
</script>
```

`注意：v-if、v-else-if、v-else一起使用时中间不能有其他元素阻隔他们，一定要在一起写`

### key值初始
```
<div id="app">
    <div v-if="show">
        姓名<input key="userName" type="text" name="" id="">
    </div>
    <div v-else="show">
        密码<input key="email" type="text" name="" id="">
    </div>
</div>
<script src="../vue.js"></script>
<script>
    var app = new Vue({
        el: '#app',
        data: {
            show: false
        }
    });
</script>
```

上述代码如果去掉input中对应的key值，在密码框输入数字，当改变show值时，input的标题已经变成姓名，但输入框中依然是之前用的密码的输入框，原因是vue会尽可能的复用页面上的元素，当给input赋值不同的key后页面就会显示正常，这里涉及到了diff算法，后期专门讲解。
### Vue中的列表渲染

遍历数组和对象
```
<div id="app">
    <div v-for="(item, index) of list" :key="index">
        {{index}}=>{{item}}
    </div>
    <div v-for="(item, key, index) of obj" :key="key">
        {{index}}=>{{key}}=>{{item}}
    </div>
</div>
<script src="../vue.js"></script>
<script>
    // pop、push、shift、unshift、splice、 sort、reverse、
    var app = new Vue({
        el: '#app',
        data: {
            list: [
                '房飞跃',
                '逗逗',
                '卢浩峰',
                '张明远',
                '张力战',
                '呼啦圈'
            ],
            obj: {
                name: 'fang',
                age: 18,
                score: 150
            }
        }
    });
</script>
```

### Vue中的set方法
上述代码，当我们改变数组中的元素引起页面重新渲染可以使用pop、push、shift等数组方法，或者给数组变变量重新赋值新数组来改变它的引用。当我们改变对象中的属性来引起页面重新渲染，只能通过改变它的引用。

我们还可以用Vue中的set属性来改变数组或对象的值，来引起页面的重新渲染。
```
<div id="app">
    <button @click='handleAddItem'>添加元素</button>
    <div v-for="(item, key) of obj" :key="key">{{item}}</div>
    <div>
        <div v-for="item of list" :key="item">{{item}}</div>
    </div>
</div>
<script src="../vue.js"></script>
<script>
    var app = new Vue({
        el: '#app',
        data: {
            obj: {
                name :'fang',
                age: 18,
                tel: '18888888888'
            },
            list: [1, 2, 3, 4, 5]
        },
        methods: {
            handleAddItem: function(){
                // 改变对象的值
                this.$set(this.obj, 'sex', '男');    
                
                // 改变数组中的值
                Vue.set(this.list, 6, 6);
            }
        }
    });
</script>
```
### 使用组件的细节点
1. is 属性
```
<div id="app">
    <table>
        <tbody>
            <tr is="row"></tr>
            <tr is="row"></tr>
            <tr is="row"></tr>
        </tbody>
    </table>
</div>
<script src="../vue.js"></script>
<script>
    var row = {
        template: '<tr><td>3item</td></tr>'
    };

    var app = new Vue({
        el: '#app',
        components: {
            row: row
        }
    });
</script>
```
上述代码当我们成功运行后，表面上看浏览器是呈现了三个3item，好像一切正常，但当我们审查元素会发现tr标签跑道table标签外面显示了，这显然不对，主要是tbody标签中必须放tr标签，tbody不能识别我们定义的组件row，浏览解析的时候就会出问题。

这个时候我们可以用is来解决这种问题,将tbody中的`<row></row>`换成`<tr is="row"></tr>`,此时再刷新页面审查元素发现Dom结构正确了。

2. 在子组件中定义data，data必须是一个函数并且发挥一个对象
```
<div id="app">
    <table>
        <tbody>
            <tr is="row"></tr>
            <tr is="row"></tr>
            <tr is="row"></tr>
        </tbody>
    </table>
</div>
<script src="../vue.js"></script>
<script>
    var row = {
        // 报错，在子组件中定义data，data必须是一个函数并且发挥一个对象
        // data:{
        //     content: '3item'
        // },
        data: function(){
            return {
                content: '3item'
            };
        },
        template: '<tr><td>{{content}}</td></tr>'
    };

    var app = new Vue({
        el: '#app',
        components: {
            row: row
        }
    });
</script>
```
因为子组件可能会被调用多次，所以每个子组件都应该有自己的数据，不应该共享一套数据， 通过一个函数来返回一个对象的目的就是为了让每个子组件都有自己的独立存储，这样的话不会出现多个子组件互相影响的情况。

3. refs引用
 - ref 应用在dom元素上

 在某些特殊情况下我们可能是需要操作Dom元素，这是我们可以使用ref
 ```
 <div id="app">
    <div ref="hello" @click="handleClick">hello world</div>
</div>
<script src="../vue.js"></script>
<script>
    var app = new Vue({
        el: '#app',
        methods: {
            handleClick: function(){
                console.log(this.$refs.hello.innerHTML);
            }
        }
    });
</script>
 ```
 上述代码我们通过给div元素添加ref属性，在js中用this.$refs.hello获取到了div元素

 - ref应用在子组件上
 ```
 <div id="app">
    <counter ref="one" @change="handleChange"></counter>
    <counter ref="two" @change="handleChange"></counter>
    <div>{{total}}</div>
</div>
<script src="../vue.js"></script>
<script>
    Vue.component('counter', {
        template: "<div @click='handleClick'>{{number}}</div>",
        data: function(){
            return {
                number: 0
            };
        },
        methods: {
            handleClick: function(){
                this.number++;
                this.$emit('change');
            }
        }
    });
    var app = new Vue({
        el: '#app',
        data: {
            total:0
        },
        methods:{
            handleChange: function(){
                this.total = this.$refs.one.number + this.$refs.two.number;
            }
        }
    });
</script>
 ```
 ### 父子组件间的数据传递

 ```
 <div id="app">
    <counter count="0" @change="handleClick"></counter>
    <counter count="0" @change="handleClick"></counter>
    {{total}}
</div>
<script src="../vue.js"></script>
<script>
    var counter = {
        props: ['count'],
        data: function(){
            return {
                number: this.count
            };
        },
        template: '<div @click="handleClick">{{number}}</div>',
        methods: {
            handleClick: function () {
                this.number++;
                this.$emit('change', 1);
            }
        }
    };
    var app = new Vue({
        el: '#app',
        components: {
            counter: counter
        },
        data: {
            total: 0
        },
        methods: {
            handleClick: function(value){
                this.total += value;
            }
        }
    });
</script>
 ```

父组件向子组件传值通过props即可，子组件向父组件传值需要通过事件，注意，vue是单向数据流，子组件不能直接修改父组件传递过来的值，实在要修改的话可以用一个变量承接，修改这个副本。

### 组件参数校验与非props特性

1.校验

父组件可以向子组件传递属性值，子组件也可以通过props接收父组件传递过来的值，同时我们可以为组件的 prop 指定验证规则。如果传入的数据不符合要求，Vue 会发出警告。这对于开发给他人使用的组件非常有用。
```
var child = {
    props: {
        content: [String, Number], // 类型的校验，多个类型要放在数组里
        content: {
            type: String, // 类型的校验
            required: true, // 是否必须
            default: 'doudou', // 默认值
            validator: function(value){ //  自定义校验器
                    return value.length > 5;
            }
        }
    },
    template:"<div>{{content}}</div>"
};
```

2. 非 Prop 特性

父组件向子组件传递了值，但子组件不通过prop进行接收，这是就不能直接使用父组件传递过来的值。 同时，父组件传递过来的属性值会显示在子组件最外层html标签上
```
// html
...
<div id="app">
    <child content="123"></child>
</div>

// js
var child = {
    template:"<div>{{content}}</div>"
};
...
```
成功运行上述代码后,打开浏览器控制台审查元素就会看到`<div content="123"></div>`div上显示了父组件传递过来的属性

### 给组件绑定原生事件

```
// html
<div id="app">
    <child @click="handleClick"></child>
</div>

// js
var child = {
    template:"<div>child</div>",
};

var app = new Vue({
    el: '#app',
    components: {
        child: child
    },
    methods: {
        handleClick: function(){
            alert('click');
        }
    }
});
```

如上代码，当我们直接点击child的组件浏览器并不会弹出click，要想弹出click必修跟child组件中的div绑定事件，然后在child组件中的methods属性中定义对应的方法，通过this.$emit触发外层的handleClick方法，从而弹出click
```
<div id="app">
    <child @click="handleClick"></child>
</div>
<script src="../vue.js"></script>
<script>

    var child = {
        template:"<div @click='handleClick'>child</div>",
        methods: {
            handleClick: function(){
                this.$emit('click');
            }
        }
    };

    var app = new Vue({
        el: '#app',
        components: {
            child: child
        },
        methods: {
            handleClick: function(){
                alert('click');
            }
        }
    });
</script>
```
这也未免操作有点麻烦，我们可以通过给父组件传递的@click后加一个native属性实现click的弹出
```
<div id="app">
    <child @click.native="handleClick"></child>
</div>
<script src="../vue.js"></script>
<script>

    var child = {
        template:"<div>child</div>"
    };

    var app = new Vue({
        el: '#app',
        components: {
            child: child
        },
        methods: {
            handleClick: function(){
                alert('click');
            }
        }
    });
</script>
```
这样代码就会简洁很多。

### 在Vue中使用插槽

1.简单的插槽使用

当我们想要给子组件中传递dom元素时，我们可以怎么做呢？

首先根据前面的知识我们一定能想到用父组件给子组件传值的方法，本例以传递一个`<p>hello world</p>`为例
```
<div id="app">
    <child content="<p>hell world</p>">
        
    </child>
</div>
<script src="../vue.js"></script>
<script>
    var child = {
        props: ['content'],
        template: `<div>
            <p>test</p>
            <div v-html="this.content"></div>
        </div>`
    };
    var app = new Vue({
        el: '#app',
        components: {
            child
        }
    });
</script>
```
运行上述代码，浏览器页面就会出现hello world。但是当我审查元素的时候就会发现hello world的p元素外面包裹了一层div，而我们只想显示p里面的hello world，这不是我们想要的结果，并且如果我们传入的dom元素多的时候，代码看起来就会很混乱。这时我们就需要使用到插槽了，更改上述代码如下。
```
<div id="app">
    <child>
        <p>hell world</p>
        <p>hell world</p>
    </child>
</div>
<script src="../vue.js"></script>
<script>
    var child = {
        props: ['content'],
        template: `<div>
            <p>test</p>
            <slot></slot>
        </div>`
    };
    var app = new Vue({
        el: '#app',
        components: {
            child
        }
    });
</script>
```
我们在引用child组件里直接输入了`<p>hello world</p>`，然后在组件里直接用`<slot></slot>`来接收，slot就是我们所说的插槽。

slot标签还可以写默认内容。上述代码，如果我们不让child包裹任何dom元素，子组件的slot中改写为`<slot>default value</slot>`运行代码页面就会显示出default value。

2. 具名插槽

上述代码我们看出slot代表的是child包裹的所有dom元素，但当我们想查分使用传入的元素改怎么办呢？
```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <child>
            <p>header</p>
            <p>footer</p>
        </child>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.5.17-beta.0/vue.js"></script>
    <script>
        var child = {
            props: ['content'],
            template: `<div>
                <slot></slot>
                <p>test</p>
                <slot></slot>
            </div>`
        };
        var app = new Vue({
            el: '#app',
            components: {
                child
            }
        });
    </script>
</body>

</html>
```
上述代码们想将footer和header分别插在test的上面和下面，运行代码后看到并不是我们想要的结果，原因是slot代表所有child插入的dom元素。如果想实现这种效果改怎么办呢？这时我们就需要使用到具名插槽了，分别给header和footer添加属性 slot="header"和slot="footer",然后再在child子组件中给slot赋值name属性,运行代码就会得到我们想要的结果
```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <child>
            <p slot="header">header</p>
            <p slot="footer">footer</p>
        </child>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.5.17-beta.0/vue.js"></script>
    <script>
        var child = {
            props: ['content'],
            template: `<div>
                <slot name="header"></slot>
                <p>test</p>
                <slot name="footer"></slot>
            </div>`
        };
        var app = new Vue({
            el: '#app',
            components: {
                child
            }
        });
    </script>
</body>

</html>
```
### 作用域插槽
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <child>
        </child>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.5.17-beta.0/vue.js"></script>
    <script>
        var child = {
            data: function(){
                return {
                    list: [1, 2, 3, 4, 5]
                };
            },
            template:`<div>
                        <ul>
                            <li v-for="item of list">{{item}}</li>
                        </ul>
                    </div>`
        };
        var app = new Vue({
            el:'#app',
            components: {
                child
            }
        });
    </script>
</body>
</html>
```
上述代码我们调用了child组件显示child组件中list数组中的内容，运行代码，结果显示正确。但是child组件可能在很多地方会被调用，我们希望根据不同的调用情况来控制子组件显示的内容和样式该怎么实现呢？
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <child>
            <template slot-scope="props">
                <li>
                    {{props.item}}
                </li>
            </template>
        </child>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.5.17-beta.0/vue.js"></script>
    <script>
        var child = {
            data: function(){
                return {
                    list: [1, 2, 3, 4, 5]
                };
            },
            template:`<div>
                        <ul>
                            <slot v-for="item of list" :item=item></slot>
                        </ul>
                    </div>`
        };
        var app = new Vue({
            el:'#app',
            components: {
                child
            }
        });
    </script>
</body>
</html>
```
上述代码我们将child组件中的li标签用slot代替了，父组件中我们向子组件传递了
```
<template slot-scope="props">
    <li>
        {{props.item}}
    </li>
</template>
```
注意，一定要用`template`包裹想传给子组件的元素，`slot-scope`接收子组件传递过来的数据。`slot-scrope`的值不必非要是props可以根据自己需求来。

### 动态组件与v-once指令
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <child-one v-if="type === 'child-one'"></child-one>
        <child-two v-else></child-two>
        <button @click="handleBtnClick">change</button>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.5.17-beta.0/vue.js"></script>
    <script>
        var childOne = {
            template: "<div>child-one</div>"
        };
        
        Vue.component('child-two', {
            template: "<div>child-two</div>"
        });

        var app = new Vue({
            el: "#app",
            components: {
                childOne
            },
            data: {
                type:'child-one' 
            },
            methods: {
                handleBtnClick: function() {
                    console.log('点击了我');
                    this.type = this.type === 'child-one' ? 'child-two' :'child-one'
                }
            }
        });
    </script>
</body>
</html>
```
上述代码当我点击change按钮的时候，实现了child-one和child-two来回切换，但如果我们需要切换的组件比较多的时候这样操作就比较麻烦，这是我们可以使用Vue提供的动态组件来实现这个功能。通过使用保留的 `<component>` 元素，并对其 `is` 特性进行动态绑定，就可以在同一个挂载点动态切换多个组件了。上述代码中的
```
<child-one v-if="type === 'child-one'"></child-one>
<child-two v-else></child-two>
```
替换为
```
<component :is="type"></component>
```
即可动态切换多个组件。完整代码如下
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <component :is="type"></component>
        <!-- <child-one v-if="type === 'child-one'"></child-one>
        <child-two v-else></child-two> -->
        <button @click="handleBtnClick">change</button>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.5.17-beta.0/vue.js"></script>
    <script>
        var childOne = {
            template: "<div>child-one</div>"
        };
        
        Vue.component('child-two', {
            template: "<div>child-two</div>"
        });

        var childThree = {
            template: "<div>child-three</div"
        };

        var app = new Vue({
            el: "#app",
            components: {
                childOne,
                childThree
            },
            data: {
                type:'child-one' 
            },
            methods: {
                handleBtnClick: function() {
                    this.type = this.type==='child-one'?'child-two':this.type==='child-three'?'child-one':'child-three';
                },
            }
        });
    </script>
</body>
</html>
```
但是上述代码还是有问题的，上述代码在组件来回切换的时候步骤大概为当一个组件不显示了就销毁需要显示的时候再创建，如果切换的组件较多这样就会比较耗费性能。我们可以给每个组件添加个`v-once`指令，这个指令只渲染元素和组件一次。随后的重新渲染，元素/组件及其所有的子节点将被视为静态内容并跳过。这可以用于优化更新性能。



### 联系方式

坐标：北京

QQ：294925572

QQ技术交流群：678941904 ( 欢迎加入此群，与志同道合的朋友一起进步 )






