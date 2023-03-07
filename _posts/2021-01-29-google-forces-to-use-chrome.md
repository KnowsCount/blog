---
title: "\U0001F310 Forcing us to Use Chrome?!"
tags: [blog]
---

<!--
 * @Date: 29/01/2021 14.14.20 +0800
 * @Author: KnowsCount
 * @LastEditTime: 29/01/2021 15.40.31 +0800
 * @FilePath: /myBlog/_posts/2021-01-29-google-forces-to-use-chrome.md
-->

In a recent [Chromium blog post](https://blog.chromium.org/2021/01/limiting-private-api-availability-in.html), it was stated:

> During a recent audit, we discovered that some third-party Chromium based browsers were able to integrate Google features, such as Chrome sync and Click to Call, that are only intended for Google’s use. This meant that a small fraction of users could sign into their Google Account and store their personal Chrome sync data, such as bookmarks, not just with Google Chrome, but also with some third-party Chromium based browsers. We are limiting access to our private Chrome APIs starting on March 15, 2021.

![photo-1501250987900-211872d97eaa](https://knowscount-1304485449.cos.ap-shanghai.myqcloud.com/img/photo-1501250987900-211872d97eaa.jpeg)

The Google Chrome teams has also sent an [email](https://groups.google.com/a/chromium.org/g/embedder-dev/c/NXm7GIKTNTE/m/Qxcew5lfCAAJ?pli=1), announcing their future plans to ban third-party browsers from using account synchronisation, and this irritated Linux distribution developers.

It is reported that from 15 March, users of third-party browsers based on Chromium using the configurations of `google_default_client_id` and `google_default_client_secret` will not be able to sign in to their Google Accounts.

According to Google, they – in recent audits – found some Chromium based browsers using services and APIs designed only for Google Chrome, most importantly, Chrome Sync API. Therefore, Google decided to remove the visiting of these APIs from third parties, claiming the decision is made as to enhance data security of users.

Linux distribution developers who have been using those APIs and services for the past decade, on the other hand, evinced their angriness and expressed it to Google by saying they will have to **cease from maintaining Chromium software package** as it will become pointless. Jochen Eisinger, Chrome's Director of Engineering, responded that **they will firmly stick to their decision in future**.

Up to this point, Slackware Linux and Arch Linux are already considering to **remove Chromium from their repos**. Take Arch Linux's [thread](https://lists.archlinux.org/pipermail/arch-dev-public/2021-January/030263.html) as an example:

> Jochen Eisinger (Director of Engineering, Chrome) has confirmed that the killing of our API keys is a done deal. He also does not seem interested in the slightest bit to explore possible remedies for Chromium packages ... I am going to stop maintaining Chromium and be in favor of dropping the package from our repos, though I have a feeling someone will want to adopt it despite the reduction in functionality.
