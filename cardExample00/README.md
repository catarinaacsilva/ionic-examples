# Card component Example

Based on the following link: 

- [card](https://www.positronx.io/ionic-card-component-example/)


## Requirements

- Ionic 5

## Create Ionic 5 Application

1. `ionic start ionic-card-example blank --type=angular`

## Full card example

1. Update home.page.html page with the following code:

```
<ion-card>
  <ion-item>
    <ion-avatar slot="start">
      <img src="../../assets/user.jpg">
    </ion-avatar>
    <ion-label>
      <h3>John Doe</h3>
      <p>Dec 10, 2019</p>
    </ion-label>
  </ion-item>

  <img src="../../assets/hotel.jpeg" alt="ion">
  <ion-card-header>
    <ion-card-title>Hotel Panama Garden</ion-card-title>
  </ion-card-header>
  <ion-card-content>
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Phasellus iaculis mollis ligula sed ultrices.
  </ion-card-content>

  <ion-footer>
    <ion-row>
      <ion-col center text-center>
        <button>
          <ion-icon name="thumbs-up"></ion-icon>
          <div>1.5k Likes</div>
        </button>
      </ion-col>
      <ion-col center text-center>
        <button>
          <ion-icon name="text"></ion-icon>
          <div>4 Comments</div>
        </button>
      </ion-col>
      <ion-col center text-center>
        <button>
          <ion-icon name="time"></ion-icon>
          <div>11h ago</div>
        </button>
      </ion-col>
    </ion-row>
  </ion-footer>
</ion-card>

```

2. Test in browser: `ionic serve`

## Authors

* **Catarina Silva** - [catarinaacsilva](https://github.com/catarinaacsilva)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details