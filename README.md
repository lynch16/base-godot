# base-godot
Repo for base tools needed for Godot game development

# Setup
Follow these steps to setup a new repository for a game:
1. Fork this repository under a new name.
    - Make it a fork so that shared tooling upgrades propagate to all games
2. Install addons (latest)
    - [gdUnit4](https://github.com/MikeSchulze/gdUnit4): Unit Testing Framework for Godot
    - [AespriteWizard](https://github.com/viniciusgerevini/godot-aseprite-wizard): Simplified import pipeline for Aesprite images and animations.
3. Rename files `src/BaseGodot.csproj` and `src/BaseGodot.sln` to match your game name.
4. Verify Godot can correctly build and run the cloned project.

# CICD
No further setup for CICD pipelines is requried.

## Access
1. GitAccess: Access token used for personal development. Do NOT use with pipelines
2. Workflow-actions: Purpose built for use only with Github Actions. ONLY use this token with pipelines.

## Consumption
Github provides [free quotas](https://github.com/settings/billing) of compute and storage. Storage will be the limiting factor for this project.

## Configure CICD
1. Create a new Repository Access Token:
    - Go to [Developer Settings / Personal Access Tokens](https://github.com/settings/personal-access-tokens) and 
    - Create a new fine-grained PAT. 
    - Pick the repo and set the permissions to \[Actions\].
2. Store CICD secrets
    - In the new repo, go to the Repository Settings tab, Secrets and variables > Actions.
    - Create 2 repository secrets:
        - GH_PAT: Token created in step 1.
        - BUTLER_API_KEY: [Itch.IO API key](https://itch.io/user/settings/api-keys) for web.
3. Update ENV
    - Update all `env:` sections in all files under .github/workflows. 

### Build Workflow
- Runs on all pushes to any branch. Uses container image of [Godot-CI](https://github.com/abarichello/godot-ci) to simplify installing Godot and C# dependencies.
- **Build**: Run a generic build process of the project. This is not strictly necessary but validates the build against standard build processes.
- **Test**: Run Testing Jobs. Currently only UT implemented. Fully dependent on the UT testing library [gdUnit4](https://github.com/MikeSchulze/gdUnit4). It is configured to upload the test results to Github as artifacts for debugging.
- **Windows Export**: Builds and exports the project as both production and debug versions, using the native Godot CLI.
- **Publish**: Publishes production version of project to Itch.IO when merging to `release` branch.

## UTs
Installing GdUnit4 - Unit Testing Framework (new for 4.5). 

- The gdunit4-actions tooling does not fully support 4.5. Ensure the NAME.csproj file is updated with the following versions
```
    <PackageReference Include="Microsoft.NET.Test.Sdk" Version="17.9.0" />
    <PackageReference Include="gdUnit4.api" Version="4.3.*" />
    <PackageReference Include="gdUnit4.test.adapter" Version="2.0.*" />
    <PackageReference Include="gdUnit4.analyzers" Version="1.0.0">
      <PrivateAssets>none</PrivateAssets>
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
    </PackageReference>
```
- The Godot-CI tooling does not fully support gdUnit6.0.0 either, which is why that addon is not checked in.
- Sometimes using the Visual Studio Test tools gives a more consistent experience than the Godot plugins.