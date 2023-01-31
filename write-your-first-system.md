# Write your first system

It's time to write your first system with the SampSharp ECS framework that spawns the player on connection and sends them a greeting!

1.  Create a namespace for our system, a system layer and a directory for our system.

    ```bash
    Project # project root
    └── Player # top-level namespace
       └── Systems # system layer namespace
          └── Spawn # system sub-layer namespace
             └── SpawnSystem.cs # system class
    ```


2.  Create a `SpawnSystem` class:

    {% code title="SpawnSystem.cs" %}
    ```csharp
    using SampSharp.Entities;
    using SampSharp.Entities.SAMP;

    namespace Project.Player.Systems.Spawn; // identical with directory layout

    // class name should be identical with the file name
    public class SpawnSystem : ISystem
    {
    	[Event]
    	private void OnPlayerConnect(Player player)
    	{

    	}
    }
    ```
    {% endcode %}

    \
    Let's have a quick breakdown on what we've had so far:

    * All system classes must implement the `ISystem` interface, which is provided by the ECS framework for them to be recognized as a system by SampSharp on startup.
    * Similar to how we create a `OnPlayerConnect` callback in Pawn, in SampSharp, we declare a method with identical name (optional) and similar parameters, then annotate it with the `Event` attribute.

    After this step, we succesfully created a method (or event handler) that is invoked when a player connects to the server.\

3.  Write the code for spawning the player.

    {% code title="SpawnSystem.cs" %}
    ```csharp
    private void OnPlayerConnect(Player player)
    {
        player.SetSpawnInfo(255, 0, new Vector3(0, 0, 3), 0);
        player.Spawn();
    }
    ```
    {% endcode %}

    \
    In this [OO](https://en.wikipedia.org/wiki/Object-oriented\_programming) piece of code, we:

    1.  Set the player's spawn info with this method in the `Player` class:\
        `SetSpawnInfo(int team, int skin, Vector3 position, float rotation, Weapon weapon1 = Weapon.None, int weapon1Ammo = 0, Weapon weapon2 = Weapon.None, int weapon2Ammo = 0, Weapon weapon3 = Weapon.None, int weapon3Ammo = 0)` .

        * `team`: 255 or NO\_TEAM according to [this](https://www.open.mp/docs/scripting/functions/SetPlayerTeam).
        * `skin`: `0` for CJ skin.
        * `position`: a `Vector3` with `X`, `Y`, `Z` coordinates correspond to `0`, `0`, `3`.
        * `rotation`: `0`.

        We can leave out the rest parameters that already have default value.
    2. Spawn the player with the `Spawn` method.
4.  Last but not least, send a greeting message to our player after spawning them.

    {% code title="SpawnSystem.cs" %}
    ```csharp
    private void OnPlayerConnect(Player player)
    {
        player.SetSpawnInfo(255, 0, new Vector3(0, 0, 3), 0);
        player.Spawn();
        player.SendClientMessage(Color.Green, "Hello from SampSharp!");
    }
    ```
    {% endcode %}

    \
    We just simply pass what is required to the argument according to this definition:\
    `SendClientMessage(Color color, string message)`

    * `color`: `Color.Green` returns an instance of type `Color` that has its `R`, `G`, `B` values defining a green color.
    * `message`: `"Hello from SampSharp!"` is a literal string, which is of course a string.
5. Build the code, run the server, go in-game and test what we've done after all these steps.
