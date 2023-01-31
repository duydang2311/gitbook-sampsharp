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

    <pre class="language-csharp" data-title="SpawnSystem.cs"><code class="lang-csharp">[Event]
    private void OnPlayerConnect(Player player)
    {
    <strong>    player.SetSpawnInfo(255, 0, new Vector3(0, 0, 3), 0);
    </strong><strong>    player.Spawn();
    </strong>}
    </code></pre>

    \
    In this [OO](https://en.wikipedia.org/wiki/Object-oriented\_programming) piece of code, we:

    1.  Set the player's spawn info using the method that comes with `Player` class:

        {% code overflow="wrap" %}
        ```csharp
        public void SetSpawnInfo(int team, int skin, Vector3 position, float rotation, Weapon weapon1 = Weapon.None, int weapon1Ammo = 0, Weapon weapon2 = Weapon.None, int weapon2Ammo = 0, Weapon weapon3 = Weapon.None, int weapon3Ammo = 0)
        ```
        {% endcode %}



        > * `team`: 255 or NO\_TEAM according to [this](https://www.open.mp/docs/scripting/functions/SetPlayerTeam).
        > * `skin`: `0` for CJ skin.
        > * `position`: a `Vector3` with `X`, `Y`, `Z` coordinates correspond to `0`, `0`, `3`.
        > * `rotation`: `0`.

        \
        And we leave out the rest parameters that already have default value.
    2. Spawn the player with a very simple `Spawn` method.\

4.  Send a nice greeting message to our player after spawning them.

    <pre class="language-csharp" data-title="SpawnSystem.cs"><code class="lang-csharp">[Event]
    private void OnPlayerConnect(Player player)
    {
        player.SetSpawnInfo(255, 0, new Vector3(0, 0, 3), 0);
        player.Spawn();
    <strong>    player.SendClientMessage(Color.Green, "Hello from SampSharp!");
    </strong>}
    </code></pre>

    \
    From this declaration in the `Player` class:

    ```csharp
    public void SendClientMessage(Color color, string message);
    ```

    \
    To use it, we simply pass these required arguments:\


    > * `color`: The color of the message.\
    >   `Color.Green` returns an instance of struct `Color` that has its `R`, `G`, `B` values defining a green color.
    > * `message`: The text of the message.\
    >   `"Hello from SampSharp!"` is a literal string, which is of course a string.

    \
    It's supposed to be working if we build the current code, run the server and go in the game. However, you won't be spawn and received any message on connecting at all.\
    It turns out that we've never asked SampSharp to load our system on startup before. The fact that our class implements `ISystem` does not matter, if we don't tell SampSharp to look for them, which takes us to the final step.\

5.  Add this line to`Startup.cs` to load all systems in our project assembly.

    <pre class="language-csharp" data-overflow="wrap"><code class="lang-csharp">public void Configure(IServiceCollection services)
    {
    <strong>    services.AddSystemsInAssembly(); // Add all systems which can be found within our project.
    </strong>}
    </code></pre>

    \
    `AddSystemsInAssembly` is a method extension for `IServiceCollection` type, that scans the executing assembly to look for all types that implement `ISystem` interface, then eventually add them as system to the service collection.

Finally, congrats on your first system ever written with the ECS framework!
