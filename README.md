# CFA-Tut-Mailgun-Heroku

This is a short tutorial on how to get Mailgun functioning on a Heroku deployed application made in Ruby on Rails. It presupposes you have already installed, configured, and successfully ran Mailgun in your production environment using local environmental variables. If you haven't done this yet use the above slide presentation to do so now. Otherwise, onward! To the tutorial!

## Overview

There are three components to getting Mailgun successfully deployed to your RoR application:

1. RoR configuration
2. Heroku configuration
3. Mailgun configuration

We'll take each in turn.

## RoR configuration

This is pretty straight forward and is almost exactly the same as what you did to configure your RoR app to run Mailgun in production environment. If you followed the above powerpoint you'd have the following in `config/environments/development.rb`:

 `config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }`  \n
 `config.action_mailer.delivery_method = :smtp`  \n
  config.action_mailer.smtp_settings = {  \n
  address:              "smtp.mailgun.org",
  port:                     587,
  domain:               ENV["MG_URL"],
  user_name:            ENV["MG_UN"],
  password:             ENV["MG_PW"],
  `authentication:       'plain'  }`

To configure for deployment to Heroku go into `config/environments/production.rb` and copy the following code:

  `config.action_mailer.delivery_method = :smtp
  config.action_mailer.smtp_settings = {
    address:              ENV["MAILGUN_SMTP_SERVER"],
    user_name:            ENV["MAILGUN_SMTP_LOGIN"],
    password:             ENV["MAILGUN_SMTP_PASSWORD"],
    domain:               "johnstewartportfolio.herokuapp.com/",
    authentication:       'plain'  }`

Once this code is in your production file you will be able to deploy to Heroku. Next we will go into what needs to be configured in Heroku for these ENV\`s to be of use. Ofcourse, push to github and then push that to Heroku.

## Heroku configuration

Before we get into the Heroku configuration there is another way to get Mailgun working with your Heroku app. This is by configuring it as an addon to your Heroku app. The downside with this is that Heroku will create its own account in Mailgun. This can cause verification issues as Mailgun may want you to verify your account which will require a valid phone number. It may also be more difficult to authorise a valid email address to send the emails received.

Whereas configuring your Heroku app to use the Mailgun account you set-up yourself will give you more options to configure in the future and have a central place for any other apps that you may want to use Mailgun with. This section will teach you about how to configure Heroku to use your own Mailgun account.

Once your RoR app has been deployed to Heroku, login into Heroku and go to the "Settings" page in your dashboard. In settings is a "Config Variables" section. This is the section where you'll enter various environmental variables to be used in production mode, just like when you used bash to store environmental variables in development mode.

Click "Reveal Config Vars" and your environmental variables will be revealed. Here is an image of what I have as my environmental variables for Mailgun, ofcourse your environmental variables will be different:

![alt tag]()

To find the values to use for these environmental variables log into your Mailgun account. If you have yet to create a domain for your Heroku app do that now by clicking on "Domains" and "Add New Domain". Name it after the address of your Heroku app. Once you have a domain you want go back to the "Domains" section and click on the domain you want to use for your Heroku app. Here is a photo of the information that you are looking for:

![alt tag]()

I've blocked my information, but you'll see a bunch of values next to the titles. This part can be slightly confusing because there isn't a direct correlation between the titles in here and the name of the environmental variables you'll be putting into Heroku. Below I've put what to name the Heroku config vars and what Mailgun value you need to put in for it:

| Heroku Env Var | Mailgun Value |
| :------- | ----: |
|ENV["MAILGUN_SMTP_SERVER"] | SMTP Hostname|
|ENV["MAILGUN_SMTP_LOGIN"] | Default SMTP Login|
|ENV["MAILGUN_SMTP_PASSWORD"] |Default Password|

In Mailgun copy the values next to each of the titles and put them into the "VALUE" section in the free space at the bottom of the config var section. Take the corresponding name for this value and put it in the "KEY" free space next to the value and click add. This will add that env var to be used by your application. For example, copy `ENV["MAILGUN_SMTP_PASSWORD"]` and paste it into the "KEY" space. Then copy the value of `Default Password` (it should look something like this: `e23664b876ff87d023c09e1c45f1ea98`) and past that into the "VALUE" space next to the key space. Click "ADD" and you env var will be added. Do this for the other 2 env vars. You'll have to restart your dynamos for these env vars to take effect.

This is all you need to do in Heroku to config the env vars for Mailgun. There were a few other env vars that I thought were needed, but I removed them and I was still able to receive mail from the contact form on my page. Now let's move onto configuring Mailgun.

## Mailgun

From what I understand the only configuration you need to do for Mailgun is to make sure your email address is authorised. If I remember correctly this is something that was done when you sign-up to Mailgun. If it wasn't then click on "Domains" then "Authorized Recipients"  then "Invite New Recipient". There are a few steps you need to take to confirm your authorization.

Another issue that I thought would stop me from using my Mailgun account was the DNS configuration. Here is the warning for this:

![alt tag]()

I don't know why i'm told "Unverified domains can not receive email", yet I'm still getting emails. However, if you want to have a different email address receiving the emails other than the email you used to set up the account you will have to config the DNS. Otherwise emails from your Heroku app will only be sent to your email address you used to sign up for. In this case that doesn't matter. However, for future apps it may be problemmatic.

There does seem to be an option to set up a custom domain name in Heroku which would require registering a domain name. In this case you'll be able to follow the steps to config the DNS. Otherwise, don't worry about it!


## That's All Folks!

That's it. Following the previous steps should be enough to get Mailgun to run on your Heroku app with your own Mailgun account. If you run into any issues drop me a line and I'll see if I can help.
