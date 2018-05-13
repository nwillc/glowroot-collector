# Glowroot Server

This project contains a Docker Compose file and friends to, using public images,
create a standalone [Glowroot](https://glowroot.org/) *collector* server.

While Glowroot is not a full featured [APM](https://en.wikipedia.org/wiki/Application_performance_management)
tool it is:

 - Simple to employ
 - Open source
 - Can provide a great deal of useful information within minutes
 
 
## Starting This Server

To start up the server (for sake of the example `gserver`), on gserver, a machine with 
`docker-compose` and access to `docker hub` just:

```bash
docker-compose -f docker-compose.yml up
```

### Exposd Ports

The server setup will expose:

 - `4000` For the Glowroot UI
 - `8181` For the collector endpoint
 
If you need to change these tinker with the `docker-compose.yml` left hand ports listed under the glowroot service.

## Accessing The Server

Once docker has brought up the server (glowroot and it's dependent Cassandra) you should be able
to target `http://gserver:4000` to get the the Growroot collector UI.

## Collecting Data from an App

This collector server, as it name implies, needs data to be pushed into it from applications. To
push data to `gserver`'s collector you need to do the following:

 - Download the [glowroot distribution](https://github.com/glowroot/glowroot/releases/download/v0.10.9/glowroot-0.10.9-dist.zip)
 and unpack it in a location accessible to the running app, for example `/opt/glowroot`.
 
 - You should now have `/opt/glowroot/` containing the distribution, is `/opt/glowroot/glowroot.jar` present?
 
 - Ensure the application you will run can write to `/opt/glowroot`.
 
 - Create the file `/opt/glowroot/glowroot.properties`, the only required content is:
 
 ```properties
collector.address=http://gserver:8181
```

 - Now, when running your application add the following JVM arguments:
 
 ```bash
 -javaagent:/opt/glowroot/glowroot.jar -Dglowroot.agent.id=some_app_identifying_name
 ```

 - Run the app.  The app should detect the agent, find the properties, create some additional files in `/opt/glowroot`
 and begin pushing data to the collector. 