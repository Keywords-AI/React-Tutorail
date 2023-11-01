# Python-React Setup SOP

## Setup the Django backend
### Install python
```
sudo apt-get install python3.10
sudo apt-get install python3-venv
```
**You must get into the habit of installing Python> 3.10 and < 3.12 else this is not likely to work when you set up the actual project**

### Make a venv
```
python3 -m venv react_tutorial_venv
source ./react_tutorial_venv/bin/activate
```

### Install python
python3 -m pip install django

### Start the project and apps
```
django-admin startproject react_tutorial
cd react_tutorial
django-admin startapp frontend
cd frontend
```
### Make the folder structure for the app
```
mkdir -p templates/frontend static/frontend/js
touch templates/frontend/index.html
```
#### Paste the following code into index.html
```
{% load static %}
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>React Setup Tutorial</title>
  </head>
  <body style="background-color:black;">
    <div id="root">
        <script src="{% static 'frontend/js/index.js' %}"></script>
    </div>
  </body>
</html>
```

### Set up urls.py and views.py
```
touch urls.py
touch views.py
```
#### Paste the following into urls.py
```
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

#### Paste this into the views.py
```
from django.shortcuts import render

# Create your views here.
def index(request):
    return render(request, 'frontend/index.html')
```
### Add frontend into the project

#### checkout the urls.py under react_tutorial, overwriting the original with
```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('frontend.urls')),
]
```
#### Add the installed app in settings.py

```
... some code ...
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'frontend.apps.FrontendConfig',
]
... the rest of the code ...
```

### Check if the server is going okay
```
python3 manage.py runserver
```
- Check if there is a link in the terminal, if yes, click.
- If you see a black screen, that means everything is right!

## Set up React frontend

### Install nvm
```
sudo apt-get upgrade
sudo apt-get update
sudo apt install curl 
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash 
```
### Install latest node
```
nvm install node
```
### Go inside frontend folder and make initializations
```
cd frontend
npm init --yes
```
### Install react
```
npm install react react-dom
```
### Install babel
```
npm i -D @babel/preset-react @babel/preset-env @babel/core babel-loader @babel/plugin-proposal-class-properties
```
### Make .babelrc
```
touch .babelrc
```
#### Paste the following into .babelrc
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

### Install webpack
```
npm i -D webpack webpack-cli webpack-dev-server html-webpack-plugin path
```
### Set up the directory and files for standalone (frontend only) development
```
mkdir src public
touch src/index.js src/App.js public/index.html
```
### Set up webpack configuration
#### Install the loaders
```
npm install style-loader css-loader file-loader
```
#### Make webpack.config.js
```
touch webpack.config.js
```
#### Paste the following into webpack.config.js
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
### Set up App.js and the entry file index.js
#### Make the files
```
touch src/App.js src/index.js
```
#### Make the following directories
```
mkdir src/pages src/components src/services src/hooks src/utilitiy src/store
```

#### Paste the following into App.js
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

#### Paste the following into index.js
```
import React from 'react'
import App from './App'
import { createRoot } from "react-dom/client";

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```
### Replace the scripts section in package.json with the following
```
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack --config webpack.config.js --watch --mode development",
    "build": "webpack --config webpack.config.js --mode production",
    "standalone": "set DISABLE_ESLINT_PLUGIN=true && react-scripts start"
  },
```

### Give it a go!
```
npm run dev
```
## Setup for TypeScript (Optional)
### Make tscofig.json
```
cd frontend
touch tsconfig.json
```

## Setup SCSS & Tailwind
### Add css loader
```
      {
        test: /\.s[ac]ss$/i,
        use: [
          "style-loader",
          "css-loader",
          {
            loader: "postcss-loader",
            options: {
              postcssOptions: {
                ident: "postcss",
                plugins: ["tailwindcss", "autoprefixer"],
              },
            },
          },
          "sass-loader",
        ],
      },
```
### Paste the following into it
```
{
  "compilerOptions": {
    "jsx": "react-jsx",
    "esModuleInterop": true,
    // ... other options
    "baseUrl": "./",
    "paths": {
        "src": ["src/*"]
    }
  },
  "include": [
    "src/**/*",
    "declarations.d.ts"
  ]
  // ... rest of the configuration
}
```
### Add typescript preset to .bablerc
```
module.exports = {
    presets:[
        "@babel/preset-env",
        ["@babel/preset-react",{"runtime": "automatic"}],
        "@babel/preset-typescript" # THIS LINE HERE!!!
    ]
}
```
### Replace the babel-loader rules in webpack.config.js with this
```
...some other code
module: {
    rules: [
      {
        test: /\.(js|jsx|ts|tsx)$/, # THIS LINE!!!
        exclude: /node_modules/,
        loader: 'babel-loader',
      },
...other rules
  ]
... other module settings
}
...some other code
```
### Change the resolve setting in webpack.config.js
```
resolve: {
    extensions: ['.js', '.jsx', '.ts', '.tsx'], ### THIS LINE!!! Add .ts and .tsx
    alias: {
      root: __dirname,
      src: path.resolve(__dirname, 'src'),
    },
  },
```

## Environmental variables (Optional)
### Make .env file
```
touch .env
```
### Install python_dotenv
```
pip install python_dotenv
```
### Add this to settings.py
```
import dotenv
dotenv.load_dotenv(BASE_DIR / '.env')
```
### Add any environmental varialbes like this in .env
```
<VARIABLE_NAME>=<value>

#example
DEBUG = True

#usage
import os
DEBUG = os.getenv("DEBUG") # DEBUG will be true
```
# Setup CORS
See link [here](https://pypi.org/project/django-cors-headers/)

# Setup CSRF Trusted Origin
In settings.py, add this
```
CSRF_TRUSTED_ORIGINS = [
    'https://www.keywordsai.co',
    'http://www.keywordsai.co',
    'https://keywordsai.co',
    'http://keywordsai.co',
    'http://www.careersgpt.app',
    'https://www.careersgpt.app',
    'http://localhost:8000',
    'http://127.0.0.1:8000',
    'https://localhost:8000',
    'https://127.0.0.1:8000',
]
```

# Setup Authentication

# Setup Asynchronous Application
## Install pachages
```
pip install daphne django-channels
```
## Modify asgi.py
```
"""
ASGI config for CareersGPT CareersGPToject.

It exposes the ASGI callable as a module-level variable named ``application``.

For more information on this file, see
https://docs.djangoCareersGPToject.com/en/4.1/howto/deployment/asgi/
"""

import os
from channels.routing import ProtocolTypeRouter, URLRouter
from django.core.asgi import get_asgi_application
from channels.auth import AuthMiddlewareStack



os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'CareersGPT.settings')
django_asgi_app = get_asgi_application()
import chatbot.routing
import job_board.routing
# import AI_rec.routing
application = ProtocolTypeRouter({
    "http": django_asgi_app,
    'websocket': AuthMiddlewareStack(
        URLRouter(
            # chatbot.routing.websocket_urlpatterns +
            # AI_rec.routing.websocket_urlpatterns
            chatbot.routing.websocket_urlpatterns +
            job_board.routing.websocket_urlpatterns
        )
    )
})
```


# Coding Convensions and Folder Structures

## Django/Python Convensions

### Django Folder Structure
**You need to strickly follow the following structure**
```
- app_folder
  - templates
    - app_folder (this name must match the app_folder name!)
      some_file.html
  - static
    - app_folder (same format as that of the template folder)
      - css
        some_file.css
      - img
        some_file.png | some_file.svg | some_file.jpg
      - js
        some_file.js
  - migrations
    some_file.py
  - utils (helper functions)
    __init__.py
    - some_folder (if necessary, usually you don't need that much helper functions)
      some_file.py
    some_file.py
  __init__.py
  urls.py
  views.py
  apps.py
  models.py
  admin.py
  tasks.py (for celery)
```
### Django Naming Convension

#### Variable Names
- concatenate with underscore (e.g. some_variable, this_variable, that_variable)
#### Long Functions
- You should change lines when the arguments gets too long
**Bad examples**
```
out_put = some_function(argument, argument1, argument1, argument1, argument1, argument1, argument1, argument1, argument1, argument1, argument1, argument1, argument1, argument1)
```
**Good example**
```
out_put = some_function(
  argument,
  argument,
  argument,
  argument,
  argument,
  argument,
  argument,
  argument,
)
```
#### Long Strings
- Use concatenation to improve readability
**Bad example**
```
some_string = "This is a very long string blablablab blablablab blablablab blablablab blablablab blablablab blablablab blablablab"
```
**Good example**
```
some_string = ("This is a very long string"
"blablablablalbalblalbalba"
"blablablab")
```
#### Long Objects
- Similar to long functions
**Bad example**
```
person = {"name": "John Doe", "age": 30, "address": "1234 Park St", "phone": "123-456-7890", "email": "johndoe@example.com"}
```
**Good example**
```
person = {
    "name": "John Doe",
    "age": 30,
    "address": "1234 Park St",
    "phone": "123-456-7890",
    "email": "johndoe@example.com"
}
```

## JavaScript/React Guide Lines.

### Folder Structure
```
- frontend
  - ... Django Folder and Files ...
  - public
    some_file.html
  - node_modules
    - package
    - pachage
    - ...
  - src
    - pages
      - pageA
        - components
          - SomeComponent
            - components
            - static
              ... The rest is the same, recursively ...
            SomeComponent.jsx
        - static
          - css
            some_file.css
          - img
            image.png
      - pageB
    - hooks
    - services
    - utility
    - store
      - actions
        action1.js
        action2.js
        action.js (This file collects all the actions, optional)
      - reducers
        reducer1.js
        reducer2.js
        reducer.js (This reducer collects all the reducers, mandatory)
      selector.js
      store.js (Stores all the states)
    - context
  
  package.js
  .babelrc
  webpack.config.js
```

### Code Formatting Guidelines
**It is Highly Recommended that you use VS Code as your text editor**  
**and install the Prettier extension that autoformats for you**
- Install Prettier extension in VS Code
- Ctrl (or CMD in Mac) + Shift + P to open panel in VS Code
  - Search `Prettier Create Configuration File`
  - Hit enter and open the config file
- Paste the following code in the configuration file
```
{
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all",
  "bracketSpacing": true,
  "jsxBracketSameLine": false,
  "arrowParens": "avoid"
}
```
- Shift + Alt + F to format the files!
