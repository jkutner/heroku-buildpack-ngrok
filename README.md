# Heroku ngrok Buildpack

This is a [Heroku Buildpack](https://devcenter.heroku.com/articles/buildpacks)
for running a auxiliary port on a process in [dyno](https://devcenter.heroku.com/articles/dynos).
The auxiliary port will be proxied by [ngrok](https://ngrok.com/), which makes it accessible from a remote machine. By default, the port is 9090, but it can be configured with the `$AUX_PORT` config variable.

## Setup

First, create a free [ngrok account](https://dashboard.ngrok.com/user/signup). This is necessary to use TCP with their service. Then capture your API key, and set it as a config var on your Heroku app like this:

```
$ heroku config:set NGROK_API_TOKEN=xxxxxx
```

Next, add this buildpack to your app:

```
$ heroku buildpacks:add jkutner/ngrok
```

Then add your primary buildpack. For example, if you are using Java:

```
$ heroku buildpacks:add heroku/java
```

Now modify your `Procfile` by prefixing your `web` process with the `with_ngrok` command. For example:

```
web: with_ngrok java $JAVA_OPTS -cp target/classes:target/dependency/* Main
```

Finally, commit your changes, and redeploy the app:

```
$ git add Procfile
$ git commit -m "Added with_ngrok"
$ git push heroku master
```

## Usage

Once your app is running with the ngrok buildpack and the `with_ngrok` command, you'll see something like
this in your logs:

```
2015-05-19T16:06:36.530988+00:00 app[web.1]: Listening for transport dt_socket at address: 8998
...
2015-05-19T16:06:37.052977+00:00 app[web.1]: [05/19/15 16:06:37] [INFO] [client] Tunnel established at tcp://ngrok.com:39678
```

Then, from your local machine, you can connect to the auxiliary port using the ngrok URL in the logs. For example:

```sh-session
$ open http://ngrok.com:39678
```

## Customizing

You can customize the aux port like so:

```
$ heroku config:set AUX_PORT=5001
```

You can customize the execution of ngrok by setting the `NGROK_OPTS` config var like so:

```
$ heroku config:set NGROK_OPTS="-subdomain=my-custom-name"
```

You can also add a `.ngrok` to your app.
