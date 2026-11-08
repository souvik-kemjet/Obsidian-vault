
![[Pasted image 20261106134636.png]]

The message means **Windows blocked the app because a security policy on your friend’s laptop doesn’t trust one of the files it tried to load**—in this case, `Kemjet.Ilp.BAL.dll`. The screenshot says Visual Studio’s build succeeded, but Windows would not let the app start.

**Is it asking for a digital signature?**  
It’s saying Windows couldn’t verify the publisher of that DLL. A trusted digital signature _may_ satisfy the policy, but the exact requirement depends on the laptop’s Application Control rules. It could instead require an administrator to approve the app or allow its publisher.

**Why is this required?**  
Organizations use Application Control to prevent unapproved or altered programs from running. It’s a Windows/device security setting, not normally a requirement for cloning this repository. A locally built development DLL is often unsigned, so it can be blocked on a tightly managed laptop.

**What should your friend do?**  
Ask their IT administrator to check the Application Control event and confirm the approved way to run development builds. If this is a personal laptop, check with whoever manages its security settings. Don’t disable or bypass the policy just to run the app.


