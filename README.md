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