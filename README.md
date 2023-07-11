# Python-React Setup SOP

## Setup the Django backend
### install python
```
sudo apt-get install python3.10
sudo apt-get install python3-venv
```
**You must get into the habit of installing Python> 3.10 and < 3.12 else this is not likely to work when you set up the actual project**

### make a venv
```
python3 -m venv react_tutorial_venv
source ./react_tutorial_venv/bin/activate
```

### install python
python3 -m pip install django

### start the project and apps
```
django-admin startproject react_tutorial
cd react_tutorial
django-admin startapp frontend
cd frontend
```
### make the folder structure for the app
```
mkdir -p templates/frontend static/frontend/js
touch templates/frontend/index.html
```
#### paste the following code into index.html
```
{% load static %}
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>React Setup Tutorial</title>
  </head>
  <body>
    <div id="root">
        <script src="{% static 'frontend/js/index.js' %}"></script>
    </div>
  </body>
</html>
```

### setup urls.py and views.py
```
touch urls.py
touch views.py
```
#### paste the following into urls.py
```
from django.urls import path

urlpatterns = [
    path('', views.index, name='index'),
]
```

#### paste this in to the views.py
```
from django.shortcuts import render

# Create your views here.
def index(request):
    return render(request, 'frontend/index.html')
```
### add frontend into the project

#### checkout the urls.py under react_tutorial, overwriting the original with
```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('frontend.urls')),
]
```
#### add the installed app in settings.py

```
...
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'frontend.apps.FrontendConfig',
]
...
```

### check if the server is going okay
```
python3 manage.py runserver
```
- Check if there is a link in the terminal, if yes, click.
- If you see a black screen, that means everything is right!

## Setup React frontend

### Install nvm
```
sudo apt-get upgrade
sudo apt-get update
sudo apt install curl 
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash 
```
### install latest node
```
nvm install node
```
### go inside frontend folder and make initializations
```
cd frontend
npm init --yes
```
### install react
```
npm install react react-dom
```
### install babel
```
npm i -D @babel/preset-react @babel/preset-env @babel/core babel-loader @babel/plugin-proposal-class-properties
```
### make .babelrc
```
touch .babelrc
```
#### paste the following into .babelrc
```
{
  "presets": [
 [ "@babel/preset-env", {
   "modules": false,
   "targets": {
  "browsers": [
    "last 2 Chrome versions",
    "last 2 Firefox versions",
    "last 2 Safari versions",
    "last 2 iOS versions",
    "last 1 Android version",
    "last 1 ChromeAndroid version",
    "ie 11"
  ]
   }
 } ],
 "@babel/preset-react"
  ],
  "plugins": [ "@babel/plugin-proposal-class-properties" ]
}
```

### install webpack
```
npm i -D webpack webpack-cli webpack-dev-server html-webpack-plugin path
```
### setup the directory and files for standalone (frontend only) development
```
mkdir src public
touch src/index.js src/App.js public/index.html
```
### setup webpack configuration
#### install the loaders
```
npm install style-loader css-loader file-loader
```
#### make webpack.config.js
```
touch webpack.config.js
```
#### paste the following into webpack.config.js
```
const HtmlWebPackPlugin = require( 'html-webpack-plugin' );
const path = require( 'path' );

module.exports = {
    context: __dirname,
    entry: './src/index.js',
    output: {
        path: path.resolve( __dirname, 'static/frontend/js' ),
        filename: 'index.js',
        publicPath: '/',
    },
    devServer: {
        historyApiFallback: true
     },
    module: {
        rules: [
            {
                test: /\.(js|jsx)?$/,
                exclude: /node_module/,
                use: 'babel-loader'
            },
            {
                test: /\.css?$/,
                use: [ 'style-loader', 'css-loader' ]
            },
            {
                test: /\.(png|j?g|svg|gif)?$/,
                use: 'file-loader'
            }
        ]
    },
    plugins: [
        new HtmlWebPackPlugin({
            template: path.resolve( __dirname, 'public/index.html' ),
            filename: 'index.html'
        })
    ],
    resolve: {
        extensions: [ '*', '.js', '.jsx' ],
        alias: {
            src: path.resolve( __dirname, 'src' ),
            root: __dirname,
        }
    }
};
```
### setup App.js and the entry file index.js
#### make the files
touch src/App.js src/index.js

#### make the following directories
mkdir src/pages src/components src/services src/hooks src/utilitiy src/store

#### paste the following into App.js
```
import React from 'react'

export default function App() {
  return (
    <div style={{
        color: 'white',
    }}>App</div>
  )
}
```

#### paste the following into index.js
```
import React from 'react'
import App from './App'
import { createRoot } from "react-dom/client";

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```
### replace the scripts section in package.json with the following
```
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack --config webpack.config.js --watch --mode development",
    "build": "webpack --config webpack.config.js --mode production",
    "standalone": "set DISABLE_ESLINT_PLUGIN=true && react-scripts start"
  },
```

### give it a go!
```
npm run dev
```


