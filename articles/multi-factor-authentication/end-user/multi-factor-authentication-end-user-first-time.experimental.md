---
title: "为工作或学校帐户设置双重验证 | Microsoft Docs"
description: "如果公司已配置 Azure 多重身份验证，系统会提示注册双重验证。 了解设置方法。 "
services: multi-factor-authentication
keywords: "如何使用 azure 目录, 云中的 active directory, active directory 教程"
documentationcenter: 
author: barlanmsft
manager: angrobe
editor: pblachar
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: e74f2cbeca5385b2e766d02a5f3f8ba3a815c91f
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-my-account-for-two-step-verification"></a>为帐户设置双重验证
双重验证是额外的安全保障步骤，通过加大他人攻破的难度，帮助保护帐户安全。 如果正在阅读本文，可能已收到公司或学校管理员发送的有关多重身份验证的电子邮件。 或者，可能已尝试登录，但收到要求设置其他安全验证的消息。 如果是这种情况，**除非完成自动注册过程，否则无法登录**。

本文可帮助你设置工作或学校帐户。 如果要为个人 Microsoft 帐户启用双重验证，请参阅[关于双重验证](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)。

## <a name="set-up-your-account"></a>设置帐户

IT 部门要求开始使用双重验证时，会出现显示“管理员要求你将此帐户设置为进行额外安全验证”的屏幕：

![设置](./media/multi-factor-authentication-end-user-first-time/first.png)

若要开始，请选择“立即设置”。

登录时如果未出现类似屏幕，请按照[管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page)中的指示操作，找到可以管理验证选项的设置页面。 

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>确定验证登录的方式

注册过程中的第一个问题是希望使用哪种联系方式。 请查看表中的选项，通过链接了解每种方法的设置步骤。

| 联系方式 | 说明 |
| --- | --- |
| [移动应用](#use-a-mobile-app-as-the-contact-method) |- 接收验证通知。 此选项将通知推送到智能手机或平板电脑上的验证器应用。 查看通知，如果合法，则在应用中选择“身份验证”。 公司或学校可能要求在身份验证之前，输入 PIN。<br>- 使用验证码。 在此模式下，验证器应用生成每隔 30 秒更新一次的验证码。 在登录界面中输入最新验证码。<br>Microsoft Authenticator 应用可用于 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)。 |
| [移动电话呼叫或短信](#use-your-mobile-phone-as-the-contact-method) |- 电话呼叫向你提供的手机号码进行自动语音呼叫。 接听电话，并按电话键盘上的 # 进行身份验证。<br>- 短信发送包含验证码的短信。 遵循短信中的提示，回复短信或在登录界面中输入提供的验证码。 |
| [办公电话呼叫](#use-your-office-phone-as-the-contact-method) |向已提供的电话号码进行自动语音呼叫。 接听电话，并按电话键盘上的 # 进行身份验证。 |

## <a name="use-a-mobile-app-as-the-contact-method"></a>使用移动应用作为联系方式
使用此方法要求在手机或平板电脑上安装验证器应用。 本文中的步骤基于 Microsoft Authenticator 应用，该应用可用于 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)。

1. 从下拉列表中选择“移动应用”。
2. 选择“接收验证通知”或“使用验证码”，并选择“设置”。

   ![“其他安全性验证”屏幕](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. 在手机或平板电脑上，打开应用并选择 **+** 来添加帐户。 （在 Android 设备上，选择三个点图标，并选择“添加帐户”。）
4. 指定要添加工作帐户或学校帐户。 手机上的二维码扫描器此时打开。 如果相机未正常工作，可以选择手动输入公司信息。 有关详细信息，请参阅[手动添加帐户](#add-an-account-manually)。  
5. 扫描与用于配置移动应用的屏幕一起显示的 QR 码图片。  选择“完成”关闭二维码屏幕。  

   ![QR 码屏幕](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. 在手机上完成激活后，选择“与我联系”。  此步骤会将通知或验证码发送到手机。 选择“验证”。  
7. 如果公司需要 PIN 才能批准登录验证，请输入它。

   ![用于输入 PIN 的框](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. 填入 PIN 条目后，选择“关闭”。 此时，验证应已成功。
9. 建议输入手机号码，以免无法访问移动应用。 通过下拉列表指定国家/地区，并在国家/地区名称旁边的框中输入手机号码。 选择“下一步”。
10. 此时，系统会提示为非浏览器应用（例如 Outlook 2010 或更低版本，或 Apple 设备上的本机电子邮件应用）设置应用密码。 之所以执行此操作是因为某些应用不支持双重验证。 如果不使用这些应用，请单击“完成”并跳过其余步骤。
11. 如果要使用这些应用，请将提供的应用密码（而不是常规密码）复制并粘贴到应用程序中。 可以将相同应用密码用于多个应用。 有关详细信息，请参阅[应用密码帮助]。
12. 单击“Done”（完成） 。

### <a name="add-an-account-manually"></a>手动添加帐户
如果要手动将帐户添加到移动应用，而不使用二维码，请执行以下步骤。

1. 选择“手动输入帐户”按钮。  
2. 输入显示条形码的同一页面上所提供的代码和 URL。 此信息将填入移动应用上的“代码”和“URL”框中。

    ![设置](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. 激活完成后，选择“与我联系”。 此步骤会将通知或验证码发送到手机。 选择“验证”。

## <a name="use-your-mobile-phone-as-the-contact-method"></a>使用手机作为联系方法
1. 从下拉列表中选择“身份验证电话”。  

    ![设置](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. 通过下拉列表选择国家/地区，并输入手机号码。
3. 选择想要使用的手机通信模式：短信或呼叫。
4. 选择“联系我”验证电话号码。 根据所选模式，我们将发送短信或拨打电话。 按照屏幕上的说明操作，并选择“验证”。
5. 此时，系统会提示为非浏览器应用（例如 Outlook 2010 或更低版本，或 Apple 设备上的本机电子邮件应用）设置应用密码。 之所以执行此操作是因为某些应用不支持双重验证。 如果不使用这些应用，请单击“完成”并跳过其余步骤。
6. 如果要使用这些应用，请将提供的应用密码（而不是常规密码）复制并粘贴到应用程序中。 可以将相同应用密码用于多个应用。 有关详细信息，请参阅[应用密码帮助]。
7. 单击“Done”（完成） 。

## <a name="use-your-office-phone-as-the-contact-method"></a>使用办公电话作为联系方式
1. 从下拉列表中选择“办公电话”  

    ![设置](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. 电话号码框自动填充公司联系信息。 如果号码错误或丢失，请联系管理员进行更改。
3. 选择“联系我”验证电话号码，我们将拨打该号码。 按照屏幕上的说明操作，并选择“验证”。
4. 此时，系统会提示为非浏览器应用（例如 Outlook 2010 或更低版本，或 Apple 设备上的本机电子邮件应用）设置应用密码。 之所以执行此操作是因为某些应用不支持双重验证。 如果不使用这些应用，请单击“完成”并跳过其余步骤。
5. 如果要使用这些应用，请将提供的应用密码（而不是常规密码）复制并粘贴到应用程序中。 可以将相同应用密码用于多个应用。 有关详细信息，请参阅[什么是应用密码](multi-factor-authentication-end-user-app-passwords.md)。
6. 单击“Done”（完成） 。

## <a name="next-steps"></a>后续步骤
* 更改首选项和[管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)
* 为不支持双重验证的本地设备应用设置[应用密码](multi-factor-authentication-end-user-app-passwords.md)。
* 使用 [Microsoft Authenticator 应用](microsoft-authenticator-app-how-to.md)完成快速、安全的身份验证（即使没有手机网络服务）。

