## How to Setup Remote Debugger for Docker (Java)
Debugging an application running on Docker can be very frustrating and very verbose because it involves a lot of print statements. Here's an alternative to using print statements that's not as verbose.

This article assumes the following:
1. You're building a Java application using SpringBoot
2. You're using intellIJ as your primary IDE.
3. Your Docker setup is set up.

### The Docker file

To set up your Docker file, do the following:

1. Add the arguments `"-Xdebug"` and `"-Xrunjdwp:server=y,transport=dt_socket,address=4000,suspend=n",` between your `java` and `-jar` arguments in your `ENTRYPOINT` array. Once you've done so, your `ENTRYPOINT` should look something like this.

```
ENTRYPOINT ["java", "-Xdebug", "-Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n", "-jar", "/ilc-service/app/service-ilc-runner-0.1.jar"]`
```
- `-Xdebug` enables debugging capabilities in the JVM.
- `Xrunjdwp` enables the Java Debug Wire protocol with the option specified. These options set the debugger to an attach state (`server=y`), uses socket transport (`transport=dt_socket`), listens for connections at port 8000 `address=8000`, and does not suspend the JVM until the main class loads `suspend=n`.

2. Expose the port you specified as your `address` in your `Xrundwp` argument.
> `EXPOSE 8000`
3. When you run your `docker run` command, specify multiple ports

```
docker run -p <host_port1>:<container_port1> -p <host_port2>:<container_port2>
``````
> B-but I'm using a compose file

Add multiple ports to your compose YAML file.

```
...
    ports:
      - "8000:8000"
      - "8080:8080"
...
```

### IntelliJ Setup

1. Run > Edit Configurations
2. Click the plus sign on the left side of your screen
3. Choose "Remote"
4. Click on "4 items more (irrelevant)..." after not finding "Remote" and choose "Remote"
5. Make sure the settings are as follows:
5. - Debugger mode: Attach to remote JVM
   - Host: localhost
   - Port: Port specified in your `Xrunjdwp` argument.

7. Click OK to save your settings

### Running
Run your Docker container then choose the run settings you just saved in IntelliJ and click the green arrow.
