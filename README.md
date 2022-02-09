# vibbra-commerce-web-app

Frontend project for use of api vibbra-commerce-api.

## Required
Is required an instance execution of the project vibbra-commerce-api for data consumption.

## Modules/Routes
- /cadastro: where the user registers for access to e-commerce.
- /login: Where the user performs authentication for access to the protected application.
- /criar-negociacao: Generate a new deal available in e-commerce.
- /editar-negociacao: Edit a deal registered.
- /: Where the deals available are listed.
- /meus-convites: Where the invites sends by the user are show.
- /negociacao: Show the information about a specific deal.

## Technologies used
1. Vue.js
2. Nuxt.js
3. Nuxt-Auth
4. Axios
5. Vue-mask
6. Vue-money

## Build Setup

Note: before the project is run, a .env file must be created with the following variables:
- API_URL=[url to the server for access to the api]

```bash
# install dependencies
$ npm install

# serve with hot reload at localhost:3000
$ npm run dev

# build for production and launch server
$ npm run build
$ npm run start

# generate static project
$ npm run generate
```