## 安装ts
通过npm（Node.js包管理器）：npm install -g typescript

安装Visual Studio的TypeScript插件

## 接口
```
interface Person {
    firstName: string;
    lastName: string;
}

function greeter(person: Person) {
    return "Hello, " + person.firstName + " " + person.lastName;
}

let user = { firstName: "Jane", lastName: "User" };

document.body.innerHTML = greeter(user);
```
## 类
```
在构造函数的参数上使用public等同于创建了同名的成员变量。

class Student {
    fullName: string;
    constructor(public firstName, public middleInitial, public lastName) {//构造函数
        this.fullName = firstName + " " + middleInitial + " " + lastName;
    }
}

interface Person {
    firstName: string;
    lastName: string;
}

function greeter(person : Person) {
    return "Hello, " + person.firstName + " " + person.lastName;
}

let user = new Student("Jane", "M.", "User");
//new一个对象
document.body.innerHTML = greeter(user);
```