# The smallest ASP.NET backend-API website you can easily run on your local computer

---

## Prerequisites

1. You must install the .NET runtime _(preferably version 7, as that's what I coded this using)_ onto your local machine in a way that lets you run commands beginning with "`dotnet`" from your computer's command prompt _(the "dotnet" executable must be in your "PATH.")_.
    * Don't have Windows admin rights?  Check out David Kou's [Install the .NET runtime onto Windows without admin rights](https://dev.to/davidkou/install-anything-without-admin-rights-4p0j#install-dotnet-sdk-or-runtime-without-admin).
2. You must download a copy of this codebase onto your local computer.

---

## Building source code into a runtime

Open up a command line interface.

Ensure that its prompt indicates that your commands will be running within the context of the folder into which you downloaded a copy of this codebase.

Run the following command:

```sh
dotnet publish --configuration Release
```

The `dotnet publish --configuration Release` command will take about a minute or two to execute.

It will add a new subfolder called `/bin/` as well as one called `/obj/` into the folder on your computer containing a copy of this codebase.

Congratulations -- you have now built an executable "runtime" for ASP.NET that, when executed, will behave as a web server.

The entirety of your "runtime" that you just built lives in the `/bin/Release/net7.0/publish/` folder _(if you were using a different version of .NET, it might not say `7.0`)_.   It should contain about 6 files, including one called `Handwritten.exe`.

---

## Running your web server

Open up a command line interface.

Ensure that its prompt indicates that your commands will be running within the context of the folder into which you downloaded a copy of this codebase.

Run the following command:

```sh
./bin/Release/net7.0/publish/Handwritten.exe
```

* **WARNING**:  Do _not_ close the command line just yet or it will be difficult to stop your web server later in this exercise.

Unless you happen to have already manually set an environment variable named "`PORT`" on your local computer to some number, the output you will see will say something like:

```
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5000
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\example
```

---

## Visiting your website

Open a web browser and navigate to [http://localhost:5000/api/sayhello](http://localhost:5000/api/sayhello) _(being sure to use an alternative port number to `5000` if indicated "`now listening on`" message you saw when you executed your runtime)_.

Take a look in the upper-left corner of the webpage you just visited:  it should say "**Hello World!**"

---

## Stopping your web server

Go back to the command line interface from which you ran `./bin/Release/net7.0/publish/Handwritten.exe`.

Hold <kbd>Ctrl</kbd> and hit <kbd>c</kbd>, then release them both.

---

## A build CI/CD pipeline for Azure DevOps Pipelines

I'm not sure I'll get around to making cloud instructions for this codebase any time soon, so if you'd like to guess your way through adapting lessons from [az-as-wa-001-node-express-tiny](https://github.com/kkgthb/az-as-wa-001-node-express-tiny) to apply to this codebase instead, here's the "build pipeline" YAML I used in Azure DevOps Pipelines:

```yaml
trigger:
- main

pool:
  vmImage: "windows-latest"

steps:

- task: UseDotNet@2
  displayName: "Install .NET Core SDK"
  inputs:
    version: 7.x
    performMultiLevelLookup: true
    includePreviewVersions: true

- task: DotNetCoreCLI@2
  displayName: "DotNet Publish"
  inputs:
    command: publish
    arguments: "--configuration Release --output $(Build.ArtifactStagingDirectory)"
    zipAfterPublish: True

- task: PublishBuildArtifacts@1
  displayName: "Publish build-artifact from staging directory to named artifact"
  inputs:
    PathToPublish: "$(Build.ArtifactStagingDirectory)"
    ArtifactName: "MyBuiltDotNetWebsite"
```

The output of running such a pipeline against this repository should be a folder structure whose contents look like the `/bin/Release/net7.0/publish/` folder you saw on your desktop computer earlier _(it should contain about 6 files including one called `Handwritten.exe`)_.