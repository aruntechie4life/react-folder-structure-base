Getting Started with Create React App Base

This project was bootstrapped with Create-React from Facebook

## Available Scripts

In the project directory, you can run:

### `npm start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in your browser.

The page will reload when you make changes.\
You may also see any lint errors in the console.

### `npm test`

Launches the test runner in the interactive watch mode.\


### `npm run build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!


### `npm run eject`

**Note: this is a one-way operation. Once you `eject`, you can't go back!**

If you aren't satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you're on your own.

You don't have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn't feel obligated to use this feature. However we understand that this tool wouldn't be useful if you couldn't customize it when you are ready for it.

#################################################################################################

React Architecture: How to Structure and Organize a React Application

#################################################################################################

There is no consensus on the right way to organize a React application. React gives you a lot of freedom, but with that freedom comes the responsibility of deciding on your own architecture. Often the case is that whoever sets up the application in the beginning throws almost everything in a components folder, or maybe components and containers if they used Redux, but I propose there's a better way. I like to be deliberate about how I organize my applications so they're easy to use, understand, and extend.

I'm going to show you what I consider to be an intuitive and scalable system for large-scale production React applications. The main concept I think is important is to make the architecture focused on feature as opposed to type, organizing only shared components on a global level and modularized all the other related entities together in the localized view.

TECH ASSUMPTIONS
Since this article will be opinionated, I'll make some assumptions about what technology the project will be using:

Application - React (Hooks)
Global state management - Redux, Redux Toolkit
Routing - React Router
Styles - Styled Components
Testing - Jest, React Testing Library
I don't have a very strong opinion about the styling, whether Styled Components or CSS modules or a custom Sass setup is ideal, but I think Styled Components is probably one of the best options for keeping your styles modular.

I'm also going to assume the tests are alongside the code, as opposed to in a top-level tests folder. I can go either way with this one, but in order for an example to work, and in the real world, decisions need to be made.

Everything here can still apply if you're using vanilla Redux instead of Redux Toolkit. I would recommend setting up your Redux as feature slices either way.

I'm also ambivalent about Storybook, but I'll include what it would look like with those files if you choose to use it in your project.

For the sake of the example, I'll use a "Library App" example, that has a page for listing books, a page for listing authors, and has an authentication system.

Directory Structure
The top level directory structure will be as follows:

assets - global static assets such as images, svgs, company logo, etc.
components - global shared/reusable components, such as layout (wrappers, navigation), form components, buttons
services - JavaScript modules
store - Global Redux store
utils - Utilities, helpers, constants, and the like
views - Can also be called "pages", the majority of the app would be contained here
I like keeping familiar conventions wherever possible, so src contains everything, index.js is the entry point, and App.js sets up the auth and routing.

.
└── /src
    ├── /assets
    ├── /components
    ├── /services
    ├── /store
    ├── /utils
    ├── /views
    ├── index.js
    └── App.js
I can see some additional folders you might have, such as types if it's a TypeScript project, middleware if necessary, maybe context for Context, etc.

Aliases
I would set up the system to use aliases, so anything within the components folder could be imported as @components, assets as @assets, etc. If you have a custom Webpack, this is done through the resolve configuration.

module.exports = {
  resolve: {
    extensions: ['js', 'ts'],
    alias: {
      '@': path.resolve(__dirname, 'src'),
      '@assets': path.resolve(__dirname, 'src/assets'),
      '@components': path.resolve(__dirname, 'src/components'),
      // ...etc
    },
  },
}
It just makes it a lot easier to import from anywhere within the project and move files around without changing imports, and you never end up with something like ../../../../../components/.

Components
Within the components folder, I would group by type - forms, tables, buttons, layout, etc. The specifics will vary by your specific app.

In this example, I'm assuming you're either creating your own form system, or creating your own bindings to an existing form system (for example, combining Formik and Material UI). In this case, you'd create a folder for each component (TextField, Select, Radio, Dropdown, etc.), and inside would be a file for the component itself, the styles, the tests, and the Storybook if it's being used.

Component.js - The actual React component
Component.styles.js - The Styled Components file for the component
Component.test.js - The tests
Component.stories.js - The Storybook file
To me, this makes a lot more sense than having one folder that contains the files for ALL components, one folder that contains all the tests, and one folder that contains all the Storybook files, etc. Everything related is grouped together and easy to find.

.
└── /src
    └── /components
        ├── /forms
        │   ├── /TextField
        │   │   ├── TextField.js
        │   │   ├── TextField.styles.js
        │   │   ├── TextField.test.js
        │   │   └── TextField.stories.js
        │   ├── /Select
        │   │   ├── Select.js
        │   │   ├── Select.styles.js
        │   │   ├── Select.test.js
        │   │   └── Select.stories.js
        │   └── index.js
        ├── /routing
        │   └── /PrivateRoute
        │       ├── /PrivateRoute.js
        │       └── /PrivateRoute.test.js
        └── /layout
            └── /navigation
                └── /NavBar
                    ├── NavBar.js
                    ├── NavBar.styles.js
                    ├── NavBar.test.js
                    └── NavBar.stories.js
You'll notice there's an index.js file in the components/forms directory. It is often rightfully suggested to avoid using index.js files as they're not explicit, but in this case it makes sense - it will end up being an index of all the forms and look something like this:

src/components/forms/index.js
import { TextField } from './TextField/TextField'
import { Select } from './Select/Select'
import { Radio } from './Radio/Radio'

export { TextField, Select, Radio }
Then when you need to use one or more of the components, you can easily import them all at once.

import { TextField, Select, Radio } from '@components/forms'
I would recommend this approach more than making an index.js inside of every folder within forms, so now you just have one index.js that actually indexes the entire directory, as opposed to ten index.js files just to make imports easier for each individual file.

Services
The services directory is less essential than components, but if you're making a plain JavaScript module that the rest of the application is using, it can be handy. A common contrived example is a LocalStorage module, which might look like this:

.
└── /src
    └── /services
        ├── /LocalStorage
        │   ├── LocalStorage.service.js
        │   └── LocalStorage.test.js
        └── index.js
An example of the service:

src/services/LocalStorage/LocalStorage.service.js
export const LocalStorage = {
  get(key) {},
  set(key, value) {},
  remove(key) {},
  clear() {},
}
import { LocalStorage } from '@services'

LocalStorage.get('foo')
Store
The global data store will be contained in the store directory - in this case, Redux. Each feature will have a folder, which will contain the Redux Toolkit slice, as well as actions and tests. This setup can also be used with regular Redux, you would just create a .reducers.js file and .actions.js file instead of a slice. If you're using sagas, it could be .saga.js instead of .actions.js for Redux Thunk actions.

.
└── /src
    ├── /store
    │   ├── /authentication
    │   │   ├── /authentication.slice.js
    │   │   ├── /authentication.actions.js
    │   │   └── /authentication.test.js
    │   ├── /authors
    │   │   ├── /authors.slice.js
    │   │   ├── /authors.actions.js
    │   │   └── /authors.test.js
    │   └── /books
    │       ├── /books.slice.js
    │       ├── /books.actions.js
    │       └── /books.test.js
    ├── rootReducer.js
    └── index.js
You can also add something like a ui section of the store to handle modals, toasts, sidebar toggling, and other global UI state, which I find better than having const [isOpen, setIsOpen] = useState(false) all over the place.

In the rootReducer you would import all your slices and combine them with combineReducers, and in index.js you would configure the store.

Utils
Whether or not your project needs a utils folder is up to you, but I think there are usually some global utility functions, like validation and conversion, that could easily be used across multiple sections of the app. If you keep it organized - not just having one helpers.js file that contains thousands of functions - it could be a helpful addition to the organization of your project.

.
└── src
    └── /utils
        ├── /constants
        │   └── countries.constants.js
        └── /helpers
            ├── validation.helpers.js
            ├── currency.helpers.js
            └── array.helpers.js
Again, the utils folder can contain anything you want that you think makes sense to keep on a global level. If you don't prefer the "multi-tier" filenames, you could just call it validation.js, but the way I see it, being explicit does not take anything away from the project, and makes it easier to navigate filenames when searching in your IDE.

Views
Here's where the main part of your app will live: in the views directory. Any page in your app is a "view". In this small example, the views line up pretty well with the Redux store, but it won't necessarily be the case that the store and views are exactly the same, which is why they're separate. Also, books might pull from authors, and so on.

Anything within a view is an item that will likely only be used within that specific view - a BookForm that will only be used at the /books route, and an AuthorBlurb that will only be used on the /authors route. It might include specific forms, modals, buttons, any component that won't be global.

The advantage of keeping everything domain-focused instead of putting all your pages together in components/pages is that it makes it really easy to look at the structure of the application and know how many top level views there are, and know where everything that's only used by that view is. If there are nested routes, you can always add a nested views folder within the main route.

.
└── /src
    └── /views
        ├── /Authors
        │   ├── /AuthorsPage
        │   │   ├── AuthorsPage.js
        │   │   └── AuthorsPage.test.js
        │   └── /AuthorBlurb
        │       ├── /AuthorBlurb.js
        │       └── /AuthorBlurb.test.js
        ├── /Books
        │   ├── /BooksPage
        │   │   ├── BooksPage.js
        │   │   └── BooksPage.test.js
        │   └── /BookForm
        │       ├── /BookForm.js
        │       └── /BookForm.test.js
        └── /Login
            ├── LoginPage
            │   ├── LoginPage.styles.js
            │   ├── LoginPage.js
            │   └── LoginPage.test.js
            └── LoginForm
                ├── LoginForm.js
                └── LoginForm.test.js
Keeping everything within folders might seem annoying if you've never set up your project that way - you can always keep it more flat, or move tests to its own directory that mimics the rest of the app.

Conclusion
This is my proposal for a sytem for React organization that scales well for a large production app, and handles testing and styling as well as keeping everything together in a feature focused way. It's more nested than the traditional structure of everything being in components and containers, but that system is a bit more dated due to Redux being much easier to implement with Hooks, and "smart" containers and "dumb" components no longer being necessary.

It's easy to look at this system and understand everything that is needed for your app and where to go to work on a specific section, or a component that affects the app globally. This system may not make sense for every type of app, but it has worked for me. I'd love to hear any comments about ways this system can be improved, or other systems that have merit.





## Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).

### Code Splitting

This section has moved here: [https://facebook.github.io/create-react-app/docs/code-splitting](https://facebook.github.io/create-react-app/docs/code-splitting)

### Analyzing the Bundle Size

This section has moved here: [https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size](https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size)

### Making a Progressive Web App

This section has moved here: [https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app](https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app)

### Advanced Configuration

This section has moved here: [https://facebook.github.io/create-react-app/docs/advanced-configuration](https://facebook.github.io/create-react-app/docs/advanced-configuration)

### Deployment

This section has moved here: [https://facebook.github.io/create-react-app/docs/deployment](https://facebook.github.io/create-react-app/docs/deployment)

### `npm run build` fails to minify

This section has moved here: [https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify](https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify)
