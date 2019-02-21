# Angular Voting Applicatoin

**Demo:** https://bi-labor.github.io/angular

**Feedback:** https://goo.gl/forms/PTYOdBNIB0MpSlzu1 

## Table of contents
- [0. Introduction](#0-introduction)
  * [0.a Used technologies](#0a-used-technologies)
  * [0.b Typescript](#0b-typescript)
- [1. Create project sceleton](#1-create-project-sceleton)
  * [1.a. Create a new project](#1a-create-a-new-project)
  * [1.b. Add simple design](#1b-add-simple-design)
- [2. Create Questions](#2-create-questions)
  * [2.a create questoin design](#2a-create-questoin-design)
  * [2.b create Model](#2b-create-model)
  * [2.c Fill UI with data binding](#2c-fill-ui-with-data-binding)
  * [2.d add new Question form](#2d-add-new-question-form)
  * [2.e Add Firebase](#2e-add-firebase)
- [3. Implement Question deletion](#3-implement-question-deletion)
    + [3.a Add delete Button to every question in the same line with the description at `votes/question/question.componenet.ts`](#3a-add-delete-button-to-every-question-in-the-same-line-with-the-description-at-votes-question-questioncomponenetts)
    + [3.b Implement `deleteQuestion()` function at `votes/question/question.component.ts`](#3b-implement-deletequestion-function-at-votes-question-questioncomponentts)
    + [3.c Implement `delete(questionId: string)` function at `services/votes.service.ts`](#3c-implement-delete-questionid-string-function-at-services-votesservicets)
- [4. Add Notifications](#4-add-notifications)
  * [4.a Add notifications module](#4a-add-notifications-module)
  * [4.b Add toasts](#4b-add-toasts)
- [5. Add Statistics](#5-add-statistics)
  * [5.a Add plotly to the project](#5a-add-plotly-to-the-project)
  * [5.b add it to the app module](#5b-add-it-to-the-app-module)
  * [5.c Create a new `statistic` component](#5c-create-a-new-statistic-component)
  * [5.d Create statistic logic at `statistic/statistic.component.ts`](#5d-create-statistic-logic-at-statistic-statistic-component-ts)
  * [5.d Create a bar chart that shows the number of votes for each question](#5d-create-a-bar-chart-that-shows-the-number-of-votes-for-each-question)

## 0. Introduction


### 0.a Used technologies
 * Typescript (+ HTML,CSS,JavaScript)
	* with ts-lint and .editoconfig
 * Angular
	* with angular-cli
 * RxJS (part of Angular)
 * bootstrap (ngx-bootstrap)
 * firabase
	* with firestore repository
 * ngx-toaster for notifications
 * ploty.js for graph plotting

### 0.b Typescript
Typescript is a typed subset of JavaScript that compiles to plain JavaScript

#### simple types
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
**note:** Explicit types are used here. It's not necessary since compiler can figure out.

#### Interfaces and classes
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


#### Enums

```Typescript
enum Colors{
red = 1, blue, green
}
```

```
#### Templates
```Typescript
class Storage<T>{
	list: T[] = [];
	add(item:T){
		this.list.push(item);
	}
}
```

#### Async await
To hide and flatten Promices
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

#### +1 Configure compile with `tsconfig.json`
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


 
## 1. Create project sceleton

### 1.a. Create a new project

#### Install angular cli
```bash
node install @angular/cli -g
```
#### Create new project
```bash
ng new bi-angular
```
**note:** It takes a while, because of it also installs the *node_modules*

#### Inspect the created project
  * `package.json` - contains the required npm modules and start up scripts
  * `angular.json` - angular configuration file
  * `.editogcondig` - editor configuration file (contains rules like indentation)
  * `tsconfig.json` - typescript configuration
  * `tslint.json` - typescript configuration (contains rules like no "", but only '')
  * src/
	* `index.html` - start up page, conatins angular `app-root`, the app entry point
	* app/
		* `app.module.ts` - list all included modules, components, pipes, services, etc..
		* `app.component.ts`  - entry component: later it will only contain a router outlet
		* `*.spec.ts`  - test files


#### Add routing
We will use this later for routing.
(Routing modul is responsible for parsing the current url and *routing*=rendering the application to the appropriate component)
```bash
ng generate module app-routing --flat --module=app
```
It creates: `app-routing.module.ts` and registeres it at`app.module.ts`

Update `app.component.ts` with a router outlet:
```Typescript
@Component({
  selector: 'app-root',
  template: `<router-outlet></router-outlet>`
})
export class AppComponent {
  title = 'bi-angular';
}

```

#### Check if it runs
`npm start` & open `localhost:<port>` in browser.


### 1.b. Add simple design
#### Install bootstrap
You can google for *angular bootstrap* and find a nice npm module.
Best practice: look for the one that has the most stars at github + has a solid documentation.
We will use valor-software's ngx-bootstrap:
```bash
ng add ngx-bootstrap 
```
**note:** this installs and ads to the packege.josn `ngx-bootstrap` and `bootstrap` dependencies

#### Add new angular component: Votes
add it with cli:
```bash
ng generate component votes
```
creates these files:
```
src/app/votes/votes.component.html
src/app/votes/votes.component.spec.ts
src/app/votes/votes.component.ts
src/app/votes/votes.component.css
```
#### Add componenet to router
Update `app-routing.module.ts`
```Typescript
const routes: Routes = [
  {path: '', redirectTo: '/votes', pathMatch: 'full'},
  {path: 'votes', component: VotesComponent}
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
```


#### Create a skeleton design for Votes component
Look for example designs at: https://getbootstrap.com/docs/4.2/examples/starter-template/

Add navigator and main container to `votes.component.html`:
```HTML
<nav class="navbar navbar-expand-md navbar-dark bg-dark fixed-top">
  <a class="navbar-brand" href="https://www.aut.bme.hu/Course/VIAUMB00">Angular Lab</a>
  <button class="navbar-toggler" (click)="isCollapsed = !isCollapsed" 
	  type="button" data-toggle="collapse" data-target="#navbarsExampleDefault"
          aria-controls="navbarsExampleDefault" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>

  <div class="collapse navbar-collapse" id="navbarsExampleDefault" [collapse]="isCollapsed">
    <ul class="navbar-nav mr-auto">
      <li class="nav-item active">
        <a class="nav-link" [routerLink]="['/votes']">Questions <span class="sr-only">(current)</span></a>
      </li>
    </ul>
    <ul class="nav justify-content-center">
      <li class="nav-item">
        <button class="btn btn-primary">New Question</button>
      </li>
    </ul>
  </div>
</nav>


<main role="main" class="container">

</main><!-- /.container -->

```
##### Add `isCollapsed` to  `votes/votes.component.ts`
```Typescript
  isCollapsed = true;
```

##### Add `CollapseModule` to `app.module.ts`
```Typescript
imports:[
...
CollapseModule.forRoot()
]
```

## 2. Create Questions

### 2.a create questoin design
#### Create new `question` component
```bash
ng generate component votes/question
```
#### Add dummy design to `votes/question/question.component.html`

```HTML
<div class="card mb-3">
  <img src="..." class="card-img-top">
  <div class="card-body">

    <div class="row">
      <div class="col-8">
        <h5 class="card-title">Question placeholder</h5>
      </div>
      <div class="col-4">
        <p class="card-text float-right">
          <small class="text-muted">Created a minute ago</small>
        </p>
      </div>
    </div>
    <div class="row">
      <div class="col-8">
        <p class="card-text">Short Description</p>
      </div>
    </div>

    <hr/>
    <div class="form-group row">
      <div class="col-12">
        <div class="custom-control custom-radio custom-control-inline">
          <input  name="radio-0" id="radio-0"
                 type="radio"
                 class="custom-control-input" value="rabbit">
          <label for="radio-0" class="custom-control-label">{{1}}) First option</label>
        </div>
		
        <div class="custom-control custom-radio custom-control-inline">
          <input  name="radio-1" id="radio-1"
                 type="radio"
                 class="custom-control-input" value="rabbit">
          <label for="radio-1" class="custom-control-label">{{2}}) First option</label>
        </div>
        <button  type="button" class="btn btn-secondary float-right">Vote</button>
      </div>
    </div>
  </div>
</div>

```

#### Use it from `votes/votes.component.html`:
```HTML
...
<main role="main" class="container">
  <app-question></app-question>
</main><!-- /.container -->

```

### 2.b create Model
#### `Question` interface at `model/Question.ts`
```Typescript
export interface Question {
  photoUrl?: string;
  question: string;
  description?: string;
  created: number;
  options: QuestionOption[];
}

export interface QuestionEntity extends Question {
  id: string;
}
```
#### `Vote` interface at `model/Vote.ts`
```Typescript
export interface Vote {
  option: string;
  timeStamp: number;
}

```

#### `QuestionOption` interface at `model/QuestionOption.ts`
```Typescript
export interface QuestionOption {
  label: string;
}
```

#### Create Create Vote service wtih dummy data
(you can use cli too:
```bash
ng generate s votes
```
)
But do it manually this time: Creat a new file at `services/vote.service.ts`

```Typescript

@Injectable()
export class VotesService {

  questions: Observable<QuestionEntity[]>;
 
  constructor() {
    this.questions = this.getDummyQuestions();
  }

  getDummyQuestions(): Observable<QuestionEntity[]> {
    return new BehaviorSubject([
      {
        id: '1',
        photoUrl: '',
        question: 'How are you?',
        description: 'I\'m good too..',
        created: Date.now(),
        options: [{label: 'good'}, {label: 'ehh'}]
      }
    ]);
  }	
	
  async addQuestion(q: Question) {
    //TODO: implement
  }

  vote(questionId: string, optionLabel: string) {
    //TODO: implement
  }

  delete(questionId: string) {
    //TODO: implement
  }

  getVotes(questionId: string): Observable<Vote[]> {  
    //TODO: implement
  }
}

```
**note:** don't forget to add it to the app module at `app.module.ts`
```Typescript
...
providers:[
...
VotesService
]
```


### 2.c Fill UI with data binding
##### Inject the VotesService to  `votes/votes.component.ts`:
```Typescript
export class VotesComponent {
  constructor(public votesService: VotesService) {
  }
}

```
##### Update the UI at `votes/votes.componenet.html`
```HTML
<main role="main" class="container">
  <app-question [question]="question"
                *ngFor="let question of votesService.questions | async"></app-question>
</main><!-- /.container -->
```
**Note:** we are using the `async` pipe, since `votesService.questions` is an`Observervable<QuestionEntity>` type.

#### Update `votes/question/question.component.ts`
Add `question` as an imput paremater of the component.
```Typescript
export class QuestionComponent {

  @Input() question: QuestionEntity;

  constructor(private votesService: VotesService) {
  }

  vote() {
	// TODO implement
  }

  deleteQuestion() {
	// TODO implement
  }

}
```
**Note:** You can see that we can now use the `[question]` tag in the `votes/votes.componenet.html` to provide an input to the component.

#### Update `votes/question/question.component.html`
```HTML
<div class="card mb-3">
  <img *ngIf="question.photoUrl" [src]="question.photoUrl" class="card-img-top">
  <div class="card-body">

    <div class="row">
      <div class="col-8">
        <h5 class="card-title">{{question.question}}</h5>
      </div>
      <div class="col-4">
        <p class="card-text float-right">
          <small class="text-muted">Created {{question.created | timeAgo}}</small>
        </p>
      </div>
    </div>
    <div class="row">
      <div class="col-8">
        <p class="card-text">{{question.description}}</p>
      </div>
    </div>

    <hr/>
    <div class="form-group row">
      <div class="col-12">
        <form #voteForm="ngForm">
        <div class="custom-control custom-radio custom-control-inline"
             *ngFor="let option of question.options; let i=index">
          <input [(ngModel)]="selected"
                 [value]="option.label"
                 name="radio-{{question.id}}"
                 id="radio-{{question.id}}{{i}}"
                 type="radio"
                 class="custom-control-input" required>
          <label for="radio-{{question.id}}{{i}}" class="custom-control-label">{{i+1}}) {{option.label}}</label>
        </div>
        <button (click)="vote()"  [disabled]="!voteForm.form.valid" type="button" class="btn btn-secondary float-right">Vote</button>
        </form>
      </div>
    </div>
  </div>
</div>


```

#### Add time transformation pipe
`votes/question/question.component.html` uses now the `timeAgo` pipe to convert the `created` timeStamp to readable string.
It is not a built in pipe, we need to implement it:

Create new pipe at `pipes/TimaAgoPipe.ts`
```Typescript
@Pipe({
  name: 'timeAgo',
  pure: false
})
export class TimeAgoPipe implements PipeTransform {


  public transform(time: number): string {
    const delta = (Date.now() - time) / 1000;

    // format string
    if (delta < 60) { // sent in last minute
      return `${Math.floor(delta)}s ago`;
    } else if (delta < 3600) { // sent in last hour
      return `${Math.floor(delta / 60)}m ago`;
    } else if (delta < 86400) { // sent on last day
      return `${Math.floor(delta / 3600)}h ago`;
    } else { // sent more than one day ago
      return `${Math.floor(delta / 86400)}d ago`;
    }
  }

}

```
**Note:** don't forget to add it to the app module at `app.module.ts`
```Typescript
declarations:[
TimeAgoPipe
]
```

### 2.d add new Question form

#### Add modal to `votes/votes.component.html`

You can use a form generator to creat bootstrap 4 forms: https://bootstrapformbuilder.com/
```HTML
<ng-template #addVoteTemplate>
  <div class="modal-header">
    <h4 class="modal-title pull-left">New Question Form</h4>
    <button type="button" class="close pull-right" aria-label="Close" (click)="modalRef.hide()">
      <span aria-hidden="true">&times;</span>
    </button>
  </div>
  <div class="modal-body">
    <form (ngSubmit)="addQuestion()" #questionForm="ngForm">
      <div class="form-group row">
        <label class="col-3 col-form-label" for="question">Question</label>
        <div class="col-9">
          <input [(ngModel)]="question.question" id="question" name="question" placeholder="How long is the...?"
                 required="required" type="text" class="form-control">
        </div>
      </div>
      <div class="form-group row">
        <label for="photo-url" class="col-3 col-form-label">Photo url</label>
        <div class="col-9">
          <input [(ngModel)]="question.photoUrl" id="photo-url" name="photo-url" placeholder="https://myurl.com/img.png"
                 type="text" class="form-control">
        </div>
      </div>
      <div class="form-group row">
        <label for="description" class="col-3 col-form-label">Description</label>
        <div class="col-9">
          <input [(ngModel)]="question.description" id="description" name="description" placeholder="Some more context"
                 type="text" class="form-control">
        </div>
      </div>

      <div class="form-group row" *ngFor="let option of options; let i = index">
        <label for="description" class="col-2 offset-3 col-form-label">{{i + 1}}.</label>
        <div class="col-7">
          <input [(ngModel)]="option.label"
                 [name]="'option-'+(i+1)"
                 placeholder="Option {{i+1}}"
                 required="required"
                 type="text"
                 class="form-control">
        </div>
      </div>
      <div class="form-group row">
        <div class="offset-3 col-4">
          <button name="submit" type="button" (click)="addOption()" class="btn btn-secondary">Add Option</button>
        </div>
        <div class="col-5">
          <button name="submit" type="submit" [disabled]="!questionForm.form.valid"
                  class="btn btn-primary float-right">Create Question
          </button>
        </div>
      </div>
    </form>
  </div>
</ng-template>
```
Add click listener to the `new Question` button at `votes/votes.component.html`
```HTML
 <li class="nav-item">
     <button class="btn btn-primary"
			 (click)="openModal(addVoteTemplate)">
	 New Question
	 </button>
 </li>
```

#### Implement the logic for the modal at `votes/votes.component.ts`
 * Create `question` and `optoins` data, that we can bind to in the html.
 * Create a model opener function, like: https://valor-software.com/ngx-bootstrap/#/modals

```Typescript
export class VotesComponent {

  modalRef: BsModalRef;
  question: Question;
  isCollapsed = true;

  constructor(public votesService: VotesService,
              private modalService: BsModalService) {
  }

  openModal(template: HTMLElement) {

     this.question = {
      question: '',
      created: Date.now(),
      photoUrl: '',
      description: '',
      options: []
    };
    this.options = [];
    this.addOption();
    this.addOption();
    this.modalRef = this.modalService.show(template);
  }

  async addQuestion() {
    this.modalRef.hide();
    await this.votesService.addQuestion(this.question, this.options);
  }

  addOption() {
    this.question.options.push({label: ''});
  }
}
```

#### Import ngx-bootstrap:ModalService and angular:FormsModule at `app.module.ts`
```Typescript
...
import:[
...
FormsModule,
ModalModule.forRoot(),
...
]
```



### 2.e Add Firebase

#### Data stucure

```
- questoins:collection
|--- id:string
|--- question:string
|--- photoUrl:string
|--- description:string
|--- created:number
|--- options:{label:string}[]
|--- votes:collection
      |--- option:string
      |--- timeStamp:number
```


#### Add Firebase to the project
```bash
npm install firebase @angular/fire --save
```
#### Set up firabase
Update `environments/environment.ts`

You can create your own firebase token, but you can use these:
```Typescript
export const environment = {
  production: false,
  firebase: {
    apiKey: "AIzaSyD0qWw7YgMqGd-ELV0a8ea8O0w2u29wR4M",
    authDomain: "bi-labor-angular2.firebaseapp.com",
    databaseURL: "https://bi-labor-angular2.firebaseio.com",
    projectId: "bi-labor-angular2",
    storageBucket: "bi-labor-angular2.appspot.com",
    messagingSenderId: "1009646526609"
  }
};
```

Alternative you can also you this settings:
```
    apiKey: "AIzaSyDol8jLaHIPSfChtIyg7X36aMyVrN83K_4",
    authDomain: "bi-labor-angular1.firebaseapp.com",
    databaseURL: "https://bi-labor-angular1.firebaseio.com",
    projectId: "bi-labor-angular1",
    storageBucket: "",
    messagingSenderId: "363613880788"
```

#### Add firebase to `app.module.ts`
```Typescript
imports: [
    AngularFireModule.initializeApp(environment.firebase),
    AngularFirestoreModule
]
```

#### There is a current issue with the default settings of firebase in angularfire2, work around:
Read more: https://github.com/angular/angularfire2/issues/1993#issuecomment-455830987
```Typescript
providers: [
 {provide: FirestoreSettingsToken, useValue: {}}
]
```

#### Update votes service at `services/votes.service.ts`
```Typescript

export class VotesService {

  questions: Observable<QuestionEntity[]>;

  private questionCollection: AngularFirestoreCollection<Question>;

  constructor(private afs: AngularFirestore) {
     this.questionCollection = afs.collection<Question>('questions',
      ref => ref.orderBy('created', 'desc').limit(50));

    this.questions = <any>this.questionCollection.snapshotChanges().pipe(
      map((actions: DocumentChangeAction<Question>[]) => {
        return actions.map(a => {
          const data = a.payload.doc.data() as Question;
          const id = a.payload.doc.id;
          return {id, ...data};
        });
      }));
  }


  getDummyQuestions(): Observable<QuestionEntity[]> {
    return new BehaviorSubject([
      {
        id: '1',
        photoUrl: '',
        question: 'How are you?',
        description: 'I\'m good too..',
        created: Date.now(),
        options: [{label: 'good'}, {label: 'ehh'}]
      }
    ]);
  }
 
  async addQuestion(q: Question) {
    await this.questionCollection.add(q);
  }

  vote(questionId: string, optionLabel: string) {
    this.questionCollection.doc(questionId)
      .collection<Vote>('votes').add({timeStamp: Date.now(), option: optionLabel}).catch(console.error);
  }

  delete(questionId: string) {
    // TODO implement
    // don not forget to delete the votes subcollection manually
  }


 getVotes(questionId: string): Observable<Vote[]> {
    return this.questionCollection.doc(questionId).collection<Vote>('votes').valueChanges();
  }

}
```


## 3. Implement Question deletion

#### 3.a Add delete Button to every question in the same line with the description at `votes/question/question.componenet.ts`
```HTML
	<div class="row">
      <div class="col-8">
        <p class="card-text">{{question.description}}</p>
      </div>
	  --------Copy between these----------
      <div class="col-4">
        <button type="button" (click)="deleteQuestion()" class="btn btn-outline-danger float-right">Delete</button>
        <a  [routerLink]="['/statistic', question.id]"
      </div>
	  --------Copy between these----------
    </div>
```

#### 3.b Implement `deleteQuestion()` function at `votes/question/question.component.ts`
#### 3.c Implement `delete(questionId: string)` function at `services/votes.service.ts`
**Note:** Firestore [does not delete subcollections automatically](https://firebase.google.com/docs/firestore/manage-data/delete-data), but you can [do it manually](https://github.com/angular/angularfire2/issues/1400).
**Hint:** Query the firestore document that has the id `questionId` and delete it.

## 4. Add Notifications

### 4.a Add notifications module

#### install and save to `package.json`
We are using ngx-toaster: https://github.com/scttcper/ngx-toastr
```bash
npm install ngx-toastr --save
npm install @angular/animations --save
```
#### Add toaster module to `app.module.ts`

#### **Note:** there is a current minor issue with the lib as the css style interfears with bootstrap
Read more here: https://github.com/scttcper/ngx-toastr/issues/602*/
#### Sollution
Add this to `styles.css`
```css
#toast-container > div {
  opacity:1;
}

```

### 4.b Add toasts
#### Show a toast on creating a new Question
#### Show a toast on deleting a Question
#### Show a toast on voting on a Question


## 5. Add Statistics
### 5.a Add plotly to the project
We are using angular-ploty: https://github.com/plotly/angular-plotly.js/blob/master/README.md
```bash
 npm install angular-plotly.js plotly.js --save
```
### 5.b add it to the app module
### 5.c Create a new `statistic` component 
#### generate it
```bash
ng g c statistic
```
#### add it to the routing so that it has an id parameter
```Typescript
const routes: Routes = [
  { path: '', redirectTo: '/votes', pathMatch: 'full' },
  { path: 'votes', component: VotesComponent },
  { path: 'statistic/:id', component: StatisticComponent }
];
```

### 5.d Design the `statistic` componenet 
#### Use  [navigation](https://getbootstrap.com/docs/4.0/components/navbar/) bar 
#### Crate a [bootstrap card](https://getbootstrap.com/docs/4.0/components/card/) for your plot

### 5.e Create statistic logic at `statistic/statistic.component.ts`
#### Use angular `ActivatedRoute` service to get the id from the active router
**Hint:** you need to `subscribe` to the `ActivatedRoute:params` observable parameters list.
#### Use `VoteService` to get votes
**Hint 1:** It is an Observable you need to `subscribe` to it.

**Hint 2:** You need to process the data to the appropriate format that plotlyJS requires.

**Hint 3:** On updating the model plotlyJs might not detect changes, trigger detection manaully.
You can use this trick `this.graphData = JSON.parse(JSON.stringify(this.graphData));`


### 5.f Create a bar chart that shows the number of votes for each question


## Feedback: https://goo.gl/forms/PTYOdBNIB0MpSlzu1 

