```tsx
//constructor约束
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}
//类接口
interface ClockInterface {
    tick();
}

// 类工厂函数的构造器 
function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
    return new ctor(hour, minute);
}
//对类的实现
class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("beep beep");
    }
}

class AnalogClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("tick tock");
    }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```



## 在泛型里使用类类型

```typescript
//简写
class BeeKeeper {
  hasMask: boolean;
}

class ZooKeeper {
  nametag: string;
}

class Animal1 {
  numLegs: number;
}

class Bee extends Animal1 {
  keeper: BeeKeeper;
}

class Lion extends Animal1 {
  keeper: ZooKeeper;
}

function createInstance<A extends Animal1>(c: new () => A): A {
  return new c();
}

//这样是可以通过类型检查的
createInstance(Lion).keeper.nametag;  // typechecks!
createInstance(Bee).keeper.hasMask;   // typechecks!
```

```
1.import {Context} from "midway"

```

```

```

