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

Function to publish the project to Alusus Net. The project has to be built first using the `build`
function before callign this library. This function is called by the `startup` macro.

