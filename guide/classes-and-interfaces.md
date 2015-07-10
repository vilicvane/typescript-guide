# 类与接口

## 类

```ts
class Foo {
    name: string;
    private age: number;
    
    constructor(name: string, age?: number) {
        this.name = name;
        this.age = age;
    }
    
    askAge(): number {
        return this.age;
    }
}
```

## 接口

TypeScript 的一个核心原则是: [类型检查只关注值的 "外形"](http://www.typescriptlang.org/Handbook#interfaces). 这一点可以先不用深究, 讲到类型收窄时我们会再做回顾.

接口在 TypeScript 中扮演的角色就是为这些关注 "外形" 的类型命名, 比如描述一个有必选的 `name: string`, `age: number` 属性, 和可选的 `gender?: Gender` 属性的对象:

```ts
enum Gender {
    male,
    female,
    other
}

interface Person {
    name: string;
    age: number;
    gender?: Gender;
}

var person: Person = {
    name: 'Wat',
    age: 99
};
```

如果没有显式声明变量的类型, 在很多时候, TypeScript 依然可以根据值推导出来 (当然这种推导也适用于前面提到的基本类型):

```ts
var person = {
    name: 'Fuk',
    age: 199,
    gender: Gender.male
};
```

在上一个例子中, `var person = ...;` 语句等价于:

```ts
var person: {
    name: string;
    age: number;
    gender: Gender;
} = {
    name: 'Fuk',
    age: 199,
    gender: Gender.male
};
```

但此时 TypeScript 会认为 `gender` 是必选的.

除此之外, TypeScript 中的接口还可以用来描述函数和索引:

### 函数接口

```ts
interface PersonHandler {
    (person: Person): void;
}

var handler: PersonHandler = function (person) {
    console.log(person.name);
};

handler({
    name: 'Biu',
    age: 3
});

```

### 索引接口

```ts
interface PersonMap {
    [key: string]: Person;
}

var map: PersonMap = { };

map['Wat'] = {
    name: 'Wat',
    age: 99
};
```

详情请参考 [接口](features/interfaces.md).

---

<p align="right">&lt; [函数](functions.md)</p>