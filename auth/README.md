# Ionic 5 Forms and Authentication Tutorial: Login & Register 

Based on the following links:

- [Ionic 5 Forms Tutorial: Login & Register UI Example with Theming](https://www.techiediaries.com/ionic-ui-forms-theming/)


- [Ionic 5/Angular JWT Authentication Tutorial: Node & Express.js Server](https://www.techiediaries.com/ionic/ionic-5-jwt-authentication-node-expressjs/)

## Requirements

- Ionic 5
- Angular 7

## Creating an Ionic 5 Project

1. `ionic start ionic-auth-demo blank --type=angular`

2. Wait for the dependencies to be installed then navigate in your project's root folder and serve your application using these commands:

    2.1 `cd ionic-auth-demo`
    2.2 `ionic serve`

## Creating an Angular Module

1. `ionic generate module auth`

2. Update src/app/auth/auth.module.ts file with the following code:

    ```
    import { NgModule } from '@angular/core';
    import { CommonModule } from '@angular/common';
    import { HttpClientModule } from '@angular/common/http';
    import { FormsModule } from '@angular/forms';

    @NgModule({
    declarations: [],
    imports: [
        CommonModule,
        HttpClientModule,
        FormsModule
    ]
    })
    export class AuthModule { }

    ```

3. Setting up Ionic Storage Module:

    `npm install --save @ionic/storage`

4. Update src/app/auth/auth.module.ts file to include IonicStorageModule.forRoot():

    ```
    import { NgModule } from '@angular/core';
    import { CommonModule } from '@angular/common';
    import { HttpClientModule } from '@angular/common/http';
    import { FormsModule } from '@angular/forms';

    import { IonicStorageModule } from '@ionic/storage';

    @NgModule({
    declarations: [],
    imports: [
        CommonModule,
        HttpClientModule,
        FormsModule,
        IonicStorageModule.forRoot()
    ]
    })
    export class AuthModule { }
    ```

## Creating the Angular Authentication Service

1. `ionic generate interface auth/user`

2. This will generate a user interface in the src/app/auth/user.ts file. Open the file and update it accordingly:

    ```
    export interface User {
        id: number;
        name: string;
        email: string;
        password: string;
    }
    ```

3. Generate an interface for the server response:

    `ionic generate interface auth/auth-response`

4. Open the src/app/auth/auth-response.ts file and update it accordingly:

    ```
    export interface AuthResponse {
        user: {
            id: number,
            name: string,
            email: string,
            access_token: string,
            expires_in: number
        }
    }
    ```

5. Generate the service using: (Two src/app/auth/auth.service.ts and src/app/auth/auth.service.spec.ts (tests) files will be generated.)

    `ionic generate service auth/auth`

6. Update src/app/auth/auth.service.ts file with the following code:

```
import { Injectable } from  '@angular/core';
import { HttpClient } from  '@angular/common/http';
import { tap } from  'rxjs/operators';
import { Observable, BehaviorSubject } from  'rxjs';

import { Storage } from  '@ionic/storage';
import { User } from  './user';
import { AuthResponse } from  './auth-response';


@Injectable({
  providedIn: 'root'
})
export class AuthService {

  AUTH_SERVER_ADDRESS:  string  =  'http://localhost:3000';
  authSubject  =  new  BehaviorSubject(false);

  //constructor() { }
  constructor(private  httpClient:  HttpClient, private  storage:  Storage) { }

  register(user: User): Observable<AuthResponse> {
    return this.httpClient.post<AuthResponse>(`${this.AUTH_SERVER_ADDRESS}/register`, user).pipe(
      tap(async (res:  AuthResponse ) => {

        if (res.user) {
          await this.storage.set("ACCESS_TOKEN", res.user.access_token);
          await this.storage.set("EXPIRES_IN", res.user.expires_in);
          this.authSubject.next(true);
        }
      })

    );
  }

  login(user: User): Observable<AuthResponse> {
    return this.httpClient.post(`${this.AUTH_SERVER_ADDRESS}/login`, user).pipe(
      tap(async (res: AuthResponse) => {

        if (res.user) {
          await this.storage.set("ACCESS_TOKEN", res.user.access_token);
          await this.storage.set("EXPIRES_IN", res.user.expires_in);
          this.authSubject.next(true);
        }
      })
    );
  }

  async logout() {
    await this.storage.remove("ACCESS_TOKEN");
    await this.storage.remove("EXPIRES_IN");
    this.authSubject.next(false);
  }

  isLoggedIn() {
    return this.authSubject.asObservable();
  }

}

```

## Creating the Register Page

1. `ionic generate page auth/register`

2. Update the src/app/app-routing.module.tsfile to enable routing to this page by adding the following route: 

    `path:  'register', loadChildren:  './auth/register/register.module#RegisterPageModule' }`

3. Open the src/app/auth/register/register.page.ts file and update with:

    ```
        import { Component, OnInit } from '@angular/core';
        import { Router } from  "@angular/router";
        import { AuthService } from '../auth.service';

        @Component({
        selector: 'app-register',
        templateUrl: './register.page.html',
        styleUrls: ['./register.page.scss'],
        })
        export class RegisterPage implements OnInit {

        constructor(private  authService:  AuthService, private  router:  Router) { }

        ngOnInit() {
        }

        register(form) {
            this.authService.register(form.value).subscribe((res) => {
                this.router.navigateByUrl('home');
            });
        }

        }
    ```

4. Open the src/auth/register/register.page.html file and add a form inside `<ion-content>`:

    ```
        <ion-content  color="primary">
        <form  #form="ngForm" (ngSubmit)="register(form)">
            <ion-grid>
            <ion-row color="primary" justify-content-center>
                <ion-col align-self-center size-md="6" size-lg="5" size-xs="12">
                <div text-center>
                    <h3>Create your account!</h3>
                </div>
                <div padding>
                    <ion-item>
                    <ion-input  name="name" type="text" placeholder="Name" ngModel required></ion-input>
                    </ion-item>
                    <ion-item>
                    <ion-input name="email" type="email" placeholder="your@email.com" ngModel required></ion-input>
                    </ion-item>
                    <ion-item>
                    <ion-input name="password" type="password" placeholder="Password" ngModel required></ion-input>
                    </ion-item>
                    <ion-item>
                    <ion-input name="confirm" type="password" placeholder="Password again" ngModel required></ion-input>
                    </ion-item>
                </div>
                <div padding>
                    <ion-button  size="large" type="submit" [disabled]="form.invalid" expand="block">Register</ion-button>
                </div>
                </ion-col>
            </ion-row>
            </ion-grid>
        </form>
        </ion-content>
    ```

5.  Open the src/auth/register/register.page.scss file and add:

    ``` 
        ion-item{
            --background: #3880ff;
            --color: #fff;
        }

        ion-button{
            --background: #062f77;
        }

    ```

## Creating a Login Page

1. `ionic generate page auth/login`

2. The src/app/app-routing.module.ts will be updated with the following route:

    `{ path:  'login', loadChildren:  './auth/login/login.module#LoginPageModule' },`

3. Open the src/app/auth/login/login.page.ts file and update with:

    ``` 
        import { Component, OnInit } from '@angular/core';
        import { Router } from  "@angular/router";
        import { AuthService } from '../auth.service';

        @Component({
        selector: 'app-login',
        templateUrl: './login.page.html',
        styleUrls: ['./login.page.scss'],
        })
        export class LoginPage implements OnInit {

            constructor(private  authService:  AuthService, private  router:  Router) { }

            ngOnInit() {
            }

            login(form){
                this.authService.login(form.value).subscribe((res)=>{
                    this.router.navigateByUrl('home');
                });
            }

        } 

    ```

4. Open the src/app/auth/login/login.page.html file and add the following code:

    ```
        <ion-content color="primary" padding>
            <form #form="ngForm" (ngSubmit)="login(form)">
                <ion-grid>
                <ion-row color="primary" justify-content-center>
                    <ion-col align-self-center size-md="6" size-lg="5" size-xs="12">
                    <div text-center>
                        <h3>Login</h3>
                    </div>
                    <div padding>
                        <ion-item>
                        <ion-input name="email" type="email" placeholder="your@email.com" ngModel required></ion-input>
                        </ion-item>
                        <ion-item>
                        <ion-input name="password" type="password" placeholder="Password" ngModel required></ion-input>
                        </ion-item>
                    </div>
                    <div padding>
                        <ion-button size="large" type="submit" [disabled]="form.invalid" expand="block">Login</ion-button>
                    </div>
                    </ion-col>
                </ion-row>
                 <ion-row>
                    <div text-center>
                    If you don't have an account, please <a routerLink='/register'>
                    register</a> first!
                    </div>
                </ion-row>
                </ion-grid>
            </form>
        </ion-content>

    ``` 

5. Open the src/app/auth/login/login.page.scss file and add these styles:

    ```
        ion-item{
            --background: #3880ff;
            --color: #fff;
        }
        ion-button{
            --background: #062f77;
        }
        a{
            color: #fff;
        }

    ```

## Creating and Running an Express.js Authentication Server

1. First, create a folder for the server code:
    
    `mkdir express-auth-demo`

2. Navigate in the folder and create a package.json file:

    `npm init -y`

3. Next, install the following dependencies:

    `npm install --save express body-parser sqlite3 bcryptjs jsonwebtoken cors`

4. Next, create an index.js file and add the following code:

    ```
    "use strict";
    const  express  =  require('express');
    const  bodyParser  =  require('body-parser');
    const cors = require('cors')
    const  sqlite3  =  require('sqlite3').verbose();
    const  jwt  =  require('jsonwebtoken');
    const  bcrypt  =  require('bcryptjs');

    const SECRET_KEY = "secretkey23456";

    const  app  =  express();
    const  router  =  express.Router();
    app.use(cors())

    router.use(bodyParser.urlencoded({ extended:  false }));
    router.use(bodyParser.json());
    const database = new sqlite3.Database("./my.db");

    const  createUsersTable  = () => {
        const  sqlQuery  =  `
            CREATE TABLE IF NOT EXISTS users (
            id integer PRIMARY KEY,
            name text,
            email text UNIQUE,
            password text)`;

        return  database.run(sqlQuery);
    }

    const  findUserByEmail  = (email, cb) => {
        return  database.get(`SELECT * FROM users WHERE email = ?`,[email], (err, row) => {
                cb(err, row)
        });
    }

    const  createUser  = (user, cb) => {
        return  database.run('INSERT INTO users (name, email, password) VALUES (?,?,?)',user, (err) => {
            cb(err)
        });
    }

    createUsersTable();

    router.get('/', (req, res) => {
        res.status(200).send('This is an authentication server');
    });

    router.post('/register', (req, res) => {

        const  name  =  req.body.name;
        const  email  =  req.body.email;
        console.log(req.body);
        const  password  =  bcrypt.hashSync(req.body.password);

        createUser([name, email, password], (err)=>{
            if(err) return  res.status(500).send("Server error!");
            findUserByEmail(email, (err, user)=>{
                if (err) return  res.status(500).send('Server error!');  
                const  expiresIn  =  24  *  60  *  60;
                const  accessToken  =  jwt.sign({ id:  user.id }, SECRET_KEY, {
                    expiresIn:  expiresIn
                });
                res.status(200).send({ "user":  user, "access_token":  accessToken, "expires_in":  expiresIn          
                });
            });
        });
    });


    router.post('/login', (req, res) => {
        const  email  =  req.body.email;
        const  password  =  req.body.password;
        findUserByEmail(email, (err, user)=>{
            if (err) return  res.status(500).send('Server error!');
            if (!user) return  res.status(404).send('User not found!');
            const  result  =  bcrypt.compareSync(password, user.password);
            if(!result) return  res.status(401).send('Password not valid!');

            const  expiresIn  =  24  *  60  *  60;
            const  accessToken  =  jwt.sign({ id:  user.id }, SECRET_KEY, {
                expiresIn:  expiresIn
            });
            res.status(200).send({ "user":  user, "access_token":  accessToken, "expires_in":  expiresIn});
        });
    });

    app.use(router);
    const  port  =  process.env.PORT  ||  3000;
    const  server  =  app.listen(port, () => {
        console.log('Server listening at http://localhost:'  +  port);
    }); 
    ```

5. Next, add a start script in package.json:

    ```
    "scripts": {
        "start": "node index.js"
    },
    ```
6. You can now, run your authentication server using:

    `npm start`

## Test

There are 2 option to test the functionality:

1. Use interface

2. See on terminal:

    2.1 `curl -H "Content-Type: application/json" -d '{"email":"test@test.com","name":"test", "password":"test"}' http://localhost:3000/register`

    2.2 `curl -H "Content-Type: application/json" -d '{"email":"test@test.com", "password":"test"}' http://localhost:3000/login`


## Authors

* **Catarina Silva** - [catarinaacsilva](https://github.com/catarinaacsilva)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details