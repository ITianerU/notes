# 简述

是js的超集, 扩展js语法

## 与js的区别

1. 定义变量必须要指定类型

# 安装

```shell
cnpm install -g typescript
yarn global add typescript
```

# 编译

## 主动编译

```shell
tsc xxx.ts
```

## 自动编译

### 初始化项目

```shell
# 会生成一个tsconfig.json文件, 将outDir选项注释打开, 配置编译后的文件生成位置
tsc --init
```

### webstrom配置

- 依次点击 **设置 -> 语言和框架 -> TypeScript**, 将**在更改时重新编译**勾选上

# 数据类型

- boolean

- number

- string

- array

  ```typescript
  // 定义数组要指定数组的数据类型
  // 方式一
  let arr:number[] = [1,2,3,4,5]
  let arr:string[] = ['1', '2', '3']
  // 方式二 通过泛型指定类型
  let arr:Array<number> = [1,2,3,4,5]
  ```

- tuple(元组)

  ```typescript
  // 元组需要指定一个列表中每个数据的数据类型
  let tur:[string, number] = ["123", 123]
  ```

- enum

  ```typescript
  // 定义枚举
  enum Flag {
      success = 1,
      error = -1
  }
  // 使用
  let f:Flag = Flag.success
  
  // 如果不指定值, 默认值为索引
  enum Color {red, blue, yellow}
  
  // 如果第一个值指定了一个数字, 后面的值默认依次+1
  enum Color2 {red = 1, blue, yellow}
  
  // 如果第一个值指定了一个非数字类型, 后面的值没有默认值
  enum Color2 {red = "123", blue, yellow}
  ```

- any(任意)

- null

- underfined

  ```typescript
  // 定义一个变量, 同时指定多个类型
  let a:number | null | underfined = 0
  ```

- void

  ```typescript
  // 用来定义方法没有返回值
  function run():void{
      
  }
  // 有返回值要指定返回的数据类型
  function run():number{
      return 0;
  }
  ```

- never

  ```typescript
  // never 是null和underfined的子类型, 代表不会出现的值
  let err:never = (() => {
      throw "错误";   
  })()
  ```

# 函数

## 定义

### 普通用法

```typescript
// 指明入参类型和返回值类型
function getInfo(name:string, age:number):string{
    return "xxx"
}

```

### 无返回值

```typescript
// 无返回值的函数
function getInfo(name:string, age:number):void{
    
}
```

### 可选参数

```typescript
// 添加?, 让参数可传可不传
function getInfo(name:string, age?:number):void{
    
}
```

### 默认参数

```typescript
// 设置默认值, 有默认值可以不传值
function getInfo(name:string, age:number=20):void{
    
}
```

### 剩余参数

```typescript
// 使用三点运算符, 接收不固定数量的参数
function sum(a:number, ...nums:number[]):void{
    
}
// 调用 a会赋值1, 剩下的数字赋值给nums
sum(1,2,3,4,5)
```

## 重载

ts有重载概念, 可以定义名称相同, 参数列表不同的函数

```typescript
// 上面两个定义重载的方法, 下面是具体的实现, 需要通过typeof区分
function getInfo(name: string):string;
function getInfo(age: number):number;
function getInfo(str: any):any{
	if(typeof str == 'string'){
        return name;
    }else if(typeof str == 'number'){
        return age;
    }
}
```

# 类

```typescript
class Person{
    name:string;
    age:number;
    // 构造函数
    constructor(name:string, age:number){
        this.name = name;
        this.age = age;
    }
    run():void{
        console.log("run");
    }
}

let p = new Person("老王", 52)
p.run()
```

## 继承

```tsx
// 使用extends继承, 并且要使用super调用父类的构造函数
class Son extends Person{
    constructor(name:string, age:number) {
        super(name, age);
    }
}
```

## 重写

```tsx
class Son extends Person{
    constructor(name:string, age:number) {
        super(name, age);
    }
    // ts也支持重写
    run():void{
        console.log("重写父类run");
    }
}
```

## 访问修饰符

ts提供三种访问修饰符

- public(默认)
- protected
- private

## 静态方法

```tsx
class Person{
    // 定义静态属性, 静态属性必须要有初始值
    static age:number = 123;
    // 使用static关键词, 声明静态方法
    static run(){
        // 静态方法中, 不能使用类的普通属性, 可以使用静态属性
        console.log(this.age);
    }
}
// 通过类名直接调用静态方法
Person.run()
// 调用静态属性
Person.age
```

## 多态

可以定义公共父类, 只定义方法名, 子类去做具体的实现

## 抽象方法

```tsx
// 抽象方法只能放在抽象类中
abstract class Animal{
    abstract eat():void;
}
// 继承并实现
class Person extends Animal{
    eat(): void {
    }
}
```

# 接口

### 属性类接口

```ts
// 属性类接口定义了一组数据, 类似于一个实体
interface FullName{
    firstName: string;
    secondName: string;
}

class Person{
    name:FullName;
    constructor(name:FullName) {
        this.name = name
    }
    getName():FullName{
        return this.name;
    }
}
// 传入的数据需要满足属性类接口定义的集合
let p = new Person({
    firstName: "老",
    secondName: "王"
})
```

#### 可选属性

```tsx
interface FullName{
    // 使用 ? 定义可选属性
    firstName?: string;
    secondName: string;
}
```

### 函数类型接口

```tsx
// 对方法传入的参数以及返回值进行约束
interface sendMsg{
    // 定义一个方法规范
    (id:string, msg:string):void
}

class Email{
    // 使用方法规范, 使用时, 参数类型, 返回值类型必须相同, 参数名称可以不同
    sendEmail:sendMsg = function (email:string, msg:string):void{
        console.log("发送成功");
    }
}
```

### 可索引接口(不常用)

#### 对数组约束

```tsx
interface UserArr{
    [index:number/*索引类型, 必须为number*/]:string // 数组类型
}
let arr:UserArr=['aaa', 'bbb']
```

#### 对对象约束

```typescript
interface UserArr{
    [key:string/*对象键的类型, 必须为string*/]:string // 对象值的数据类型
}
let arr:UserArr={name: '老王', age: '123'}
```

### 类类型接口

```tsx
// 定义接口
interface Animal{
    name: string
    eat():void
}
// 实现接口
class Dog implements Animal{
    name: string;
    constructor(name:string) {
        this.name = name
    }
    // 实现接口抽象方法
    eat(): void {

    }
}
```

### 接口扩展

```tsx
interface Animal{
    eat():void
} 
// 接口继承接口
interface Person extends Animal{
    work():void
}
// 子类需要实现全部接口的方法
class coder implements Person{
    eat(): void {
    }

    work(): void {
    }
}
```

# 泛型