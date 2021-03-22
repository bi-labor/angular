# Angular Voting Application

**Demo:** https://bi-labor.github.io/angular
**Feedback:** https://goo.gl/forms/PTYOdBNIB0MpSlzu1 


![Application](assets/main.jpg)

## Table of contents
- [0. Introduction](#0-introduction)
  * [0.a Used technologies](#0a-used-technologies)
  * [0.b Typescript 101](#0b-typescript-101)
  * [0.c Angular 101](#0c-angular-101)
- [1. Create project sceleton](#1-create-project-sceleton)
  * [1.a. Create a new project](#1a-create-a-new-project)
  * [1.b. Add simple design](#1b-add-simple-design)
- [2. Create Questions](#2-create-questions)
  * [2.a Create question design](#2a-create-question-design)
  * [2.b Create Model](#2b-create-model)
  * [2.c Fill UI with data binding](#2c-fill-ui-with-data-binding)
  * [2.d Add new Question form](#2d-add-new-question-form)
  * [2.e Add Firebase](#2e-add-firebase)
- [3. Implement Question deletion](#3-implement-question-deletion)
    + [3.a Add delete Button to every question in the same line with the description at `votes/question/question.component.ts`](#3a-add-delete-button-to-every-question-in-the-same-line-with-the-description-at-votesquestionquestioncomponenthtml)
    + [3.b Implement `deleteQuestion()` function at `votes/question/question.component.ts`](#3b-implement-deletequestion-function-at-votesquestionquestioncomponentts)
    + [3.c Implement `delete(questionId: string)` function at `services/votes.service.ts`](#3c-implement-deletequestionid-string-function-at-servicesvotesservicets)
- [4. Add Notifications](#4-add-notifications)
  * [4.a Add notifications module](#4a-add-notifications-module)
  * [4.b Add toasts](#4b-add-toasts)
- [5. Add Statistics](#5-add-statistics)
  * [5.a Add plotly to the project](#5a-add-plotly-to-the-project)
  * [5.b Add it to the app module](#5b-add-it-to-the-app-module)
  * [5.c Create a new `statistic` component](#5c-create-a-new-statistic-component)
  * [5.d Design the statistic component](#5d-design-the-statistic-component)
  * [5.e Create statistic logic at `statistic/statistic.component.ts`](#5e-create-statistic-logic-at-statisticstatisticcomponentts)
  * [5.f Create a bar chart that shows the number of votes for each question](5f-create-a-bar-chart-that-shows-the-number-of-votes-for-each-question)

## 0. Introduction
At the end of the guide, please upload the resulting code (without the node_modules folder) in a zip file to course Moodle site.


### 0.a Used technologies
 * Typescript (+ HTML,CSS,JavaScript)
	* with ts-lint and .editorconfig
 * Angular
	* with angular-cli
 * RxJS (part of Angular)
 * Bootstrap (ngx-bootstrap)
 * Firebase
	* with firestore repository
 * ngx-toastr for notifications
 * ploty.js for graph plotting

### 0.b [Typescript 101](typescript_101.md)
### 0.c [Angular 101](angular_101.md)
 
### 0.d Requirements
* Download and install the latest LTS versino of NodeJS from https://nodejs.org/en/
 
## 1. Create project skeleton

### 1.a. Create a new project
Open a Terminal / Command Prompt, and type in the following commands.

#### Install angular cli
```bash
npm install @angular/cli -g
```
#### Create new project
```bash
ng new bi-angular
```

- If asked about the enforcement of strict tpye checking, answer No.
- Don't add routing.
- Use CSS

**Note:** It takes a while, because of it also installs the *node_modules*

#### Inspect the created project
  * `package.json` - contains the required npm modules and start up scripts
  * `angular.json` - angular configuration file
  * `.editorconfig` - editor configuration file (contains rules like indentation)
  * `tsconfig.json` - typescript configuration
  * `tslint.json` - typescript configuration (contains rules like no "", but only '')
  * src/
	* `index.html` - start up page, conatins angular `app-root`, the app entry point
	* app/
		* `app.module.ts` - list all included modules, components, pipes, services, etc.
		* `app.component.ts`  - entry component: later it will only contain a router outlet
		* `*.spec.ts`  - test files




#### Check if it runs
`npm start` & open `localhost:<port>` in browser.




### 1.b. Add simple design

#### Add new angular component: Votes
Add it with cli:
```bash
ng generate component votes
```
Creates these files:
```
src/app/votes/votes.component.html
src/app/votes/votes.component.spec.ts
src/app/votes/votes.component.ts
src/app/votes/votes.component.css
```
#### Add routing
Routing helps us to navigate between screens like:

Between the voting screen and the statistic screen:

![Votes route](assets/votes_navigation.jpg)

![Statistic route](assets/statistic_navigation.jpg)

_(Routing modul is responsible for parsing the current url and *routing*=rendering the application to the appropriate component)_
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


#### Add component to router
Update `app-routing.module.ts` to look like this:

```Typescript
import { NgModule } from '@angular/core';
import {VotesComponent} from './votes/votes.component';
import {RouterModule, Routes} from '@angular/router';


const routes: Routes = [
  {path: '', redirectTo: '/votes', pathMatch: 'full'},
  {path: 'votes', component: VotesComponent}
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }

```


#### Install bootstrap
You can google for *angular bootstrap* and find a nice npm module.
Best practice: look for the one that has the most stars at github + has a solid documentation.
We will use valor-software's ngx-bootstrap:
```bash
ng add @ng-bootstrap/ng-bootstrap
```
**Note:** This installs and adds to the package.json `ngx-bootstrap` and `bootstrap` dependencies


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

  <div class="collapse navbar-collapse" id="navbarsExampleDefault" [ngbCollapse]="isCollapsed">
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
##### Add `isCollapsed` to  `votes/votes.component.ts` as a member field

```Typescript
  isCollapsed = true;
```

##### Add `BrowserAnimationsModule` to `app.module.ts`
```Typescript

import { BrowserAnimationsModule } from '@angular/platform-browser/animations';

...

imports:[
...
    BrowserAnimationsModule,
]
```

Your final app.module.ts should look like this:
```Typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent } from './app.component';
import { AppRoutingModule } from './app-routing.module';
import { NgbModule } from '@ng-bootstrap/ng-bootstrap';
import { VotesComponent } from './votes/votes.component';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';


@NgModule({
  declarations: [
    AppComponent,
    VotesComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    NgbModule,
    BrowserAnimationsModule,
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }


```


## 2. Create Questions

### 2.a Create question design
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

##### Fix the design
Add a top margin to body (src/styles.css), this will seperate the content from the navbar.
```
body {
 margin-top: 100px;
}
```

### 2.b Create Model
#### `QuestionOption` interface at `src/app/model/QuestionOption.ts`
```Typescript
export interface QuestionOption {
  label: string;
}
```

#### `Question` interface at `src/app/model/Question.ts`
```Typescript
import {QuestionOption} from './QuestionOption';

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

#### `Vote` interface at `src/app/model/Vote.ts`
```Typescript
export interface Vote {
  option: string;
  timeStamp: number;
}
```

#### Create Vote service with dummy data
_You can use cli:_
```bash
ng generate s votes
```

Or create manually in a new file at `services/vote.service.ts`

```Typescript

import {Injectable} from '@angular/core';
import {BehaviorSubject, Observable} from 'rxjs';
import {Question, QuestionEntity} from './model/Question';
import {Vote} from './model/Vote';

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
      },
      {
        id: '2',
        photoUrl: '',
        question: 'How are you again?',
        description: 'Just cheking...',
        created: Date.now(),
        options: [{label: 'good again'}, {label: 'still ehh'}]
      }
    ]);
  }

  async addQuestion(q: Question) {
    // TODO: implement
  }

  async vote(questionId: string, optionLabel: string) {
    // TODO: implement
  }

  async delete(questionId: string) {
    // TODO: implement
  }

  getVotes(questionId: string): Observable<Vote[]> {
    // TODO: implement
    return null;
  }
}

```
**Note:** Don't forget to add it to the app module at `app.module.ts`
```Typescript
import {VotesService} from './votes.service';
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
##### Update the UI at `votes/votes.component.html`
```HTML
<main role="main" class="container">
  <app-question [question]="question"
                *ngFor="let question of votesService.questions | async"></app-question>
</main><!-- /.container -->
```
**Note:** We are using the `async` pipe, since `votesService.questions` is an `Observervable<QuestionEntity>` type.

#### Update `votes/question/question.component.ts`
Add `question` as an input parameter of the component.

```Typescript
import {Component, Input, OnInit} from '@angular/core';
import {QuestionEntity} from '../../model/Question';
import {VotesService} from '../../votes.service';

@Component({
  selector: 'app-question',
  templateUrl: './question.component.html',
  styleUrls: ['./question.component.css']
})
export class QuestionComponent {

  selected: string;
  @Input() question: QuestionEntity;

  constructor(private votesService: VotesService) {
  }

  async vote() {
    if (!this.selected) {
      return;
    }
    await this.votesService.vote(this.question.id, this.selected);
    // TODO: add toast
  }

  async deleteQuestion() {
    // TODO: implement
  }

}
```
**Note:** You can see that we can now use the `[question]` tag in the `votes/votes.component.html` to provide an input to the component.

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

### Import angular:FormsModule at `app.module.ts`
We are using angular forms to disable the voting button if no radio button is selected
```Typescript
import {FormsModule} from '@angular/forms';
...
import:[
...
FormsModule
...
]
```


#### Add time transformation pipe
`votes/question/question.component.html` uses now the `timeAgo` pipe to convert the `created` timeStamp to readable string.
It is not a built in pipe, we need to implement it:

Create new pipe at `src/app/pipes/TimeAgoPipe.ts`
```Typescript
import {Pipe, PipeTransform} from '@angular/core';

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
**Note:** Don't forget to add it to the app module at `app.module.ts`
```Typescript
import {TimeAgoPipe} from './pipes/TimeAgoPipe';
...
declarations:[
TimeAgoPipe
]
```

### 2.d Add new Question form

#### Add a modal to `votes/votes.component.html` (insert this at the end of the file)
![Modal](assets/modal.jpg)

You can use a form generator to create bootstrap 4 forms: https://bootstrapformbuilder.com/
```HTML
<ng-template #addVoteTemplate>
  <div class="modal-header">
    <h4 class="modal-title pull-left">New Question Form</h4>
    <button type="button" class="close pull-right" aria-label="Close" (click)="modalRef.dismiss()">
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

      <div class="form-group row" *ngFor="let option of question.options; let i = index">
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
 * Create `question` and `options` data, that we can bind to in the html.

```Typescript
import {Component, TemplateRef} from '@angular/core';
import {VotesService} from '../votes.service';
import {NgbModal, NgbModalRef} from '@ng-bootstrap/ng-bootstrap';
import {Question} from '../model/Question';

@Component({
  selector: 'app-votes',
  templateUrl: './votes.component.html',
  styleUrls: ['./votes.component.css']
})
export class VotesComponent {

  modalRef: NgbModalRef;
  question: Question;
  isCollapsed = true;

  constructor(public votesService: VotesService,
              private modalService: NgbModal) {
  }

  openModal(template: TemplateRef<any>) {

    this.question = {
      question: '',
      created: Date.now(),
      photoUrl: '',
      description: '',
      options: []
    };
    this.addOption();
    this.addOption();
    this.modalRef = this.modalService.open(template);
  }

  async addQuestion() {
    this.modalRef.close();
    await this.votesService.addQuestion(this.question);
  }

  addOption() {
    this.question.options.push({label: ''});
  }
}

```

### 2.e Add Firebase

#### Data stucure

```
- questions:collection
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
import {AngularFireModule} from '@angular/fire';
import {AngularFirestoreModule} from '@angular/fire/firestore';
import {environment} from '../environments/environment';
...
imports: [
...
    AngularFireModule.initializeApp(environment.firebase),
    AngularFirestoreModule
]
```

#### Update votes service at `services/votes.service.ts`
```Typescript
import {Injectable} from '@angular/core';
import {BehaviorSubject, Observable} from 'rxjs';
import {Question, QuestionEntity} from './model/Question';
import {Vote} from './model/Vote';
import {AngularFirestore, AngularFirestoreCollection, DocumentChangeAction} from '@angular/fire/firestore';
import {map} from 'rxjs/operators';

@Injectable()
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
      },
      {
        id: '2',
        photoUrl: '',
        question: 'How are you again?',
        description: 'Just cheking...',
        created: Date.now(),
        options: [{label: 'good again'}, {label: 'still ehh'}]
      }
    ]);
  }

  async addQuestion(q: Question) {
    await this.questionCollection.add(q);
  }

  async vote(questionId: string, optionLabel: string) {
    await this.questionCollection.doc(questionId)
      .collection<Vote>('votes').add({timeStamp: Date.now(), option: optionLabel});
  }

  async delete(questionId: string) {
    // TODO: implement
    // do not forget to delete the votes subcollection manually
  }


  getVotes(questionId: string): Observable<Vote[]> {
    return this.questionCollection.doc(questionId).collection<Vote>('votes').valueChanges();
  }

}
```


## 3. Implement Question deletion

#### 3.a Add delete Button to every question in the same line with the description at `votes/question/question.component.html`
```HTML
	<div class="row">
      <div class="col-8">
        <p class="card-text">{{question.description}}</p>
      </div>
	  --------Copy between these----------
       <div class="col-4">
        <button type="button" (click)="deleteQuestion()" class="btn btn-outline-danger float-right">Delete</button>
        <a  [routerLink]="['/statistic', question.id]"
           class="btn btn-outline-primary float-right mr-2 ml-2">Statistic</a>
      </div>
	  --------Copy between these----------
    </div>
```

#### 3.b Implement `deleteQuestion()` function at `votes/question/question.component.ts`
#### 3.c Implement `delete(questionId: string)` function at `services/votes.service.ts`
**Note:** Firestore [does not delete subcollections automatically](https://firebase.google.com/docs/firestore/manage-data/delete-data), but you can [do it manually](https://github.com/angular/angularfire2/issues/1400).
**Hint:** Query the firestore document that has the id `questionId` and delete it.

## 4. Add Notifications
![Notification](assets/notification.jpg)
### 4.a Add notifications module

#### Install and save to `package.json`
We are using ngx-toastr: https://github.com/scttcper/ngx-toastr
```bash
npm install ngx-toastr --save
npm install @angular/animations --save
```
#### Add toaster module to `app.module.ts`
#### Also add the toaster stylesheet to `angular.json`, read more at: https://github.com/scttcper/ngx-toastr

#### **Note:** There is a current minor issue with the lib as the css style interfears with bootstrap
Read more here: https://github.com/scttcper/ngx-toastr/issues/602
#### Solution
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
### 5.b Add it to the app module
### 5.c Create a new `statistic` component 
#### Generate it
```bash
ng g c statistic
```
#### Add it to the routing so that it has an id parameter
```Typescript
const routes: Routes = [
  { path: '', redirectTo: '/votes', pathMatch: 'full' },
  { path: 'votes', component: VotesComponent },
  { path: 'statistic/:id', component: StatisticComponent }
];
```

### 5.d Design the `statistic` component 
![Notification](assets/statistic.jpg)
#### Use  [navigation](https://getbootstrap.com/docs/4.0/components/navbar/) bar 
#### Create a [bootstrap card](https://getbootstrap.com/docs/4.0/components/card/) for your plot

### 5.e Create statistic logic at `statistic/statistic.component.ts`
#### Use angular `ActivatedRoute` service to get the id from the active router
**Hint:** You need to `subscribe` to the `ActivatedRoute:params` observable parameters list.
#### Use `VoteService` to get votes
**Hint 1:** It is an Observable you need to `subscribe` to it.

**Hint 2:** You need to process the data to the appropriate format that plotlyJS requires.

**Hint 3:** On updating the model plotlyJs might not detect changes, trigger detection manually.
You can use this trick `this.graphData = JSON.parse(JSON.stringify(this.graphData));`


### 5.f Create a bar chart that shows the number of votes for each question


## Feedback: https://goo.gl/forms/PTYOdBNIB0MpSlzu1 

