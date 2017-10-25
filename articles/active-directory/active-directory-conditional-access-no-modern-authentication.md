---
title: Configurar o SharePoint Online e o Exchange Online para acesso condicional do Azure Active Directory | Microsoft Docs
description: Saiba como configurar o SharePoint Online e o Exchange Online para acesso condicional do Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5d09021627ca60a94cb1984b29b8dc7913d093d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>Configurar o SharePoint Online e o Exchange Online para acesso condicional do Azure Active Directory 

Com o [acesso condicional do Azure AD (Azure Active Directory)](active-directory-conditional-access-azure-portal.md), você pode controlar como os usuários acessam seus aplicativos de nuvem. Se você quiser usar o acesso condicional para controlar o acesso ao SharePoint e ao Exchange Online, você precisará:

- Examinar se há suporte para seu cenário de acesso condicional
- Impedir que aplicativos de cliente ignorem a imposição de suas políticas de acesso condicional.   

Este artigo explica como você pode resolver os dois casos.


## <a name="what-you-need-to-know"></a>O que você precisa saber

Você pode usar o acesso condicional do Azure AD para proteger os aplicativos de nuvem quando uma tentativa de autenticação for proveniente de:

- Um navegador da Web

- Um aplicativo de cliente que usa [autenticação moderna](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)

- Exchange ActiveSync 

Alguns aplicativos de nuvem também dão suporte a protocolos de autenticação herdados. Isso se aplica, por exemplo, ao SharePoint Online e ao Exchange Online. Quando um aplicativo de cliente pode usar um protocolo de autenticação herdado para acessar um aplicativo de nuvem, o Azure AD não pode impor uma política de acesso condicional nessa tentativa de acesso. Para impedir que um aplicativo de cliente ignore a imposição de políticas, verifique se é possível habilitar a autenticação moderna apenas nos aplicativos de nuvem afetados. 

Entre os exemplos em que o acesso condicional de aplicativos de cliente não se aplica estão:

- Office 2010 e versões anterior

- Office 2013 quando a autenticação moderna não estiver habilitada



 
## <a name="control-access-to-sharepoint-online"></a>Controlar o acesso ao SharePoint Online

Além de autenticação moderna, o SharePoint Online também dá suporte a protocolos de autenticação herdados. Se os protocolos de autenticação herdados estiverem habilitados, suas políticas de acesso condicional para SharePoint não serão impostas para clientes que não usam a autenticação moderna.

Você pode desabilitar os protocolos de autenticação herdados para acesso do SharePoint usando o cmdlet **[Set-SPOTenant](https://technet.microsoft.com/library/fp161390.aspx)**: 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Controlar o acesso ao Exchange Online

Quando você configura políticas de acesso condicional para o Exchange Online, examine o seguinte:

- Exchange ActiveSync

- Protocolos de autenticação herdados



### <a name="exchange-activesync"></a>Exchange ActiveSync

Embora o Exchange Active Sync dê suporte à autenticação moderna, há algumas limitações no suporte para cenários de acesso condicional:

- Você só pode configurar a condição de plataformas do dispositivo  

    ![Plataformas de dispositivo](./media/active-directory-conditional-access-no-modern-authentication/05.png)

- Não há suporte para a definição do requisito de autenticação multifator  

    ![Acesso condicional](./media/active-directory-conditional-access-no-modern-authentication/01.png)

Para proteger efetivamente o acesso ao Exchange Online do Exchange ActiveSync, você pode:

- Configurar uma política de acesso condicional com suporte executando estas etapas:

    a. Selecione apenas **Exchange Online do Office 365** como aplicativo de nuvem.  

    ![Acesso condicional](./media/active-directory-conditional-access-no-modern-authentication/04.png)

    b. Selecione **Exchange Active Sync** como **aplicativo de cliente** e, em seguida, selecione **Aplicar política somente para as plataformas com suporte**.  

    ![Plataformas de dispositivo](./media/active-directory-conditional-access-no-modern-authentication/03.png)

- Bloqueie o Exchange ActiveSync usando regras do AD FS (Serviços de Federação do Active Directory).

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>Protocolos de autenticação herdados

Além de autenticação moderna, o Exchange Online também dá suporte a protocolos de autenticação herdados. Se os protocolos de autenticação herdados estiverem habilitados, suas políticas de acesso condicional para Exchange Online não serão impostas para clientes que não usam a autenticação moderna.

Você pode desabilitar os protocolos de autenticação herdados para o Exchange Online definindo regras do AD FS. Isso bloqueia o acesso de:

- Clientes mais antigos do Office, como o Office 2013, que não tem a autenticação moderna habilitada 

- Versões anteriores do Office


## <a name="set-up-ad-fs-rules"></a>Configurar regras do AD FS

Use as regras de autorização de emissão a seguir para habilitar ou bloquear o tráfego no nível do AD FS. 

### <a name="block-legacy-traffic-from-the-extranet"></a>Bloquear o tráfego herdado da extranet

Ao aplicar as três regras a seguir: 

- Você habilita o acesso para:
    - Tráfego do Exchange ActiveSync
    - Tráfego do navegador
    - Tráfego da autenticação moderna
- Você bloqueia o acesso para: 
    - Aplicativos cliente herdados da extranet

**Regra 1**

    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Regra 2**

    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Regra 3**

    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="block-legacy-traffic-from-anywhere"></a>Bloquear o tráfego herdado de qualquer lugar

Ao aplicar as três regras a seguir:

- Você habilita o acesso para: 
    - Tráfego do Exchange ActiveSync
    - Tráfego do navegador
    - Tráfego da autenticação moderna
- Você bloqueia o acesso para: 
    - Aplicativos herdados de qualquer local

##### <a name="rule-1"></a>Regra 1
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Regra 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Regra 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira [Acesso condicional no Azure Active Directory](active-directory-conditional-access.md)




