# Dynamic Side Menu

Based on the following link:

- [Add Dynamic Side Menu in Ionic 5 App with Active Class](https://www.positronx.io/add-dynamic-side-menu-in-ionic-with-active-class/)

## Requirements

- Ionic 5
- Angular 7

## Create a Blank App

1. `ionic start ionic-side-menu-tabs blank --type=angular`

2. `cd ionic-side-menu-tabs`

3. `ionic serve`

## Generate Pages

1. `ionic generate page menu`
2. `ionic generate page login`
3. `ionic generate page contact`
4. `ionic generate page register`

## Set Up Routes for Side Menu

1. Open the app/app-routing.module.ts file and replace the existing code with the given below code:

    ```

    import { NgModule } from '@angular/core';
    import { PreloadAllModules, RouterModule, Routes } from '@angular/router';

    const routes: Routes = [
    {
        path: '',
        loadChildren: './menu/menu.module#MenuPageModule'
    }
    ];

    @NgModule({
    imports: [
        RouterModule.forRoot(routes, { preloadingStrategy: PreloadAllModules })
    ],
    exports: [RouterModule]
    })

    export class AppRoutingModule { }

    ```


2. Open the menu.module.ts page and replace with the following code:

    ```
    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { CommonModule } from '@angular/common';
    import { FormsModule } from '@angular/forms';
    import { IonicModule } from '@ionic/angular';

    import { MenuPage } from './menu.page';

    const routes: Routes = [
    {
        path: 'menu',
        component: MenuPage,
        children: [
        {
            path: 'home',
            loadChildren: '../home/home.module#HomePageModule'
        },
        {
            path: 'login',
            loadChildren: '../login/login.module#LoginPageModule'
        },
        {
            path: 'register',
            loadChildren: '../register/register.module#RegisterPageModule'
        },
        {
            path: 'contact',
            loadChildren: '../contact/contact.module#ContactPageModule'
        }
        ]
    },
    {
        path: '',
        redirectTo: '/menu/home'
    }
    ]

    @NgModule({
    imports: [
        CommonModule,
        FormsModule,
        IonicModule,
        RouterModule.forChild(routes)
    ],
    declarations: [MenuPage]
    })

    export class MenuPageModule { }

    ```

## Creating Dynamic Side Menu in Ionic

1. Open the menu.page.ts file, add the following code in it:

    ```

    import { Component, OnInit } from '@angular/core';
    import { Router, RouterEvent } from '@angular/router';

    @Component({
    selector: 'app-menu',
    templateUrl: './menu.page.html',
    styleUrls: ['./menu.page.scss'],
    })
    export class MenuPage implements OnInit {

    activePath = '';

    pages = [
        {
        name: 'Login',
        path: '/menu/login'
        },
        {
        name: 'Register',
        path: '/menu/register'
        },
        {
        name: 'Home',
        path: '/menu/home'
        },
        {
        name: 'Contact',
        path: '/menu/contact'
        }
    ]

    constructor(private router: Router) {
        this.router.events.subscribe((event: RouterEvent) => {
        this.activePath = event.url
        })
    }

    ngOnInit() {
    }
    }

    ```

2. Open menu.page.html and add the following code:

    ```
    <ion-app>
    <ion-menu contentId="main-content" side="start">
        <ion-header>
        <ion-toolbar>
            <ion-title>Menu</ion-title>
        </ion-toolbar>
        </ion-header>

        <ion-content>
        <ion-list>
            <ion-menu-toggle auto-hide="false" *ngFor="let page of pages">
            <ion-item [routerLink]="page.path" routerDirection="root" [class.active-menu]="activePath === page.path">
                {{page.name}}
            </ion-item>
            </ion-menu-toggle>
        </ion-list>
        </ion-content>
    </ion-menu>

    <ion-router-outlet id="main-content" main></ion-router-outlet>

    </ion-app>

    ```

3. Open home.page.html and insert the following code:


    ```
    <ion-header>
    <ion-toolbar>
        <ion-title>Home</ion-title>

        <!-- Side menu button -->
        <ion-buttons slot="start">
        <ion-menu-button autoHide="false"></ion-menu-button>
        </ion-buttons>

    </ion-toolbar>
    </ion-header>

    ```


## Authors

* **Catarina Silva** - [catarinaacsilva](https://github.com/catarinaacsilva)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details