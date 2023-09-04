#Example webpack usage with #React.js

Firstly :

```npm init -y```

Let's create the package.json file with the command.
As we can see in this file created later, there is the index.js page as the main file. Let's create a file named index.js in the root directory.

Now we will install webpack and some necessary tools for it.

here are the required npm commands:

```npm i webpack --save-dev```

```npm i webpack-cli --save-dev```

Thanks to the webpack-cli package, we will be able to use webpack commands.

You can see the packages we have installed for now and what we will install later in the package.json file in the devDependencies section.

Our next step is to create a file called webpack.config.js in the root directory (this is important).

```javascript
module.exports = {
     entry: './index.js',
     output: {
         path: __dirname,
         filename:'bundle.js'
     }
}
```

The entry file here, as the name suggests, is the input file, that is, the file that our webpack will process. output is the file that will be the output of the processed file. The path inside the output object is the path and __dirname is the folder where the webpack.config.js file is located. filename refers to the name of the file to be created.

Let's move on to our next action, let's first open our package.json file and there is a script named test in the scripts section, let's delete it and write `"dev": "webpack --mode development"` there. Let's do a little experiment right now, open the index.js file in the root directory and console.log("Hello World"); Let's write. Then let's type `npm run dev` on the command line.

If you saw the message "... compiled successfully ..." on the command line and a file named bundle.js was created in the file directory, everything is correct up to this point.

Now let's add another script to our package.json file.

```"build": "webpack --mode production"```

When you run this script, you will see that the bundle.js file takes up less space, that is, it compresses it. This script is used after the development phase of the project is over.

Now let's move on to the file directory structure in modern projects. You will understand better what I mean later.

Let's create two files named dist and src in the root directory. Let's create another folder called js inside the src folder and move the index.js file that we created before. Next, let's create a file called index.html inside the dist folder, and then create 3 different files named css, img and js. It is a good idea to delete the bundle.js file.

We created some new files and relocated some of them, so some adjustments need to be made in the webpack.config.js file.

The current webpack.config.js should look like this:

```javascript
const path = require('path');

module.exports = {
     entry: './src/js/index.js',
     output: {
         path: path.resolve(__dirname,'dist'),
         filename: 'js/bundle.js',
     },
};
```

Since the file path has changed, we include the path module in node.js by saying `const path = require('path');` and introduce the new path as above.

Now let's check if we did everything right, and if you type `npm run dev` on the command line and you get the answer ".. compiled successfully ..." and you have a dist>js>bundle.js file, congratulations, everything is correct until here.

What we have done so far was only for webpack configuration, now we will install the devServer package, which monitors the changes we have made and displays it in the html file.

Required command for this:

```npm i webpack-dev-server --save-dev```

I know you won't like it, but to use devServer we need to update the webpack.config.js file and it's updated:

```javascript
const path = require('path');

module.exports = {
     entry: './src/js/index.js',
     output: {
         path: path.resolve(__dirname,'dist'),
         filename: 'js/bundle.js',
     },
     devServer: {
         static:'./dist'
     }
};
```



To run our project using devServer, we write a new script and include it in the package.json file. Here is the script:

```"start": "webpack-dev-server --mode development --open"```

We can see the "Hello World" text that we wrote earlier in the console section of the page opened at localhost:8080.


Now it's time to include react and sass. To do this, run the following commands in order.

```npm i react react-dom --save```

If you have installed the react project through `npx create-react-app`, in the package.json file
Change `"build": "webpack --config webpack.config.js"` and rename the dist folder to build in both the file directory and the webpack.config.js file.

We've included React.js, now we'll load babel.js next.

```npm i @babel/core babel-loader --save-dev```

While the above command loads the basic babel, the loader package is loaded to scan files with js and jsx extensions. In the command below, the preset-env package converts ES6 and later js codes to ES5 codes. The preset-react package converts the react codes to js codes.

```npm i @babel/preset-env @babel/preset-react --save-dev```


To optionally install sass:

```npm i sass sass-loader style-loader css-loader --save-dev```


After installing these packages, after making some adjustments in the webpack.config.js file:

```javascript
const path = require('path');

module.exports = {
     entry: './src/js/index.js',
     output: {
         path: path.resolve(__dirname, 'dist'),
         filename: 'js/bundle.js',
     },
     devServer: {
         static: './dist',
     },
     module: {
         rules: [
             {
                 test: /\.(js|jsx)$/,
                 exclude: /node_modules/,
                 use: {
                     loader: 'babel-loader',
                 },
             },
             {
                 test: /\.scss$/,
                 use: ['style-loader','css-loader','sass-loader'],
             },
         ],
     },
};

```
Here are the file extensions to be followed in the test section, and the packages that will convert the followed files in the use section.
If you don't want to use sass, simply remove the 'sass-loader' part.

Finally, add a file named .babelrc to the root directory and write the code block below. Thanks to these code blocks, babel will do the necessary ES5 transformations.

Then we write `<script src="js/bundle.js"></script>` and `<div id="root"></div>` between the body tags of the index.html file.


Then we clean the index.js file and include the following codes.

```react.js
import React from 'react';
import { createRoot } from 'react-dom/client';
import App from './App';

createRoot(document.getElementById('root')).render(<App />);
```

We can observe the ES5 transformations of the codes written in index.js in bundle.js.

Finally :

``npm i babel-polyfill --save```

Async-await function transformations can be achieved by installing the package, otherwise we may have problems with asynchronous operations, and the final version of the webpack.config.js file is as follows.

`` javascript
const path = require('path');

module.exports = {
     entry: ['babel-polyfill', './src/js/index.js'],
     output: {
         path: path.resolve(__dirname, 'dist'),
         filename: 'js/bundle.js',
     },
     devServer: {
         static: './dist',
     },
     module: {
         rules: [
             {
                 test: /\.(js|jsx)$/,
                 exclude: /node_modules/,
                 use: {
                     loader: 'babel-loader',
                     options: {
                         presets: ['@babel/preset-react'],
                     },
                 },
             },
             {
                 test: /\.scss$/,
                 use: ['style-loader', 'css-loader', 'sass-loader'],
             },
         ],
     },
};

```

__*NOTE :__
- --save: This parameter refers to the dependencies of the installed package that are used at your project's runtime. That is, it generates the list of packages directly required for your project to work. These packages are also required in your project's production environment. Therefore, these packages need to be installed when you deploy your project.

* For example, the command npm install react --save adds the React library to your project and adds it to the dependencies section of the package.json file. This is a required package for your project to work.

- --save-dev: This parameter refers to the dependencies of the installed package that are used only during the development process. These packages can be things like tools used in the development process of your project, test libraries, build or packaging utilities. These packages are not required in your project's production environment and do not need to be installed during deployment.
* For example, the command npm install webpack --save-dev adds the Webpack tool to your project and adds it to the devDependencies section of the package.json file. This is a tool used in the development process of your project and is not required in a production environment.


* *Author* : **Ökkeş Akkurt** - [Github](https://github.com/Okkes-Akkurt)