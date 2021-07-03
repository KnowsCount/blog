---
title: "\U0001F912 bitter lesson installing BBRPlus"
---

A friend of mine charged me $1500 on vultr recently and I therefore started my journey exploring cloud instances. It was a pleasure—I've been learning something new every day since then for I, as someone who specialises more in front-end, do not know servers very well at all.

I firstly deployed a CentOS 9 VPS with 4 cores, 8GB ram in Soeul, Korea. I installed LNMP packages on it, and hosted some websites on it. Everything was good, until I installed an open-source (not entirely—there is a pro version which costs roughly 200 CNY) web drive programme and found that the download speed does not satisfy me at all. So, I started researching about optimising network of a VPS, and found BBR(Plus).

For some weird reason, just hours after I found BBR, download speed of my server grew and became a few mb/s; yet after some cogitation, I decided to try it nevertheless—I mean, fuck it, I can uninstall it later anyway.

But my research, evidently, was not enough. It turned out that BBRPlus will delete some of the cores of CentOS 9. So my server was basically dead after I approved everything the install script tried to do. I could `ssh` it no more; could visit my websites hosted no more... anyways, it was a catastrophe.

So please, just enabling BBR on CentOS 9 is enough... after all, my new server located in Japan had a most astonishing result according to my speedtest—some two thousand mbit/s... that's like 300mb/s!
