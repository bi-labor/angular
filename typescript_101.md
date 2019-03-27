# Typescript 101
Typescript is a typed subset of JavaScript that compiles to plain JavaScript

### Simple types
```Typescript
let a:number = 5;
let b:string = 'a simple text'; // you can also use const too
let c:Date = new Date();
let d:any = 6;
let e:number[] = [1,2,4];
let f:{name:string, age:number} = {name:'John', age:92}; // its basically an interface definition

// You can create your own type
type fruit_string = 'apple' | 'cherry';
let fruit:fruit_string = 'apple';

// Const is also allowed
const day:string = 'Monday'; 
```
**Note:** Explicit types are used here. It's not necessary since compiler can figure out.

### Interfaces and classes
```Typescript
interface Animal{
	legs:number;
	speak():void;
}
interface Pet extends Animal{
	name:string;
}

class Dog implements Pet{
	legs = 4;
	constructor(public name:string){
		
	}
	speak(){
		console.log('--bark--bark--');
	}
}
```

### Enums
```Typescript
enum Colors{
red = 1, blue, green
}
```

### Templates
```Typescript
class Storage<T>{
	list: T[] = [];
	add(item:T){
		this.list.push(item);
	}
}
```

### Async await
To hide and flatten promises
```Typescript
longCalculagtion(num:number):Promise<number>{
	return new Promise((resolve,reject)=>{
		setTimeout(()=>{
			resolve(num*2);
		},1000);
	});
}

async run(){
  console.log('before');
  const results = await longCalculagtion(2);
  console.log('calculated:', results);
  console.log('after')
}
```
Output:
```
before
calculated: 4
after
```

### +1 Configure compile with `tsconfig.json`
```json
{
  "compileOnSave": false,
  "compilerOptions": {
    "baseUrl": "./",
    "outDir": "./dist/out-tsc",
    "sourceMap": true,
    "declaration": false,
    "module": "es2015",
    "moduleResolution": "node",
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "importHelpers": true,
    "target": "es5",
    "typeRoots": [
      "node_modules/@types"
    ],
    "lib": [
      "es2018",
      "dom"
    ]
  }
}

```

