# Pull Raycast Extension Changes

This simple __GitHub Action__ allows you to store a [custom extension](https://github.com/raycast/extensions) for [Raycast](https://raycast.com/) in your own repo, and automatically create a PR to that repo whenever any changes are made to your extension "upstream" in the `raycast/extensions` repo.

## Why is this useful?

When you start writing a Raycast extension, you'll naturally create your own repo with its own issues, pull requests, readme, continuous integration setup, etc.

However, when you want to release the extension in the Raycast Store, it has to be committed to the [`raycast/extensions` repo]((https://github.com/raycast/extensions). 

Getting code between your repo and the central one is a bit of a pain. When you make changes, you'll need to copy them across to the central repo, and occasionally changes can be made by the Raycast team when reviewing a pull request that you want to bring back to your own repo.

This GitHub Action, combined with my ["Publish Raycast Extension" action](https://github.com/timrogers/publish-raycast-extension), allows you to have to best of both worlds: your extension can live in your own repo, but be automatically synced in both directions with `raycast/extensions`.

## Usage

These instructions assume that your extension is already included in the `raycast/extensions` repo and published in the Raycast Store.

### Setting up the GitHub action

1. Create a `.github/workflows/pull_upstream_changes.yml` file, and paste in the contents below:

```yaml
name: Pull upstream changes from the `raycast/extensions` repo

on:
  schedule:
    # This workflow will automatically run at 5am each day...
    - cron:  '0 5 * * *'
  # ...and you can also trigger it manually from GitHub's UI.
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: timrogers/pull-raycast-extension-changes@v1.0.0
        with:
          # CHANGE ME to the name of your extension. This should be the name of the folder in `raycast/extensions`, under `extensions/`, where your extension lives.
          extension_name: CHANGE ME
          # Leave this as it is. It'll automatically use the GitHub token provided by the GitHub Actions runtime.
          github_access_token: ${{ secrets.GITHUB_TOKEN }}
```

2. Update the `extension_name` variable, marked `CHANGE ME`, adding the name of your extension.
3. Add, commit and push your `.github/workflows/pull_from_upstream.yml` file to your repo.
4. The action will automatically check for updates every 24 hours once the YML is in your primary Git branch. You can also trigger it manually from the "Actions" tab of your repo.
5. If there are any upstream changes, a PR will automatically be created. This PR should be merged without modifications so that your repo reflects what is upstream in the `raycast/extensions` repo.