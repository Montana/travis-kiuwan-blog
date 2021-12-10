---
title: "Kiuwan Integration with Travis CI"
created_at: Fri Dec 10 2021 15:00:00 EDT
author: Montana Mendy
layout: post
permalink: 2021-12-10-kiuwanintegration
category: news
excerpt_separator: <!-- more --> 
tags:
  - news
  - feature
  - infrastructure
  - community
---

<img width="732" alt="Screen Shot 2021-12-10 at 1 33 28 PM" src="https://user-images.githubusercontent.com/20936398/145644269-cf5be5f7-6ee7-4808-b418-de5ff10bf7f0.png">

It's finally here! The integration between Travis and Kiuwan. In this post I'll give you all the details you need to try it yourself! 

<!-- more --> 

## Usage

Be sure to make a [Kiuwan](https://www.kiuwan.com) account. Then set your `environment variables` in your `.travis.yml`, using either the Travis GUI or CLI, if you're going to use the CLI run:

```bash
travis encrypt kiuwan_user=""
travis encrypt kiuwan_password=""
```

If the route you choose is by the Travis GUI, just name the `env vars` the following:

```bash
kiuwan_user
kiuwan_password
```

In Travis, I told Travis to fetch the Local Analyzer, then went through Kiuwan's LA's documentation to make a command that checks the project. This project in particular is in TypeScript, below I'll share the .travis.yml file I've created:

```yaml
language: java
install: skip

script:
  - echo $TRAVIS_BUILD_DIR
  - export APPNAME=$(basename $TRAVIS_BUILD_DIR)
  - export BRANCH=$(if [ "$TRAVIS_PULL_REQUEST" == "false" ]; then echo $TRAVIS_BRANCH; else echo $TRAVIS_PULL_REQUEST_BRANCH; fi)
  - echo "Fetching Kiuwan Local Analyzer"
  - wget -v https://www.kiuwan.com/pub/analyzer/KiuwanLocalAnalyzer.zip
  - unzip KiuwanLocalAnalyzer.zip -d $HOME/.
  - $HOME/KiuwanLocalAnalyzer/bin/agent.sh --user $kiuwan_user --pass $kiuwan_password -s $TRAVIS_BUILD_DIR -n $APPNAME -l $TRAVIS_BUILD_ID -c
```

This then sends this to Kiuwan, and from there I can access more insights about my project. In this project in particular I made this have a few vulnerabilities so Kiuwan would catch them. I also used environment variables from my account I registered from the Jelly service.

## Invokation of Kiuwan 

This is the first time this has been done on GitHub to my knowledge, there was some questions on the conditionals when running the `agent.sh`. 

## Kiuwan Local Analyzer

You'll then notice output of the Local Analyzer working:

```bash
Discovery: STARTED
Technologies discovered: html,javascript
Technologies that will be analyzed: html,javascript
Discovery: FINISHED
Preprocess: STARTED
Preprocess: FINISHED
Model retrieval: STARTED
Model downloaded from Kiuwan
Model retrieval: FINISHED
License check: STARTED
License check: FINISHED
Prepare analysis data: STARTED
Supported technologies in current model: html,javascript
Prepare analysis data: FINISHED
Prepare source code files for upload: STARTED
Prepare source code files for upload: FINISHED

Remember you can select VM size as well - this will affect (in my case) how deep the scan will go, in this particular use case though I just used baseline. Kiuwan will calculate the heap size, and collect the bill of materials (TypeScript, other dependencies):

bill-of-materials: 
bill-of-materials format: 
includes: 
excludes: **/src/test/**,**/__MACOSX/**,**/*.min.js,**/*.Designer.vb,**/*.designer.vb,**/*Reference.vb,**/*Service.vb,**/*Silverlight.vb,**/*.Designer.cs,**/*.designer.cs,**/*Reference.cs,**/*Service.cs,**/*Silverlight.cs,**/.*,**/Pods/BuildHeaders/**/*.h,**/Pods/Headers/**/*.h,**/node_modules/**,**/bower_components/**,**/target/**,**/bin/**,**/obj/**,**/dist/**,**/lib/**
configuration: 
VM version: 11.0.2
VM settings:
    Stack Size: 2.00M
    Min. Heap Size: 128.00M
    Max. Heap Size: 1.00G
    Using VM: OpenJDK 64-Bit Server VM
    
```
You should then get an email by the Jelly service. You then can scan your project on a more granular level. Here's the [test project](https://www.github.com/Montana/travis-kiuwan) I created. Below you'll see a screenshot of the Jelly service email I received after a successful scan:


<img width="521" alt="Screen Shot 2021-12-10 at 1 49 35 PM" src="https://user-images.githubusercontent.com/20936398/145645709-e70c0d6e-23e9-4eda-872b-bba43a470a54.png">

Then you'll click the link within the email, and you'll get to explore around the Kiuwan GUI:

<img width="1600" alt="Screen Shot 2021-12-10 at 1 50 05 PM" src="https://user-images.githubusercontent.com/20936398/145645749-ef9dfa46-bf22-46a0-a330-f3b3693a9f74.png">

You then can check your project out on a more granular security level. With Kiuwan you're that much more secure when deploying code with Travis. 

Happy building! 

As always, if you have any questions please email me at [montana@travis-ci.org](montana@travis-ci.org).
