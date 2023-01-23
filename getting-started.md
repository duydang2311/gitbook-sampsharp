# Getting started

## Create a SA:MP server directory

There are many SA:MP server versions that you can download and extract to create a SA:MP server directory.

1. Download the latest server version from [https://sa-mp.com/download.php](https://sa-mp.com/download.php) that corresponds with your operating system.
2. Extract it to a directory, e.g. `my-server`.
3.  If there is any redundant directory, like `samp03`, make sure to move its content outside so it looks similar to mine.\


    ```
    my-server
    ├── announce
    ├── filterscripts
    ├── gamemodes
    ├── include
    ├── LICENSE
    ├── npcmodes
    ├── README
    ├── samp-npc
    ├── samp03svr
    ├── scriptfiles
    └── server.cfg
    ```

## Install SampSharp plugin

1. Download the latest SampSharp plugin from [https://github.com/ikkentim/SampSharp/releases](https://github.com/ikkentim/SampSharp/releases).
2.  Extract the zip content to your SA:MP server directory. After this step, your SA:MP server should look like below (I truncated the files that are not from SampSharp).\


    <pre><code>my-server
    ├── filterscripts
    │  ├── empty.amx
    │  ├── empty.pwn
    │  ├── intermission.amx
    │  ├── intermission.pwn
    <strong>├── gamemodes
    </strong><strong>│  ├── empty.amx
    </strong><strong>│  ├── empty.pwn
    </strong><strong>└── plugins
    </strong><strong>   ├── libSampSharp.so
    </strong><strong>   └── SampSharp.dll
    </strong></code></pre>


3.  Last but not least, edit your `server.cfg` to load the plugin. In the `plugins` line, pick just one that matches your OS.\


    <pre data-title="server.cfg"><code>echo Executing Server Config...
    lanmode 0
    rcon_password changeme123
    maxplayers 50
    port 7777
    hostname SA-MP 0.3 Server
    gamemode0 empty 1
    <strong>plugins SampSharp.dll # windows
    </strong><strong>plugins libSampSharp.so # linux
    </strong>announce 0
    query 1
    weburl www.sa-mp.com
    maxnpc 0
    onfoot_rate 40
    incar_rate 40
    weapon_rate 40
    stream_distance 300.0
    stream_rate 1000
    </code></pre>

## Create .NET 6 runtime directory

To be able to run server using SampSharp, a .NET 6 runtime is required.

### Download for Windows

Download a x86 .NET 6 runtime binaries (neither Desktop Runtime nor ASP.NET Core Runtime) from [https://dotnet.microsoft.com/en-us/download/dotnet/6.0](https://dotnet.microsoft.com/en-us/download/dotnet/6.0).

### Download for Linux

Microsoft does not officially provide a x86 runtime for Linux, so you have to find an unofficial equivalent x86 patch.

Download x86 .NET 6 runtime: [https://github.com/Servarr/dotnet-linux-x86/releases/](https://github.com/Servarr/dotnet-linux-x86/releases/).

### Create runtime directory

Create a `runtime` directory in your SA:MP server directory, and extract the runtime binaries to the `runtime` directory.

The layout of the server directory after this step (truncated).

<pre><code>my-server
<strong>└── runtime
</strong><strong>   ├── dotnet
</strong><strong>   ├── host
</strong><strong>   │  └── fxr
</strong><strong>   │     └── 6.0.12
</strong><strong>   ├── LICENSE.txt
</strong><strong>   ├── shared
</strong><strong>   │  └── Microsoft.NETCore.App
</strong><strong>   │     └── 6.0.12
</strong><strong>   └── ThirdPartyNotices.txt
</strong>
</code></pre>

## Create a C# gamemode project

A sample of SampSharp ECS project can be found here: [https://github.com/SampSharp/sample-ecs-grandlarc](https://github.com/SampSharp/sample-ecs-grandlarc)

Alternatively, if you want to create a C# project on your own using the [.NET CLI](https://learn.microsoft.com/en-us/dotnet/core/tools/), try these following steps.

1. Create a `src` directory inside your SA:MP server directory.
2. Make sure your current working directory is located at `src` directory, then create a C# console project with: `dotnet new console -n MyProject`.
3. Configure your `MyProject.csproj` to use .NET 6; install SampSharp nuget packages; specify output path and start program.&#x20;

<pre class="language-xml"><code class="lang-xml">&#x3C;Project Sdk="Microsoft.NET.Sdk">
  &#x3C;PropertyGroup>
    &#x3C;OutputType>Exe&#x3C;/OutputType>
    &#x3C;TargetFramework>net6.0&#x3C;/TargetFramework>
    &#x3C;ImplicitUsings>enable&#x3C;/ImplicitUsings>
    &#x3C;Nullable>enable&#x3C;/Nullable>
    &#x3C;OutputPath>..\..\gamemode&#x3C;/OutputPath>
    &#x3C;StartAction>Program&#x3C;/StartAction>
    &#x3C;StartWorkingDirectory>..\..\&#x3C;/StartWorkingDirectory>
    
<strong>    &#x3C;!-- Linux -->
</strong><strong>    &#x3C;!-- &#x3C;StartProgram>..\..\samp03svr&#x3C;/StartProgram> -->
</strong>    
<strong>    &#x3C;!-- Windows -->
</strong><strong>    &#x3C;!-- &#x3C;StartProgram>..\..\samp-server.exe&#x3C;/StartProgram> -->
</strong>    
  &#x3C;/PropertyGroup>
  &#x3C;ItemGroup>
    &#x3C;PackageReference Include="SampSharp.Entities" Version="0.10.1" />
  &#x3C;/ItemGroup>
&#x3C;/Project>

</code></pre>

Finally, in `MyProject` project directory, run `dotnet restore` to install dependencies for the project.

### Write code, build and run.

I will be using the sample code from [https://github.com/SampSharp/sample-ecs-grandlarc](https://github.com/SampSharp/sample-ecs-grandlarc) with adjustments for minimal working program.

1.  Edit your the program entry file:\


    {% code title="Program.cs" %}
    ```csharp
    using SampSharp.Core;
    using SampSharp.Entities;

    namespace MyProject
    {
        public class Program
        {
            private static void Main()
            {
                // This is the main entry-point of this application.
                // Start SampSharp with the ECS configuration provided by th Startup class.
                new GameModeBuilder()
                    .UseEcs<Startup>()
                    .Run();
            }
        }
    }
    ```
    {% endcode %}


2.  Create a file for startup class at the same location as `Program.cs` with the content:\


    {% code title="Startup.cs" %}
    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using SampSharp.Entities;
    using SampSharp.Entities.SAMP;

    namespace MyProject
    {
        /// <summary>
        /// Represents a class which provides the configuration for the ECS game mode.
        /// </summary>
        public class Startup : IStartup
        {
            public void Configure(IServiceCollection services)
            {
            }

            public void Configure(IEcsBuilder builder)
            {
                // Enable or disable features of ECS or other libraries here.
                builder.EnableSampEvents(); // Enable all stock SA-MP callbacks as events which can be listened to by systems.
            }
        }
    }

    ```
    {% endcode %}


3. In your project directory, run `dotnet build`.\
   You should see the build output in `gamemode` directory in your SA:MP server directory.
4. Execute `dotnet run` to start running the SA:MP server.\
   This should be what your `server_log.txt` looks like after all these steps:

```
SA-MP Dedicated Server
----------------------
v0.3.7-R2, (C)2005-2015 SA-MP Team

[00:12:50]
[00:12:50] Server Plugins
[00:12:50] --------------
[00:12:50]  Loading plugin: libSampSharp.so
[00:12:50] [SampSharp:INFO] Runtime path: .../my-server/runtime/shared/Microsoft.NETCore.App/6.0.12
[00:12:50] [SampSharp:INFO] Game mode path: .../my-server/gamemode/net6.0/MyProject.dll
[00:12:50]   Loaded.
[00:12:50]  Loaded 1 plugins.

[00:12:50]
[00:12:50] Filterscripts
[00:12:50] ---------------
[00:12:50]   Loaded 0 filterscripts.

[00:12:50]   Filterscript 'empty.amx' loaded.
[00:12:50]
[00:12:50] SampSharp Plugin
[00:12:50] ----------------
[00:12:50] v0.10.0, (C)2014-2022 Tim Potze
[00:12:50]
[00:12:50] [SampSharp:INFO] Initializing .NET runtime...
[00:12:50] [SampSharp:INFO] Starting game mode host...
[00:12:50] [SampSharp:INFO] Game mode host running.
[00:12:50] Number of vehicle models: 0
```
