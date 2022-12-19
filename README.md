![ngx-multi-pixel header](https://repository-images.githubusercontent.com/315121543/bd67de80-2d1b-11eb-92b9-05272a615364)

<p align="center">
An Angular library to simplify tracking using a Facebook Pixel. It supports multiple pixel Ids.
</p>

---

# Introduction

Using a Facebook Pixel is fairly simple. You add the script to the `head` section of all pages, after which you can use the `fbq` function. However, in Angular it's not as straight-forward. The main two problems are as follows:

- Navigation using the Angular Router is not tracked.
- There are no TypeScript definitions by default, so no Intellisense or linting.

**_ngx-multi-pixel_** solves both of these issues. It adds a service that can be used in any component to track events and when enabled, it automatically tracks page views for each router navigation.

By default, **_ngx-multi-pixel_** is **disabled** to make it easier to comply with GDPR and other privacy regulations. The Facebook script is only loaded after **_ngx-multi-pixel_** is enabled, which also helps cut down the initial load time of your application. Read [here](#enabling) how to _enable_ and _disable_ **_ngx-multi-pixel_**.

---

# Usage

Using **_ngx-multi-pixel_** is very simple.

## 1. Installing the NPM package

You can install the NPM package with `npm install ngx-multi-pixel`

## 2. Add it to your app module

Add the library to your app's module, usually `app.module.ts`. Make sure you use the `forRoot()` method. Within this method, add your [Facebook Pixel ID](https://www.facebook.com/business/help/952192354843755).

```typescript
import { PixelModule } from "ngx-multi-pixel";

@NgModule({
  declarations: [AppComponent],
  imports: [
    BrowserModule,
    PixelModule.forRoot({ enabled: true, pixelId: ["YOUR_PIXEL_IDS"] }),
  ],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

**NOTE:** By default, the library does not start tracking immediately. In most cases this requires user consent to comply with GDPR and other privacy regulations. If you would still like start tracking as soon as your app module loads, include the `enabled: true` parameter in when you call `forRoot()`.

## 3. Add it to your components

In any component where you would like to track certain events, you can import the **_ngx-multi-pixel service_** with `import { PixelService } from 'ngx-multi-pixel';`
Then you can inject the service into your component as follows:

```TypeScript
export class HomeComponent {

  constructor(
    private pixel: PixelService
  ) { }

}
```

## 4. Tracking events

There are two groups of events that you can track, namely _Standard events_ and _Custom events_.

### Standard events

**Standard events** are common events that Facebook has created. You can find the full list [here](https://developers.facebook.com/docs/facebook-pixel/reference). You can track a Standard event like this:
![Track Standard events using ngx-multi-pixel](https://storage.googleapis.com/nielskersic/static-images/github/ngx-multi-pixel-track-large.gif)

The first argument is the type of event as defined by Facebook. The optional second argument can be used to pass more information about the event. E.g.:

```typescript
this.pixel.track("InitiateCheckout", {
  content_ids: ["ABC123", "XYZ456"], // Item SKUs
  value: 100, // Value of all items
  currency: "USD", // Currency of the value
});
```

### Custom events

Tracking **Custom events** works very similar to tracking Standard events. The only major difference is that there are no TypeScript interfaces and therefore no Intellisense. This is because the event _name_ and optional _properties_ can be anything. Tracking a custom event with **_ngx-multi-pixel_** looks like this:

```TypeScript
this.pixel.trackCustom('MyCustomEvent');
```

And just like with Standard events, you can add more properties. This is recommended, since this enables you to create even more specific audiences within Facebook Business Manager. Which properties you add is completely up to you. Here is an example:

```TypeScript
this.pixel.trackCustom('MyCustomEvent', {
  platform: 'limewire'
})
```

---

# Enabling and disabling **_ngx-multi-pixel_** <a name="enabling"></a>

**_ngx-multi-pixel_** is disabled by default. In many cases, tracking without user consent is not allowed by privacy regulations like the European GDPR. **_ngx-multi-pixel_** also doesn't inject the Facebook scripts until it is iniaitlized (upon consent), which helps cut down the initial loading size and time of your application.

## Enabling **_ngx-multi-pixel_** immediately

It is still possible to initialize **_ngx-multi-pixel_** as soon as your app module loads.
When adding **_ngx-multi-pixel_** to `app.module.ts`, add the parameter `enabled: true`.

```TypeScript
imports: [
  BrowserModule,
  PixelModule.forRoot({ enabled: true, pixelId: ["YOUR_PIXEL_IDS"]})
],
```

## Enabling **_ngx-multi-pixel_** from a component

You can also enable **_ngx-multi-pixel_** from within any of your components, like so:

```TypeScript
export class HomeComponent {

  constructor(
    private pixel: PixelService
  ) { }

  onConsent(): void {
    this.pixel.initialize();
    // this.pixel.initialize(['pixelId1', 'pixelId2',...]);
  }

}
```

## Enabling with a dynamic Pixel ID

In situations where the Pixel ID needs to be changed dynamically, this can be done using `initialize()` with the new Pixel ID as an optional argument.
**Notes:**

- A Pixel ID still needs to be provided when importing **_ngx-multi-pixel_** in the module.
- The previous instance should be removed with `remove()` before initializing a new Pixel ID.
- This approach should **not** be used in combination with serverside rendering (SSR). As the module is initialized on each request, the Pixel script will default to the ID provided in the module.

## Disabling **_ngx-multi-pixel_**

Disabling works very similar to _enabling_ from within a component and looks like this:

```TypeScript
export class HomeComponent {

  constructor(
    private pixel: PixelService
  ) { }

  onRevokeConsent(): void {
    this.pixel.remove();
  }

}
```

---

# Important notes

- Backwards compatibility is not guaranteed. **_ngx-multi-pixel_** was developed using Angular 11, which uses the Ivy compiler instead of the older View Engine compiler. If your project uses Angular 8 or earlier, or if you've decided to keep using View Engine with newer Angular versions, **_ngx-multi-pixel_** might not be compatible, although I have not yet tested this to confirm.

---

# What's next?

- [x] Adding Angular Universal SSR support.
- [ ] Adding tests.
- [ ] Removing all Facebook scripts on removal, not just the initial script.

---

Created with ❤️ by Abhinav Akhil, inspired from [niels.codes](https://niels.codes).
