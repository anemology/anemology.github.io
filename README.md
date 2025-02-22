# anemology.cc

Source code and markdown for [anemology.cc](https://anemology.cc/)

## Develop

Download Hugo executable from [gohugoio/hugo](https://github.com/gohugoio/hugo/releases), and run the command:

```bash
hugo server
```

Add a new post file:

```bash
hugo new post/i-am-post-name.md
```

## theme

[matsuyoshi30/harbor: Simple and minimal personal blog theme.](https://github.com/matsuyoshi30/harbor)

```bash
git clone https://github.com/matsuyoshi30/harbor.git theme/harbor
```

1. Change language code `languageCode = "zh-TW"`  
  <https://discourse.gohugo.io/t/i18n-support-for-language-country-code/20303/9>
1. Custom CSS in [static/css/custom.css](static/css/custom.css)

Update submodule

Because I made some modifications from original repo, so I choose use rebase in order to prevent a merge commit, and easy to compare with original repo.

```bash
# make sure submodule is cloned, and add original repo as another remote
git clone git@github.com:anemology/harbor.git theme/harbor && cd theme/harbor
git remote add matsuyoshi30 git@github.com:matsuyoshi30/harbor.git

git pull --rebase matsuyoshi30 master
git push --force
```
