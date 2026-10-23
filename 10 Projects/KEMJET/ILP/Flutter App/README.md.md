

# User App
1. [[User App Full Endpoint Construct]]


To make the `flutterfire` command work normally from any terminal session, you need to add its location to your Windows PATH.

1. **Copy this path:** Open File Explorer and verify that this folder exists (replace `YourUsername` with your actual Windows username):  
    `C:\Users\YourUsername\AppData\Local\Pub\Cache\bin`
2. **Open Environment Variables:** Press the `Windows Key`, type **"env"**, and select **Edit the system environment variables**.
3. **Edit Path:** Click **Environment Variables...** at the bottom right. Under _User variables_, select **Path** and click **Edit...**.
4. **Add New:** Click **New** and paste the path from Step 1.
5. **Save and Restart:** Click **OK** on all windows. **You must completely close and reopen VS Code or your terminal** for the changes to take effect.





---
1. **Install the FlutterFire CLI**

	Run this command to download and install the CLI tool globally:
	```
	dart pub global activate flutterfire_cli
	```
	


---
## Third Part Services used by this Project

1. [[firebase|Firebase]]
2. 

