---
layout: post
title: Deploy private app via Android Enterprise
subtitle: How to deploy a private app (.apk) on Android Enterprise (AE) devices via the Play Store
tags: [Android, Android Enterprise, Play Store, App]
readtime: true
comments: true
author: Mathieu Beaugrand
---
## Why host your APK with Google:
1. Reliability and service
    1. High-uptime Google servers
    2. Automated device compatibility testing
    3. Faster downloads
    4. Compressed patch updates, rather than downloading the whole APK each time your app is updated.
    5. Benefit from Cloud Test Lab pre-launch reports which can help you identify runtime issues across a range of Android devices before your app hits the field.
2. Easy administration
    1. Google-managed servers, so you don’t have to manage or support your own server
    2. Alpha and beta channels to manage new app pilot programs and new app versions
    3. Automatic generation of APK store-listing meta-data, which allows compatibility with Android Wear, Android Auto, and Android TV
3. Security
    1. Google’s enterprise-grade security, including SSL downloads.
    2. Malware security scanning.
    3. Alerts if we detect security vulnerabilities in versions of SDKs that you’re using, such as Heartbleed in OpenSSL.

## How to:
Manage Google Play private apps: <https://support.google.com/a/answer/2494992?hl=en>
Publish private apps from the Play Console: <https://support.google.com/googleplay/android-developer/answer/6145139?hl=en>

## Best practise and checklist:
Google Launch checklist: <https://developer.android.com/distribute/best-practices/launch/launch-checklist>