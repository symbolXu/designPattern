### 1、什么是单例模式  
单例模式顾名思义就是一个类只有一个实例。它属于设计模式的创建型模式，在很多框架中都有应用，例如JQ，vuex，redux等。本文将从单例模式原理与实践入手，带你了解到主流框架中单例模式的应用;在随后的章节策略模式中，带你使用单例模式实现单例模式与策略模式的组合使用。请看如下代码:  
```javaScript
function Coder{};
const coder1 = new Coder();
const coder2 = new Coder();
```
在代码中实例化了两个Coder，通常情况下我们比较coder1和coder2是否相等时，由于他们是两个不同的实例所以是false。而在单例模式下coder1 === coder2 为true。是不是很神奇。

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
    })()
    ```
    下面我们进行一下测试：
    ```javaScript
    var a = Singleton.createSingleton(1)
    var b = Singleton.createSingleton(2)
    a.getNo() //1
    b.getNo() //1
    a === b  //true
    ```
    可以看到 a 和 b 是同一个实例。单例模式的已经实现了但是代码不是很美观，而且也不能通过new来创建单例。  
    实现单例模式的核心代码在于Singleton的createSingleton方法，那我们可以参照此方法来实现单例模式。
    ```javaScript
    var Singleton = (function(){
      var example;
      var Singleton = function(no){
        this.no = no;
      };
      Singleton.prototype.getNo = function(){
        console.log(this.no);
      };
      return function(no){
        if (!example) {
          example = new Singleton( no );
        }
        return example;
      }
    })()
    ```
    下面我们进行一下测试
    ```javaScript
    var a = new Singleton(1)
    var b = new Singleton(2)
    a.getNo() //1
    b.getNo() //1
    a === b  //true
    ```
    至此ES5版本的单例模式就实现完成了。
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
      接下来我们对constructor进行一下改造：在这里我们需要借助原型，首先我们要判断Singleton上的example是否存在，如果存在我们直接返回该属性；不存在我们需要执行constructor里的逻辑，然后把让该属性等于this，代码如下
      ```javaScript
      class Singleton{
        constructor(no) {
          // 首次实例化
          if (!Singleton.example){
            this.no = no;
            // 把this挂载到Singleton类的example属性
            Singleton.example = this;
          }
          return Singleton.example
        }
      }
      ```
      下面测试下代码
      ```javaScript
      var a = new Singleton(1)
      var b = new Singleton(2)
      a === b  //true
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
      ```
      下面测试下代码
      ```javaScript
      var a = Singleton.createSingleton(1)
      var b = Singleton.createSingleton(2)
      a === b  //true
      ```

### 4、单例模式的作用
  * 模块间通信
  * 系统中某个对象的实例只能存在一个
  * 保护自己的属性和方法
