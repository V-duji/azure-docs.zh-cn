---
title: "Azure AD Connect：无缝单一登录 - 快速入门 | Microsoft 文档"
description: "本文介绍如何开始使用 Azure Active Directory 无缝单一登录。"
services: active-directory
keywords: "什么是 Azure AD Connect, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: billmath
ms.openlocfilehash: 9d91c59d3e4d73879d95ab193949d54f7b86d6cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory 无缝单一登录：快速入门

## <a name="how-to-deploy-seamless-sso"></a>如何部署无缝 SSO

Azure Active Directory 无缝单一登录（Azure AD 无缝 SSO）可使登录连接到企业网络的企业台式机用户自动登录。 此功能可让用户轻松访问基于云的应用程序，而无需使用其他任何本地组件。

要部署无缝 SSO，需要遵循以下步骤：

## <a name="step-1-check-prerequisites"></a>步骤 1：检查先决条件

请确保符合以下先决条件：

1. 设置 Azure AD Connect 服务器：如果使用[直通身份验证](active-directory-aadconnect-pass-through-authentication.md)作为登录方法，则无需进一步操作。 如果使用[密码哈希同步](active-directory-aadconnectsync-implement-password-synchronization.md)作为登录方法，并且 Azure AD Connect 与 Azure AD 之间有防火墙，则请确保：
- 你所使用的是 Azure AD Connect 版本 1.1.484.0 或更高版本。
- Azure AD Connect 可与 `*.msappproxy.net` URL 进行通信，并可通过端口 443 进行通信。 此先决条件仅适用于启用了该功能的情况，不适用于实际用户登录。
- Azure AD Connect 可以与 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)建立直接 IP 连接。 再次重申，此先决条件仅适用于启用了该功能的情况。
2. 需要与 Azure AD 同步（使用 Azure AD Connect）的每个 AD 林以及要为其用户启用无缝 SSO 的域管理员凭据。

## <a name="step-2-enable-the-feature"></a>步骤 2：启用功能

可使用 [Azure AD Connect](active-directory-aadconnect.md) 启用无缝 SSO。

如果你要执行 Azure AD Connect 全新安装，请选择[自定义安装路径](active-directory-aadconnect-get-started-custom.md)。 在“用户登录”页上，选中“启用单一登录”选项。

![Azure AD Connect - 用户登录](./media/active-directory-aadconnect-sso/sso8.png)

如果你已安装了 Azure AD Connect，请在 Azure AD Connect 上选择“更改用户登录页”，并单击“下一步”。 然后选中“启用单一登录”选项。

![Azure AD Connect - 更改用户登录](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

在向导中继续操作，直到出现“启用单一登录”页。 提供与 Azure AD 同步的（使用 Azure AD Connect）的每个 AD 林以及要为其用户启用无缝 SSO 的域管理员凭据。 

完成向导后，租户即启用了无缝 SSO。

>[!NOTE]
> 域管理员凭据不存储在 Azure AD Connect 或 Azure AD 中，仅用于启用该功能。

按照这些说明验证是否已正确启用无缝 SSO：

1. 使用租户的全局管理员凭据登录到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。
2. 在左侧导航栏中，选择“Azure Active Directory”。
3. 选择“Azure AD Connect”。
4. 验证无缝单一登录功能是否显示为“已启用”。

![Azure 门户 - Azure AD Connect 边栏选项卡](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>步骤 3：扩展此功能

若要向用户推广该功能，需要使用 Active Directory 中的组策略将以下 Azure AD URL 添加到用户的 Intranet 区域设置：

- https://autologon.microsoftazuread-sso.com
- https://aadg.windows.net.nsatc.net

>[!NOTE]
> 以下说明仅适用于 Windows 上的 Internet Explorer 和 Google Chrome（如果它与 Internet Explorer 共用一组相同的受信任站点 URL）。 请阅读下一节，了解在 Mac 上设置 Mozilla Firefox 和 Google Chrome 的说明。

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>为什么需要修改用户的 Intranet 区域设置？

默认情况下，浏览器自动从 URL 计算适当的区域（Internet 或 Intranet）。 例如，http://contoso/ 将映射到 Intranet 区域，而 http://intranet.contoso.com/ 将映射到 Internet 区域（因为 URL 包含句点）。 与两个 Azure AD URL 类似，浏览器不会将 Kerberos 票证发送到云终结点，除非该终结点的 URL 已显式添加到浏览器 Intranet 区域。

### <a name="detailed-steps"></a>详细步骤

1. 打开“组策略管理”工具。
2. 编辑适用于部分或全部用户的组策略。 在此例中，我们使用“默认域策略”。
3. 导航到“用户配置\管理模板\Windows 组件\Internet Explorer\Internet 控制面板\安全性”页面，并选择“区域分配列表的站点”。
![单一登录](./media/active-directory-aadconnect-sso/sso6.png)  
4. 启用该策略，并在对话框中输入以下值（Kerberos 票证所转发到的 Azure AD URL）和数据（1 表示 Intranet 区域）。

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1
>[!NOTE]
> 如果想禁止某些用户使用无缝 SSO，例如，如果这些用户登录共享的电话亭，请将以上值设置为 4。 此操作将 Azure AD URL 添加到受限区域，并且始终无法使用无缝 SSO。

5. 请单击两次“确定”。

![单一登录](./media/active-directory-aadconnect-sso/sso7.png)

### <a name="browser-considerations"></a>浏览器注意事项

#### <a name="mozilla-firefox"></a>Mozilla Firefox

Mozilla Firefox 不会自动执行 Kerberos 身份验证。 每个用户必须使用以下步骤手动将 Azure AD URL 添加到其 Firefox 设置：
1. 运行 Firefox 并在地址栏中输入 `about:config`。 关闭你看到的任何通知。
2. 搜索 network.negotiate-auth.trusted-uris 首选项。 此首选项列出了用于 Kerberos 身份验证的 Firefox 的受信任站点。
3. 右键单击并选择“修改”。
4. 在该字段内输入“https://autologon.microsoftazuread-sso.com、https://aadg.windows.net.nsatc.net”。
5. 单击“确定”并重新打开浏览器。

#### <a name="safari-on-mac-os"></a>Mac OS 上的 Safari

确保运行 Mac OS 的计算机已加入 AD。 有关如何执行该操作的说明，请参阅[此处](http://training.apple.com/pdf/Best_Practices_for_Integrating_OS_X_with_Active_Directory.pdf)。

#### <a name="google-chrome-on-mac-os"></a>Mac OS 上的 Google Chrome

对于 Mac OS 和其他非 Windows 平台上的 Google Chrome，请参阅[此文](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist)，了解有关如何针对集成身份验证将 Azure AD URL 列入允许列表的信息。

使用第三方 Active Directory 组策略扩展将 Azure AD URL 扩展到 Mac 上的 Firefox、Google Chrome，不在本文讨论范围之内。

#### <a name="known-browser-limitations"></a>已知的浏览器限制

无缝 SSO 在 Firefox 和 Edge 浏览器的隐私浏览模式下不起作用。 它在以增强保护模式运行的 Internet Explorer 中也不起作用。

>[!IMPORTANT]
>我们最近中止了对 Microsoft Edge 的支持，以调查客户报告的问题。

## <a name="step-4-test-the-feature"></a>步骤 4：测试功能

要测试特定用户的功能，请确保符合以下所有条件：
  - 用户正在登录到某个企业设备。
  - 该设备事先已加入 Active Directory (AD) 域。
  - 该设备已通过远程访问连接（例如 VPN 连接）与企业有线或无线网络中的域控制器 (DC) 建立了直接连接。
  - 你可以使用组策略[将此功能扩展到](##step-3-roll-out-the-feature)该用户。

要测试用户仅输入用户名而不是密码的场景：
- 在新的私密浏览会话中登录到 https://myapps.microsoft.com/。

要测试用户并非必须输入用户名或密码的场景： 
- 在新的私密浏览会话中登录到 https://myapps.microsoft.com/contoso.onmicrosoft.com。 将“contoso”替换为租户的名称。
- 或在新的私密浏览会话中登录到 https://myapps.microsoft.com/contoso.com。 将“contoso.com”替换为租户中的已验证域（而不是联盟域）。

## <a name="step-5-roll-over-keys"></a>步骤 5：滚动更新密钥

在步骤 2 中，Azure AD Connect 在已启用无缝 SSO 的所有 AD 林中创建计算机帐户（表示 Azure AD）。 在[此处](active-directory-aadconnect-sso-how-it-works.md)了解更多详细信息。 为了提高安全性，建议定期滚动更新这些计算机帐户的 Kerberos 解密密钥。 有关如何进行滚动更新的说明，请参阅[此处](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account)。

>[!IMPORTANT]
>启用该功能后无需_立即_执行此步骤。 至少每隔 30 天滚动更新一次 Kerberos 解密密钥。

## <a name="next-steps"></a>后续步骤

- [深入技术探究](active-directory-aadconnect-sso-how-it-works.md) - 了解此功能如何运作。
- [**常见问题**](active-directory-aadconnect-sso-faq.md) - 常见问题解答。
- [故障排除](active-directory-aadconnect-troubleshoot-sso.md) - 了解如何解决使用此功能时遇到的常见问题。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。
