# QA Copilot Unreal Demo

This is a demo to show how to use the [WYVRN Unreal SDK](https://www.wyvrn.com/) to communicate with Razer AI QA Copilot. This project was made with Unreal Engine 5.5.4.

## How to Use

1. Make sure MSVC is installed. Preferred compiler version for Unreal Engine 5.5.4 is v14.38-17.8. Not tested on Clang.
2. Make sure Razer Chroma is installed and running.
3. Log in to Razer AI QA Copilot
4. Create Game project with game design document in "Content/GDD/Unreal.Shooter.GDD.pdf" and ensure the Game Title of the project is set to "RazerUnrealQADemo".
5. Go to the settings page to download and install the Razer AI Service.
6. Download and replace the service configuration file.
7. Open QACopilotDemo.uproject.
8. Press play.
9. You should see InitSDK_SUCCESS in the top-left corner along with some other messages.
10. Go through the level. You should see some other SUCCESS messages. Bug should be reported in the QA Copilot web dashboard.

## Razer AI QA Copilot events

This is a list of blueprints used in the demo along with an explanation of events triggered from these blueprints. Events are sent using the SetEventName function. Events are **bolded** and variables are enclosed in "{}".

### BP_ThirdPersonGameMode

- InitSDK is called at the start. 

- After a successful init, **GameSessionStart** is sent. Currently, InitSDK and **GameSessionStart** is triggered from the constructor for the game mode because the demo only has one level. Also, so that **GameSessionStart** is triggered before anything else like **PlayerSpawned;Player;{PlayerId};Room;{RoomId}**.

### BP_ThirdPersonCharacter

- **PlayerSpawned;Player;{PlayerId};Room;{RoomId}** when the Player is spawned. The demo level is divided into 4 rooms so we have RoomId, but this might not be applicable to other games. 

- **LevelStarted;Level;{LevelId}** is also sent at when the player spawns because the player only has one life in the demo and the level is restarted if the player dies.

- **CurrentLocation;Player;{PlayerId};Location;{Location}** is sent every second.

- **FiredWeapon;Player;{PlayerId};Weapon;{WeaponId};CurrentAmmo;{Ammo}** when player fires his weapon. Player only has 1 magazine in the demo and the ammo display is (Ammo in Magazine) / (Max Ammo).

- **ReceivedDamage;Player;{PlayerId};Damage;{Damage};CurrentHealth;{Health}** when player receives damage.

- **OutOfHealth;Player;{PlayerId}** when player runs out of health.

- **OutOfTime;Player;{PlayerId}** when player runs out of time. In the demo, players only have a limited amount of time to complete the level.

- **IsMoving;Player;{PlayerId};MovingState;{MovingState}** when player stops or starts moving.

### BP_AI_002

- Enemies spawned will send the **BotSpawned;Player;{PlayerId};Room;{RoomId}** event. 

- **ReceivedDamage;Player;{PlayerId};Damage;{Damage};CurrentHealth;{Health}** when enemies receive damage. Currently, enemies will continue to receive damage after being killed, so if player shoots a dead enemy, this will trigger a bug report for QA Copilot.

- **BotDead;Player;{PlayerId};CurrentRoundTimer;{TimeLeft}** when enemy runs out of health. 

### BP_RoomCheck

- **EnteredLocation;Player;{PlayerId};Room;{Room}** when player enters a room.

- **LeftLocation;Player;{PlayerId};Room;{Room}** when player exits a room.

### BP_Healthpickup

- **PickUpHealth;Player;{PlayerId};CurrentHealth;{Health}** when player picks up health.

### BP_AmmoPickup

- **PickUpAmmo;Player;{PlayerId};CurrentAmmo;{Ammo}** when player picks up ammo.

### BP_Enggamepickup and WP_Pause

- **GameSessionEnd;Player;{PlayerId}** when player completes the game by picking up the Endgame item or quits via the pause menu. When this happens, first we send this event and then we call UninitSDK after a short delay, and finally we quit the game. If there isn't a delay between SetEventName and UninitSDK, then the event won't be sent.

### Events in table form
These events are specific to this demo and probably won’t work for other games even if similar.

| Event | Purpose | Found in Blueprint |
| ---   | ---     | ---                |
| GameSessionStart | Start of a gamesession. Should be sent first before any other event. This will reset certain cached game states. | BP_ThirdPersonGameMode |
| PlayerSpawned;Player;{PlayerId};Room;{RoomId} | To signify a player has spawned. This will reset the cached state for the {PlayerId}. | BP_ThirdPersonCharacter |
| BotSpawned;Player;{PlayerId};Room;{RoomId} | To signify a bot/enemy has spawned. This will reset the cached state for the {PlayerId}. | BP_AI_002 |
| LevelStarted;Level;{LevelId} | To signify a level has started. This will reset the cached state for the {LevelId}. | BP_ThirdPersonCharacter |
| CurrentLocation;Player;{PlayerId};Location;{Location} | Updates the location of {PlayerId}. Location is in the “X= Y= Z=” format provided by Unreal. | BP_ThirdPersonCharacter |
| FiredWeapon;Player;{PlayerId};Weapon;{WeaponId};CurrentAmmo;{Ammo} | Updates the ammo count for {WeaponId} held by {PlayerId}. | BP_ThirdPersonCharacter |
| ReceivedDamage;Player;{PlayerId};Damage;{Damage};CurrentHealth;{Health} | Updates HP for {PlayerId} to {Health}. | BP_ThirdPersonCharacter<br>BP_AI_002 |
| OutOfHealth;Player;{PlayerId} | Signifies that {PlayerId} is dead. | BP_ThirdPersonCharacter |
| OutOfTime;Player;{PlayerId} | Signifies that {PlayerId} is out of time and has failed to completed the level. | BP_ThirdPersonCharacter |
| IsMoving;Player;{PlayerId};MovingState;{MovingState} | Updates whether {PlayerId} is moving or not. | BP_ThirdPersonCharacter |
| BotDead;Player;{PlayerId};CurrentRoundTimer;{TimeLeft} | Signifies that the bot/enemy {PlayerId} is dead. Updates time left to {TimeLeft} | BP_AI_002 |
| EnteredLocation;Player;{PlayerId};Room;{Room} | Updates {PlayerId} room location to {Room} | BP_RoomCheck |
| LeftLocation;Player;{PlayerId};Room;{Room} | Clears room location info {PlayerId} | BP_RoomCheck |
| PickUpHealth;Player;{PlayerId};CurrentHealth;{Health} | Updates HP for {PlayerId} to {Health} | BP_Healthpickup |
| PickUpAmmo;Player;{PlayerId};CurrentAmmo;{Ammo} | Updates current ammo for {PlayerId} to {Ammo} | BP_AmmoPickup |
| GameSessionEnd;Player;{PlayerId} | End of the gamesession. | BP_Enggamepickup<br>WP_Pause |

## Packages Used

1. [Animation Starter Pack](https://www.fab.com/listings/98ff449d-79db-4f54-9303-75486c4fb9d9)
2. [FPS Weapon Bundle](https://www.fab.com/listings/8aeb9c48-b404-4dcd-9e56-1d0ecedba7f5)
3. [Modular SciFi Season 1 Starter Bundle](https://www.fab.com/listings/86913335-3c75-42bf-8404-54fe9d9d7396)