# itch.io workflow
This repository is a template for Unity projects with CI/CD through GitHub Actions.

There are three total pipelines:
- Acquire activation file, to get the unity license activation file.
- Test the application, which runs all tests and generates reports.
- Test, build and push the application to itch.io, which runs all tests, generates reports, makes builds for various platforms and uploads these builds to itch.io.

The various actions are based off the [GameCI documentation](https://game.ci/docs/github/getting-started).

# Getting started
To get started with this template, fork this repository.

After forking the repository, navigate to the Actions tab on the top bar of your repository and click the "I accept, turn on actions for this repository" button. You should now be able to run the pipelines manually.

# Setting up the required variables and secrets
There's some sensitive data used within the project, in order to hide these from other team members, they are stored in GitHub Actions secrets.

Other variables that are not sensitive such as what itch.io page to push to are stored in GitHub Actions variables.

To set or change these, navigate to the settings tab on the top of your repository, and head to the "Actions" menu under the "Secrets and variables" tab.

## Variables
- ITCH_GAMEID, the name of the itch.io game page to push to, as noted in the url (so for https://woutvanriel.itch.io/itchio-workflow, it would be itchio-workflow).
- ITCH_USERNAME, the name of the itch.io user to push to, as noted in the url (so for https://woutvanriel.itch.io/itchio-workflow, it would be woutvanriel).
- PROJECT_PATH, the path where the unity project is located, I reccomend keeping it as "./" and placing your project in the root folder.

## Secrets
- ITCH_KEY, the itch.io butler api key (can be generated here: https://itch.io/user/settings/api-keys).
- UNITY_EMAIL, the email you use to log in to the Unity website.
- UNITY_PASSWORD, the password you use to log in to the Unity website (used to validate the unity license, not shared in the project).
- UNITY_LICENSE, the Unity license file, instructions to generage these can be found at Unity license activation.
