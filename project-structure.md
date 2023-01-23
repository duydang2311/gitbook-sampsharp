# Project structure

Designing a project structure is one of the very first thing that comes to my mind when starting on any new project.

Although each type of project has its own common structure, e.g MVC, MVVM, MVP... you'll easily recognize that most of them follows the layered style. In this [SampSharp Grandlarc](https://github.com/SampSharp/sample-ecs-grandlarc/tree/main/src/GrandLarceny) project using the ECS pattern, this is how the layered style looks like in practical:

```bash
GrandLarceny
├── Components # component classes...
├── Systems # system classes...
├── Services # service classes...
└── Repositories # repositories classes...
```

The whole project is broken up into multiple layers with purpose. This structure works for small projects, but not for a SA:MP gamemode project where you have a bunch of files that belong to similar layers.

In reality, you are likely need to break up your project into even more small pieces. The approach I'm following is to group the same functional areas first, then apply the layered style within.

For better demonstration, have a quick glance over a brief of my current structure for a Role-Play project.

```
Server
├── Account
│  ├── Components
│  │  └── AccountComponent.cs
│  ├── Extensions
│  │  └── IServiceCollectionExtensions.cs
│  ├── Models
│  │  └── AccountModel.cs
│  └── Systems
│     ├── Authentication
│     │  ├── AuthenticatedEvent.cs
│     │  ├── AuthenticationSystem.cs
│     │  └── IAuthenticatedEvent.cs
│     ├── Login
│     │  ├── ILoggedInEvent.cs
│     │  ├── LoggedInEvent.cs
│     │  └── LoginSystem.cs
│     └── SignUp
│        ├── ISignedUpEvent.cs
│        ├── SignUpEvent.cs
│        └── SignUpSystem.cs
├── Character
│  ├── Components
│  ├── Services
│  └── Systems
└── Chat
   ├── Components
   ├── Extensions
   ├── Middlewares
   ├── Services
   └── Systems
```

If a top-level namespace gets too big, further split will eventually be done, although I haven't needed to break up any of them twice so far.

The purpose of some common layers inside any top-level namespace is as follows:

1. Components\
   This layer contains component classes which inherit `SampSharp.Entities.Component`.
2. Extensions\
   This layer contains static classes that have method extensions. I personally put method extensions here for `SampSharp.Entities.IEcsBuilder`, `Microsoft.Extensions.DependencyInjection.IServiceCollection` for easier use on startup class.
3. Models\
   This layer I use to contain model classes for the EF core.&#x20;
4. Systems\
   This layer contains system classes that handle the logic of our gamemode. Knowing that a system can fire its own event for others to use, so putting events along their related system is reasonable to me.

Finally, you are totally free on what you want your project structure to be like. If you haven't thought of any, feel free to consider this as a reference for your own structure design.
