stages:
  - build
  - deploy

variables:
  SOLUTION_FILE: "YourApp.sln"
  BUILD_DIR: "YourApp/bin/Release"
  DEPLOY_DIR: "\\path\to\deployment\directory"
  MSBUILD_PATH: "C:\Program Files\Microsoft Visual Studio\2022\Community\MSBuild\Current\Bin\MSBuild.exe"  # Update this path based on your Visual Studio version

default:
  tags:
    - windows-runner

before_script:
  # Update NuGet packages
  - nuget restore $SOLUTION_FILE

build:
  stage: build
  script:
    # Build the solution using the specified MSBuild path
    - &"$MSBUILD_PATH" $SOLUTION_FILE /p:Configuration=Release

  artifacts:
    paths:
      - $BUILD_DIR

deploy:
  stage: deploy
  script:
    # Deploy the built application to the specified directory
    - Copy-Item -Path "$BUILD_DIR\*" -Destination "$DEPLOY_DIR" -Recurse
  environment:
    name: production
    url: https://yourapp.com
  only:
    - main