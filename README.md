# jekyll-deploy

Builds and deploys a jekyll page to GitHub pages

## Usage

To deploy from master and update once a day:
```yaml
name: Jekyll Deploy

on:
  push:
    branches:
      # only deploy from master
      - master
  schedule:
    # redeploy every morning to update unpublished pages
    - cron: "0 2 * * *"

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2

      - name: Cache gems
        uses: actions/cache@v1
        with:
          path: vendor/gems
          key: ${{ runner.os }}-build-${{ hashFiles('**/Gemfile.lock') }}
          restore-keys: |
            ${{ runner.os }}-build-
            ${{ runner.os }}-

      - name: Build & Deploy to GitHub Pages
        uses: DavidS/jekyll-deploy@master
        env:
          JEKYLL_ENV: production
          GH_PAGES_TOKEN: ${{ secrets.GH_PAGES_TOKEN }}
```

To check PRs, set `build-only: true`:

```yaml
name: Jekyll Test

on: [ pull_request ]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2

      - name: Cache gems
        uses: actions/cache@v1
        with:
          path: vendor/gems
          key: ${{ runner.os }}-build-${{ hashFiles('**/Gemfile.lock') }}
          restore-keys: |
            ${{ runner.os }}-build-
            ${{ runner.os }}-

      - name: Test
        uses: DavidS/jekyll-deploy@master
        with:
          build-only: true
        env:
          JEKYLL_ENV: production
          GH_PAGES_TOKEN: ${{ secrets.GH_PAGES_TOKEN }}
```

## Create GH_PAGES_TOKEN access token to trigger GitHub Pages rebuild

GitHub Pages deploys changes automatically in case of new commit.
For scheduled depoyment it's required to trigger GitHub Pages [rebuild manually using API](https://developer.github.com/v3/repos/pages/#request-a-page-build).

To do this it's required to create
