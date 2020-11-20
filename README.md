## Containerized Typescript Svelte app template
This is a template project with everything you need to start making a containerized svelte application in typescript.

### To Use This Template
Copy the template onto your computer using degit:
```
$ npx degit parkernilson/svelte-container-template your-project-name
```
Run in development mode (with hot-reloading):
```
$ npm run dev
```
Then, view it in the browser at `localhost:5001`.

## Enabling The API Proxy
The api proxy is disabled by default in this template. To use it, you must:
- un-comment the `proxy` attribute in `webpack.config.js`. See [here](#Enable-API-Proxy-in-Webpack)

## Svelte Dev Server
By default, the svelte dev server listens on localhost:8080. In the webpack configuration of this project it has been changed to listen on host 0.0.0.0, so that it can be connected to from outside of the docker container:
#### **`webpack.config.json`**
```js
module.exports = {
    // ...rest of the webpack config

    devServer: {
        host: '0.0.0.0'
    }
}
```

### Enable API Proxy in Webpack
To enable the api proxy uncomment the `proxy` section of the dev server configuration: 
#### **`webpack.config.json`**
```js
module.exports = {
    // ... rest of the webpack config

    devServer: {
        host: '0.0.0.0',
        proxy: {
            '/api': {
                target: 'http://your-api-here',
                pathRewrite: {'^/api': ''}
            }
        }
    }
}
```

If using `docker-compose` to orchestrate multiple containers, you may specify `links` for a service to connect with other services. For example, we may have:
#### **`docker-compose.yml`**
```yml
version: "3.8"
services:
    server-app:
        # server config here
    client-app:
        # client config here
        links:
            - server-app
```
In this case we may set the proxy to `target: 'http://server-app'`.
For more on docker compose files, check out the [Docker Compose Docs](https://docs.docker.com/compose/compose-file/).