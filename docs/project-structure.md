# 🗄️ Project Structure

Most of the code lives in the `src` folder and looks something like this:

```sh
src
|
+-- assets            # assets folder can contain all the static files such as images, fonts, etc.
|
+-- components        # shared components used across the entire application
|
+-- config            # all the global configuration, env variables etc. get exported from here and used in the app
|
+-- features          # feature based modules
|
+-- hooks             # shared hooks used across the entire application
|
+-- lib               # reusable libraries preconfigured for the application
|
+-- providers         # all of the application providers
|
+-- routes            # routes configuration
|
+-- stores            # global state stores
|
+-- test              # test utilities and mocks
|
+-- types             # shared types used across the application
|
+-- utils             # shared utility functions
```

For easy scalability and maintenance, organize most of the code within the features folder. Each feature folder should contain code specific to that feature, keeping things neatly separated. This approach helps prevent mixing feature-related code with shared components, making it simpler to manage and maintain the codebase compared to having many files in a flat folder structure. By adopting this method, you can enhance collaboration, readability, and scalability in the application's architecture.

A feature could have the following structure:

```sh
src/features/awesome-feature
|
+-- api         # exported API request declarations and api hooks related to a specific feature
|
+-- assets      # assets folder can contain all the static files for a specific feature
|
+-- components  # components scoped to a specific feature
|
+-- hooks       # hooks scoped to a specific feature
|
+-- routes      # route components for a specific feature pages
|
+-- stores      # state stores for a specific feature
|
+-- types       # typescript types for TS specific feature domain
|
+-- utils       # utility functions for a specific feature
|
+-- index.ts    # entry point for the feature, it should serve as the public API of the given feature and exports everything that should be used outside the feature
```

Everything from a feature should be exported from the `index.ts` file which behaves as the public API of the feature.

You should import stuff from other features only by using:

`import {AwesomeComponent} from "@/features/awesome-feature"`

and not

`import {AwesomeComponent} from "@/features/awesome-feature/components/awesome-component`

This can also be configured in the ESLint configuration to disallow the later import by the following rule:

```js
{
    rules: {
        'no-restricted-imports': [
            'error',
            {
                patterns: ['@/features/*/*'],
            },
        ],

    // ...rest of the configuration
}
```

To prevent circular dependencies, here is another ESLint rule that can be used:

```js
{
    rules: {
        'import/no-cycle': 'error',
    // ...rest of the configuration
}
```

This was inspired by how [NX](https://nx.dev/) handles libraries that are isolated but available to be used by the other modules. Think of a feature as a library or a module that is self-contained but can expose different parts to other features via its entry point. This approach would also make it easier to split the application in a monorepo in the future.

This way, you can ensure that the codebase is clean and easy to maintain.

If you are still getting circular dependencies, consider breaking the feature into smaller features or moving the shared code to the `lib` folder. Or you can always opt-out of the barrel export pattern and import the files directly.

Sometimes, it will make more sense to move the whole API layer to the `lib` folder, especially if it is shared across multiple features. The same goes for the `stores` folder. If you have a global store that is used across the entire application, it should be moved to the `stores` folder.
