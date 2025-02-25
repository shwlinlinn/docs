---
title: Persisting environment variables and temporary files
intro: You can configure custom environment variables so that they are set to the same value every time you open a codespace. You can also ensure that temporary files are not deleted when a codespace stops.
versions:
  fpt: '*'
  ghec: '*'
type: how_to
topics:
  - Codespaces
  - Fundamentals
  - Developer
shortTitle: Persist variables and files
---

## Setting persistent environment variables

You can set persistent custom environment variables in multiple ways, depending on which codespaces, repositories, or users you want the variables to be available to.

For all the methods of setting custom variables listed below, you can access the custom variable in your codespace by using syntax like `echo $VARNAME`.

### For a single codespace

You can set the value of the environment variable in the `~/.bashrc` file, or in an equivalent configuration file if you are not using the Bash shell. For example, add the statement `VARNAME=value`.

After you save the change to this file, the value will be set the next time you open the codespace, or you can set it immediately by using a command such as `source ~/.bashrc`. The variable will remain set if you stop and start the codespace. However, changes to files in the home directory will be reset if you rebuild the container, so variables set in the `~/.bashrc` file will not persist over a rebuild.

### For all codespaces for a repository

There are three ways that you can set persistent custom environment variables for all codespaces that you create for a repository:

- You can edit the `devcontainer.json` configuration file for the repository
- You can use a custom Dockerfile
- You can use encrypted secrets

#### Edit the `devcontainer.json` configuration file for the repository

Edit the `devcontainer.json` configuration file for the repository, and use the `remoteEnv` property to set the environment variable value:

```
{
    "remoteEnv": {
      "VARNAME": "value"
   }
}
```

Only use this method for values that you are happy to commit to your repository as plaintext. For sensitive values such as access tokens, use encrypted secrets.

The environment variable will be set within your editor's remote server process, and will be available for sub-processes of that remote server process, such as terminals and debugging sessions. However, the variable will not be available more broadly inside the container. This method is useful if you don't need the environment variable to be set for other background processes that run at startup, and if you are using a premade image and don't have or want a custom Dockerfile.

This setting will take effect when you rebuild your container or create a new codespace after pushing this change to the repository. For more information about applying configuration changes to a codespace, see "[AUTOTITLE](/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers)."

#### Use a custom Dockerfile

If you are using a custom Dockerfile you can set the environment variable there by adding `ENV VARNAME=value`.

This method is useful if you already have a Dockerfile and want to set a variable on a container-wide level.

This setting will take effect when you rebuild your container or create a new codespace after pushing this change to the repository. For more information about applying configuration changes to a codespace, see "[AUTOTITLE](/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers)."

#### Use encrypted secrets

You can use encrypted secrets for {% data variables.product.prodname_github_codespaces %} to set custom variables for codespaces created for the repository. For more information, see "[AUTOTITLE](/codespaces/managing-your-codespaces/managing-encrypted-secrets-for-your-codespaces)."

You should use this method for environment variable values that you do not want to commit to the repository as plaintext.

This setting will take effect the next time you create a codespace for this repository, or when you restart an existing codespace.

### For all codespaces that you create

If you want to set a personalized environment variable for all codespaces that you create you can set this using a file in your `dotfiles` repository. For example, add `VARNAME=value` in the `.bash_profile` file. Environment variables you set in a dotfile are personal to you and are not set for anyone else. For more information about Dotfiles, see "[AUTOTITLE](/codespaces/customizing-your-codespace/personalizing-github-codespaces-for-your-account#dotfiles)."

## Preventing temporary files from being automatically deleted

Files saved to your workspace will be available the next time you start the codespace. The exception to this is any file you save in the `/tmp` directory. The contents of this directory are deleted each time the codespace stops (for example, when the codespace session times out after a period of inactivity).

If you have temporary files that you want to be available the next time you start the codespace, do not save them in the `/tmp` directory. For more information, see "[AUTOTITLE](/codespaces/customizing-your-codespace/setting-your-timeout-period-for-github-codespaces)."
