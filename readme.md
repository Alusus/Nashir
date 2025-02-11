# Nashir
[[عربي]](readme.ar.md)

A library to facilitate building and deploying projects. This library is designed to allow the
addition of drivers for various hosting services. Currently, two drivers are available: one
specifically for the Alusus Net service, and another general-purpose driver that relies on
the SSH protocol for project deployment.

## Usage

These steps assume you already have a web application and want to publish it.

* Add the Nashir library to the project, including the requested driver:

```
import "Apm";
Apm.importFile("Alusus/Nashir", { "Nashir.alusus", "PublishDrivers/AlususNet.alusus" });
```

or to use SSh driver:

```
import "Apm";
Apm.importFile("Alusus/Nashir", { "Nashir.alusus", "PublishDrivers/Ssh.alusus" });
```

* Configure the Nashir library at the end of your program using the setup macro. You can set the
  verbose value to 1 to get more detailed information during project deployment. The macro takes
  a single argument, which is an object of the Config class. This macro will handle configuring
  the Nashir settings and the ProgArg library settings, which Nashir uses to process user inputs.
  If the program uses the Web Platform library, this macro will also create the two main functions
  responsible for starting the program and running the server, unless the user provides these
  functions themselves. Nashir requires two separate functions: one to run the server directly
  without building, and another to serve as the program's entry point in case of building.

```
Nashir.verbose = false;
Nashir.setup[Nashir.Config().{
    projectName = "Chat";
    projectVersion = "v1";
    serverPort = 8000;
    assets.add(String("Assets"));
    publishDriver = Nashir.AlususNetPublishDriver();
}];
```

* Nashir relies on the ProgArg library to parse program arguments. After calling the setup macro,
  Nashir will have completed the initialization of commands in ProgArg, and you can now ask ProgArg
  to process user inputs.

```
ProgArg.parse(2);
```

* After finishing these steps you'll be able to run the app directly:

```
alusus main.alusus start
```

And you can build an executable app:

```
alusus main.alusus build
```

And you can publish the app to AlususNet using the `publish` command:

```
alusus main.alusus publish
```

When calling the publish command, the library will build an executable version of the project,
then prompt you to log in to your Alusus Net account, upload the project to the server, and
run it automatically. Follow the instructions that appear to complete the deployment.

## Reference

### Config

The class contains the following variables:

* `projectName: String

* `projectVersion: String`
* `serverPort: Int`
* `serverOptions: Array[CharsPtr]` - This parameter is used to specify the Http server options if
  we ask Nashir to create the two main functions.
* `webPlatformMainAssetsPath: String` - The path to the main assets required by the Web Platform
  library. Ignored if Nashir is not asked to create the two main functions.
* `webPlatformUiEndpointsPath: String` - The path to the UI endpoints files required by the Web
  Platform library. Ignored if Nashir is not asked to create the two main functions.
* `assets: Array[String]` - A list of paths to resources used by the program. These resources will
  be included with the project when building and deploying.
* `dependencies: Array[String]` - A list of libraries required by the project during building.
* `publishDriver: SrdRef[PublishDriver]` - The driver used to publish the project.
* `serverModules: ref[TiObject]` - A reference to the module or modules required by the Web Platform
  during the project's execution and building.
* `startJit: ref[TiObject]` - A reference to the server startup function for direct execution. If
  this function is not provided and the project uses the Web Platform, Nashir will create the
  function itself.
* `startExe: ref[TiObject]` - A reference to the program entry point function used when creating an
  executable version of the project. If this function is not provided and the project uses the Web
  Platform, Nashir will create the function itself.

### build

Function to build the project. This will compile the code and generate the executable as well as
copy the needed asset files to the build directory. This function is called by the `startup` macro.

```
func build [start: ast_ref, modules: ast_ref] ()
```

Arguments:
* `start`: Template arg. An AST reference to the program's entry point function.
* `modules`: A template parameter pointing to the module or modules containing the server endpoints.

### publish

A function to publish the project using the driver specified during initialization. The project must
be built using the `build` function before calling this function. This function is called automatically
when requesting project deployment using Nashir's standard method.

### Drivers

#### AlususNetDriver

For publishing the project to Alusus Net. To initialize:

```
AlususNetDriver();
```

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

