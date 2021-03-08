### 1、什么是单例模式  
单例模式顾名思义就是一个类只有一个实例。它属于设计模式的创建型模式，在很多框架中都有应用，例如JQ，vuex，redux等。本文将从单例模式原理与实现入手，带你了解到主流框架中单例模式的应用;在随后的章节策略模式中，带你使用单例模式实现单例模式与策略模式的组合使用。
```javaScript
function Coder{};
const coder1 = new Coder();
const coder2 = new Coder();
```
在以上代码中实例化了两个Coder，通常情况下我们比较coder1和coder2是否相等时，由于他们是两个不同的实例所以是false。而在单例模式下coder1 === coder2 为true,是不是很神奇。

### 2、单例模式实现原理
ES5我们可以通过闭包实现单例模式；ES6我们可以通过constructor或者静态方法实现单例模式。

### 3、单例模式的实现
单例模式的实现思路就是有实例是返回实例，没有实例时创建实例并返回实例。
  * #### ES5版本  
    明确了大致思路我们先来写第一版的代码
    ```javaScript
    // 编写构造函数
    function Singleton(no){
      this.no = no;
    }
    // 给构造函数原型添加方法
    Singleton.prototype.getNo = function(){
      console.log(this.no);
    };
    ```
    现在一个简单的用于测试的构造函数就编写好了，这时如果我们通过new来创建实例，得到的是两个不同的实例。我们可以考虑给Singleton构造函数添加方法，通过闭包来保存已经创建的实例，从而实现单例。
    ```javaScript
    // 该方法调用时就会返回实例，所以应该是一个立即执行的函数
    Singleton.createSingleton = (function(){
      // 定义变量用于存储第一次创建的实例
      var example = null;
      return  function(no){
        // 如果没有实例创建实例
        if (!example) {
          example = new Singleton(no);
        }
        // 返回实例
        return example;
      }
    })();

    var a = Singleton.createSingleton(1);
    var b = Singleton.createSingleton(2);
    a.getNo(); //1
    b.getNo(); //1
    a === b;  //true
    ```
    可以看到 a 和 b 是同一个实例，第一版的单例模式就实现了。下面我们来实现一个可以通过new创建单例的构造函数。
    ```javaScript
    var Singleton = (function(){
      // 定义变量用于存储第一次创建的实例
      var example;
      // 用于创建实例的内部构造函数
      function Singleton(no){
        this.no = no;
      };
      // 给构造函数添加方法
      Singleton.prototype.getNo = function(){
        console.log(this.no);
      };
      return function(no){
        if (!example) {
          example = new Singleton( no );
        }
        return example;
      }
    })();

    var a = new Singleton(1);
    var b = new Singleton(2);
    a.getNo(); //1
    b.getNo(); //1
    a === b;  //true
    ```
  * #### ES6版本  
    ES6版本的构造函数有两种实现方式，一种是借助constructor，另一种是使用静态方法。

    * constructor版  
      首先我们写一个正常的class
      ```javaScript
      class Singleton{
        constructor(no) {
          this.no = no;
        }
      }
      ```
      接下来我们对constructor进行一下改造：在这里我们需要先判断Singleton上的example属性（用于存放已经创建好的单例）是否存在，如果存在我们直接返回该属性；不存在我们需要执行constructor里的逻辑，然后把让该属性等于this，代码如下
      ```javaScript
      class Singleton{
        constructor(no) {
          // 首次实例化
          if (!Singleton.example){
            this.no = no;
            // 把this挂载到Singleton类的example属性
            Singleton.example = this;
          }
          return Singleton.example;
        }
      }

      var a = new Singleton(1);
      var b = new Singleton(2);
      a === b;  //true
      ```

    * 静态方法版  
      由于ES6提供了静态方法，所以我们可以通过静态方法实现单例模式。
      ```javascript
      class Singleton {
        constructor(no) {
          this.no = no;
        }
        //静态方法
        static createSingleton(no) {
          if(!this.example) {
            this.example = new Singleton(no);
          }
          return this.example;
        }
      }

      var a = Singleton.createSingleton(1);
      var b = Singleton.createSingleton(2);
      a === b;  //true
      ```
  * #### 单例模式组合[代理模式]()
    ```javascript
    // 编写一个普通的构造函数
    function Singleton(no){
      this.no = no;
    };
    Singleton.prototype.getNo = function(){
      console.log(this.no);
    };
    // 编写代理类
    const proxySingleton = (function(){
      var example;
      return function(no){
        if(!example){
          example = new Singleton(no); 
        }
        return example;
      }
    })();
    
    var a = new proxySingleton(1);
    var b = new proxySingleton(2);
    a === b;  //true
    ```
  * #### 单例模式组合[工厂模式]()
    ```javascript
    // 存放实例的对象
    const singObj = Object.create(null);
    // 编写帮我们实现单例的工厂函数，该函数接受的是一个构造函数
    function AgentSingleton(constructor) {
      // 我们需要一个对象帮我们存储构造好的实例，需要把constructor的name取出来做对象的key
      let name = constructor.name;
      return function() {
        // 有就返回，没有就创建
        if(!singObj[name]){
            singObj[name] = new constructor(...arguments);
        }
        return singObj[name];
      }
    }

    // 编写一个普通的构造函数
    function SingletonTest1(no){
      this.no = no;
    }
    SingletonTest1.prototype.getNo = function(){
      console.log(this.no);
    };

    // 编写一个普通的构造函数
    function SingletonTest2(no){
      this.no = no;
    }
    SingletonTest2.prototype.getNo = function(){
      console.log(this.no);
    };

    // 使用代理对象创建单例
    var a = AgentSingleton(SingletonTest1)(1);
    var b = AgentSingleton(SingletonTest1)(2);
    var c = AgentSingleton(SingletonTest2)(1);
    var d = AgentSingleton(SingletonTest2)(2);
    a === b;  //true
    b === c;  //false
    c === d;  //true
    ```
### 4、单例模式在框架中的应用
由于js这门语言的特殊性，所以并不适合完全的照搬类继承语言的设计模式，所以很多时候js把全局的对象当作单例来使用，vuex就是使用了这种方式。
```javascript
let Vue // bind on install
  ...
export function install (_Vue) {
  if (Vue && _Vue === Vue) {
    if (__DEV__) {
      console.error(
        '[vuex] already installed. Vue.use(Vuex) should be called only once.'
      )
    }
    return
  }
  Vue = _Vue
    ...
}
```
