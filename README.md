# Webhook CMS bundle

By default Webhook CMS files are only available over a non-secure HTTP connection from `http://cms.webhook.com/`. By bundling these CMS files with your own website, you can serve them over a secure HTTPS connection.


## Secure you website

### The problem

[Webhook](http://www.webhook.com) is a static site generator with a CMS. Webhook includes hosting for its projects on `http://your-project.webhook.org`. While you can add a custom domain, you can't add an SSL certificate. [HTTPS support for Webhook has been a long outstanding issue](http://forums.webhook.com/t/are-https-sites-supported/572/22). This means your website is served over a non-secure HTTP connection.

### The solution

You can add a secure proxy or CDN in front of your Webhook site. Your project will still be deployed to `http://your-project.webhook.org`. However you can setup a CDN like Cloudfront with a (free) SSL certificate and connect this to your custom domain (e.g. `https://your-project.com`) The CDN will fetch files from Webhook, and serve them over a secure HTTPS connection to your users. 


## Secure your CMS

### The problem

Your CMS is still available on the non-secure `http://your-project.webhook.org/cms/`. The CMS on your secure `https://your-project.com/cms/` will not work yet. The reason is that the CMS's HTML page is served over HTTPS, but the CMS assets are served from `http://cms.webhook.com/` over a non-secure HTTP connection. This causes a mixed-content error and results in a blank page in your browser.

### The solution

Serve the CMS assets from your now secure website instead of using `http://cms.webhook.com/`. You achieve this by bundling the CMS assets with your own project:

#### Copy `static/cms/` files

A copy of all CMS assets served from `http://cms.webhook.com/` is available in this repository. [Download this repository](https://github.com/jbmoelker/webhook-cms-bundle/archive/master.zip). Then copy all files from [`static/cms/`](https://github.com/jbmoelker/webhook-cms-bundle/tree/master/static/cms) into `your-project/static/cms/` directory.

#### Update `pages/cms.html` template

The references to all CMS assets in `your-project/pages/cms.html` still point to `http://cms.webhook.com/`. So you need to change those to `/static/cms/`. An [adapted `page/cms.html](https://github.com/jbmoelker/webhook-cms-bundle/blob/master/pages/cms.html#L2) is available in this repository. You can copy that file, or make the following changes:

Set new assets directory at the top of the template:
```jinja
{% set cmsAssetsBase = '/static/cms/v2/assets' %}
```

Update reference to stylesheet in head:
```jinja
<link rel="stylesheet" href="{{ cmsAssetsBase }}/app.min.css">
```

Update references to scripts in body:
```jinja
<script src="{{ cmsAssetsBase }}/js/config.min.js"></script>
<script src="{{ cmsAssetsBase }}/vendor.min.js"></script>
<script src="{{ cmsAssetsBase }}/js/app.min.js"></script>
```

#### Deploy bundled CMS assets

Finally deploy the new version of your website with bundled CMS assets by running

```bash
$ wh deploy
```

