---
layout: post
comments: true
title: 最近iOS App审核上架遇到的问题小结
---

最近上架了2个app，遇到了点小问题，还好经过几天折腾之后，审核通过了；

#### 遇到的问题点

#####  三方登陆的问题，4.2.3 & 4.8.0

 Bug Fix Submissions

The issues we've identified below are eligible to be resolved on your next update. If this submission includes bug fixes and you'd like to have it approved at this time, reply to this message and let us know. You do not need to resubmit your app for us to proceed.

Alternatively, if you'd like to resolve these issues now, please review the details, make the appropriate changes, and resubmit.

Guideline 4.2.3 - Design - Minimum Functionality


We were required to install the WeChat app before we could log in via WeChat. Users should be able to log in with WeChat and access their accounts without having to install any additional apps.

Next Steps

If you would like to offer authentication through WeChat, please use a mechanism that allows users to log in with WeChat from within your app without first having to install an additional app.

We recommend implementing the Safari View Controller API to display web content within your app. The Safari View Controller allows the display of a URL and inspection of the certificate from an embedded browser in an app so that customers can verify the webpage URL and SSL certificate to confirm they are entering their sign in credentials into a legitimate page.

Resources

For additional information on the Safari View Controller API, please review the [What's New in Safari](https://developer.apple.com/library/archive/releasenotes/General/WhatsNewInSafari/Introduction/Introduction.html) webpage.

Guideline 4.8 - Design - Sign in with Apple


Your app uses a third-party login service, but does not offer Sign in with Apple. Apps that use a third-party login service for account authentication need to offer Sign in with Apple to users as an equivalent option to provide the sign-in experience App Store users expect.

Next Steps

Please revise your app to offer Sign in with Apple as an equivalent option for account authentication.

Resources

- Review [Sign in with Apple sample code](https://developer.apple.com/documentation/authenticationservices/implementing_user_authentication_with_sign_in_with_apple). 
- For an overview of design and formatting recommendations for Sign in with Apple, see the [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/technologies/sign-in-with-apple/introduction) .
- Learn about the [benefits of Sign in with Apple](https://developer.apple.com/sign-in-with-apple/).

Please see attached screenshots for details. 
iOS App 8.1.0App 版本
拒绝原因：

4.2.3 Design: Minimum Functionality

4.8.0 Design: Sign in with Apple

可能是因为这个app是由原来的账号转让过来的，审核人员比较客气，只是让承诺下个版本修复这个问题；

回复承诺下个版本修复，就过审核了；

好久没折腾上架的事情了，后面经同事提醒，看了一下苹果的文档，的确有提到如果接入了三方登陆，那就必须接入苹果登陆；

接入苹果登陆对我们的应用来说意义不大，后续会检测到未安装微信，就直接隐藏掉相应的选项；

***

下面是新app上架遇到的问题
##### 元数据 2.3.1
Guideline 2.3.1 - Performance

We discovered that your app contains hidden features. 
The next submission of this app may require a longer review time.

Next Steps

- Review the Performance section of the [App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/).
- Ensure your app is compliant with all sections of the [App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/) and the [Terms & Conditions](https://developer.apple.com/terms/) of the Apple Developer Program. 
- Once your app is fully compliant, resubmit your app for review.

Submitting apps designed to mislead or harm customers or evade the review process may result in the termination of your Apple Developer Program account. Review the Terms & Conditions of the Apple Developer Program to learn more about our policies regarding termination.

iOS App 1.1.0App 版本
拒绝原因：

2.3.1 Performance: Accurate Metadata

回复了苹果审核，问一下到底是哪里有问题，但是给的回复和这个没啥区别；咨询了一下有经验的同事，意思是他遇到的时候，最后靠嘴上功夫扯皮过审核了；不过关于元数据，大概怀疑的方向是描述、截图和审核人员打开app时有差别；先把流程较深的截图去掉；

截图里面的宣传是列表中有数据的情况，其实审核人员登陆的时，列表里是没数据的；

因为app里的列表里的数据是和定位相关的，这个要么针对审核账号弄假数据，或者把截图换成没数据的截图吧，别折腾乱七八糟的功能宣传；

后来选择了录制一下有数据时视频，上传一下，并且在备注里写清楚有数据、没数据的情况说明；

##### 2.1.0、4.2.3、4.8.0
Hello,

Thank you for your resubmission. Upon further review, we identified an additional issue that needs your attention. See below for more information.

If you have any questions, we are here to help. Reply to this message in App Store Connect and let us know. 

Guideline 2.1 - Information Needed


We’re looking forward to completing the review of your app, but we need more information to continue.

Next Steps

Please provide detailed answers to the following questions in your reply to this message in App Store Connect:

- Do users have to pay or make purchase in order to become a member?
- Do users have to pay or make purchase in order to advance in the membership structure?
- Can users make profit from lower tier members’ membership fees or transactions?
- What kind of services or products users are paying for?
- Please provide more details on "更多活动". 

Guideline 4.2.3 - Design - Minimum Functionality


We were required to install the WeChat app before we could log in via WeChat. Users should be able to log in with WeChat and access their accounts without having to install any additional apps.

Next Steps

If you would like to offer authentication through WeChat, please use a mechanism that allows users to log in with WeChat from within your app without first having to install an additional app.

We recommend implementing the Safari View Controller API to display web content within your app. The Safari View Controller allows the display of a URL and inspection of the certificate from an embedded browser in an app so that customers can verify the webpage URL and SSL certificate to confirm they are entering their sign in credentials into a legitimate page.

Resources

For additional information on the Safari View Controller API, please review the What's New in Safari webpage.

Guideline 4.8 - Design - Sign in with Apple


Your app uses a third-party login service, but does not offer Sign in with Apple. Apps that use a third-party login service for account authentication need to offer Sign in with Apple to users as an equivalent option to provide the sign-in experience App Store users expect.

Next Steps

Please revise your app to offer Sign in with Apple as an equivalent option for account authentication.

Resources

- Review Sign in with Apple sample code. 
- For an overview of design and formatting recommendations for Sign in with Apple, see the Human Interface Guidelines .
- Learn about the benefits of Sign in with Apple.
Please see attached screenshots for details. 
iOS App 1.1.0App 版本
拒绝原因：

2.1.0 Performance: App Completeness

4.2.3 Design: Minimum Functionality

4.8.0 Design: Sign in with Apple

审核人员问询的问题，让产品回复去吧，尽量避免扯上内购的问题，遇到问题尽量往线下服务上靠吧；

三方登陆的事情躲不过了，需要修改代码，把微信登陆根据是否安装先隐藏；

本来以为应该会过审核了，结果又被拒绝了；

##### 5.1.1
Hello,

Thank you for your efforts to follow our guidelines. There are still some issues that need your attention.

If you have any questions, we are here to help. Reply to this message in App Store Connect and let us know. 

Guideline 5.1.1 - Legal - Privacy - Data Collection and Storage


We noticed your app encourages or directs users to allow the app to access the photos and location. Specifically, your app directs the user to grant permission in the following way(s): 

- The user is redirected to the Settings app to grant access after tapping "Don't Allow."
- A message appears before the permission request, and to proceed users press a “我知道了” button. Use words like "Continue" or "Next" on the button instead.

Permission requests give users control of their personal information. It is important to respect their decision about how their data is used.

Next Steps

To resolve this issue, please revise the permission request process in your app to not direct users to the Settings app after they deny permission and display messages before the permission request with inappropriate words on buttons.

If necessary, you may provide more information about why you are requesting permission before the request appears. If the user is trying to use a feature in your app that won't function without access to the photos and location, you may include a notification to inform the user and provide a link to the Settings app. 

Resources 

- Watch a video from App Store Review with [tips for doing more for users with less data](https://developer.apple.com/go/?id=ar-tips-3).
- Learn more about designing [appropriate permission requests](https://developer.apple.com/design/human-interface-guidelines/patterns/accessing-private-data/).
Please see attached screenshots for details. 
iOS App 1.1.0App 版本
拒绝原因：

5.1.1 Legal: Privacy - Data Collection and Storage

这里遇到两个问题，一个是授权说明弹框的按钮文案是有要求的，需要改成“继续”或者“下一步”；这里建议看一下交互设计规范，的确有说明如果是授权说明前置弹框，按钮文案是有要求的，且按钮数量也是有要求的；

另外一个问题是相册授权拒绝时，不要立即弹框给用户提示，让用户去设置里授权处理；后面用户再点击相应功能，如果没有授权，是可以有弹框引导说明的；

把这两个问题处理了，审核终于过了；

##### App 审核加快请求
简称加急，点击 “联系我们” -> "App审核加快请求",进入加急提交表单。

Note: On average, 90% of submissions are reviewed in less than 24 hours. However, if you’re facing extenuating circumstances, such as fixing a critical bug or releasing your app to coincide with an event, you can request an expedited review by completing this form. Please keep in mind that:
Only submissions that have been sent to App Review can be expedited.
Submitting excessive expedited requests may lead to future expedite requests not being approved.

注：平均而言，90%的提交材料在不到24小时的时间内得到审查。然而，如果您面临情有可原的情况，例如修复关键错误或发布应用程序以配合活动，您可以通过填写此表格来请求快速审查。请记住：
只有已发送到App Review的提交才能被加速。
提交过多的加速请求可能会导致未来的加速请求未获批准。

传言每年有3次加急，可能只是传言，应该没有明确的次数限制；

#### 小结
1. 如果是app的第一版上架，可能遇到很多问题，因为周期可能比较长，可以考虑走审核加急；加急后，直到这个版本审核过了，前面的修改都是属于加急状态，可以大量的减少等待时间；
2. 有不清楚的地方，与苹果审核沟通请求再重新提交审核，沟通回复的时间一般比重新的审核速度要快；避免无效的重复提交；

***
##### 5.0

Guideline 5.0 - Legal
Your app contains content - or facilitates, enables, and encourages an activity - that is not legal in all of the locations where the app is available. Specifically, your app includes multi-level marketing features. 
Next Steps 
To resolve this issue, please remove all content and features that are illegal in the locations where your app is available. Please note that app concepts that are considered illegal are not allowed on the App Store.
Please see attached screenshots for details.

拒绝原因：5.0.0 Legal: Preamble

推广活动页面有多层次营销功能，根据测试账号屏蔽相关功能

##### 5.1.1 又一次被拒
大致意思是相册申请权限描述文案不够明确，需要包含用户如何使用的例子场景，修改文案后过审；
