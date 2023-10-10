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
- UNITY_LICENSE, the Unity license file, instructions to generage these can be found at [Unity license activation](generating-a-license-request-file).

# License activation
## Generating a license request file
In order to use Unity from a dockerized or command line environment, it needs to know which Unity license to use. For the upcoming tutorial, a Unity personal license is assumed. If you have a different license, refer to the [GameCI documentation](https://game.ci/docs/github/activation).

Prerequisites for this step: the UNITY_EMAIL and UNITY_PASSWORD need to be filled in and connected to a Unity account with an active personal license.

Navigate to the Actions tab, and click on the "Acquire activation file" pipeline. It should show a popup saying "This workflow has a workflow_dispatch event trigger.". Click the button on the right and run the workflow on the main branch.

This should start the pipeline, it generates an artifact containing a .alf file. Download the artifact and extract the .alf file.

## Requesting the license file
Now comes the tricky bit, requesting the actual license file using the Unity license request file we generated. For some reason the site to manually request these licenses is incredibly buggy and requires you to mess with the element inspector in order to even have the option to activate personal licenses, so make sure to read the following steps thorougly.

Firstly, use Google Chrome. For some reason when loading this site in FireFox or other browsers, it infinitely keeps reloading.

Navigate to https://license.unity3d.com/ and log in to your Unity account.

Click on the file input below the text "Locate license request file" and select the .alf file that was generated in the previous section.

On the following page, it will request you to inter a serial number, ignore this. Open the chrome dev tools by clicking the F12 key on your keyboard, or by right clicking any part of the document and clicking "Inspect".

With the dev tools opened, click on the part of the toolkit containing the HTML of the page, and press Control + F (Command + F on mac).

Search for "option-personal", it should show you a div with a style tag containing "display: none;".

Remove the "display: none;" from the div by doubleclicking the text and clicking backspace. A radiobutton for choosing Unity Personal Edition should now be visible.

Select the Unity Personal Edition, and choose one of the options below, I suggest clicking "I don't use Unity in a professional capacity".

Click next, and it should allow you to download the Unity Licence File.

Open the file in a text editor like notepad, notepad++ or visual studio code. Copy the contents of the file and place it into the UNITY_LICENSE secret.

You should now be able to run the tests and builds in GitHub Actions. I suggest running the test pipeline to make sure it has worked.

# Code coverage reports
The pipeline uses the project settings for code coverage reports, you should be able to set the settings in your Unity project code coverage settings, I reccomend at least making sure the "Generate HTML report" tick is turned on, or the testing pipeline will fail.

# GitHub pages
The testing pipeline is currently set up to push the code coverage report to GitHub pages on completion.

To enable this for your own repository, navigate to settings -> pages. Then change the Source to "GitHub Actions".

This should create a protection rule in the settings -> environments page. Open and change this environment to make sure the branches you intend to run the tests in are able to push to GitHub pages.

After running the pipeline, the url to the GitHub Pages page should be visible when inspecting the pipeline run. This url will remain the same for the entire project, allowing for easy access to the latest code coverage report.

# Branches
## Branching strategy
The pipeline is currently set up for the following branching strategy:

The main branch is for the latest released version of the game. Pull requests to main are only allowed from the dev branch. It's protected against direct pushes, only allowing pull requests to make code changes. Every pull request to the main branch requires the test pipeline to be completed succesfully and it requires at least one reviewer to approve the pull request.

The dev branch is the active development branch. It's protected against direct pushes, only allowing pull requests to make code changes. There are no restrictions on pull requests to dev.

More information on how to protect branches can be found on the [GitHub documentation](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches).

## Pipeline triggers
There's currently two automated pipeline triggers, on the main branch and on the dev branch. Though these can be easily changed in the workflow .yml files.

The "test, build and push the application to itch.io" pipeline is ran on any new push to main.

The "test the application" pipeline is ran on any new push to dev.

# Unused pipeline jobs
If there are jobs you don't intend to use, remove them from the .yml files. The workflow files can be found in the .github/workflows folder, or under the Actions tab when selecting a pipeline. I suggest editing the .yml files in a text editor and pushing them to a featur branch, then manually activating them manually to test before merging to dev, then main.
