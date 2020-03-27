# Making Http Requests in Angular

Form Making a http request in angular we need to import HttpClientModule from '@angular/common/http'

```typescript
import { HttpClientModule } from "@angular/common/http";
```

## Sending a POST Request

For sending a POST request we need to use post method.First we need to import HttpClient:

```typescript
import { HttpClient } from "@angular/common/http";
```

After that we need to hold it in variable into constructor function:

```typescript
constructor(private http: HttpClient) { }
```

### syntex for POST Request

```typescript
this.http
  .post("https://my-angular-project-64d75.firebaseio.com/posts.json", postData)
  .subscribe(responseData => {
    console.log(responseData);
  });
```

## GETing Data

By get method we can get data but we need to transform this data to show in the app.

### Using the Get method

```typescript
this.http.get(this.FirebaseUrl).subscribe(postData => {
  console.log(postData);
});
```

## Using RxJS Operators to Transform Response Data

After geting the data we need to transform the data by RxJS operators.
First we need to import map from rxjs/operators

```typescript
import { map } from "rxjs/operators";
```

```typescript
private fetchPosts(){
  this.http.get('https://my-angular-project-64d75.firebaseio.com/posts.json')
  .pipe(map((responseData:{[key:string]:})=>{
      const postsArray=[];
      for(const key in responseData){
        postsArray.push({...responseData[key],id:key});}
      return postsArray;
  }))
  .subscribe((postData)=>{
    console.log(postData)
  })
}
```

## Using Types with the HttpClient

For assigning type to responce data we have two methods. let's se one by one:

### Method 1

In the first method we can set type of response data in map function like below:

```typescript
private fetchPosts(){
  this.http.get('https://my-angular-project-64d75.firebaseio.com/posts.json')
  .pipe(map((responseData:{[key:string]:{title:string,content:string}})=>{
      const postsArray=[];
      for(const key in responseData){
        postsArray.push({...responseData[key],id:key})
        }
      return postsArray;
  }))
  .subscribe((posts)=>{
    console.log(posts)
  })
}

```

In This example we are using inline type assertion for assigning type of response data:

### Method 2

In this method we will use Post Model for asigning type of response data and <> bracts after http methods:

#### post.model.ts

```typescript
export interface Post {
  title: string;
  content: string;
  id?: string;
}
```

```typescript
private fetchPosts(){
  this.isLoading=true;
  this.http.get<{[key:string]:Post}>('https://my-angular-project-64d75.firebaseio.com/posts.json')
  .pipe(map((responseData)=>{

      const postsArray:Post[]=[];
      for(const key in responseData){
        postsArray.push({...responseData[key],id:key})
        }
      return postsArray;
  }))
  .subscribe((posts)=>{
    this.isLoading=false;
    this.LoadedPosts=posts
  })
}

```

## Outputing the Data

For Output the fatched data we need to hold all data array in a local array:

```typescript
LoadedPosts:Post[]=[];
private fetchPosts(){
  this.isLoading=true;
  this.http.get('https://my-angular-project-64d75.firebaseio.com/posts.json')
  .pipe(map((responseData:{[key:string]:Post})=>{

      const postsArray:Post[]=[];
      for(const key in responseData){
        postsArray.push({...responseData[key],id:key})
        }
      return postsArray;
  }))
  .subscribe((posts)=>{
    this.isLoading=false;
    this.LoadedPosts=posts
  })
}

```

Now we have our complate data in LoadedPosts array so we can use this accroding to requirment here we are going to show all data in list:

```html
<div class="row">
  <div class="col-xs-12 col-md-6 col-md-offset-3">
    <p *ngIf="isLoading">Data is Loading from server</p>
    <p *ngIf="LoadedPosts.length<1 && !isLoading">No posts available!</p>
    <ul *ngIf="LoadedPosts.length>=1" class="list-group">
      <li *ngFor="let post of LoadedPosts" class="list-group-item">
        <h3>{{post.title}}</h3>
        <p>{{post.content}}</p>
      </li>
    </ul>
  </div>
</div>
```

## Using Service for HTTP Request

let's convert http request into http service:

```typescript
import { Injectable } from "@angular/core";
@Injectable({ providedIn: "root" })
export class PostService {
  constructor(private http: HttpClient) {}
}
```

### Post Request in Service

In this service we are not using subscribe method. we will use subscribe in the component.

```typescript
createAndStorePost(title: string, content: string) {
    const postData: Post = { title: title, content: content };
    this.http.post<{ name: string }>('https://my-angular-project-64d75.firebaseio.com/posts.json',
      postData)
      .subscribe((responseData) => {
        console.log(responseData)
      })
  }
```

### Fatch Request in Service

In this service we are not using subscribe method. we will use subscribe in the component.

```typescript
  fatchPosts() {
    return this.http.get<{ [key: string]: Post }>('https://my-angular-project-64d75.firebaseio.com/posts.json')
      .pipe(map((responseData) => {

        const postsArray: Post[] = [];
        for (const key in responseData) {
          postsArray.push({ ...responseData[key], id: key })
        }
        return postsArray;
      }))
  }
```

### Delete Request in Service

In this service we are not using subscribe method. we will use subscribe in the component.

```typescript
clearPost() {
    return this.http.delete('https://my-angular-project-64d75.firebaseio.com/posts.json')

  }
```

### http-module.component.ts

```typescript
import { PostService } from "./posts.service";
import { Component, OnInit } from "@angular/core";
import { HttpClient } from "@angular/common/http";
import { map } from "rxjs/operators";
import { Post } from "./post.model";

@Component({
  selector: "app-http-module",
  templateUrl: "./http-module.component.html",
  styleUrls: ["./http-module.component.css"]
})
export class HttpModuleComponent implements OnInit {
  LoadedPosts: Post[] = [];
  posts: any[];
  isLoading = false;
  private FirebaseUrl =
    "https://my-angular-project-64d75.firebaseio.com/posts.json";
  constructor(private http: HttpClient, private postService: PostService) {}

  ngOnInit() {
    this.isLoading = true;
    this.postService.fatchPosts().subscribe(posts => {
      this.LoadedPosts = posts;
      this.isLoading = false;
    });
  }
  onCreatePost(postData: { title: string; content: string }) {
    this.postService.createAndStorePost(postData.title, postData.content);
    this.onFetchPosts();
  }
  onFetchPosts() {
    this.isLoading = true;
    this.postService.fatchPosts().subscribe(posts => {
      this.LoadedPosts = posts;
      this.isLoading = false;
    });
  }
  onClearPosts() {
    this.postService.clearPost().subscribe(postData => {
      this.LoadedPosts = [];
    });
  }
  log(x) {
    console.log(x);
  }
}
```

## Errors Handling Method One(When subscribe http request in component)

By passing second arrgument to subscribe function we can get errors.
For displaying error on the page we need to assign it to a variable.

```typescript
error = "";
this.postService.fatchPosts().subscribe(
  posts => {
    this.LoadedPosts = posts;
    this.isLoading = false;
  },
  error => {
    this.error = error.message;
    console.log(error);
  }
);
```

Display error in template:

```html
<div class="alert alert-danger">
  <h3>An Error occurred</h3>
  <p>{{ error }}</p>
</div>
```

## Subject for Errors Handling Method Two(When not using subscribe in component)

By Using Subject we can do error handing.Error handling by subject will be in two part.In the first part we wil work in service:

First we will import Subject and create a instance by holding it into a variable:

```typescript
import { Subject } from "rxjs";

export class PostService {
  error = new Subject<string>();
  constructor(private http: HttpClient) {}
  createAndStorePost(title: string, content: string) {
    const postData: Post = { title: title, content: content };
    this.http
      .post<{ name: string }>(
        "https://my-angular-project-64d75.firebaseio.com/posts.json",
        postData
      )
      .subscribe(
        responseData => {
          console.log(responseData);
        },
        error => {
          this.error.next(error.message);
        }
      );
  }
}
```

In the second part we will work in component. In the component we will subscribe error :

```typescript
this.postService.error.subscribe(errorMessage => {
  this.error = errorMessage;
});
```

After that we need to unsubscribe this error so for this we need to import Subscription:

```typescript
import { Subscription } from "rxjs";
```

after import we need to assign Subscription type to a variable and hold our error subscribe function into Subscription and we need to do unsubscribe in ngOnDestroy:

```typescript
export class HttpModuleComponent implements OnInit {
  private errorSub: Subscription;
  ngOnInit() {
    this.errorSub = this.postService.error.subscribe(errorMessage => {
      this.error = errorMessage;
    });
  }
  ngOnDestroy() {
    this.errorSub.unsubscribe;
  }
}
```
