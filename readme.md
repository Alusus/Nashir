# Nashir
[[عربي]](readme.ar.md)

A library that enables buildign and publishing web apps. It's uses a driver based design to enable
supporting multiple publishing methods or publishing targets. There are currently two drivers, one
for publishing on AlususNet service, and the other for publishing using SSH protocol.

## Usage

These steps assumes that you already have an app built using WebPlatform and you want to publish it.

* Add the Nashir library to the project, including the requested driver:

```
import "Apm";
Apm.importFile("Alusus/Nashir", { "Nashir.alusus", "Drivers/AlususNet.alusus" });
```

or to use SSh driver:

```
import "Apm";
Apm.importFile("Alusus/Nashir", { "Nashir.alusus", "Drivers/Ssh.alusus" });
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
  info during the process. The arg of the `initialize` macro is the instantation statement of the
  driver used for publishing. Instantiating the driver may require providing arguments. For example
  the Alusus Net driver requires two args: the name of the project and the port number at which the
  server is initialized. The project name is used to set the name of the published project on Alusus Net.

```
Nashir.verbose = false;
Nashir.initialize[Nashir.AlususNetDriver(String("&lt;ProjectName&gt;"), 8000)];
```

* Set the the list of assets and build dependencies.

```
Nashir.assets.add({ String("Assets") });
Nashir.dependencies.add(WebPlatform.getBuildDependencies());
```

* Call the startup macro, giving it the name of the main function (the
  function that starts the server).

```
Nashir.startup[startServer];
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

## Reference

### createStartExe

Helper macro to create an executable's entry function. This function will set the UI and main assets
paths before calling the main function. This macro takes two arguments:
* name: The name of the function to be created.
* startJit: The name of the function to call after setting the paths. This would be the function
  that starts the server, which is usually the function called to start the server when running
  in JIT mode.

### build

Function to build the project. This will compile the code and generate the executable as well as
copy the needed asset files to the build directory. This function is called by the `startup` macro.

```
func build [start: ast_ref] (assets: Array[String], deps: Array[String])
```

Arguments:
* `start`: Template arg. An AST reference to the program's entry point function.
* `assets`: The list of asset files/folders. These assets are assumed to be relative to the current
  directory and will be copied to the `Build/` directory with the same relative path.
* `deps`: The list of all shared library dependencies of the project.

### publish

Function to publish the project usign the driver selected during initialization. The project has to
be built first using the `build` function before callign this library. This function is called by
the `startup` macro.

### Drivers

#### AlususNetDriver

For publishing the project to Alusus Net. To initialize:

```
AlususNetDriver(projectName: String, servicePort: Int);
```

The first argument is the name of the project on Alusus Net.
The second argument is the port number the project uses when initializing the HTTP server.

### SshDriver

Used to publish the project using SSH to any service supporting SSH. To initialize the driver:

```
SshDriver();
SshDriver(
    username: String,
    host: String,
    port: Int,
    serverPath: String,
    serviceName: String
);
```

The first format let the driver ask the user in the console for the information needed for
publishing, while the second format provides those info programmatically.

The first three arguments are the info used for the SSH protocol. The fourth argument is
the path on the server where the uploaded files will be stored.

The fifth argument is optional, and it's the name of the systemd service used to run
the project. If this argument is provided that systemd service will be restarted
after publishing.

