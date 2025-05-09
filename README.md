# QA Copilot Unreal Demo

This is a demo to show how to use the [WYVRN Unreal SDK](https://www.wyvrn.com/) to communicate with Razer AI QA Copilot. This project was made with Unreal Engine 5.5.4.

## How to Use

1. Make sure MSVC is installed. 
2. Make sure Razer Chroma is installed and running.
3. Log in to Razer AI QA Copilot
4. Create Game project with game design document in "Content/GDD/Unreal Shooter GDD.pdf" and ensure the Game Title of the project is set to "RazerUnrealQADemo".
5. Go to the settings page to download and install the Razer AI Service.
6. Download and replace the service configuration file.
7. Open QACopilotDemo.uproject.
8. Press play.
9. You should see InitSDK_SUCCESS in the top-left corner along with some other messages.
10. Go through the level. You should see some other SUCCESS messages. Bug should be reported in the QA Copilot web dashboard.