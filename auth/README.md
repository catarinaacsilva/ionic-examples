# Ionic 5 Forms and Authentication Tutorial: Login & Register 

Based on the following links:

- [Ionic 5 Forms Tutorial: Login & Register UI Example with Theming](https://www.techiediaries.com/ionic-ui-forms-theming/)


- [Ionic 5/Angular JWT Authentication Tutorial: Node & Express.js Server](https://www.techiediaries.com/ionic/ionic-5-jwt-authentication-node-expressjs/)

## Requirements

- Ionic 5
- Angular 7


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

## Authors

* **Catarina Silva** - [catarinaacsilva](https://github.com/catarinaacsilva)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details