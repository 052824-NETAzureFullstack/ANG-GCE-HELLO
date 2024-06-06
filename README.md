# ANG-GCE-HELLO
ANG-GCE-HELLO is a simple Angular project we can use to explore the basics of the framework. We will look at the important files that make up a boilerplate NG project, and explore Components, Decorators, and how it all comes together as a single page application.

If you clone this project and want to run it yourself, remember to get the node dependencies after cloning with the CLI command: `npm install`.

### Version Information
|  |  |
| ----------- | ------- |
| Angular CLI | 17.0.7  |
| Node        | 20.12.0 |
| npm         | 10.5.0  |


## Bootstrapping
Angular projects are single page applications, all the files in a project are bundled together at build-time. The bundle is sent to the browser, and the SPA is rendered. Bootstrapping refers to how each individual file comes together in the bundle, and how the browser executes the steps to show you the site.

Let's start with `index.html` and `main.ts`. Both of these files can be found under the `src/` folder. These files are generated automatically when you create a new angular project, and this is where the bootstrapping process begins.

`index.html` is a complete HTML document (but does not contain the HTML of the entire project). Take a look at the `<body>` section:

```HTML
<body>
  <app-root></app-root>
</body>
```

HTML is made up of tags which describe elements. The HTML document will be used to construct a DOM, which is the tree structure the HTML describes. Browsers interpret HTML and create a DOM. The only thing in the body of our project is `<app-root></app-root>` which are a pair of opening and closing tags representing the `App` component. In a moment we will go look at the files which make up that component, which include more HTML. In this way we are building up a larger DOM tree out of smaller sub-trees. `app-root` represents a sub-tree, more HTML nested within this element.

Next let's consider `main.ts`:
```JS
bootstrapApplication(AppComponent, appConfig)
  .catch((err) => console.error(err));
```
There are two main pillars to an Angular project, the HTML views and the TypeScript behaviors. The HTML "templates" or "views" tell the browser where on the screen elements should be shown, the TypeScript behaviors tell the browser how to act. This code above invokes the angular `bootstrapApplication()` method, and passes it two arguments:

 - `AppComponent` which is a typescript class, this is the TypeScript code that goes with the `<app-component>` view.
 - `appConfig` which is a simple object containing configurations. We will discuss what this is in another demo.

And we're off! We have a bare bones HTML document which refers to other documents containing more HTML, and we have corresponding TypeScript classes to go with the nested HTML. These two things, an HTML template (or view) and a corresponding TypeScript class make up something called a "component" which is the building block of Angular projects.

## Components
Components are the basic unit of an Angular SPA. Each component is made up of a view ("template" and "view" mean the same thing and will be used interchangably) and a class. The view represents how the page should be rendered by the browser, and the class holds the TypeScript behaviors for the component. Let's take a look at the App component. We can find this in the `src/app/` folder. There are two more components within that folder, `child-one` and `child-two`, we will explore these additional components soon.

Because we are looking at the root component, that is the one which is bootstrapped inside `main.ts`, there are some other files here including `app.config.ts` and `app.routes.ts`. The first are the configurations we mentioned above, and the second is for routing which will be discussed in another demo. The other 4 files in `src/app/` make up the app component:
 - `app.component.html` - The app component view
 - `app.component.ts` - The app component TypeScript class
 - `app.component.css` - The app component stylesheet, this is just external CSS
 - `app.component.spec.ts` - The app component unit tests (We will be ignoring this for now)

Technically, only the TypeScript class file is necessary but we will often see components split into several files like these. When we generate new components with the CLI we get these 4 files. 

### Templates / Views
Let's first take a look at the HTML files. They're very basic, only containing a single paragraph element with some text. Any HTML we place here will be "inlined" when we build the project. This means that, once finally built, all of the HTML will be combined into a single document, which is used to build a single DOM. You can explore this by launching your SPA with the following CLI command: `ng serve --open`. This will build your project and serve it up from a temporary webserver. The `--open` flag tells your machine to open the page in your default browser. Once there, open the browser's dev tools (often with the F12 key) and you can explore the HTML. You'll find a structure like this:

```
html
|
---app-root
   |
   ---div
      |
      ---app-child-one
      |  |
      |  ---p
      |
      ---app-child-two
         |
         ---p
```
The HTML in the root component, as well as the two children are all combined together into a single document. The browser receives this document and renders it all together on the screen.

### Component Classes
Next let's take a look at the TypeScript files that control the behaviors of the components. This simple project doesn't have much written here. Each component simply barks out to the console when it is constructed. If you open your SPA in the browser and take a look at the browser console, you will see these log messages. Later we will be putting more code here in the class, but this is enough for now. The last thing to consider are decorators.

### Decorators
Decorators are syntax prepended to some other syntax which will tell Angular how to behave. This is similar to annotations in Java, though there are some differences. Take a look at the `@Component` decorator in the `app.component.ts` file. This indicates that this TypeScript class is part of a component, and tells Angular about some things it needs to properly build our project. The object inside the decorator's parameter list contains the following properties:
 - `selector` indicates the name of the HTML tag which places this component in the DOM
 - `standalone` indicates if this is a standalone component, meaning it is not part of a module (this is a new v17 feature)
 - `imports` contains an array of dependencies necessary to build this component
 - `templateUrl` references this component's template/view file
 - `styleUrl` references this component's external stylesheet

There are other properties which may be present on other components. Earlier we said that technically the TS file is the only file needed to describe a component. Instead of referencing other files with `tempalteUrl` and `styleUrl` you will sometimes see people place the contents of those files here. Below is an example of the `app-root` component written in a single file:
```TypeScript
@Component({
  selector: 'app-root',
  standalone: true,
  imports: [CommonModule, ChildOneComponent, ChildTwoComponent],
  template: `
    <h2>This is the App component</h2>
    <p>This is the root component of this Angular SPA. Check the developer tools 
    in your browser and you can explore the HTML. You'll find this HTML nested within the 
    app-component tag which appears within the body tag. Below are two child components of
    this one, which should be rendered side-by-side thanks to the styling found 
    in app.component.css.</p>
    <hr>
    <div class="flexContainer">
        <app-child-one class="flexItem"></app-child-one>
        <app-child-two class="flexItem"></app-child-two>
    </div>`,
  styles: `
    .flexContainer {
      display: flex;
      flex-direction: row;
      flex-wrap: nowrap;
      justify-content: center;   
    }

    .flexItem {
      flex: 0 0 30%;  /* flex-grow, flex-shrink, flex-basis */
      border: 2px;
      border-style: solid;
      margin: 20px;
      padding: 10px;
    }`
})
export class AppComponent {
  constructor () {
    console.log("This is the root component, AppComponent");
  }
}
```
Note how we have a `template` property instead of `templateUrl` and that this property contains the same code that was referenced previously in the other file. The same is true of the `styles` property replacing `styleUrl`.

Next take a look at the `imports` property. We can ignore `CommonModule` for now. The other two elements in this array are the TypeScript classes for `ChildOneComponent` and `ChildTwoComponent` These are both referenced in the HTML by their selector or tag name. This continues the bootstrapping process we saw earlier. Recall that the `<app-root>` element is referenced in `index.html` and the `AppComponent` class is referenced in `main.ts`. From here in `app.component.ts` we reference the other classes in the `imports` array, and we place the corresponding tags in the HTML. 

We can place other things in this array as well. Many of the dependencies a component needs will be "provided" in this way. There are other methods to provide dependencies in Angular which will be discussed later.

### JavaScript Modules vs. Angular Modules
Note that we have an `imports` array which contains dependencies for our components, but at the top of the file we also see several `import` statements. Those at the top are part of JavaScript/TypeScript modules, they basically link files together to share code between them. This is necessary here but shouldn't confused with the Angular specific syntax found in the `@Component` decorator. There are also the concept of modules in Angular, which shouldn't be confused with JavaScript/TypeScript modules. The `import` statement often found at the top of JS/TS files are used to link files together. Angular `imports` array is used to tell Angular to provide dependencies.

## Bundling
The last thing to look at is the bundle, the thing angular builds out of our project. If you look at the console after executing the `ng serve` command, you may see mention of some files generated like `polyfill.js` and `main.js`. This is the bundle. When you `build` or `serve` your project this is what gets produced and given to the browser. When using `serve` we get a temporary bundle. If you run the command `ng build` you will see a new folder get created which contains the bundle files. Instead of having dozens or hundreds of files, the project is "bundled" into a handful of HTML and JS files. At the time of this writing the default bundler used in Angular projects is software called ESBuild. Other bundlers you might use instead include Vite or Webpack.

## Conslusion
What we've seen here are the basic building blocks of a simple Angular SPA. We see how Angular references and bootstraps our syntax across many files, how the project gets assembled into a bundle of several files instead of dozens, and how we compose our SPA out of many smaller components. There's still a lot to see, including routing, fetching, data binding, services, and more. 

## Exercises
Try to re-create this project on your own. You will need the following CLI commands:
 - `ng new PROJECT-NAME` will create a new boilerplate project
 - `ng generate component COMPONENT-NAME` will create a new component, but not bootstrap it
 - `ng serve --open` to run your SPA in the dev environment
 - `ng build` to produce the bundle

 - Use `ng new` to create the project, then add one or more components with `ng generate component`.
 - Bootstrap the components by referencing the selector as an HTML element and referencing the corresponding TypeScript class in the `imports` array. This will nest one component within another, and show them on screen. Remember that `main.ts` and `index.html` are where this process starts, leading into the root component. Nest your added components within the root component. You'll need to delete the boilerplate HTML that angular places there.
 - Add some content in the HTML.
 - Run the SPA with `ng serve --open` and see what you've built.