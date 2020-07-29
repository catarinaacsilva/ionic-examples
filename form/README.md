# Form example

Based on the following link:

- [form](https://www.positronx.io/ionic-form-validation-tutorial/)


## Requirements

- ionic 5

## Steps

1. `ionic start ionic-form-validation blank --type=angular`

2. Open home.page.html and update with the following code:

```
<ion-header>
  <ion-toolbar>
    <ion-title>Ionic Forms</ion-title>
  </ion-toolbar>
</ion-header>

<ion-content>
  <form [formGroup]="ionicForm" (ngSubmit)="submitForm()" novalidate>

    <ion-item lines="full">
      <ion-label position="floating">Name</ion-label>
      <ion-input formControlName="name" type="text"></ion-input>
    </ion-item>
    <span class="error ion-padding" *ngIf="isSubmitted && errorControl.name.errors?.required">
      Name is required.
    </span>
    <span class="error ion-padding" *ngIf="isSubmitted && errorControl.name.errors?.minlength">
      Name should be min 2 chars long.
    </span>

    <ion-item lines="full">
      <ion-label position="floating">Email</ion-label>
      <ion-input formControlName="email" type="email"></ion-input>
    </ion-item>
    <span class="error ion-padding" *ngIf="isSubmitted && errorControl.email.errors?.required">
      Email is required.
    </span>
    <span class="error ion-padding" *ngIf="isSubmitted && errorControl.email.errors?.pattern">
      Please provide valid email id.
    </span>

    <ion-item lines="full">
      <ion-label position="floating">DOB</ion-label>
      <ion-datetime (ionChange)="getDate($event)" formControlName="dob" [value]="defaultDate"></ion-datetime>
    </ion-item>

    <ion-item lines="full">
      <ion-label position="floating">Mobile</ion-label>
      <ion-input maxlength="10" formControlName="mobile" type="text" required></ion-input>
    </ion-item>
    <span class="error ion-padding" *ngIf="isSubmitted && errorControl.mobile.errors?.required">
      Mobile number is required.
    </span>
    <span class="error ion-padding" *ngIf="isSubmitted && errorControl.mobile.errors?.pattern">
      Only numerical values allowed.
    </span>

    <ion-row>
      <ion-col>
        <ion-button type="submit" color="danger" expand="block">Submit</ion-button>
      </ion-col>
    </ion-row>
  </form>
</ion-content>

```


3. Open home.module.ts and update with this code:


```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { IonicModule } from '@ionic/angular';
import { FormsModule, ReactiveFormsModule } from '@angular/forms';
import { RouterModule } from '@angular/router';

import { HomePage } from './home.page';

@NgModule({
  imports: [
    CommonModule,
    ReactiveFormsModule,
    FormsModule,
    IonicModule,
    RouterModule.forChild([
      {
        path: '',
        component: HomePage
      }
    ])
  ],
  declarations: [HomePage]
})

export class HomePageModule { }

```


4. Open home.page.ts and update with the following code:


```
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormBuilder, Validators } from "@angular/forms";

@Component({
  selector: 'app-home',
  templateUrl: 'home.page.html',
  styleUrls: ['home.page.scss'],
})

export class HomePage implements OnInit {
  ionicForm: FormGroup;
  defaultDate = "1987-06-30";
  isSubmitted = false;

  constructor(public formBuilder: FormBuilder) { }

  ngOnInit() {
    this.ionicForm = this.formBuilder.group({
      name: ['', [Validators.required, Validators.minLength(2)]],
      email: ['', [Validators.required, Validators.pattern('[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,3}$')]],
      dob: [this.defaultDate],
      mobile: ['', [Validators.required, Validators.pattern('^[0-9]+$')]]
    })
  }

  getDate(e) {
    let date = new Date(e.target.value).toISOString().substring(0, 10);
    this.ionicForm.get('dob').setValue(date, {
      onlyself: true
    })
  }

  get errorControl() {
    return this.ionicForm.controls;
  }

  submitForm() {
    this.isSubmitted = true;
    if (!this.ionicForm.valid) {
      console.log('Please provide all the required values!')
      return false;
    } else {
      console.log(this.ionicForm.value)
    }
  }
}

```

## Authors

* **Catarina Silva** - [catarinaacsilva](https://github.com/catarinaacsilva)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details