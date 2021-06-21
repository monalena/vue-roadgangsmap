# Vue-Absconder Map

A visualisation of absconding convicts in Van Diemen's Land between 1810-1860. 
Filter per years and genders. Change between the recreated Frankland map from 1839 showing historical County, Hundred and Parish names and a satellite projection. 
Click on the circles to find more information on the individual absconding events (Convict name, date...) Visualisation created using Vue.js and Mapbox. 

#### Try it out here:

https://monalena.github.io/vue-abscondermap/

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Deploy to Github-Pages
After 'npm run build'  run
```$xslt
git add dist && git commit -m "New commit"
```
then
```
git subtree push --prefix dist origin gh-pages
```
should this not work (due to pull issues) run
```
npm run deploy-demo
```
instead. 

For new deployment remember:

* remove /dist from gitignore file
* change vue.config.js to project name
* when loading data get your paths right with process.env.BASE_URL

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
