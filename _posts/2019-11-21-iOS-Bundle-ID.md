---
layout: post
title: Find iOS app bundle ID
subtitle: Step by step guide on how to find an iOS app bundle ID
tags: [Apple, iOS, App]
readtime: true
comments: true
author: Mathieu Beaugrand
---
There is currently no way to look up app bundle IDs in the Apple iOS App Store directly. To find the identifier, you will need to look at a file inside the app.

## For Apps in the App Store
1.	Search for an app in the App Store via the web - <https://www.apple.com/ios/app-store/>
2.	Extract the unique ID in the URL
    1.	For Pages: <https://apps.apple.com/app/pages/id361309726>
3.	Open a new tab in your browser and go to <https://itunes.apple.com/lookup?id=361309726>
    1.	Replace `361309726` with the unique ID found in step 2
4.	Download the file to your computer
5.	Open the file downloaded and search for “bundleId”
    1.	For Pages: com.apple.Pages
