### 1、什么是代理模式  
一个类（对象）无法直接访问到另一个类（对象），需要其他的类（对象）代理才能间接的进行访问，这样的模式就是代理模式。它属于设计模式的结构型模式，日常工作中我们常用的事件委托、proxy都是属于代理模式的应用。本小节将带你实现一个通用的事件委托函数以及进行vue3响应原理实现核心——proxy的初体验。

### 2、从生活实例理解
我可以直接去菜市场买菜。但是因为我从中风险地区回家，居委会通知我要隔离，这时我无法直接买菜，居委会的阿姨告诉我，她可以帮我买菜给我送过来。我直接买菜的过程变成了我告诉阿姨我要买什么菜，阿姨买好了给我送过来。这就是生活中的一个代理模式。

### 3、代理模式的实现
我们用代码来实现一下上面买菜的过程:
```javaScript
const xu = {
  goShopping(){
    console.log(shop.toSell([{name:'西红柿',money:5}]))
  }
}
const shop = {
  toSell(detailed){
    let str = ''
    detailed.forEach(item => {str = `${str}${item.money}块钱的${item.name},`})
    return str
  }
}
```
隔离前我是直接买东西，隔离后我要告诉阿姨，让阿姨帮我买，我们需要改造一下goShopping方法。
```javaScript
const xu = {
  goShopping(){
    console.log(ayi.help([{name:'西红柿',money:5}]))
  }
}
const ayi = {
  help(detailed){
    return shop.toSell(detailed)
  }
}
const shop = {
  toSell(detailed){
    let str = ''
    detailed.forEach(item => {str = `${str}${item.money}块钱的${item.name},`})
    return str
  }
}
```
这是实现了阿姨帮我们买菜的操作，但是还是不够，阿姨仅仅是帮我们买了菜，买的菜有坏的，我们告诉阿姨：菜市场有的菜坏了，这种菜我们不要。
```javaScript
const xu = {
  goShopping(){
    console.log(ayi.help([{name:'西红柿',money:5},{name:'西葫芦',money:2},{name:'黄瓜',money:1},{name:'菜花',money:9}]))
  }
}
const ayi = {
  help(detailed){
    let goodDish = []
    let badDish = []
    let arr = shop.toSell(detailed)
    arr.forEach(item => {
      if(item.flag = 'good'){
        goodDish.push(item)
      }else{
        badDish.push(item)
      }
    })
    return `${goodDish.join()}是好的菜，${badDish.join()}坏了`
  }
}
const shop = {
  toSell(detailed){
    detailed.forEach((item,index) => {
      if(index%3 ===0){
        item.flag = 'bad'
      }else{
        item.flag = 'good'
      }
    })
    return detailed
  }
}
```
阿姨帮我们过滤掉了坏的菜，这时候这种代理叫做保护代理。
古人三省其身，今人三省吃啥，我们告诉阿姨，菜你看着买吧。
```javaScript
const xu = {
  goShopping(){
    console.log(ayi.help())
  }
}
const ayi = {
  think(){
    return [{name:'西红柿',money:5},{name:'西葫芦',money:2},{name:'黄瓜',money:1},{name:'菜花',money:9}]
  },
  help(){
    let goodDish = []
    let badDish = []
    let arr = shop.toSell(this.think())
    arr.forEach(item => {
      if(item.flag = 'good'){
        goodDish.push(item.name)
      }else{
        badDish.push(item.name)
      }
    })
    return `${goodDish.join()}是好的菜，${badDish.join()}坏了`
  }
}
const shop = {
  toSell(detailed){
    detailed.forEach((item,index) => {
      if(index%3 ===0){
        item.flag = 'bad'
      }else{
        item.flag = 'good'
      }
    })
    return detailed
  }
}
```
我们把费脑子的问题交给了阿姨，这时候实现的代理叫做虚拟代理。

### 4、单例模式在框架中的应用
