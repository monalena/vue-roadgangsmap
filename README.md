# Vue - Road Gangs Map

A visualisation of assignments of convicts to road gangs, road stations and penal stations in Van Diemen's Land between 1829-1839. 
Filter per years and click on circles for more detail. Background is the recreated Frankland map from 1839 showing historical County, Hundred and Parish names. 
Visualisation created using Vue.js and Mapbox. Data collected from the convict records transcribed by volunteers through DHT (Digital History Tasmania).

#### Try it out here:

https://monalena.github.io/vue-roadgangsmap/

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
