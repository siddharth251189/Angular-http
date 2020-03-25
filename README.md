# Making Http Requests in Angular
 
Form Making a http request in angular we need to import HttpClientModule from '@angular/common/http'

```typescript
import { HttpClientModule } from '@angular/common/http';
```

## Sending a POST Request
For sending a POST request we need to use post method.First we need to import HttpClient:

```typescript
import { HttpClient } from '@angular/common/http';
```
After that we need to hold it in variable into constructor function:

```typescript
constructor(private http: HttpClient) { }
```
### syntex for POST Request

```typescript
this.http.post('https://my-angular-project-64d75.firebaseio.com/posts.json',postData)
    .subscribe((responseData)=>{
      console.log(responseData)
    })
```

## GETing Data
By get method we can get data but we need to transform this data to show in the app.

### Using the Get method

```typescript
this.http.get(this.FirebaseUrl)
  .subscribe((postData)=>{
    console.log(postData)
  })
```

## Using RxJS Operators to Transform Response Data
After geting the data we need to transform the data by RxJS operators.
First we need to import map from rxjs/operators
```typescript
import { map } from 'rxjs/operators';
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
For assigning type to responce data we have two methods. let's se  one by one:


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
In this method we will use Post Model ssigning type of response data:

#### post.model.ts

```typescript
export interface Post{
    title:string;
    content:string;
    id?:string
}

```

```typescript
private fetchPosts(){
  this.http.get('https://my-angular-project-64d75.firebaseio.com/posts.json')
  .pipe(map((responseData:{[key:string]:Post})=>{
      const postsArray:Post[]=[];
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

