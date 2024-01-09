# Bedrock and Atlas vector crime scene demo

Repository for the AWS Bedrock and Atlas Vector search workshop.

## Project setup
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
