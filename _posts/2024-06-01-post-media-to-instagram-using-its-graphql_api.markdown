---
title: Post Media To Instagram Using Its Graphql API
date: 2024-06-01 12:00:00 -500
categories: [programming]
tags: [Tutorial,Graphql,Instagram]
---

I wanted to automate the posting of media to Instgram so I started researching what was needed thinking it could be as easy as it was for X (formerly known as Twitter) and quickly noticed how messy and comboluted the entire process is, here I'll be going over the current steps needed to accomplish the goal starting from zero.

### Game Plan

* Set Instagram account to Business.
* Create Facebook app.
* Create Facebook Page and link to Instagram.
* Post to Instagram.

#### Set Instagram Account to Business

1. Tap your profile picture
2. Tap **Edit profile**
3. Scroll down and tap on **Switch to profession account**
4. Tap **Continue**
5. Select **Business** and tap **Next**.

#### Create Facebook App

Head over to [Meta for Developers](https://developers.facebook.com/), login and click on the **Create App** button. When selecting an use case choose **Other** and click **Next**.
Now we need to choose the type, we need to access the GraphQL api so we will select **Business** and click **Next**

Once back on the app's dashboard scroll down to **Add products to your app**, find **Instagram Graph API** and click on **Set up**.

#### Create Facebook Page & Link Instagram Account

##### Create Facebook Page

Use your Facebook Login to access [Meta Business Suite](https://business.facebook.com/)
go to **Settings** and under **Accounts** select **Pages** and click on **+ Add**, set a title and save. The Facebook Page is a necessary step needed to link our instagram account to our Facebook account.

##### Link Instagram account from Settings

without leaving the **Settings** page select **Instagram Accounts** under **Accounts** and click on **+ Add**.

After the instagram account has been added you should see the **Instagram account ID** under the **Summary** Tab, copy and paste that somewhere because we are gonna needed soon.

##### Link Instagram account from Facebook Page

From the **Settings** page, head over the left side menu and click on **Home**. Here you should see your newly created Facebook Page and right under the page's name you should see two hyperlinks, one to edit facebook page and another one to add Instagram account. Click on add Instagram account and follow the steps.

#### Post to Instagram.

We will be using the [Graph API Explorer](https://developers.facebook.com/tools/explorer) to make our calls to the API. From this explorer we are gonna select the necesarry permission before generating the **Access Token** needed to make the request. As of today the necessary permissions are the following: **pages_show_list, ads_management, business_management, instagram_basic, instagram_content_publish and pages_read_engagement**. Once you add them click on **Generate Access Token**.

Using the [API's Docs](https://developers.facebook.com/docs/instagram-api/guides/content-publishing/) you will see that posting media is a two step process with two different end points. 

* First we make a `POST` call to `/{ig-user-id}/media` where **{ig-user-id}** is the ID we copied in the **Link Instagram account from Settings** step passing only the `image_url` parameter (for its value Ill using a remote image of a cat by the CDC https://www.cdc.gov/importation/images/cat.jpg).

```
POST <-- https://graph.facebook.com/version/{ig-user-id}/media
```

If everything is working fine we should get the container id with the response like so:

```
{
    id: 12345234772452
}
```

* Second we publish the container by sending the container id as a parameter on our call. If everything worked as expected we should resive another ID back with our response, this one will be the IG Media ID.

```
POST https://graph.facebook.com/version/{ig-user-id}/media_publish?creation_id=12345234772452
```
and TA DA, we are done.

### Conclusion