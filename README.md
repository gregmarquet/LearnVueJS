# LearnVueJS

## Vue JS

[Vue.js](https://vuejs.org/)

[vue.js installation](https://vuejs.org/v2/guide/installation.html)

```html
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```
In order to display our Vue template, we need first to load vue, (in this example, we use the script tag to do that), and a distination div, using an ID generally called "app" in our html document. 

``` html
<body>
  <script src="https://unpkg.com/vue/dist/vue.js"></script>
  <div id="app">
  </div>
</body>
```

Then in the JS file, we need to create a new vue instance

``` javascript
new Vue({
	el: '#app',
	data: {
		title: 'Hello World!'
	}
})
```
The `el` attribute is where you store the name of the ID of the HTML element that will contain the view app.

In this example, you can also see the `data` attribute, it's a little bit like the state in React. We will have access to the values under data using the `{{ title }}` notation. We will not need to write `{{ data.title }}` as vue is doing some magic under the hood.

In the view instance, you can also store methods adding a methods attribute like so: 

``` javascript
new Vue({
	el: '#app',
	data: {
		title: 'Hello World!'
	},
    methods: {
      sayHello: function() {
        return 'Hello!';
      }
    }
})
```
And in the html file, you can access the method the same way you were accessing the data, using `{{ sayHello() }}` without having to use `methods` in front of `sayHello()`

```html
<div id="app">
  <p>{{ sayHello() }}</p>
</div>
```
If you wanted to access the data in the methods, you could use `this` and you would not have to use `data.title`, but `this.title`

```javascript
new Vue({
	el: '#app',
	data: {
		title: 'Hello World!'
	},
    methods: {
      sayHello: function() {
        return this.title;
      }
    }
})
```
The `{{ }}` syntax is used where text would normally appear in you HTML, you cannot use that syntax the same way to use data as an attribute. For example, this would not work: 

```html
<a href="{{ link }}">google</a>
```
You would have to use a `directive` `v-bind` and it would look like this:

``` html
<a v-bind:href="link">Google</a>
```

when using `v-bind` the directive is telling Vue, "don't use the normal attribute, use this v-bind attribute" in this case `v-bind:href`
For sure, this `link` attribute needs to exist in the data vue instance.

``` javascript
new Vue({
	el: '#app',
	data: {
		title: 'Hello World!',
        link: 'http://google.com'
	},
    methods: {
      sayHello: function() {
        return this.title;
      }
    }
})
```

### what is a Directive?

A directive is an instruction you place in your code. You can pass argument to directive using a colon `:` it allows you to pass dynamic data to html attribute.

### v-once
You can change the value of title using a function for example and in Vue, if the data is changed then all instance of that data attribute would be re-rendered. If you don't want a certain instance of this attribute to be re-rendered, then use `v-once` then the attribute will be used only once.

For example 

```javascript
new Vue({
	el: '#app',
	data: {
	  title: 'Hello World!',
      link: 'http://google.com'
	},
    methods: {
      sayHello: function() {
        this.title = 'Hello';
        return this.title;
      }
    }
})
```
On line 9, we change the `title` to be 'Hello'. If we want the `title` in the `<h1>` to not be changed, we can use `v-once`, so the title will be rendered only once and when all other reference of `title` will be re-rendered because of the title re-assignment, the `H1` will not change.

``` html
<div id="app">
  <h1 v-once>{{ title }}</h1>
    <p>{{ sayHello() }} - <a v-bind:href="link">Google</a></p>
  </div>
```
### v-html
You have to be careful when using this one. Normally Vue is displaying text and not html tag, so if your were to set this in data for example: 

``` javascript
finishedLink: '<a href="http://google.com">Google</a>'
```
and try to render it in your html like this:

``` html
<p >{{ finishedLink }}</p>
```
the page would display:

`<a href="http://google.com">Google</a>`

because vue display the string, if you want instead to display the link, then you can use `v-html` but you have to be careful and you really need to trust the link that you display, but you would do that:

``` html
<p v-html="finishedLink"></p>
```
This would display the link that you expect.

### v-on

If you want to listen to an event, you can use the `v-on` directive.

Here is an example where I bind an event listener to a button and call a function on click. 

``` javascript
new Vue({
	el: '#app',
	data: { 
      counter: 0
	},
    methods: {
      increase: function() {
        this.counter++;
      }
    }
})
```

The counter in the `<p>` tag re-render on every data.counter update: 

``` html
<div id="app">
  <button v-on:click="increase">Click me</button>
  <p>{{ counter }}</p>
</div>
```
The `event` is automatically passed to the function that is called on the event, So we can use it in the function, here is an example:

``` html
<div id="app">
  <p v-on:mousemove="updateCoordinates">Coordinates: {{ x }} / {{ y }}</p>
</div>
```
And on the vue function, we can access the event object:

``` javascript
new Vue({
	el: '#app',
	data: {
		x: 0,
    y:0
	},
    methods: {
      updateCoordinates: function(event) {
        this.x = event.clientX;
        this.y = event.clientY;
      }
    }
})
```

If we want to pass an argument to our function, we can pass it in the html template and use the argument in our function in the Vue instance.

``` html
<div id="app">
  <button v-on:click="increase(2)">Click me</button>
  <p>{{ counter }}</p>
</div>
```
In this example, I pass the argument 2, look at the function in the vue instance now: 

``` javascript
new Vue({
	el: '#app',
	data: {
		counter: 0
	},
    methods: {
      increase: function(num) {
        this.counter += num;
      }
    }
})
```

Also, if you want to pass the `event` argument, you need to use the special keyword `$event` like so:

``` html
<button v-on:click="increase(2, $event)">Click me</button>
```

### Event Modifier

Looking back at the `updateCoordinates` example, if we wanted to add a span where the even was not propagating in the `<p>` tag, we could do:

``` html
<div id="app">
  <p v-on:mousemove="updateCoordinates">
  Coordinates: {{ x }} / {{ y }} 
  - <span v-on:mousemove="inactive">DEAD ZONE</span>
  </p>
</div>
```
so adding a new event and using another function that would stop the propagation, like that: 

``` javascript
new Vue({
	el: '#app',
	data: {
		x: 0,
    y:0
	},
    methods: {
      updateCoordinates: function(event) {
        this.x = event.clientX;
        this.y = event.clientY;
      },
      inactive: function(event) {
        event.stopPropagation();
      }
    }
})
```

But there is a better way. To modify event, Vue gives us access to some event modifier. So we could use the `stop` event modifier:

``` html
<div id="app">
  <p v-on:mousemove="updateCoordinates">
  Coordinates: {{ x }} / {{ y }} 
  - <span v-on:mousemove.stop="">DEAD ZONE</span>
  </p>
</div>
```
It is that easy and we don't need to use a function.

Another very important modifier is the `prevent` modifier which will `preventDefault`. On another note, it is possible to chain modifier using a period in between.

### Key Modifier

Te same way we have event modifier, Vue give us Key modifier that we would use the same way. Imagine we have an input and want our function to run only when the enter key is up, or both the enter key and the space key.

```html 
<input type="text" v-on:keyup.enter.space="alertMe">
```

In this example, we imagine that we have the function alertMe in our vue instance. the key modifier `enter` and `space` are modifying the keyup event, so that now, we are listening for an enter or space keyup.

As an important point, keep in mind that you can use simple javascript expression between the `{{ }}` and also in between the  `" "` when using a directive for exemple: 

```html
<button v-on:click="value = $event.target.innerHTML">Show Alert</button>
```
Not sure why I would do the last one, but it works.

### Two way data binding with v-model

To set up a two way data binding, you use the v-model:

```html
<input type="text" v-model="name">
<p>{{ name }}</p>
```

In this case, the input will display the name on first render, but if you change the input field, the `<p>` tag will also update, so the model binded two ways.

### Computer Properties

functions can be added to the `methods` property in the Vue instance, in wich case it will be run everytime anything is rendered/re-rendered on the page.

On the other end, if you add a function to the `computed` property, first you don't need to call it using the function call `()`, you just call it like any properties in the `data` property in vue instance. And secondly, it keeps track of the properties that it depens on. So it is run only when the properties that it depens on is updated.

``` html
<div id="exercise">
    <button v-on:click="counter++">increase</button>
    <button v-on:click="secounCounter++">increase second</button>

    <p>{{ counter }} | {{ secoundCounter }}</p>
    <p>{{ result() }} | {{ output }}</p>
</div>
```

``` javascript
new Vue({
        el: '#exercise',
        data: {
            counter: 0,
            secoundCounter: 0
        },
        computed: {
        	output: function() {
          		console.log('output')
              return this.counter > 5 ? 'Greater' : 'Smaller';
          }
        },
        methods: {
        		result: function() {
            		console.log('result')
            		return this.counter > 5 ? 'Greater' : 'Smaller';
            }
        }
    });
```

In this example, the output function will be run only when increase button is clicked and not when the button increase second is clicked, because it knows that it doesn't depend on `secoundCounter`

on the other hand, the `result` function will be run no matter what button is clicked.

The computed properties is the prefered way and we should try to use computed properties wherever we can, because it utilizes the cashing system and run only when needed, but computed properties can only run syncronously. 

### watch

So when you need to run asyncronist code, you should use the `watch` property.
The watch property will track a data property and run only when this data is updated:

``` javascript
new Vue({
        el: '#exercise',
        data: {
            counter: 0,
            secounCounter: 0
        },
        computed: {
        	output: function() {
          		console.log('output')
              return this.counter > 5 ? 'Greater' : 'Smaller';
          }
        },
        methods: {
        		result: function() {
            		console.log('result')
            		return this.counter > 5 ? 'Greater' : 'Smaller';
            }
        },
        watch: {
        	counter: function(value) {
          		let vm = this;
              setTimeout(function() {
              		vm.counter = 0
              }, 2000);
          }
        }
    });
```

On the watch counter function, you can see that I have access to the upcoming change with the `value` argument. 

You can see that I have to set the vm variable to store `this` because the callback closure would not have access to `this`.

### shorthands

#### v-on --- @

```html
<button v-on:click="counter++">increase</button>
```
Is the same as:

```html
<button @click="counter++">increase</button>
```
#### v-bind --- :

``` html
<a v-bind:href="link">Google</a>
```

Is the same as:

``` html
<a :href="link">Google</a>
```
### Dynamic Styling - Basic

We can dynamically attach a class on a click for example using `:class`

```html
<body>
  <script src="https://unpkg.com/vue/dist/vue.js"></script>
  <div id="app">
    <div 
      class="box" 
      @click="attachRed = !attachRed"
      :class="{ red: attachRed }"  ></div>
    <div class="box"></div>
    <div class="box"></div>
  </div>
</body>
```

``` javascript
new Vue({
    el: '#app',
    data: {
        attachRed: false
    }
})
```

In CSS we would have the class `.red` here `attachRed` is either true or false on click, we assign `attachRed` its opposite value, so if it's true, it becomes false.

we then use  

``` html 
:class="{ red: attachRed }" 
```
to assign the class red when `attachRed` is true.

### Dynamic Styling - Using Object

If we can use a computer function to return an object:

``` html
<body>
  <script src="https://unpkg.com/vue/dist/vue.js"></script>
  <div id="app">
    <div 
      class="box" 
      @click="attachRed = !attachRed"
      :class="divClass"></div>
    <div class="box"></div>
    <div class="box"></div>
  </div>
</body>
```
Notice on line 7, we are using the computed property, and we don't use `{ }`.

``` javascript
new Vue({
    el: '#app',
    data: {
        attachRed: false
    },
    computed: {
    	divClass: function() {
          return {
            red: this.attachRed
          }
      }
    }
})
```

### Dynamic Styling - Using Names

Sometimes you don't want to decide `if` a class should be attached or not, but `which` class should be attached.

``` html
<body>
  <script src="https://unpkg.com/vue/dist/vue.js"></script>
  <div id="app">
    <div 
      class="box" 
      @click="attachRed = !attachRed"
      :class="divClass"></div>
    <div class="box" :class="color"></div>
    <input type="text" v-model="color">
  </div>
</body>
```

You can see on line 9, we set a two way data binding using the v-model. and on line 8 `:class` is set to the `color` data property. as long as we have a class that match the data property it will work. If you change what is in the input for another class name, it will change the color. For sure you would have to set the data property of `color`.

``` javascript
new Vue({
    el: '#app',
    data: {
        attachRed: false,
        color: 'green'
    },
    computed: {
    	divClass: function() {
        return {
          red: this.attachRed
        }
      }
    }
})
```

You don't have to just assign a name value, you can also assign an array like this: 

```html
<div class="box" :class="[color, {red: attachRed}]"></div>
```

and Vue will be able to analyse and merge together those different classes.

### Dynamic Styling - Without CSS Classes

You can directly interact with the style of an element:

``` html
<div class="box" :style="{backgroundColor: color}"></div>
```
when using `:style` you can also use computed properties: 

``` html
<div class="box" :style="myStyle"></div>
```

``` javascript
new Vue({
    el: '#app',
    data: {
        color: 'green',
        width: 100
    },
    computed: {
    	myStyle: function() {
          return {
            backgroundColor: this.color,
            width: this.width + 'px'
          }
      }
    }
})
```

`:class="{someClass: condition}"`  allows you to specify under which condition someClass should be added to this element.

`:class="someClass"`  on the other hand allows you to dynamically bind to some object working in the same way as in the first example (so you basically removed the logic from the template and put it into your JS code).

`:class="[someClass, anotherClass]"`  allows you to add multiple classes to an element. Here, `someClass`  CAN also be an object with the `name: condition`  mapping but it can also just be a class name (which then is always added, since it doesn't have a condition).

 ### conditional rendering with v-if and v-else
 
 The directive `v-if` allows us to show if evaluated to true or hide an element if evaluated to false. The directive would totally remove the element from the DOM.
 
 ``` html
<div id="app">
	<p v-if="show">You can see me!</p>
	<p v-else>Do you also see me?</p>
  <button @click="show = !show">Show/hide</button>
</div>
 ```
 
 ``` javascript
 new Vue({
    el: '#app',
    data: {
      show: true
    }
})
 ```
 
Here you can see that the directive `v-if` will display when evaluated to true. `v-else` will refere to the `v-if` coming before and will do the opposite of `v-if` so if `v-if` show, it will hide.
 
The button changes the value of show to be the opposite of what it was.

Also in Vue.js version 2.1 there is also the `v-else-if`

An alternative is to attach the `v-if` to a template tag, the template tag is a HTML5 element that will not be displayed in the DOM but can be used to group elements. Since the v-if can only affect one element at the time, if we wrap multiple elements in a template tag and attach the `v-if` on the template, all the nested element will display or not depending on the same condition.

``` html
<div id="app">
	<p v-if="show">You can see me!</p>
    <template v-if="show">
      <h1>The title</h1>
      <p>The paragraph</p>
    </template>
	<p v-else>Do you also see me?</p>
  <button @click="show = !show">Show/hide</button>
</div>
```

### Don't detach with v-show

You can do something very similar with `v-show` where you show or hide an element depending on the evaluation of the expression, the difference is that the element that is not shown is still attached to the DOM, it is only hidden with `display: none`

### Rendering Lists with v-for

You can render a list with `v-for` just like that:

``` html
<div id="app">
	<ul>
	  <li v-for="ingredient in ingredients">{{ ingredient }}</li>
	</ul>
</div>
```
``` javascript
new Vue({
	el: '#app',
    data: {
      ingredients: ['meat', 'fruits', 'vegetables']
    }
});
```
### Getting current index

Not only do you get access to the element in the array, but you get also access to the index of the element with `v-for`, we just need to add parentheses around the arguments, but be careful, the first argument is the item, the second is the index :


``` html
<div id="app">
	<ul>
	  <li v-for="(ingredient, i) in ingredients">{{ ingredient }} ({{ i }})</li>
	</ul>
</div>
```
``` javascript
new Vue({
	el: '#app',
    data: {
      ingredients: ['meat', 'fruits', 'vegetables']
    }
});
```
### Alternative v-for with template

The same way we could use `v-if` on a template, we cn do the same with `v-for` like that we can itterate through a list and display something different than a list.

``` html
<div id="app">
  <template v-for="(ingredient, i) in ingredients">
    <h1>{{ ingredient }}</h1>
    <p>{{ i }}</p>
  </template>
</div>
```
``` javascript
new Vue({
	el: '#app',
    data: {
      ingredients: ['meat', 'fruits', 'vegetables']
    }
});
```
### Looping through Object

The same way you can loop through arrays, you can loop through object using `v-for` and when you do, you have access to the value, the key and the index:


``` html
<div id="app">
  <ul>
	  <li v-for="person in people">
      <div v-for="(value, key) in person">{{ key }}: {{ value }}</div>
    </li>
	</ul>
</div>
```
``` javascript
new Vue({
	el: '#app',
    data: {
      people: [
        {name: 'max', age: 22, color: 'red'},
        {name: 'bob', age: 26, color: 'blue'}
      ]
    }
});
```

### Looping though a list of number

you can easily loop through a list of number with `v-for` like so:

``` html
<span v-for="n in 10">{{ n }}</span>
```
when vue sees the number in the v-for, it know to treat it like a number and it will loop from 1 to the number.

### Keeping track of elements when using v-for

When using v-for, you even add elements to the array dynamically, but if you want to reorder the element you might want to track the elements with a unique identifier, not just the index, If you remove an element, the index will change. For this you can bind using `v-bind`.

``` html
<li v-for="(ingredient, i) in ingredients" :key="ingredient">{{ ingredient }} ({{ i }})</li>
```

### Understand VueJS Instance

It is possible to have two Vue instances affect two different element on the same page:

``` html
<div id="app">
<h1>{{ title }}</h1>
<button @click="changeTitle">Change Title</button>
</div>

<div id="app2">
<h1>{{ title }}</h1>
<button @click="changeTitle">Change other title</button>
</div>
```

``` javascript
new Vue({
	el: '#app',
    data: {
      title: 'Hello'
    },
    methods: {
      changeTitle: function() {
        this.title = 'New title';
      }
    }
});

new Vue({
	el: '#app2',
    data: {
      title: 'Hello'
    },
    methods: {
      changeTitle: function() {
        this.title = 'New second title';
      }
    }
});
```

It is also possible to access a Vue instance from another Vue instance, even if it doesn't make too much sense, because in the case of one Vue instance affecting another one, it would make more sense to have them be under one. But it is possible and it is also possible to access a Vue instance using vanilla JS.

``` javascript
var vm1 = new Vue({
	el: '#app',
    data: {
      title: 'Hello'
    },
    methods: {
      changeTitle: function() {
        this.title = 'New title';
      }
    }
});

setTimeout(function() {
  vm1.title = 'changed by timer';
}, 3000);

var vm2 = new Vue({
	el: '#app2',
    data: {
      title: 'Hello'
    },
    methods: {
      changeTitle: function() {
        vm1.title = 'New first title';
      }
    }
});
```
In this example, we can see that on line 24, we are affecting the first Vue instance from the seconde one, and on line 13, we are affecting the first Vue instance with vanilla JS.

### Important point

You are able to add an attribute to data or methods from the outside like this:

``` javascript
vm1.newProp = 'new prop'
```
But this doesn't set the setter and getter that Vue is creating under the cover for the props that are added to the Vue instance in general so it will not be accessible, it will not be watched by Vue.

### $refs

There is a `ref` property that Vue exposes to us. We can add the key `ref` on any html tags and it will be tracked with the `$refs` property. We can then access it from inside the Vue instance or outside. We can get the value or even alter the element, but it is important to keep in mind that the change that we would make will not update the instance, it will only alter the DOM, so when the instance re-render the changes would be overriten.

``` html
<div id="app">
<h1 ref="heading">{{ title }}</h1>
<button @click="changeTitle" ref="myButton">Change Title</button>
</div>
```

``` javascript
var vm1 = new Vue({
	el: '#app',
    data: {
      title: 'Hello'
    },
    methods: {
      changeTitle: function() {
        this.title = 'New title';
        this.$refs.myButton.innerText = 'Test';
      }
    }
});

vm1.$refs.heading.innerText = 'New heading';
 ```
 
On line 9, we are changing the text of the button from inside the instance. The button inner text is not already controlled by Vue.

On line 14, we are changing the heading. Which was controlled by the Vue instance with `{{ title }}`. When this title is re-render, it will be changed by the Vue instance, because the changes we did using the `$refs` is not watch, it is just a simple DOM manipulation.

So this method is generally better to get value than to change values.

### $mount()

for the moment we have been using `el` to target the place in the html to mount our Vue instance. It is also possible to use the `$mount()` method.

``` javascript
vm1.$mount('#app');
```
When doing it this way, you shouldn't add the `el` element to your instance.

It is also possible to set the template in the view instance instead ot the html doc. 

``` html
<div id="app">

</div>
```
``` javascript
var vm1 = new Vue({
	template: '<h1>Hello!</h1>'
});

vm1.$mount();
document.getElementById('app').appendChild(vm3.$el);
 ```
 
 ### Component
 
 It is possible to use a reusable component with the `Vue.component`:
 
 ``` javascript
 Vue.component('hello', {
  template: '<h1>Hello!</h1>'
 })
 ```
 
 ``` html
<hello></hello>
```
This will display the template in the hello element.

### Instance lifeCycle

`beforeCreate()`
`created()`
`beforeMount()`
`mounted()`
`beforeUpdate()`
`updated()`
`beforeDestroy()`
`destroyed()`

``` html
<div id="app">
  <h1>{{ title }}</h1>
  <button @click="title = 'Changed'">Update Title</button>
  <button @click="destroy">Destroy</button>
</div>
```

``` javascript
new Vue({
	el: '#app',
    data: {
      title: 'Hello'
    },
    beforeCreate: function() {
      console.log('beforeCreate()')
    },
    created: function() {
      console.log('created()')
    },
    beforeMount: function() {
      console.log('beforeMount()')
    },
    mounted: function() {
      console.log('mounted()')
    },
    beforeUpdate: function() {
      console.log('beforeUpdate()')
    },
    updated: function() {
      console.log('updated()')
    },
    beforeDestroy: function() {
      console.log('beforeDestroy()')
    },
    destroyed: function() {
      console.log('destroyed()')
    },
    methods: {
      destroy: function() {
        this.$destroy();
      }
    }
});
```
When using destroy, we are not removing the element from the page, but we are removing the vue instance connection, nothing from the instance can affect the page anymore.

### Vue CLI

`npm install -g vue-cli`

then to create a project and use the `webpack-simple` starter kit, and create a new project:

`vue init webpack-simple name-of-project`

CD into the project then run `npm install`

Then run `npm run dev` to start your local server

### single vue file

A single Vue file has a template, a sript tag and optionally a style tag.

In the single file, the script tag and the export default is like the vue instance we had before, if we want to add business logic, we can add the data, methods etc... to it.

When you want to get your project ready for production, you can run `npm run build`

### Components

``` html
<div id="app">
  <my-cmp></my-cmp>
  <hr>
  <my-cmp></my-cmp>
</div>
```

``` javascript
 Vue.component('my-cmp', {
    data: function() {
      return {
        status: 'Critical'
      }
    },
    template: '<p>Server Status: {{ status }}</p>'
 });
 
 new Vue({
  el: '#app'
 })
 ```

The component can have the data, but the data needs to be a function that returns the object, it can not be an object like we had it in the normal vue instance.

### use components locally

What we see above was a component used globally. We can also use the component locally like so.


``` html
<div id="app">
  <my-cmp></my-cmp>
  <hr>
  <my-cmp></my-cmp>
</div>
```

``` javascript

 var cmp = {
    data: function() {
      return {
        status: 'Critical'
      };
    },
    template: '<p>Server Status: {{ status }}</p>'
 };
 
 new Vue({
  el: '#app',
  components: {
    'my-cmp': cmp
  }
 })
 ```
 
 ### Creating a component
 
 When working with a pre-compiler like webpack and with single file component, here is how we will need to setup our files:
 
New component file, for example: `Home.vue`

``` javascript
<template>
  <div>
    <p>Server status: {{ status }}</p>
    <hr>
    <button @click="changeStatus">Change Status</button>
  </div>
</template>

<script>
  export default {
    data: function() {
        return {
            status: 'Critical'
        }
    },
    methods: {
      changeStatus() {
        this.status = 'Normal';
      }
    }
  }
</script>
```
In the `main.js` file :

``` javascript
import Vue from 'vue'
import App from './App.vue'
import Home from './Home.vue'

Vue.component('app-server-status', Home);

new Vue({
  el: '#app',
  render: h => h(App)
})
```
In the `App.vue`:

``` javascript
<template>
  <app-server-status></app-server-status>
</template>

<script>
</script>

<style>
</style>
```

What is important to know is that in your component template in Home.vue, you can have only one children element. So under template, if you have more than one element, they need to be surrounded by one parent element.

We can either import those Vue component globally, or locally. Globally is like in the example above where the `Home` vue component is imported in the main.js file. We also could have imported that `Home` component locally in `App.vue` knowing that we will only use it there like this:

``` javascript
<template>
  <app-server-status></app-server-status>
</template>

<script>
  import Home from './Home.vue'
  
  export default {
    components: {
      'app-server-status': Home
    }
  }
</script>

<style>
</style>
```
### scoped style

if we add style to each component, the style is subject to the cascading, since all those styles will be compiled together. If you want your style to apply to only one component, you can add the work `scoped` to your style tag like this:

``` html
<style scoped>
</style>
```

### Props Parent to Child

To pass data from a parent to its child, we can use props, just like in React.

But in order to this props to send dynamic data we need to use `v-bind` or `:` like so on line 9:

``` javascript
<template>
    <div class="component">
        <h1>The User Component</h1>
        <p>I'm an awesome User!</p>
        <button @click="changeName">Change my name</button>
        <hr>
        <div class="row">
            <div class="col-xs-12 col-sm-6">
                <app-user-detail :name="name"></app-user-detail>
            </div>
            <div class="col-xs-12 col-sm-6">
                <app-user-edit></app-user-edit>
            </div>
        </div>
    </div>
</template>

<script>
    import UserDetail from './UserDetail.vue';
    import UserEdit from './UserEdit.vue';

    export default {
        data: function() {
          return {
            name: 'Greg'
          };
        },
        methods: {
          changeName() {
            this.name = 'Bob';
          }
        },
        components: {
            appUserDetail: UserDetail,
            appUserEdit: UserEdit
        }
    }
</script>
```

In the child, You need to set the property to match the props in the parent, here on line 5 we use `name` and we set the props on line 11:

``` javascript
<template>
    <div class="component">
        <h3>You may view the User Details here</h3>
        <p>Many Details</p>
        <p>User Name: {{ name }}</p>
    </div>
</template>

<script>
  export default {
    props: ['name']
  }
</script>
```
We can use the props in the child component the same way we use any data, using `this.propName` in a method.

### Props validation

We have seen how we passed the props in an array, but we can also pass the props in an object to add validation like this:

``` javascript
<template>
    <div class="component">
        <h3>You may view the User Details here</h3>
        <p>Many Details</p>
        <p>User Name: {{ name }}</p>
    </div>
</template>

<script>
  export default {
    props: {
      name: String
    }
  }
</script>
```
We can even go further like this: 

``` javascript
<script>
  export default {
    props: {
      name: {
        type: String,
        required: true
      }
    }
  }
</script>
```
You can even add a default value.

### Props child to parent

When passing data from children to parent, you can use `$emit()` in the child to emit and then `$event` in the parent to track the event:

``` javascript
<template>
    <div class="component">
        <h3>You may view the User Details here</h3>
        <p>Many Details</p>
        <p>User Name: {{ name }}</p>
        <button @click="resetName">Reset Name</button>
    </div>
</template>

<script>
  export default {
    props: {
      name: {
        type: String
      }
    },
    methods: {
      resetName() {
        this.name = 'Greg';
        this.$emit('nameWasReset', this.name);
      }
    }
  }
</script>
```
You see on line 20 we are setting the `$emit` and on the parent:

``` javascript
<div class="col-xs-12 col-sm-6">
    <app-user-detail 
        :name="name" 
        @nameWasReset="name = $event">
    </app-user-detail>
</div>
```
and on line 4 here we are setting the `$event` in the parent

### callback function

We can also use a callback function set as a props to reset the name instead of using the child function:

In the parent:

``` javascript
<template>
    <div class="component">
        <h1>The User Component</h1>
        <p>I'm an awesome User!</p>
        <button @click="changeName">Change my name</button>
        <p>Name is {{ name }}</p>
        <hr>
        <div class="row">
            <div class="col-xs-12 col-sm-6">
                <app-user-detail 
                  :name="name" 
                  @nameWasReset="name = $event"
                  :resetFn="resetName">
                </app-user-detail>
            </div>
            <div class="col-xs-12 col-sm-6">
                <app-user-edit></app-user-edit>
            </div>
        </div>
    </div>
</template>

<script>
    import UserDetail from './UserDetail.vue';
    import UserEdit from './UserEdit.vue';

    export default {
        data: function() {
          return {
            name: 'Greg'
          };
        },
        methods: {
          changeName() {
            this.name = 'Bob';
          },
          resetName() {
            this.name = 'Greg';
          }
        },
        components: {
            appUserDetail: UserDetail,
            appUserEdit: UserEdit
        }
    }
</script>
```
You see on line 13, we pass the resetFn props and on line 37, we declare that function.

On the child:

``` javascript
<template>
    <div class="component">
        <h3>You may view the User Details here</h3>
        <p>Many Details</p>
        <p>User Name: {{ name }}</p>
        <button @click="resetName">Reset Name</button>
        <button @click="resetFn()">Reset Name</button>
    </div>
</template>

<script>
  export default {
    props: {
      name: {
        type: String
      },
      resetFn: Function
    },
    methods: {
      resetName() {
        this.name = 'Greg';
        this.$emit('nameWasReset', this.name);
      }
    }
  }
</script>
```
You can see on line 7 we attach the resetFn that we have as props and we will use that function which is from the parent.

### communication between siblings

We cannot change the data in one component and have it affect the sibling without passing by the parent.

Parent: 
The parent has the data `age`, but no method to change the age.

``` javascript
<template>
    <div class="component">
        <h1>The User Component</h1>
        <p>I'm an awesome User!</p>
        <button @click="changeName">Change my name</button>
        <p>Name is {{ name }}</p>
        <hr>
        <div class="row">
            <div class="col-xs-12 col-sm-6">
                <app-user-detail 
                  :name="name" 
                  @nameWasReset="name = $event"
                  :resetFn="resetName"
                  :userAge="age"
                ></app-user-detail>
            </div>
            <div class="col-xs-12 col-sm-6">
                <app-user-edit :userAge="age" @ageWasEdited="age = $event"></app-user-edit>
            </div>
        </div>
    </div>
</template>

<script>
    import UserDetail from './UserDetail.vue';
    import UserEdit from './UserEdit.vue';

    export default {
        data: function() {
          return {
            name: 'Greg',
            age: 27
          };
        },
        methods: {
          changeName() {
            this.name = 'Bob';
          },
          resetName() {
            this.name = 'Greg';
          }
        },
        components: {
            appUserDetail: UserDetail,
            appUserEdit: UserEdit
        }
    }
</script>
```

Child who is initiating the change:
This child has a method to edit the age and emits that change to the parent.

``` javascript
<template>
    <div class="component">
        <h3>You may edit the User here</h3>
        <p>Edit me!</p>
        <p>User Age: {{ userAge }}</p>
        <button @click="editAge">Edit Age</button>
    </div>
</template>

<script>
  export default {
    props: ['editAge', 'userAge'],
    methods: {
      editAge() {
        this.userAge = 32;
        this.$emit('ageWasEdited', this.userAge)
      }
    }
  }
</script>
```

Child that receives the change:
This child recives the updated age when the parent receives the emitted change and update the age.

``` javascript
<template>
    <div class="component">
        <h3>You may view the User Details here</h3>
        <p>Many Details</p>
        <p>User Name: {{ name }}</p>
        <p>User Age: {{ userAge }}</p>
        <button @click="resetName">Reset Name</button>
        <button @click="resetFn()">Reset Name</button>
    </div>
</template>

<script>
  export default {
    props: {
      name: {
        type: String
      },
      resetFn: Function,
      userAge: Number
    },
    methods: {
      resetName() {
        this.name = 'Greg';
        this.$emit('nameWasReset', this.name);
      }
    }
  }
</script>
```
### Event Bus

There is even a better way to communicate between child component, this is the Event Bus. (kind of like Angular services) 

In main.js, we are exporting a new vue instance like so:

``` javascript
import Vue from 'vue'
import App from './App.vue'

export const eventBus = new Vue();

new Vue({
  el: '#app',
  render: h => h(App)
})
```

In the child that will initiate the change I import that vue instance and use the eventBus:

``` javascript
<template>
    <div class="component">
        <h3>You may edit the User here</h3>
        <p>Edit me!</p>
        <p>User Age: {{ userAge }}</p>
        <button @click="editAge">Edit Age</button>
    </div>
</template>

<script>
  import { eventBus } from '../main';

  export default {
    props: ['editAge', 'userAge'],
    methods: {
      editAge() {
        this.userAge = 32;
        // this.$emit('ageWasEdited', this.userAge)
        eventBus.$emit('ageWasEdited', this.userAge);
      }
    }
  }
</script>
```
In the child that will get the changed value, we are also importing the eventBus, and we are setting the eventBus.$on() inside the `created()` lifecycle method like this: 

``` javascript
<template>
    <div class="component">
        <h3>You may view the User Details here</h3>
        <p>Many Details</p>
        <p>User Name: {{ name }}</p>
        <p>User Age: {{ userAge }}</p>
        <button @click="resetName">Reset Name</button>
        <button @click="resetFn()">Reset Name</button>
    </div>
</template>

<script>
  import { eventBus } from '../main';

  export default {
    props: {
      name: {
        type: String
      },
      resetFn: Function,
      userAge: Number
    },
    methods: {
      resetName() {
        this.name = 'Greg';
        this.$emit('nameWasReset', this.name);
      }
    },
    created() {
      eventBus.$on('ageWasEdited', (age) => {
        this.userAge = age;
      });
    }
  }
</script>
```

We could also centralize our code by putting the logic in the eventBus directly in the main.js, like this the method is available everywhere:

In main.js:

``` javascript
import Vue from 'vue'
import App from './App.vue'

export const eventBus = new Vue({
  methods: {
    changeAge(age) {
      this.$emit('ageWasEdited', age);
    }
  }
});

new Vue({
  el: '#app',
  render: h => h(App)
})
```

In child that initiate change:

``` javascript 
<script>
  import { eventBus } from '../main';

  export default {
    props: ['editAge', 'userAge'],
    methods: {
      editAge() {
        this.userAge = 32;
        // this.$emit('ageWasEdited', this.userAge)
        // eventBus.$emit('ageWasEdited', this.userAge);
        eventBus.changeAge(this.userAge);
      }
    }
  }
</script>
```
### reminder
You got `props`  to pass data from a `PARENT => CHILD`.

You got events `($emit )` to pass data from `CHILD => PARENT`.

### slot

Slots help you distribute your content in other components.
You can pass an element from the parent to the child using a slot. The logic will remain on the parent, but the styling will be handled by the child: 

Parent:
``` javascript 
<template>
  <div>
    <app-quote>
      <h2>{{ quoteTitle }}</h2>
      <p>A wonderful Qute</p>
    </app-quote>
  </div>
</template>
<script>
  import Quote from './components/Quote.vue';

  export default {
    data: function() {
      return {
        quoteTitle: 'The Quote'
      }
    },
    components: {
      appQuote: Quote
    }
  }
</script>
```
Child:

``` javascript 
<template>
  <div>
    <slot></slot>
  </div>
</template>

<script>
  export default {
  }
</script>

<style scoped>
  div {
    border: 1px solid #ccc;
  }
</style>
```
The logic is on the parent and the style on the child. But it is now also possible to style the content you pass into a slot from inside the parent component.

### named slots

It is also possible to use named slots to break down your slot or to just ave multiple slots:


Parent:
``` javascript 
<template>
  <div>
    <app-quote>
      <h2 slot="title">{{ quoteTitle }}</h2>
      <p slot="content">A wonderful Qute</p>
    </app-quote>
  </div>
</template>
<script>
  import Quote from './components/Quote.vue';

  export default {
    data: function() {
      return {
        quoteTitle: 'The Quote'
      }
    },
    components: {
      appQuote: Quote
    }
  }
</script>
```
Child:

``` javascript 
<template>
  <div>
    <slot name="title"></slot>
  </div>
  <div>
    <slot name="content"></slot>
  </div>
</template>

<script>
  export default {
  }
</script>

<style scoped>
  div {
    border: 1px solid #ccc;
  }
</style>
```
### Default slots

VueJS is displaying in the default slot content that is not in a named slot:

Parent:
``` javascript 
<template>
  <div>
    <app-quote>
      <h2 slot="title">{{ quoteTitle }}</h2>
      <p>A wonderful Qute</p>
    </app-quote>
  </div>
</template>
```
Child:

``` javascript 
<template>
  <div>
    <slot name="title"></slot>
    <span><slot name="subtitle">The subtitle</slot></span>
  </div>
  <div>
    <slot></slot>
  </div>
</template>

<script>
  export default {
  }
</script>

<style scoped>
  div {
    border: 1px solid #ccc;
  }
</style>
```
In the parent, the `<p>` tag will display in the slot that is not named. 

Also, it is possible to have a default content in case we are not passing anything from the parent. You just have to add the content between the `<slot>` tag in the child.

On line 4 of the child, between the `<slot>` tags, the defult value. In the parent we didn't pass a value 

### dynamic Components

We can use the `<component>` tag to pass component dynamically:

Parent:
``` javascript 
<template>
  <div>
      <button @click="selectedComponent = 'appQuote'">Quote</button>
      <button @click="selectedComponent = 'appAuthor'">Author</button>
      <button @click="selectedComponent = 'appNew'">New</button>
      <component :is="selectedComponent"></component>
  </div>
</template>
<script>
  import Quote from './components/Quote.vue';
  import Author from './components/Author.vue';
  import New from './components/New.vue';

  export default {
    data: function() {
      return {
        quoteTitle: 'The Quote',
        selectedComponent: 'appQuote'
      }
    },
    components: {
      appQuote: Quote,
      appAuthor: Author,
      appNew: New
      
    }
  }
</script>
```

### keep dynamic components alive

By adding the `<component>` insinde the `<keep-alive>` tag, the component is kept alive meaning that it is not destroyed when navigating away.

``` javascript
  <keep-alive>
    <component :is="selectedComponent"></component>
  </keep-alive>
```

### Dynamic component Lifecycle Hooks

We can use `activated()`livecycle when navigating to the component and `deactivated()`livecycle when navigating away from the component 

## Form

We can use v-model to create a two way binding on input field. We also use the modifiers on this v-model, like the `lazy` modifier to update only when the input is unfocused instead of each key stroke.

In the case where we are storing a message with line breaks, the line breaks are kept, if we want to display them, we need to use `style="white-space: pre"` where we render the message.

### checkbox

We can push all the checkboxes to the same array using the same v-model. Vue will automatically add the value to the v-model.

``` html
<div class="form-group">
    <label for="sendmail">
        <input
                type="checkbox"
                id="sendmail"
                value="SendMail"
                v-model="sendMail"> Send Mail
    </label>
    <label for="sendInfomail">
        <input
                type="checkbox"
                id="sendInfomail"
                value="SendInfoMail"
                v-model="sendMail"> Send Infomail
    </label>
</div>
```
Line 7 and 9, we are using the v-model to push the value of the clicked checkbox to the sendMail array. 
To display it, we can use a v-for and display the array

### radio button

With Radio button, we are adding the v-model the same way we did with other input. Just doing that makes that only one radio button can be clicked.

``` html
<label for="male">
    <input
            type="radio"
            id="male"
            value="Male"
            v-model="gender"> Male
</label>
<label for="female">
    <input
            type="radio"
            id="female"
            value="Female"
            v-model="gender"> Female
</label>
```
``` javascript 
<script>
    export default {
      data() {
        return {
          userData: {
            email: '',
            password: '',
            age: undefined
          },
          message: 'This is the default message',
          sendMail: [],
          gender: 'Female'
        }
      }
    }
</script>
```
Like you see we have to add the gender attribute in data, the value that is added here will be the default value.

### dropdawn

We could add on the option `:selected="priority == 'medium'"` to create a selected value, but we also can do that with the v-model.

The v-mode will be added to the select tag and not the option tag.

``` html
<label for="priority">Priority</label>
<select
        id="priority"
        class="form-control"
        v-model="selectedPriority">
    <option v-for="priority in priorities">{{ priority }}</option>
</select>
```
``` javascript 
<script>
    export default {
      data() {
        return {
          userData: {
            email: '',
            password: '',
            age: undefined
          },
          message: 'This is the default message',
          sendMail: [],
          gender: 'Female',
          selectedPriority: 'high',
          priorities: ['high', 'medium', 'low']
        }
      }
    }
</script>
```
### v-model

What does v-model does behind the scene? 
On an input tag, adding `v-model="userData.email"` is the same as adding `:value="userData.email"` and `@input="userData.email = $event.target.value"`

### custom control

On the custom control:

``` html
<template>
    <div>
        <div
            id="on"
            @click="switched(true)"
            :class="{active: value}">On</div>
        <div
            id="off"
            @click="switched(false)"
            :class="{active: !value}">Off</div>
    </div>
</template>

<script>
    export default {
        props: ['value'],
        methods: {
          switched(isOn) {
            this.$emit('input', isOn);
          }
        }
    }
</script>
```
On the Parent, where the component is used:

``` html
<template>
    <app-switch v-model="dataSwitch"></app-switch>
</template>

<script>
    import Switch from './Switch.vue';

    export default {
      data() {
        return {
          userData: {
            email: '',
            password: '',
            age: undefined
          },
          message: 'This is the default message',
          sendMail: [],
          gender: 'Female',
          selectedPriority: 'high',
          priorities: ['high', 'medium', 'low'],
          dataSwitch: true
        }
      },
      components: {
        appSwitch: Switch
      }
    }
</script>
```
### submit the form

If you want to handle the form with vue, you should add the modifier prevent on click like this:
``` html
<button @click.prevent="submitted">Submit</button>
```
submitted here is a method that we will add to our methods.

## custom directive

`bind(el, binding, vnode)`: Once directive is attached
`inserted(el, binding, vnode)`: Inserted in parent node
`update(el,binding,vnode,oldVnode)`: Once component is updated (without children)
`componentUpdated(el,binding,vnod,oldVnode)`: Once component is updated (with children)
`unbind(el,binding,vnode)`: Once directive is removed

### Global Directive
If we want to create a global directive, we would add it to the main.js file like this:

``` javascript
Vue.directive('highlight', {
  bind(el, binding, vnode) {
    el.style.backgroundColor = 'green';
  }
});

new Vue({
  el: '#app',
  render: h => h(App)
})
```

and use it like this:

``` html
<p v-highlight>Color this</p>
```

Now if we wanted to pass the color a value, we could modify the directive like this:

``` javascript
Vue.directive('highlight', {
  bind(el, binding, vnode) {
    el.style.backgroundColor = binding.value;
  }
});
```
and use it like this:

``` html
<p v-highlight="'red'">Color this</p>
```
We can also pass an argument to the directive like this:

``` javascript
Vue.directive('highlight', {
  bind(el, binding, vnode) {
    if(binding.arg == 'background') {
      el.style.backgroundColor = binding.value;
    } else {
      el.style.color = binding.value;
    }
  }
});
```
And now we can pass the argument when using the directive or not:

``` html
<p v-highlight:background="'red'">Color this</p>
```
`:background` is the argument.

We can also use modifier with directives.

``` javascript
Vue.directive('highlight', {
  bind(el, binding, vnode) {
    var delay = 0;
    if(binding.modifiers['delayed']) {
      delay = 3000;
    }

    setTimeout(() => {
      if (binding.arg == 'background') {
        el.style.backgroundColor = binding.value;
      } else {
        el.style.color = binding.value;
      }
    }, delay);
  }
});
```
see on line 9 is how we check if there is a modifier when the directive is used. 

``` html
<p v-highlight:background.delayed="'red'">Color this</p>
```
This is how we would add the modifier.

### Create a local directive

If we want to create a local directive, we just add it to the default export like this: 

``` html
<script>
    export default {
      directives: {
        'local-highlight': {
          bind(el, binding, vnode) {
            var delay = 0;
            if(binding.modifiers['delayed']) {
              delay = 3000;
            }

            setTimeout(() => {
              if (binding.arg == 'background') {
                el.style.backgroundColor = binding.value;
              } else {
                el.style.color = binding.value;
              }
            }, delay);
          }
        }
      }
    }
</script>
```

And we use it the same way:

``` html
<p v-local-highlight:background.delayed="'red'">Color this</p>
```

We can add multiple arguments to the directive:

``` javascript
export default {
      directives: {
        'local-highlight': {
          bind(el, binding, vnode) {
            var delay = 0;
            if(binding.modifiers['delayed']) {
              delay = 3000;
            }
            if(binding.modifiers['blink']) {
              let mainColor = binding.value;
              let secondColor = 'blue';
              let currentColor = mainColor;

              setTimeout(() => {
                setInterval(() => {
                  currentColor == secondColor ? currentColor = mainColor : currentColor = secondColor;
                  if (binding.arg == 'background') {
                    el.style.backgroundColor = currentColor;
                  } else {
                    el.style.color = currentColor;
                  }
                },1000);
              }, delay);
            } else {
              setTimeout(() => {
                if (binding.arg == 'background') {
                  el.style.backgroundColor = binding.value;
                } else {
                  el.style.color = binding.value;
                }
              }, delay);
            }
          }
        }
      }
    }
```

And we use it the same way:

``` html
<p v-local-highlight:background.delayed.blink="'red'">Color this</p>
```

You can also pass more complex value to your directive, like an object for example:

``` html
<p v-local-highlight.delayed.blink="{mainColor: 'red', secondColor: 'green', delay: 500}">Color this</p>
```

To access the data you need, you would use `binding.value.mainColor` for example.

### Filter

A filter is a syntax feature you can use in your template to transform some output in the template. It doesn't transforms the data itself, it transform what the user sees.

Vue Js doesn't come with built-in filters, so all filters will have to be built.

### local filter

To create a local filter, just add it to the export default object:

``` javascript
export default {
  data() {
    return {
      text: 'Hello there!'
    }
  },
  filters: {
    toUppercase(value) {
      return value.toUpperCase();
    }
  }
}
```

to use the filter, just add it with the `|` pipe symbol:

``` html
<p>{{ text | toUppercase }}</p>
```

### global filter

To add a global filter, just add it to the main.js file like this:

``` javascript
Vue.filter('to-lowercase', function(value) {
  return value.toLowerCase();
});
```

and use it the same way you used the local filter, you can even chain the filter like this:

``` html
<p>{{ text | toUppercase | to-lowercase }}</p>
```
### Alternative to Filters: Computed Properties

Filter are not very efficient in certain case and a better alternative is often to use computed properties.

for example to render a filtered list:

``` html
<input v-model="filteredText">
<ul>
  <li v-for="fruit in filteredFruits">{{ fruit }}</li>
</ul>
```
``` javascript
export default {
  data() {
    return {
      text: 'Hello there!',
      fruits: ['Banana', 'Apple', 'Mango', 'Melon'],
      filteredText: ''
    }
  },
  computed: {
    filteredFruits() {
      return this.fruits.filter((element) => {
        return element.match(this.filteredText);
      });
    }
  }
}
```

### Mixins

If we wanted to reuse the filteredFruit computed property in another component, we can use a mixin instead of duplicating code.

To create a mixin, just create a JS file and create a named export with everything needed:

``` javascript
export const fruitMixin = {
  data() {
    return {
      fruits: ['Banana', 'Apple', 'Mango', 'Melon'],
      filteredText: ''
    }
  },
  computed: {
    filteredFruits() {
      return this.fruits.filter((element) => {
        return element.match(this.filteredText);
      });
    }
  }
}
```

Then import the mixin in the component you want to use the mixin. Also add the mixin to the mixins property.

``` javascript
import { fruitMixin } from './fruitMixin';

export default {
  mixins: [fruitMixin]
}
```
Vue is cleverly combining the mixin and the data of the component even for the lifecycle method, and even if they have the same name. It will apply the mixin first.

It is possible to create add the mixin globally, but it is very rare and is normally better to use the local mixins.

### Transition

Transition CSS classes:
`*-enter`
`*-enter-active`
`*-leave`
`*-leave-active`

Default if no name is supplied: `v-enter` etc..

To do a transition in Vue, we add the element that we want to have a transition into the `<transition>` tag.

We can add a name attribute to this transition tag and Vue will attach automatically classes to the name for example:

``` html
<transition name="fade">
  <div class="alert alert-info" v-if="show">This is some info</div>
</transition>
```
In this case, Vue will look for `fade-enter` `fade-enter-active` etc...

Once you set your name to your animation, you add the style to your css:

``` html
<style>
  .fade-enter {
    opacity: 0;
  }

  .fade-enter-active {
    transition: opacity 1s;
  }

  .fade-leave {
    /* opacity: 1; */
  }

  .fade-leave-active {
    transition: opacity 1s;
    opacity: 0;
  }
</style>
```

Doing this is enough to set the transition. You don't need to add those classes to the element itself. Vue will attach and remove those classes.

We can do the same thing with animation:

``` html
<transition name="slide">
  <div class="alert alert-info" v-if="show">This is some info</div>
</transition>
```

``` html
<style>
  .slide-enter {
    /* transform: translateY(20px); */
  }

  .slide-enter-active {
    animation: slide-in 1s ease-out forwards;
  }

  .slide-leave {
    
  }

  .slide-leave-active {
    animation: slide-out 1s ease-out forwards;
  }

  @keyframes slide-in {
    from {
      transform: translateY(20px);
    }
    to {
      transform: translateY(0);
    }
  }

  @keyframes slide-out {
    from {
      transform: translateY(0);
    }
    to {
      transform: translateY(20px);
    }
  }
</style>
```

If you mix animation and transition, make sure to add the type to tell Vue which one dictates when vue should stop.

``` html
<transition name="slide" type="animation">
  <div class="alert alert-info" v-if="show">This is some info</div>
</transition>
```

When doing animation or transition, you can use `v-if` or `v-show`

You can use appear for the initial load and it will create the animation on the first display.

``` html
<transition name="slide" appear>
  <div class="alert alert-info" v-if="show">This is some info</div>
</transition>
```
### Using animated.css

to use our own custom class for animation and transition, we can tell Vue what class to use instead of the auto generated `*-enter` `*-enter-active` etc like this:

``` html
<transition
  enter-active-class="animated bounce"
  leave-active-class="animated shake"
>
  <div class="alert alert-info" v-if="show">This is some info</div>
</transition>
```

Note, it seems that `appear` doesn't work with custum animation.

### using dynamic name and attribute

We can bind name or attribute using `bind` or `:`

``` html
<transition :name="alertAnimation">
  <div class="alert alert-info" v-if="show">This is some info</div>
</transition>
```
When transitioning between two element, we can use `mode="out-in"`to tell vue, we want to animate the out first and then the in.

### Transition JS Hooks

`before-enter`
`enter`
`after-enter`
`after-enter-cancelled`
`before-leave`
`leave`
`after-leave`
`after-leave-cancelled`

### Creating animation with Javascript

We can create animation using only javascript or a mix of css and javascript. Lets look at an example of only JS animation using the transition hooks:

``` html
<transition
    @before-enter="beforeEnter"
    @enter="enter"
    @after-enter="afterEnter"
    @enter-cancelled="enterCancelled"

    @before-leave="beforeLeave"
    @leave="leave"
    @after-leave="afterLeave"
    @leave-cancelled="leaveCancelled"
    :css="false"
    >
    <div style="width:300px; height:100px; background-color:lightgreen;" v-if="load"></div>
</transition>
```
If you use JS only, you can use the `:css="false"` to tell Vue that you will not need the auto generetade classes.

``` javascript
export default {
    data() {
        return {
          show: false,
          load: true,
          alertAnimation: 'fade',
          elementWidth: 100
        }
    },
    methods: {
      beforeEnter(el) {
        console.log('beforeEnter');
        el.style.width = this.elementWidth + 'px';
      },
      enter(el, done) {
        console.log('enter');
        let round = 1;
        const interval = setInterval(() => {
          el.style.width = (this.elementWidth + round * 10) + 'px';
          round++;
          if (round > 20) {
            clearInterval(interval);
            done();
          }
        }, 20);
      },
      afterEnter(el) {
        console.log('afterEnter');
      },
      enterCancelled(el) {
        console.log('enterCancelled');
      },
      beforeLeave(el) {
        console.log('beforeLeave');
        this.elementWidth = 300;
        el.style.width = this.elementWidth + 'px';
      },
      leave(el, done) {
        console.log('leave');
        let round = 1;
        const interval = setInterval(() => {
          el.style.width = (this.elementWidth - round * 10) + 'px';
          round++;
          if (round > 20) {
            clearInterval(interval);
            done();
          }
        }, 20);
      },
      afterLeave(el) {
        console.log('afterLeave');
      },
      leaveCancelled(el) {
        console.log('leaveCancelled');
      }
    }
}
```

### Animate Dynamic Components

We could create two components and display them conditionally. Either one or the other. In order to animate them when they display, we would just add them inside a transition tag and use a transition that we have create like before.

``` html
<button 
  @click="selectedComponent == 'app-success-alert' ? selectedComponent = 'app-danger-alert' : selectedComponent = 'app-success-alert'"
  >Toggle Component</button>
<br>
<transition name="fade" mode="out-in">
  <component :is="selectedComponent"></component>
</transition>
```
### Animating list with transition-group

`<transition>` is not rendered t the DOM. `<transition-group>` is rendered to the DOM. It renderes and new HTML tag. By default that will be a spam, but you can overwrite this by setting `<transition-group tag="TAG">`

`<transition-group>` gets access to the same css class as `<transition>` but it also have `.slide-move` to apply to the element that needs to move went another one is removed.

### Connecting to severs via HTTP using vue-resource

To use `vue-resource` we can install it using `npm` `npm install --save vue-resource`

On the global Vue object, we need to import vue-resource:
`import VueResource from 'vue-resource';` 
and then we can use `use()` which tells Vue to add a plugin to the core VueJS fonctionality:
`Vue.use(VueResource);`

``` javascript
import Vue from 'vue'
import VueResource from 'vue-resource'
import App from './App.vue'

Vue.use(VueResource);

new Vue({
  el: '#app',
  render: h => h(App)
})
```
Now we can access all the methods from vue-resource.

To test the vue-resource, we setup a firebase database. We chose realtime database in test mode.

Now we can setup a form on our vue instance and connect it to a method like `submit()` in this case.

``` html
<div class="form-group">
  <label>Username</label>
  <input type="text" class="form-control" v-model="user.username">
</div>
<div class="form-group">
  <label>Mail</label>
  <input type="text" class="form-control" v-model="user.email">
</div>
<button class="btn btn-primary" @click="submit">Submit</button>
```
And in our script:

``` javascript
export default {
  data() {
    return {
      user: {
        username: '',
        email: '' 
      }
    }
  },
  methods: {
    submit() {
      this.$http.post('https://vuejs-http-f8a69.firebaseio.com/data.json', this.user)
        .then(response => {
          console.log(response)
        }, error => {
          console.log(error)
      });
    }
  }
}
```

Since we are working with firebase, we will have to create a node and add the json extension

`'https://vuejs-http-f8a69.firebaseio.com/data.json'` is the url we need to pass to the $http.post method. The `data` node name could be anything, but firebase only accepts json extension.

The second argument of the $http.post method is the data we want to send with the request. In this case we are sending our user. We also have access to the error.

vue-resource is returning a promise, so we can use .then to do something with the response.

### sending a GET request

You can fetch data using the `$http.get()` method. This method takes the url as the first argument. It doesn't need a second argument. It also returns a promise, and vue-resource comes with some methods you can use on a get request like `.json()` for example. It turns the response into an object and returns it. This method is also a promise, it will return the data so you have to `return response.json()` and chaine another .then method to it to use the data returned.

As an example, look at the fetchData method:

``` javascript
methods: {
  submit() {
    this.$http.post('https://vuejs-http-f8a69.firebaseio.com/data.json', this.user)
      .then(response => {
        console.log(response)
      }, error => {
        console.log(error)
      });
  },
  fetchData() {
    this.$http.get('https://vuejs-http-f8a69.firebaseio.com/data.json')
      .then(response => {
        return response.json();
      })
      .then( data => {
        const resultArray = [];
        for(let key in data) {
          resultArray.push(data[key]);
        }
        this.users = resultArray;
      })
  }
}
```

### Configure vue-resource globally

You can see it the code above, on line 3 and line 11, we are repeating the url to which we are making the request. We could set it up globally. vue-resource gives use a lots of methods to help us.

In the global instance, where we called `Vue.use(VueResource)`, we can use `Vue.http.options.root = 'YOUR URL'`

``` javascript
import Vue from 'vue'
import VueResource from 'vue-resource'
import App from './App.vue'

Vue.use(VueResource);
Vue.http.options.root = 'https://vuejs-http-f8a69.firebaseio.com/data.json';

new Vue({
  el: '#app',
  render: h => h(App)
})
```

Then in your request, you can pass an empty string or append something to the url you made globally available.

``` javascript
methods: {
  submit() {
    this.$http.post('', this.user)
      .then(response => {
        console.log(response)
      }, error => {
        console.log(error)
      });
  },
  fetchData() {
    this.$http.get('')
      .then(response => {
        return response.json();
      })
      .then( data => {
        const resultArray = [];
        for(let key in data) {
          resultArray.push(data[key]);
        }
        this.users = resultArray;
      })
  }
}
```

On this global options object you could also set a default header for your requests amongst other things.

Just keep in mind that in the global instance, you don't need the dollar sign in front of the http method, only outside of the global instance.

### intercepting requests

In the global instance, we can also set some interceptors.
Interceptors is an array of function that we want to execute on each request basically.

As an example:

``` javascript
import Vue from 'vue'
import VueResource from 'vue-resource'
import App from './App.vue'

Vue.use(VueResource);
Vue.http.options.root = 'https://vuejs-http-f8a69.firebaseio.com/data.json';
Vue.http.interceptors.push((request, next) => {
  console.log(request);
  if(request.method == 'POST') {
    request.method = 'PUT'
  }
  next();
});

new Vue({
  el: '#app',
  render: h => h(App)
})
```

In this example, we intercept the request that are Post request and transform them into Put requests.

The function next is to allow the request to continue running.

### intercepting Responses

You can also intercept responses in the next function, you have access to the response. So you could modify the response on every requests.

``` javascript
import Vue from 'vue'
import VueResource from 'vue-resource'
import App from './App.vue'

Vue.use(VueResource);
Vue.http.options.root = 'https://vuejs-http-f8a69.firebaseio.com/data.json';
Vue.http.interceptors.push((request, next) => {
  console.log(request);
  if(request.method == 'POST') {
    request.method = 'PUT'
  }
  next(response => {
    response.json = () => {
      return {
        messages: response.body
      }
    }
  });
});

new Vue({
  el: '#app',
  render: h => h(App)
})
```
### Routing

To add the vue-router pluggin we are doing the same thing as we did for vue-resource, we import it in the global instance and use the `Vue.use()` method.

``` javascript
import Vue from 'vue'
import Vuerouter from 'vue-router';
import App from './App.vue'

Vue.use(VueRouter);

new Vue({
  el: '#app',
  render: h => h(App)
})
```

Then you want to setup your routes. You can set them up in the global instance or outsource it in another file, but still it will need to be registered in the global instance.

So we create routes.js file:

Inside, we export a const routes that is an array, each element of this array is a route.

``` javascript
import User from './components/user/User.vue';
import Home from './components/Home.vue';

export const routes = [
  { path: '', component: Home},
  { path: '/user', component: User}
];

```

Now back in main.js, we can register those routes:

``` javascript
import Vue from 'vue'
import Vuerouter from 'vue-router';
import App from './App.vue';
import { routes } from ',/routes';

Vue.use(VueRouter);

const router = new VueRouter({
  routes //es6 same as routes: routes
});

new Vue({
  el: '#app',
  router, //es6 same as router: router
  render: h => h(App)
})
```

Then in your App.vue component, you can use `<router-view>` to tell Vue were to display the component that needs to be displayed on a specific route.

``` html
<router-view></router-view>
```
router-view is a built in componentcomming with vue-router and it tell vue were to display the component needed on the desired route.

### routing mode Hash vs History

In single page application we generally use the Hashtag to the url to tell the browser to handle the part before the Hash by sending it to the server and let the part after the Hash to be handled by our javascript application.

Since it is not hard to configure the server and having the hash in the url is not that pretty. The trick is to configure your server so that it returns the index html file in all cases. Because in our index.html file , the vue application is launched and can parse the urls and take over

In order to tell vue-router that we have configured our server correctly and that we want to use the history mode (the mode without hastag) we will add the mode in the router like this in the main.js file:

``` javascript
const router = new VueRouter({
  routes, //es6 same as routes: routes
  mode: 'history'
});
```

### Navigating with Router Links

When using router-view, we have access to `<router-link>` The router links are setup in a way that it will not reload the page, it will simply isplay the right component.

We can use them like this:

``` html
<template>
  <ul class="nav nav-pills">
    <li role="representation"><router-link to="/">Home</router-link></li>
    <li role="representation"><router-link to="/user">User</router-link></li>
  </ul>
</template>
```
### Navigate from code

If we want to navigate to a certain page using a button click for example instead of a link, we would do:

``` html
<template>
    <div>
      <h1>The User Page</h1>
      <button @click="navigateToHome" class="btn btn-primary">Go Home</button>
    </div>
</template>

<script>
export default {
  methods: {
    navigateToHome() {
      this.$router.push('/');
    }
  }
}
</script>
```

You can see that we have access to the `$router` method using the `$` because it is external. In main.js were we import vue-router we have access to it without the `$` but not in the other VUe instances.

### Setting Up Route Parameters

In our routes.js file, we can add a dynamic part. for example for user, we can add the Id.

``` javascript
import User from './components/user/User.vue';
import Home from './components/Home.vue';

export const routes = [
  { path: '', component: Home},
  { path: '/user/:id', component: User}
];
```
The `:id` is telling the route that it needs to match user/something

now if we are trying to navigate to /user it will not work, because the route is expection something after the /user. In Header we can simulate that by hard coding /something after /user like this:

``` html
<template>
  <ul class="nav nav-pills">
    <li role="representation"><router-link to="/">Home</router-link></li>
    <li role="representation"><router-link to="/user/10">User</router-link></li>
  </ul>
</template>
```

### Fetching and Using Route Parameters

To get the parateter passed to the route, we can use the $route.params. For example, in the user route, we added a parameter :id, and in the user, we can do that:

``` html
<template>
    <div>
      <h1>The User Page</h1>
      <p>Loaded Params: {{ id }}</p>
      <button @click="navigateToHome" class="btn btn-primary">Go Home</button>
    </div>
</template>

<script>
export default {
  data() {
    return {
      id: this.$route.params.id
    }
  },
  methods: {
    navigateToHome() {
      this.$router.push('/');
    }
  }
}
</script>
```

See on line 13, we ave access to the $route.params.id

### Reacting to changes in Route Params

if vue is loading the same component and only the params is changing. The Id in user will not be updated. In order to track the change in params, we can setup a watcher.

``` html
<template>
    <div>
      <h1>The User Page</h1>
      <p>Loaded Params: {{ id }}</p>
      <button @click="navigateToHome" class="btn btn-primary">Go Home</button>
    </div>
</template>

<script>
export default {
  data() {
    return {
      id: this.$route.params.id
    }
  },
  watch: {
    '$route'(to, from) {
      this.id = to.params.id;
    }
  },
  methods: {
    navigateToHome() {
      this.$router.push('/');
    }
  }
}
</script>
```

Line 16 we are setting a watcher, we have access to this '$route(to, from)' method where to is the route we are going to and from the route we are coming from.

In vue-router 2.2 we can extract the route params via "props" eliminating the need for watching the $route

### Setting up child routes (Nested Routes)

to setup child route, we use the children object in the routes.js. import all the nested components and setup the routes:

``` javascript
import User from './components/user/User.vue';
import Home from './components/Home.vue';
import UserStart from './components/user/UserStart.vue';
import UserDetail from './components/user/UserDetail.vue';
import UserEdit from './components/user/UserEdit.vue';


export const routes = [
  { path: '', component: Home},
  { path: '/user', component: User, children: [
    { path: '', component: UserStart },
    { path: ':id', component: UserDetail },
    { path: ':id/edit', component: UserEdit }
  ]}
];
```

And the you can use the router-view component inside the parent component, here the user component to display the nested component. Here line 5

``` html
<template>
    <div>
      <h1>The User Page</h1>
      <button @click="navigateToHome" class="btn btn-primary">Go Home</button>
      <router-view></router-view>
    </div>
</template>

<script>
export default {
  methods: {
    navigateToHome() {
      this.$router.push('/');
    }
  }
}
</script>
```

### Navigate to nested routes

We can setup route-link to navigate to nested routes:

``` html
<template>
  <div>
    <p>Please select a User</p>
    <hr>
    <ul class="list-group">
      <router-link 
        tag="li"
        to="/user/1" 
        class="list-group-item" 
        style="cursor: pointer">User 1</router-link>
      <router-link 
        tag="li"
        to="/user/2" 
        class="list-group-item" 
        style="cursor: pointer">User 2</router-link>
      <router-link 
        tag="li"
        to="/user/3" 
        class="list-group-item" 
        style="cursor: pointer">User 3</router-link>
    </ul>
  </div>
</template>
```

The `to=""` attribute will tell whhich route to point the element to.

In the UserDetail component we can access the params like this:

``` html
<template>
    <div>
      <h3>Some User Details</h3>
      <p>User Loaded has Id: {{ $route.params.id }}</p>
    </div>
</template>
```

### Making Router Link more Dynamic

You can make router link more dynamic by binding the `:to=""` 

``` html
<template>
    <div>
      <h3>Some User Details</h3>
      <p>User Loaded has Id: {{ $route.params.id }}</p>
      <router-link tag="button" :to="'/user/' + $route.params.id + '/edit'">Edit user</router-link>
    </div>
</template>
```

### Better way of creating links with Named Routes

When setting up the routes, you can setup a name for each route and then use it anywhere you use the route.

``` javascript
import User from './components/user/User.vue';
import Home from './components/Home.vue';
import UserStart from './components/user/UserStart.vue';
import UserDetail from './components/user/UserDetail.vue';
import UserEdit from './components/user/UserEdit.vue';


export const routes = [
  { path: '', component: Home},
  { path: '/user', component: User, children: [
    { path: '', component: UserStart },
    { path: ':id', component: UserDetail },
    { path: ':id/edit', component: UserEdit, name: 'userEdit' }
  ]}
];
```
On line 13 we are setting up the name for the route, now we can use it in the router-link, but now we could pass the params

``` html
<template>
    <div>
      <h3>Some User Details</h3>
      <p>User Loaded has Id: {{ $route.params.id }}</p>
      <router-link tag="button" :to="{ name: 'userEdit', parmas: { id: $route.params.id } }">Edit user</router-link>
    </div>
</template>
```

### Multiple router views (named router views)

It is also possible to name a router views

``` javascript
import User from './components/user/User.vue';
import Home from './components/Home.vue';
import UserStart from './components/user/UserStart.vue';
import UserDetail from './components/user/UserDetail.vue';
import UserEdit from './components/user/UserEdit.vue';
import Header from './components/Header.vue';


export const routes = [
  { path: '', name: Home, components: {
    default: Home,
    'header-top': Header
  }},
  { path: '/user', components: {
    default: User,
    'header-bottom': Header
  }, children: [
    { path: '', component: UserStart },
    { path: ':id', component: UserDetail },
    { path: ':id/edit', component: UserEdit, name: 'userEdit' }
  ]}
];
```

See how we are using components instead of component on line 10 and 14. we are passing a default and a named component.

In our App.vue, we can now load the header at different position depending if I am in the home page or the user page:

``` html
<template>
    <div class="container">
        <div class="row">
            <div class="col-xs-12 col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3">
                <h1>Routing</h1>
                <hr>
                <router-view name="header-top"></router-view>
                <router-view></router-view>
                <router-view name="header-bottom"></router-view>
            </div>
        </div>
    </div>
</template>
```

### Redirecting

We can redirect a user by setting up a redirect in our routes.js file after all our routes.

``` javascript
export const routes = [
  { path: '', name: Home, components: {
    default: Home,
    'header-top': Header
  }},
  { path: '/user', components: {
    default: User,
    'header-bottom': Header
  }, children: [
    { path: '', component: UserStart },
    { path: ':id', component: UserDetail },
    { path: ':id/edit', component: UserEdit, name: 'userEdit' }
  ]},
  { path: '/redirect-me', redirect: '/'}
];
```

### Setting Up "Catch All" routes / Wildcards

We can setup  route that would redirect any routes that are not the routes we setup.

After all our routes, we setup another routes with the path:'*'

``` javascript
{ path: '*', redirect: '/'}
```
