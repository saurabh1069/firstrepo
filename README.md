stages:
  - build
  - deploy

variables:
  SOLUTION_FILE: "YourApp.sln"
  BUILD_DIR: "YourApp/bin/Release"
  DEPLOY_DIR: "\\path\to\deployment\directory"

# Define the tag of the runner to be used
default:
  tags:
    - windows-runner

before_script:
  # Update NuGet packages
  - nuget restore $SOLUTION_FILE

build:
  stage: build
  script:
    # Build the solution in Release mode
    - msbuild $SOLUTION_FILE /p:Configuration=Release

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