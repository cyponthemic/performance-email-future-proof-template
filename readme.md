# Grunt Email Design Workflow - Future-Proof Responsive Email Without Media Queries

Need to be written


# Grunt Email Design Workflow

Designing and testing emails is a pain. HTML tables, inline CSS, various devices and clients to test, and varying support for the latest web standards.

This grunt task helps simplify things at the design stage.

1. Compiles your SCSS to CSS

2. Builds your HTML and TXT email templates

3. Inlines your CSS

4. Uploads any images to a CDN (optional)

5. Sends you a test email to your inbox (optional)

## Requirements

* Node.js - [Install Node.js](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)
* Grunt-cli and Grunt (`npm install grunt-cli -g`)
* Ruby - [Install ruby with RVM](https://rvm.io/rvm/install)
* Premailer (`gem install premailer hpricot nokogiri`) - Inlines the CSS
* [Mailgun](http://www.mailgun.com) (optional) - Sends the email
* [Litmus](https://litmus.com) (optional) - Tests the email across all clients/browsers/devices
* [Rackspace Cloud](http://www.rackspace.com/cloud/files/) (optional) - Uses Cloud Files as a CDN

## Getting started

If you haven't used [Grunt](http://gruntjs.com/) before check out Chris Coyier's post on [getting started with Grunt](http://24ways.org/2013/grunt-is-not-weird-and-hard/).

Clone this repo, cd to the directory, run `npm install` to install the necessary packages.

```
git clone https://github.com/leemunroe/grunt-email-design.git
cd grunt-email-design
npm install
grunt
```

### Sensitive Information
We encourage you __not__ to store sensitive data in your git repository. If you must, please look into [git-encrypt](https://github.com/shadowhand/git-encrypt) or some other method of encrypting your configuration secrets.

1. Create a file `secrets.json` in your project root.
2. Paste the following sample code in `secrets.json` and enter the appropriate credentials for the services you want to connect with. It's ok to leave these defaults, but they should exist.

```
{
  "mailgun": {
    "api_key": "YOUR MG PRIVATE API KEY",
    "sender": "E.G. POSTMASTER@YOURDOMAIN.COM",
    "recipient": "WHO YOU WANT TO SEND THE EMAIL TO"
  },
  "litmus": {
    "username": "LITMUS USER NAME",
    "password": "LITMUS PASS",
    "company": "LITMUS COMPANY/API SUBDOMAIN NAME"
  },
  "cloudfiles": {
    "user": "CLOUDFILES USERNAME",
    "key": "CLOUDFILES KEY",
    "region": "CLOUDFILES REGION E.G. ORD",
    "container": "CLOUDFILES CONTAINER NAME",
    "uri": "CLOUDFILES URI"
  },
  "s3": {
    "key": "AMAZON S3 KEY",
    "secret": "AMAZON S3 SECRET",
    "region": "AMAZON S3 REGION",
    "bucketname": "AMAZON S3 BUCKET NAME",
    "bucketdir": "AMAZON S3 BUCKET SUBDIRECTORY (optional)",
    "bucketuri": "AMAZON S3 PATH (ex: https://s3.amazonaws.com/)"
  }
}
```



## How it works

<img src="http://i.imgur.com/yrHpTdr.jpg" width="500">

### CSS

This project uses [SCSS](http://sass-lang.com/). You don't need to touch the .css files, these are compiled automatically.

For changes to CSS, modify the `.scss` files.

Media queries and responsive styles are in a separate style sheet so that they don't get inlined. Note that only a few clients support media queries e.g. iOS Mail app.

### Email templates and content

Handlebars and Assemble are used for templating.

`/layouts` contains the standard header/footer HTML markup. You most likely will only need one layout template, but you can have as many as you like.

`/emails` is where your email content will go. To start you off I've included example transactional emails based on my [simple HTML email template](https://github.com/leemunroe/html-email-template).

`/data` contains _optional_ .yml or .json data files that can be used in your templates. It's a good way to store commonly used strings. See `/data/default.yml` and `/partials/follow_lee.hbs` for an example.

`/partials` contains _optional_ .hbs files that can be thought of like includes. To use a partial, for example `/partials/follow_lee.hbs` you would use the following code in your emails template:

```
{{> follow_lee }}
```

### Generate your email templates

In terminal, run `grunt`. This will:

* Compile your SCSS to CSS
* Generate your email layout and content
* Inline your CSS

See the output HTML in the `dist` folder. Open them and preview it the browser.

<img src="http://i.imgur.com/WoWgRxm.gif" width="500">

Alternatively run `grunt watch`. This will check for any changes you make to your .scss and .hbs templates, then automatically run the tasks. Saves you having to run grunt every time.

### Send the email to yourself

* Sign up for a [Mailgun](http://www.mailgun.com) account (it's free)
* Open up `Gruntfile.js`
* Replace 'MAILGUN_KEY' with your actual Mailgun API key
* Change the sender and recipient to your own email address (or whoever you want to send it to)

Run `grunt send --template=transaction.html`. This will email out the template you specify.

<img src="http://i.imgur.com/6N8VRen.gif" width="500">

Change 'transaction.html' to the name of the email template you want to send.

### How to test with Litmus

If you have a [Litmus](http://www.litmus.com) account and want to test the email in multiple clients/devices:

* Open up `Gruntfile.js` 
* Replace `username`, `password` and `yourcompany` under the Litmus task with your credentials

Run `grunt litmus --template=TEMPLATE_NAME.html` to send the email to Litmus. This will create a new test using the `<title>` value of your template.

[See the Litmus results](https://litmus.com/pub/eb33459/screenshots) for the simple transactional email template that is included.

<img src="https://s3.amazonaws.com/f.cl.ly/items/1a1H0B1o3v160147100S/Image%202014-12-31%20at%2010.10.01%20AM.png" width=-"500">


### CDN and working with image assets

If your email contains images you'll want to serve them from a CDN. This Gruntfile has support for Rackspace Cloud Files ([pricing](http://www.rackspace.com/cloud/files/pricing/)).

<img src="http://i.imgur.com/oO5gfkZ.jpg" width="500">

* Sign up for a Rackspace Cloud account (use the [Developer Discount](http://developer.rackspace.com/devtrial/) for $300 credit)
* Create a new Cloud Files container
* Open up `Gruntfile.js`
* Change 'cloudfiles' settings to your settings (you can find your Rackspace API key under your account settings)
* Make any other config changes as per [grunt-cloudfiles](https://github.com/rtgibbons/grunt-cloudfiles) instructions

Run `grunt cdnify` to run the default tasks as well as upload any images to your CDN.

Run `grunt cdnify send --template=branded.html` to send the email to yourself with the 'CDNified' images.


### Using Amazon S3 for image assets

Another option for serving images is to use Amazon S3. Basic service is free of charge. For more information on setting up an account, visit [Amazon](http://aws.amazon.com/s3/).

The Gruntfile uses [grunt-aws-s3](https://github.com/MathieuLoutre/grunt-aws-s3).

Once your AWS account is setup, create a Bucket within S3. You will need to ensure your Bucket has a policy setup under Permissions. Below is a very loose sample policy for testing purposes. You should read up on [AWS Identity and Access Management](http://aws.amazon.com/iam/) for more information.

**Sample S3 Bucket Policy**

```
{
  "Version": "2008-10-17",
  "Id": "Policy123",
  "Statement": [
    {
      "Sid": "Stmt456",
      "Effect": "Allow",
      "Principal": {
        "AWS": "*"
      },
      "Action": "s3:*",
      "Resource": "arn:aws:s3:::BUCKETNAME"
    }
  ]
}
```

Run `grunt s3upload` to upload images to your S3 Bucket. This will also run a replace task to change image paths within the destination directory to use the new S3 path.


### Sample email templates

I've added two templates here to help you get started.

* [Simple transactional email template](http://leemunroe.github.io/grunt-email-design/dist/transaction.html)
* [Branded email via CDN](http://leemunroe.github.io/grunt-email-design/dist/branded.html)

For more transactional email templates check out [Mailgun's collection of templates](http://github.com/mailgun/transactional-email-templates).
