# Nashir
[[عربي]](readme.ar.md)

A library that enables buildign and publishing web apps to AlususNet hosting service.

## Usage

These steps assumes that you already have an app built using WebPlatform and you want to publish it.

* Add the Nashir library to the project:

```
import "Apm";
Apm.importFile("Alusus/Nashir");
```

* Create a main function for starting the web server. Make sure to run the server on port 8000 and
  make sure to wait after starting the server so that the program doesn't quit immediately.

```
func startServer {
    Console.print("Starting server on port 8000...\n");
    startServer({ "listening_ports", "8000", "static_file_max_age", "0" });
    Console.print("Server is running.\n");
    while 1 System.sleep(1000000);
}
```

* Initialize the Nashir library in your project. You can set the `verbose` value to true to get more
  info during the process.

```
Nashir.verbose = false;
Nashir.initialize[];
```

* Call the startup macro after the initialize call, giving it the name of the main function (the
  function that starts the server), an array with paths for assets needed by the app, and an
  array with the list of build dependencies.

```
Nashir.startup[
    startServer,
    Array[String]({ String("Assets") }),
    WebPlatform.getBuildDependencies()
];
```

* After finishing these steps you'll be able to run the app directly:

```
alusus main.alusus
```

And you can build an executable app:

```
alusus main.alusus build
```

And you can publish the app to AlususNet using the `publish` command:

```
alusus main.alusus publish
```

When you run the publish command the library will request the username and password for your AlususNet
account and will then publish your app and run it. Follow the instructions to complete the publishing.

