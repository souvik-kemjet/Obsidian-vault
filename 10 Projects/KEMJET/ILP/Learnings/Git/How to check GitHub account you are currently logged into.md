

1. **In Your Web Browser**
Open [GitHub](https://github.com/) in your browser. Look at the **top-right corner** of the page
- Your **profile picture** will be visible.
- Click your profile picture to open the dropdown menu. Your **GitHub username** will be explicitly displayed at the very top of that menu. 

2. **In Your Terminal via SSH**

If you communicate with GitHub over SSH, you can test your connection to see which account your current SSH key belongs to. Run the following command:

```bash
ssh -T git@github.com
```

- **Expected Output:** `Hi username! You've successfully authenticated, but GitHub does not provide shell access.`
- The `username` shown in the message is the account currently authenticated to your active SSH key.

3. In Your Terminal via Git Config (Commit Identity)

To see what name and email Git is using to sign your commits, check your local or global Git configurations. 

- To check the **active repository configuration**, run:
    ```bash
    git config user.name
    git config user.email
    ```

 
- To check your **system-wide global default**, run: 
    ```bash
    git config --global user.name
    git config --global user.email
    ```

_(Note: As users on [Stack Overflow](https://stackoverflow.com/questions/46941346/how-to-know-the-git-username-and-email-saved-during-configuration) note, local repository configurations override global ones. Keep in mind that these settings only label your commits; they do not dictate your actual login credentials for pushing code)._

4. **In Your Terminal via GitHub CLI (`gh`)**

If you use GitHub’s official command-line tool (`gh`), you can check your active login status directly by running:

```bash
gh auth status
```

- **Expected Output:** This will explicitly return the username of the logged-in account and your active authentication method (HTTPS or SSH protocol).

5. **In GitHub Desktop**

If you are using the desktop application:

- **macOS:** Go to **GitHub Desktop** > **Preferences** > **Accounts**. 
- **Windows:** Go to **File** > **Options** > **Accounts**.
- Your logged-in GitHub account username will be displayed right at the top of the **Accounts** tab.


![[Pasted image 20261016181057.png]]