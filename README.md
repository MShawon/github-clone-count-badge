<p align="center">
    <img alt="ViewCount" src="https://views.whatilearened.today/views/github/MShawon/github-clone-count-badge.svg">
    <a href="https://github.com/MShawon/github-clone-count-badge"><img alt="GitHub Clones" src="https://img.shields.io/badge/dynamic/json?color=success&label=Clone&query=count&url=https://github.com/MShawon/github-clone-count-badge/blob/master/clone.json?raw=True&logo=github"></a>
</p>

# GitHub Clone Count Badge

This is a clone count badge shows 14 days clone statistics of a GitHub repository. 

## How it works
* Use a GitHub action to get repository clones using `https://api.github.com/repos/{username}/{repo}/traffic/clones` this api. A json file named clone will be added to the repository's default branch.
* Then shields.io dynamic badge will parse this **clone.json** file to show the clone count.
* GitHub actions will run every 8 hours.

## Setup (~5 minute setup)
1) Create a new workflow from the `Actions` tab of your repository and paste the following.
```yaml
name: GitHub Clone Count for 14 days at every 8 hours

# Controls when the action will run. 
on:
  schedule:
    - cron: "0 */8 * * *"
  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      - name: Parse clone count using REST API
        run: |
          curl --user "${{ github.actor }}:${{ secrets.SECRET_TOKEN }}" \
            -H "Accept: application/vnd.github.v3+json" \
            https://api.github.com/repos/${{ github.repository }}/traffic/clones \
            > clone.json

      - name: Add to git repo
        run: |
          git add .
          git config --global user.name "GitHub Action"
          git config --global user.email "action@github.com"
          git commit -m "Automated clone.json update"
          
      - name: Push
        uses: ad-m/github-push-action@master
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}

```
2) To use this, you will need a personal access token. See this https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token to get one.
3) Next, add action secrets to this repository like https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository this. Remember **SECRET_TOKEN** must be the secret name. And the value will be **personal access token**.  

## Usage
Replace `<url>` with clone.json url.

**Markdown**
```markdown
[![GitHub Clones](https://img.shields.io/badge/dynamic/json?color=success&label=Clone&query=count&url=<url>?raw=True&logo=github)](https://github.com/MShawon/github-clone-count-badge)
```

 **HTML**
```html
<a href="https://github.com/MShawon/github-clone-count-badge"><img alt="GitHub Clones" src="https://img.shields.io/badge/dynamic/json?color=success&label=Clone&query=count&url=<url>?raw=True&logo=github"></a>
```
