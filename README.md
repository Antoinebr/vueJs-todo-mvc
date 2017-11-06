# vue-todo

> A PWA vue TODO list

[demo](https://todo.antoinebrossault.com) 

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For detailed explanation on how things work, checkout the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
 

 ## Deploy 

```bash
 rsync -arv {$pathToProject}vue-todo/dist/ {$login}@{$serverIp}:/home/todo/ -e "ssh -p {$port}"
 ```
 
 ## Computed properties

Computed properties are usuefull to avoid re-running all the methods when not necessaries.
Compoutes properties can have getter and setter.

### Get
```allDone``` will return true if 'this.remainning === 0```

### Set
Here the checkbox send a value of ```true``` or ```false``` that's send to the set function.
In this example we putt all the todo completed propoertie at true;

```html
<input type="checkbox" class="toggle-all" v-model="allDone">
```

```js
computed : {
    
    allDone: {

      get(){
        return this.remaining === 0;
      },

      set(value){
         
         this.todos.forEach( t => t.completed = value );

      }

    }
}
```

## Usefull built-in directives 

### class 

If the propertie ```filter``` is === to ```all``` my element will have the ```selected``` class

```html
<a href="" :class="{selected : filter === 'all' }" > All </a>
```

We can add more than one Class 

```html  
<div :class="{completed: todo.completed, editing: todo === editing}"></div> 
```

We can do ternary conditions :

Here if ```blockingJs``` is in the ```recos```array the ```md-primary``` class will be added.
**NB:** The ```md-fab``` class will be here all the time.

```html
<md-button :class="[recos.includes('blockingJs') ? 'md-primary' : '', 'md-fab' ]" @click.prevent=" addReco('blockingJs')">
```


<hr>

### @click.prevent 

This act like 

```JavaScript
$('.el').click( e =>  e.preventDefault() );
```

```html 
<button @click.prevent="deleteCompleteted()">Delete terminated tasks</button>
 ```

<hr>

## Custom Directive 

In this example we create a focus directive.

```html
<input type="text" class="edit" v-focus="todo === editing">
```

We get two values from it ```el``` and ```value``` here we just add a focus to the element.
We wrapped it with ```Vue.nextTick``` to apply this change at the next Vue DOM refresh

```JavaScript 
directives:{

  focus (el,value){

    if(value) Vue.nextTick( _ => el.focus() ); // nextTick == Next Dom refresh ( helot fix bug )

  }

}
```

NB Import Vue ```import Vue from 'vue';```


<hr>

## Offline Management

## Created to get the data when the component is created

```JavaScript 
created(){

  let retrievedObject = localStorage.getItem('todos');

  if( retrievedObject ) this.todos = JSON.parse(retrievedObject);

},
```

### Watchers to refresh the datas

I chose to save the ```todos``` array of object  in the localstorage each time ```todos```is modified.

The watcher will observe the ```todos```array of object.

```JavaScript 
watch: {

  todos: {
    handler(val){
      
      localStorage.setItem( 'todos', JSON.stringify(this.todos) );

    },
    deep: true // to watch nested element of the object otherwise it will be refresh only when a new array element will be added / deleted. 

}
``` 


**NB : Never use Arrow function (ES6) for watchers otherwise it will faill**

> Why not using debounce for performance https://lodash.com/docs#debounce


<hr>

## Webpack

If the project will go in a directory in production edit the wbepackconfig

```JavaScript 
module.exports = {
  build: {

    assetsPublicPath: './',

  },
}
  ```
