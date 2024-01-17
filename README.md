# Bedrock and Atlas vector crime scene demo

Repository for the AWS Bedrock and Atlas Vector search workshop. Please make sure to complete all backend steps 

## Using Github codespaces 
Lunch a github codespace conntainer.
1. Set the needed `.env` file content:
```
VUE_APP_BASE_APP_SERVICE_URL='<your app services endpoint>'
VUE_APP_SEARCH_ENDPOINT='getImageSearch'
VUE_APP_MEDAL_ENDPOINT='generateMedal'
VUE_APP_REGISTER_ENDPOINT='registerPlayer'
VUE_APP_SAVE_CHATS_ENDPOINT='saveChats'
VUE_APP_GET_CHATS_ENDPOINT='getChats'
##VUE_APP_SIDE_IFRAME='https://charts.mongodb.com/charts-fsidemo-ubsdv/embed/charts?id=65a67383-010f-4c3d-81b7-7cf19ca7000b&maxDataAge=3600&theme=light&autoRefresh=true'
VUE_APP_SIDE_IFRAME='https://soundscout.s3.eu-central-1.amazonaws.com/The+CLUE.pdf'
```

Install `serve` :
```
npm install serve -g
```

Build the app:
```
npm run build
```

Run project: 
```
serve -d dist/
```

Redirect to the URL and open app.

## Project local setup
1. Clone the repo : `git clone https://github.com/mongodb-developer/atlas-bedrock-crime-story-demo`
1. Set the needed `.env` file content:
```
VUE_APP_BASE_APP_SERVICE_URL='<your app services endpoint>'
VUE_APP_SEARCH_ENDPOINT='getImageSearch'
VUE_APP_MEDAL_ENDPOINT='generateMedal'
VUE_APP_REGISTER_ENDPOINT='registerPlayer'
VUE_APP_SAVE_CHATS_ENDPOINT='saveChats'
VUE_APP_GET_CHATS_ENDPOINT='getChats'
```
1. install depndencies
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

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
