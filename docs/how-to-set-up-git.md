# How to Set Up Git with GitHub Credential Helper

This guide explains how to configure Git to use a credential helper for authenticating with GitHub using a personal access token.

## Step 1: Create the Credentials File

Create a file named `.git-credentials` in the root folder of your project. This file will store your GitHub credentials.

Ensure that `.git-credentials` is added to your `.gitignore` file to prevent it from being committed to version control.

## Step 2: Format the Credentials File

The `.git-credentials` file should contain your GitHub credentials in the following format:

```
https://<user-name>:<token>@github.com
```

Replace `<user-name>` with your GitHub username and `<token>` with your personal access token.

## Step 3: Configure the Credential Helper

Run the following command in your terminal to configure Git to use the credential helper that reads from the `.git-credentials` file:

```
git config --local credential.helper "store --file .git-credentials"
```

## Step 4: Set the Remote URL

Set or modify your Git remote URL to include your username. Use the following format:

```
https://<user-name>@github.com/<repo-path>.git
```

Replace `<user-name>` with your GitHub username and `<repo-path>` with the path to your repository (e.g., `username/repo-name`).

To add the origin remote:

```
git remote add origin https://<user-name>@github.com/<repo-path>.git
```

To edit the origin remote:

```
git remote set-url origin https://<user-name>@github.com/<repo-path>.git
```

This setup allows Git to automatically use your stored credentials for authentication with GitHub.
