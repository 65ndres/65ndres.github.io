---
title: Create Your Own Blog For Free Online
date: 2024-01-10 12:00:00 -500
categories: [programming]
tags: [tutorial,jekyll,Chirpy]
---

Yes, you read that right! with only a few steps you can get your own blog / portfolio live for free. If you dont know it by now I'm referring to Jekyll, the static site generator made by Github that does not need a database or a backend. I believe Jekyll is the perfect solution for me, combining blog-like features and the autonomy of allowing me to keep a local copy of every post as code.

## Game Plan

1. Jekyll installation via Chirpy
2. Create our first post.
3. Edit Chirpy.
4. Deployment.

Before installing Jekyll lets make sure we have all the dependencies installed using the command bellow

```bash
sudo apt-get install ruby-full build-essentials zlib1g-dev
```

### Jekyll installation via Chirpy

After our dependencies have been installed we need to install [Jekyll](https://jekyllrb.com/) but there is an amazing and easy to use starter out there called [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy). Chirpy is clean, minimal, has good SEO and has the search function built in.From their Github repo you will find their [ official documentation](https://chirpy.cotes.page/posts/getting-started/)

Here you will be given two option for installation: 
 * Use the starter
 * Fork the repo

We are gonna use the starter, clicking on the starter option should take you to the starter's github page where you should see the "Use this template" option if you're logged into Github. Click on it and make sure you use the following format when naming your repo `<USERNAME.github.io>` if you want the site to be automatically deployed using Github pages (Also make sure the repo is set to Public)

Next we will clone the repo, cd into it and install the projects dependencies with bundle

```bash
git clone git@github.com:<USERNAME>/<USERNAME>.github.io.git
cd <USERNAME>.github.io
bundle
```

and now that all the dependencies are install we are gonna start Jekyll with:

```bash
bundle exec jekyll s
```

We should be able to open `http://localhost:4000` and see out site running

### Create our first post

To create our first post we need to create a file inside the `_posts` folder with the following naming pattern: `YYYY-MM-DD-post-title.markdown`. This very specific blog post in my repo is named `2024-01-10-create-your-own-blog-for-free.markdown`. Now that the file is created we are gonna add our content which we are gonna manage as code written in markdown syntax (Here is a [link](https://markdownguide.offshoot.io/basic-syntax/) with the documentation for it ).

Before getting started with our post we will need to add some metadata at the very top of our file like so:

```
---
title: Create Your Own Blog For Free Online
date: 2024-01-10 12:00:00 -500
categories: [homelab]
tags: [tutorial,jekyll,Chirpy]
---

[Markdown syntax below]
```

After the saving you should see the post appear in the home page of the site.


### Edit Chirpy

Now that we have added content by creating our first post we might want to change configuration Chirpy has a default like SEO tags, socials urls, web analytics settings, timezone and more. To edit these values head over to the `_config.yml` file. If you want to make changes to the website's theme you will have to create a file called `jekyll-theme-chirpy.scss` inside the `assets/css` folder and add the following piece of code at the very top.

```scss
---
---

@import 'main {% raw  %}
{%- if jekyll.environment == 'production' -%}
  .bundle
{%- endif -%}{% endraw %}
';
```

### Deployment

If you want to host the site using Github Pages then you're done. Yes, you read that right, most of the deployment process is being handled for us by the Github action in the `github/workflows/pages-deploy.yml` file. Head over `https://<USERNAME>.github.io` to see your site live.

## Conclusion

Jekyll is an amazing, quick and easy solution for deploying your own blog for free.



