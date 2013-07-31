{{$meta}}
type: guide
title: Command Line Utility 
{{$endmeta}}

{{$layout /index.html as content}}

The Backlift Command Line Interface (CLI) is an alternative for developers that are comfortable working in the terminal. Currently the CLI is the fastest way to synchronize your application changes with Backlift. The CLI requires Python 2.6.1 or later (all Macintintosh computers with OS X 10.6 or later come with this installed by default).

To install the CLI, open up your terminal and type:

    sudo easy_install backlift

Alternatively, you can use pip to install the Backlift CLI into a virtual environment.

Once you have backlift installed, create an account at [backlift.com](https://backlift.com) by clicking on the 'Sign-in' button in the upper right corner. You will need a registration key. In order to obtain one please click the 'Get Early Access' link and supply your email address. We will send you a key as soon as possible.

Once you have your api-key, setup backlift with the command:

    backlift setup <api-key>

Now you're ready to create new apps and upload them to backlift.

From time to time you should make sure that you have the most recent version of the Backlift CLI. You can check that by typing `backlift --version`. It should match the 'Backlift CLI current version' listed on your account dashboard. You can update your Backlift CLI by entering the command 

    sudo easy_install --update backlift.

### Create and Upload a new app

Create a new app with the command:

    backlift create newapp

This will create a new folder in the current directory called 'newapp' and begin downloading the backlift default project template. Once the project template has finished downloading, check it out by opening the 'newapp' folder with a text editor.

To see this app running on a public web server, first change directories into the 'newapp' folder and type 'backlift push'.

    cd newapp
    backlift push

This will upload the content of the 'newapp' folder to Backlift. Alternatively you can use the command:
    `backlift watch`

The watch command will first upload the 'newapp' folder and then wait for changes to the project. Any time you edit and save a file in the 'newapp' folder while the watch command is running, your changes will automatically by synced to Backlift.


### View and manage your app

You can launch a browser to view your app by typing:

    backlift open

You can alternatively view the website's admin page by typing:

    backlift admin

That's all there is to getting started. The demo template contains a tutorial that will show you how to build a backbone.js app with Backlift. You can run it by typing:

	backlift create:backbone-demo demoapp



