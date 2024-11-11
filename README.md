# [UnrealBits] Save System Demo
A demo project for the UBSaveSystem plugin. The plugin is required to compile the demo project

## Plugin Features
<ol>
    <li>Automatic save backups: Every time the game is saved, a backup is created and will be used if the latest save can't be loaded. The amount of backups can be changed in the project settings</li>
    <li>Easy saving/loading with multiple callbacks via an interface:</li>
    <ul>
        <li>Pre Loaded: A callback for when loading save data is about to start</li>
        <li>Post Loaded: A callback for when the save data was loaded (includes a reference to the save data)</li>
        <li>Pre Saved: A callback for when saving the data is about to start. This allows you to update information in the save data before saving</li>
        <li>Post Saved: A callback for when the save data was updated/saved (includes a reference to the save data)</li>
        <li>Save Game Deleted: A callback for when the save data is deleted. This allows you to reset actors to their default state if needed</li>
    </ul>
    <li>Checkpoint system: All variables from actors that are marked as SaveGame will automatically be saved (either to the save game file or in-memory). The values will automatically be restored when loading the game or can be manually saved/restored for specific actors. There's also a function to clear the checkpoint data</li>
    <li>Multiple save slots: The plugin supports multiple save slots with their own backups and checkpoints</li>
    <li>Full Blueprint and C++ support</li>
</ol>

<i>* All these features are built on top of the Unreal Engine save system and can be integrated into existing projects without rewriting a lot of code</i>

## Requirements
1. The UnrealBits Save System Plugin
2. Unreal Engine 5.0 or newer

## Setup
<ol>
    <li>Download the latest version of the plugin</li>
    <li>Create a plugin folder in the root of your project</li>
    <li>Copy the UBSaveSystem folder to the plugin folder in your project</li>
    <li>Restart Unreal Engine</li>
    <li>Click on Edit > Plugins</li>
    <li>Make sure the plugin is enabled</li>
</ol>

## Getting Started
### Project Settings
You can access the plugin settings underneath the project settings. After enabling the plugin, you should see a new UnrealBits section in the project settings. The follow settings can be changed:
<ul>
    <li><code>Automatically Save Actor Properties:</code> The plugin can automatically save variables from actors whenever you save the game. These values will automatically be applied whenever you load the game. You can use this setting to include the values in the save game, keep them in memory or disable the feature (you can still manually save/load the data even if this is disabled)</li>
    <li><code>Save Game Name:</code> The name of the save game files</li>
    <li><code>Max Save Backups:</code> The amount of backups to keep when saving the game</li>
</ul>

### UnrealBits Save Game
The plugin requires that you use the <code>UnrealBitsSaveGame</code> as the base class for all your save games. This is based on the standard <code>USaveGame</code> class and you should be able to just change the parent class on your existing save games. The only difference is the <code>ActorProperties</code> field which is used for checkpoints

### UnrealBits Saver Interface
The plugin includes an <code>UnrealBitsSaver</code> interface. This is used to notify actors whenever the game is saved/loaded. This interface is also used automatically save/load variables for actors that implements the interface. The following functions are available:
<ul>
    <li><code>GetUniqueId:</code> Returns an ID for the actor. This value is used to save/load all specified variables for the actor. You don't need to override this function. If it returns an empty string, then the path name of the actor in the level will be used. Overriding this function allows you to save/restore checkpoints across multiple levels</li>
    <li><code>OnSaveGamePreLoaded:</code> This function is called before the game is loaded. This can be used to display a saving widget or change something in the world while the game is saved</li>
    <li><code>OnSaveGamePostLoaded:</code> This function is called after the game is loaded. It includes a reference to the latest save game</li>
    <li><code>OnSaveGamePreSaved:</code> This function is called before the game is saved. It includes a reference to the latest save game. You can also return an updated version of the save game if you want to change some values. Returning an updated save game is optional</li>
    <li><code>OnSaveGamePostSaved:</code> This function is called after the game is saved and allows you to update the state of the actor based on the values from the save game. A reference to the updated save game is included</li>
    <li><code>OnSaveGameDeleted:</code> This function is called after the save game is deleted and allows you to reset the state of the actor if needed</li>
</ul>

### Saving the game
You can save the game by calling the static <code>SaveGame</code> function inside the <code>USaveManagerUtils</code> class. You are required to pass in a world context (C++ only), the <code>UnrealBitsSaveGame</code>, the index of the save slot and a user index integer. This will automatically create a checkpoint (if enabled in the project settings), create a backup of the previous save file and notify all actors implementing the <code>UnrealBitsSaver</code> interface

### Loading the game
You can load the game by calling the static <code>LoadGame</code> function inside the <code>USaveManagerUtils</code> class. You are required to pass in a world context (C++ only), the index of the save slot and a user index integer. You can also enable/disable notifying actors about this process by setting the <code>NotifyLoad</code> flag

## Checkpoints
Checkpoints are automatically saved/loaded whenever you save/load the game (if enabled in the project settings). But you can also manually handle checkpoints if you prefer

### In-Memory Checkpoints
Checkpoints can be kept in memory and be excluded from the save file. You can manually save/load checkpoints by calling the static <code>SaveActorPropertiesToMemory</code> and <code>LoadActorPropertiesFromMemory</code> functions inside the <code>USaveManagerUtils</code> class. Both of these function only require a reference to the actor. In-Memory checkpoints can be deleted by calling the static <code>DeleteActorPropertiesFromMemory</code> function inside the <code>USaveManagerUtils</code> class

### Include checkpoints in save game
Checkpoints can also be included in the save game. You can manually save/load checkpoints by calling the static <code>SaveActorPropertiesToFile</code> and <code>LoadActorPropertiesFromFile</code> functions inside the <code>USaveManagerUtils</code> class. Both of these function require a reference to the actor and the <code>UnrealBitsSaveGame</code> which should be updated

### Including variables in checkpoint data
Variables can automatically be included in the checkpoint data:
1. Make sure you've implemented the <code>UnrealBitsSaver</code> interface in the actor
2. Select the variable you want to include
3. Expand the Advanced section in the details panel
4. Enable the SaveGame flag

## Running the demo project
1. Clone the project
2. Add the plugin to the project (check the Setup section for a step-by-step guide on how to add the plugin)
3. Open the project

The demo project includes a basic level that showcases the In-Memory checkpoint system. This covers saving data, loading data, tracking variables and deleting save data

## Support
If you need any help integrating the plugin or find any bugs, please let us know at <a href="mailto:officialunrealbits@gmail.com">officialunrealbits@gmail.com</a>