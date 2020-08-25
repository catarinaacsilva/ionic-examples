# Login & Registration Form using Guard Working UI Example Application

Ionic 4/5 is using Angular Routing, so it becomes very easy to add authentication in an Ionic application using Auth Guards.

## Requirements

- ionic
- Install storage package using the following npm command: `npm install --save @ionic/storage`

## Project

1. `ionic start IonicAuthGaurds blank --type=angular`
2. `cd IonicAuthGaurds`
3. Generate the following pages
```
    $ ionic g page login
    $ ionic g page dashboard
    $ ionic g service services/authentication
    $ ionic g service services/authGuard
```
4. Next import services and Storage module in the app.module.ts file as shown below:

```
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { RouteReuseStrategy } from '@angular/router';

import { IonicModule, IonicRouteStrategy } from '@ionic/angular';
import { SplashScreen } from '@ionic-native/splash-screen/ngx';
import { StatusBar } from '@ionic-native/status-bar/ngx';

import { AppComponent } from './app.component';
import { AppRoutingModule } from './app-routing.module';
import { IonicStorageModule } from '@ionic/storage';
import { AuthGuard } from './services/AuthGuard.service';
import { AuthenticationService } from './services/Authentication.service';

@NgModule({
  declarations: [
    AppComponent
  ],
  entryComponents: [],
  imports: [
    BrowserModule, 
    IonicModule.forRoot(), 
    AppRoutingModule,
    IonicStorageModule.forRoot()
  ],
  providers: [
    StatusBar,
    SplashScreen,
    AuthGuard,
    AuthenticationService,
    { provide: RouteReuseStrategy, useClass: IonicRouteStrategy }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

5. In "~services/Authentication.service.ts" add the following code:

```
import { Injectable } from '@angular/core';
import { Router } from '@angular/router';
import { Storage } from '@ionic/storage';
import { ToastController, Platform } from '@ionic/angular';
import { BehaviorSubject } from 'rxjs';


@Injectable()
export class AuthenticationService {

  authState = new BehaviorSubject(false);

  constructor(
    private router: Router,
    private storage: Storage,
    private platform: Platform,
    public toastController: ToastController
  ) {
    this.platform.ready().then(() => {
      this.ifLoggedIn();
    });
  }

  ifLoggedIn() {
    this.storage.get('USER_INFO').then((response) => {
      if (response) {
        this.authState.next(true);
      }
    });
  }


  login() {
    var dummy_response = {
      user_id: '007',
      user_name: 'test'
    };
    this.storage.set('USER_INFO', dummy_response).then((response) => {
      this.router.navigate(['dashboard']);
      this.authState.next(true);
    });
  }

  logout() {
    this.storage.remove('USER_INFO').then(() => {
      this.router.navigate(['login']);
      this.authState.next(false);
    });
  }

  isAuthenticated() {
    return this.authState.value;
  }



}
```

6. Angular provides Guards to prevent navigation and loading of routes. To implement Guards we add **canActivate** parameter attribute on Routes in the app-routing.module.ts file (see this file on the project)

7.  In another service AuthGuard, we will implement **CanActivate** to convert this service into and an **Angular Guard**. Add the following code in "~services/AuthGuard.service.ts" file to be used in canActivate parameter in Routes:

```
import { Injectable } from '@angular/core';
import { CanActivate } from '@angular/router';
import { AuthenticationService } from './Authentication.service';


@Injectable()
export class AuthGuard implements CanActivate {
    constructor(
      public authenticationService: AuthenticationService
        ) {}

    canActivate(): boolean {
      return this.authenticationService.isAuthenticated();
    }

}
```

8. To check if the user is logged in on app load, we will call isAuthenticated() method from app.component.ts in the platform ready():

```
import { Component } from '@angular/core';

import { Platform } from '@ionic/angular';
import { SplashScreen } from '@ionic-native/splash-screen/ngx';
import { StatusBar } from '@ionic-native/status-bar/ngx';
import { Router } from '@angular/router';
import { AuthenticationService } from './services/Authentication.service';

@Component({
  selector: 'app-root',
  templateUrl: 'app.component.html'
})
export class AppComponent {
  constructor(        
    private router: Router,
    private platform: Platform,
    private splashScreen: SplashScreen,
    private statusBar: StatusBar,
    private authenticationService: AuthenticationService
  ) {
    this.initializeApp();
  }

  initializeApp() {
    this.platform.ready().then(() => {
      this.statusBar.styleDefault();
      this.splashScreen.hide();


      this.authenticationService.authState.subscribe(state => {
        if (state) {
          this.router.navigate(['dashboard']);
        } else {
          this.router.navigate(['login']);
        }
      });

    });
  }
}
```

9. On the login page, we will have a Login button which will add dummy user values in Session. There is also a "Go to dashboard" link to demonstrate CanActive Guard usage. On this link, we are simply redirecting to dashboard page, but a user is not authenticated yet by clicking on Login so it will alert the user by an alert. On the Dashboard, we will have a logout button to clear authentication state and move back to login.

Login HTML and Component code

login.page.html

```
 <ion-button (click)="loginUser()">
    login
  </ion-button>

  <a [routerLink]="['/dashboard']">Go to dashboard</a>
```

login.page.ts

```

import { Component, OnInit } from '@angular/core';
import { AuthenticationService } from '../services/Authentication.service';

@Component({
  selector: 'app-login',
  templateUrl: './login.page.html',
  styleUrls: ['./login.page.scss'],
})
export class LoginPage implements OnInit {

  constructor(
    private authService: AuthenticationService
     ) { }

  ngOnInit() {
  }

  loginUser(){
    this.authService.login()
  }

}


```

10. Similarly, on the Dashboard page in HTML template, we have a logout button ad in a component we will call logout() method from AuthenticationService

dashboard.page.html

```

<ion-content padding>
    <ion-button (click)="logoutUser()">
        Logout
      </ion-button>
</ion-content>

```

dashboard.page.ts

```
import { Component, OnInit } from '@angular/core';
import { AuthenticationService } from '../services/Authentication.service';

@Component({
  selector: 'app-dashboard',
  templateUrl: './dashboard.page.html',
  styleUrls: ['./dashboard.page.scss'],
})
export class DashboardPage implements OnInit {

  constructor(
    private authService: AuthenticationService
    ) { }

  ngOnInit() {
  }

  logoutUser(){
    this.authService.logout();
  }

}


```

11. That's it now you can run the application to check Login flow using Angular Guard on dashboard link.

## Authors

* **Catarina Silva** - [catarinaacsilva](https://github.com/catarinaacsilva)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details