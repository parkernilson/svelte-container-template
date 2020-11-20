## Docker/Svelte/NGINX (w/ Api proxy) template
This is a template project with everything you need to start making a containerized svelte application in typescript, served by NGINX, that can proxy requests to a linked api.

### To Use This Template
Copy the template onto your computer using degit:
```
$ npx degit parkernilson/docker-svelte-nginx-template your-project-name
```
Run in development mode (with hot-reloading):
```
$ npm run dev
```
Then, view it in the browser at `localhost:5001`.

Run in production mode (served by nginx):
```
$ npm run prod
```
Then, view it in the browser at `localhost:5001`.

## Enabling The API Proxy
The api proxy is disabled by default in this template. To use it, you must:
- un-comment the `proxy` attribute in `webpack.config.js`. See [here](#Enable-API-Proxy-in-Webpack)
- un-comment the `/api` location in `nginx/conf.d/default.conf`. See [here](#Enable-API-Proxy-in-NGINX)

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

## NGINX configuration
All the nginx configuration we are going to use to override the default configuration in the nginx docker image is specified in the `nginx/` directory. 
### Enable API Proxy in NGINX
In order to pass requests to /api to an external service,
uncomment the /api location block in the nginx default conf file:
#### **`nginx/conf.d/default.conf`**
```nginx
location /api/ {
    proxy_pass http://your-api/;
    proxy_set_header Host $host;
}
```
Replace `http://your-api/` with the url you would like to pass to.

NOTE: If you would like the proxy pass to remove the /api portion of the url (e.g. `http://website.com/api/something` to `http://proxy-host/something` instead of `http://proxy-host/api/something`),
you *must* include the trailing / after `http://your-api`.