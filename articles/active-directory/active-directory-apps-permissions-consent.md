---
title: "Aplicativos, permissões e consentimento no Azure Active Directory. | Microsoft Docs"
description: "O Azure AD Connect integrará seus diretórios locais com o Azure Active Directory.  Isso permite que você forneça uma identidade comum para os aplicativos do Office 365, Azure e SaaS integrados ao AD do Azure."
keywords: "introdução ao Azure AD, aplicativos, o que é o Azure AD Connect, instalar o Active Directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 25897cc4-7687-49b6-b0d5-71f51302b6b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/31/2017
ms.author: billmath
ms.reviewer: jesakowi
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 6f6baf5e1538fb280a899065c64ca5688473c04a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="apps-permissions-and-consent-in-azure-active-directory"></a>Aplicativos, permissões e consentimento no Azure Active Directory
Dentro do Azure Active Directory, você pode adicionar aplicativos ao seu diretório.  Os aplicativos podem variar dependendo do tipo de aplicativo.  Para exibir os aplicativos no portal clássico, selecione um diretório e escolha os aplicativos.

![](media/active-directory-apps-permissions-consent/apps1.png)

> [!IMPORTANT]
> A Microsoft recomenda que você gerencie o Azure AD usando o [Centro de administração do AD do Azure](https://aad.portal.azure.com) no portal do Azure em vez de usar o portal clássico do Azure mencionado neste artigo.

## <a name="types-of-apps"></a>Tipos de aplicativos

1. **Aplicativos de locatário único** </br>
    - **Aplicativos de locatário único** – muitas vezes mencionados como aplicativos LOB (de linha de negócios). Esse é o caso em que alguém de sua organização desenvolve seu próprio aplicativo e gostaria que os usuários da organização pudessem entrar no aplicativo.
    ![](media/active-directory-apps-permissions-consent/apps2.png)
    - **Proxy de aplicativo aplicativos** – ao expor um aplicativo local com o Proxy de aplicativo do Azure AD, um aplicativo de locatário único é registrado em seu locatário (além do serviço de proxy de aplicativo). Este aplicativo é o que representa seu aplicativo local para todas as interações de nuvem (por exemplo, autenticação). (O proxy de aplicativo requer o Azure AD Básico ou superior.)


2. **Aplicativos multilocatário**
    - **Aplicativos multilocatário para os quais outras pessoas podem dar consentimento** – semelhante a "aplicativos de locatário único que sua organização desenvolve". A principal diferença (além de lógica no próprio aplicativo) é que os usuários de outros locatários também podem dar consentimento para o aplicativo e entrar nele.</br>
    ![](media/active-directory-apps-permissions-consent/apps4.png)
    - **Aplicativos multilocatário desenvolvidos por outros, para os quais a Contoso pode dar consentimento**. (Ou "aplicativos com consentimento" de forma abreviada). Isso é o oposto de "aplicativos multilocatário desenvolvidos por sua organização". Quando outra organização desenvolve um aplicativo multilocatário, os usuários da sua organização podem dar consentimento ao aplicativo e entrar nele.
    - **Aplicativos próprios da Microsoft** – aplicativos que representam os serviços da Microsoft. O consentimento é controlado pelo fato de você se inscrever para o serviço. Às vezes, há UX e lógica especiais para certos aplicativos de terceiros que são geralmente usadas ao estabelecer políticas de acesso para o aplicativo.</br>
    ![](media/active-directory-apps-permissions-consent/apps3.png)
    - **Aplicativos pré-integrados** – aplicativos disponíveis na Galeria de Aplicativos do Azure AD, que podem ser adicionados ao diretório para fornecer logon único (e em alguns casos, o provisionamento) para aplicativos SaaS populares.
    - **Logon único do Azure AD**: SSO "Real", para aplicativos que podem ser integrados ao Azure AD por meio de um protocolo de entrada com suporte como SAML 2.0 ou OpenID Connect. O assistente orienta você pelo processo de configurá-lo.
    - **Logon único com senha**: o Azure AD armazena com segurança as credenciais do usuário para o aplicativo e as credenciais são "injetadas" no formulário de entrada pela extensão de navegador de Acesso de Aplicativo do Azure AD. Também conhecido como "cofre para senhas".

## <a name="permissions"></a>Permissões

Quando um aplicativo é registrado, o usuário que está executando o registro do aplicativo (ou seja, o desenvolvedor) define para quais permissões e quais recursos o aplicativo precisa de acesso. (Os recursos são definidos como outros aplicativos.) Por exemplo, alguém criando um aplicativo de leitor de email afirmaria que seu aplicativo requer a permissão "Acessar caixas de correio do usuário conectado" no recurso "Office 365 Exchange Online":
    
![](media/active-directory-apps-permissions-consent/apps6.png)

Para que um aplicativo (o cliente) solicite uma determinada permissão de outro aplicativo (o recurso), o desenvolvedor do aplicativo de recurso define as permissões existentes. Em nosso exemplo, a Microsoft, o proprietário do aplicativo de recurso "Office 365 Exchange Online", define uma permissão chamada "Acessar caixas de correio como o usuário conectado".

Ao definir permissões, o desenvolvedor do aplicativo deverá definir se é possível dar consentimento à permissão ou se ela requer o consentimento do administrador. Isso permite que os desenvolvedores deem permissão aos usuários para dar consentimento por conta própria a aplicativos solicitando apenas permissões de baixa confidencialidade, mas exige o consentimento dos administradores para permissões mais confidenciais. Por exemplo, o aplicativo de recurso "Azure Active Directory" foi definido de modo que os usuários possam dar consentimento para aplicativos, solicitando permissões somente leitura limitadas.  No entanto, o consentimento do administrador é necessário para permissões de leitura completas e todas as permissões de gravação.

Como clientes nativos não são autenticados, um aplicativo definido como um aplicativo cliente nativo somente pode solicitar permissões delegadas. Isso significa que sempre deve haver um usuário real envolvido ao obter um token. Aplicativos Web e APIs Web (clientes confidenciais) devem sempre ser autenticados com o Azure AD ao obter um token de acesso. Isso significa que eles também têm a possibilidade de solicitar permissões somente de aplicativo. Por exemplo, se um serviço de back-end precisar autenticar-se para outro serviço de back-end. Aplicativos solicitando permissões somente do aplicativo sempre exigem o consentimento do administrador.

Resumindo:



- Um aplicativo (cliente) informa as permissões de que precisa para outros aplicativos (recursos).
- Um aplicativo (recurso) informa quais permissões são expostas a outros aplicativos (clientes).
- Uma permissão pode ser uma permissão somente de aplicativo ou uma permissão delegada.
- Uma permissão delegada pode ser marcada como "permite o consentimento do usuário" ou "requer o consentimento do administrador".
- Um aplicativo pode se comportar como um cliente (declarando que precisa de permissões a um recurso), como um recurso (declarando as permissões que ele expõe) ou ambos.

## <a name="controls"></a>Controles

A seguir está uma lista dos controles de administrador diferentes disponíveis para todo esse comportamento. Os controles de administrador podem ser acessados no Portal Clássico da configuração sob o diretório.

![](media/active-directory-apps-permissions-consent/apps7.png)

No Portal do Azure, em **gerenciar**, **configurações de usuário**.

![](media/active-directory-apps-permissions-consent/apps11.png)



- Você pode controlar se os usuários podem ou não dar consentimento para aplicativos:

No portal clássico, selecione **Os usuários podem dar permissões a aplicativos para que acessem seus dados.**
![](media/active-directory-apps-permissions-consent/apps8.png)

No portal do Azure, **usuários selecionados podem permitir que aplicativos acessem seus dados**.
![](media/active-directory-apps-permissions-consent/apps12.png)



- Você pode controlar se os usuários podem ou não registrar seus próprios aplicativos de LOB de locatário único: no Portal Clássico, selecione **Os usuários podem adicionar aplicativos integrados.**
![](media/active-directory-apps-permissions-consent/apps9.png)

No portal do Azure, **usuários selecionados podem permitir que aplicativos acessem seus dados**.
![](media/active-directory-apps-permissions-consent/apps13.png)

>[!NOTE]
>Mesmo se você permitir que os usuários se registrem aplicativos LOB de único locatário, haverá limites para o que pode ser registrado.  
>Por exemplo, os desenvolvedores que não são administradores de diretório.
>
>- Os usuários não podem transformar um aplicativo de locatário único em um aplicativo multilocatário.
>- Ao registrar os aplicativos LOB de locatário único, os usuários não podem solicitar permissões somente de aplicativo para outros aplicativos.
>- Ao registrar os aplicativos LOB de locatário único, os usuários não poderão solicitar permissões delegadas a outros aplicativos se essas permissões exigirem o consentimento do administrador.
>- Usuários não podem fazer alterações em aplicativos dos quais eles não são proprietários.



- Você pode controlar se os usuários podem ou não adicionar por conta própria aplicativos pré-integrados que usam o SSO de senha (também conhecido como "cofre para senhas") ![](media/active-directory-apps-permissions-consent/apps10.png)



- Você pode controlar as condições sob as quais aplicativos podem ser acessados (ou seja, acesso condicional). Lembre-se de que isso se aplica ao aplicativo cliente e ao aplicativo de recurso. Digamos então que você defina uma política de acesso condicional que diz que o aplicativo "Office 365 Exchange Online" só pode ser acessado de computadores que são compatíveis.  Essa política também se aplicará quando um usuário tentar usar um aplicativo cliente que solicite permissões para o Exchange Online.



- Você tem visibilidade de quais aplicativos receberam consentimento e quais estão sendo usados.

1.  Quando um usuário dá consentimento para um aplicativo, um objeto ServicePrincipal é criado no locatário. A criação do ServicePrincipal está incluída no relatório de auditoria.
2.  Os relatórios de atividade de entrada do usuário informam a você em qual aplicativo o usuário está entrando. 

## <a name="example"></a>Exemplo

Como exemplo, vejamos o aplicativo "FabrikamMail para Office 365", no qual você percebeu que usuários em seu locatário estão entrando. "FabrikamMail" é um aplicativo de leitor de email para o Android, publicado pela "Fabrikam, Inc.". Isso se enquadra nos “aplicativos multilocatário desenvolvidos por outros, para os quais a Contoso pode dar consentimento”.

Se os usuários tiverem permissão para dar consentimento, eles obterão uma solicitação de consentimento na primeira vez que entrarem: ![](media/active-directory-apps-permissions-consent/apps14.png)

"Acessar suas caixas de correio" é a cadeia de caracteres de consentimento voltada para o usuário para a permissão "Acesso a caixas de correio do usuário conectado" exposta pelo "Office 365 Exchange Online" (ou seja, o Exchange).

Você pode ver as permissões pesquisando o objeto ServicePrincipal para o Exchange (o recurso), que foi adicionado quando você se inscreveu no Office 365. Você pode pensar no objeto ServicePrincipal de uma "instância" do aplicativo em seu locatário, que é usado para registrar as diferentes configurações e opções.  Você pode ver isso usando o `Get-AzureADServicePrincipal` no PowerShell.

    PS C:\> Get-AzureADServicePrincipal -ObjectId 383f7b97-6754-4d3d-9474-3908ebcba1c6 | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : Office 365 Exchange Online
    AppId                     : 00000002-0000-0ff1-ce00-000000000000
    AppOwnerTenantId          : 
    AppRoleAssignmentRequired : False
    AppRoles                  : {...}
    DisplayName               : Microsoft.Exchange
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {...
                                , class OAuth2Permission {
                                  AdminConsentDescription : Allows the app to have the same access to mailboxes as the signed-in user via Exchange Web Services.
                                  AdminConsentDisplayName : Access mailboxes as the signed-in user via Exchange Web Services
                                  Id                      : 3b5f3d61-589b-4a3c-a359-5dd4b5ee5bd5
                                  IsEnabled               : True
                                  Type                    : User
                                  UserConsentDescription  : Allows the app full access to your mailboxes on your behalf.
                                  UserConsentDisplayName  : Access your mailboxes
                                  Value                   : full_access_as_user
                                },
                                ...}
    PasswordCredentials       : {}
    PublisherName             : 
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {00000002-0000-0ff1-ce00-000000000000/outlook.office365.com, 00000002-0000-0ff1-ce00-000000000000/mail.office365.com, 00000002-0000-0ff1-ce00-000000000000/outlook.com, 
                                00000002-0000-0ff1-ce00-000000000000/*.outlook.com...}
    Tags                      : {}

O consentimento é iniciado quando o usuário clica em "Aceitar". Primeiro, um objeto ServicePrincipal para "FabrikamMail do Office 365" é criado no locatário. O ServicePrincipal é semelhante a:

    PS C:\> Get-AzureADServicePrincipal -SearchString "FabrikamMail for Office 365" | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : a8b16333-851d-42e8-acd2-eac155849b37
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : FabrikamMail for Office 365
    AppId                     : aba7c072-2267-4031-8960-e7a2db6e0590
    AppOwnerTenantId          : 4a4076e0-a70f-41c6-b819-6f9c4a86df89
    AppRoleAssignmentRequired : False
    AppRoles                  : {}
    DisplayName               : FabrikamMail for Office 365
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {}
    PasswordCredentials       : {}
    PublisherName             : Fabrikam, Inc.
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {aba7c072-2267-4031-8960-e7a2db6e0590}
    Tags                      : {WindowsAzureActiveDirectoryIntegratedApp}

Dar consentimento a um aplicativo cria um link Oauth2PermissionGrant entre os seguintes:
  
- o objeto de usuário
- os aplicativos cliente SPN (ServicePrincipalName)
- os aplicativos de recurso SPN (ServicePrincipalName)
- permissões no aplicativo de recurso.  

No caso de FabrikamMail, ele tem esta aparência:

    PS C:\> Get-AzureADUserOAuth2PermissionGrant -ObjectId ddiggle@aadpremiumlab.onmicrosoft.com | fl *
    
    ClientId    : a8b16333-851d-42e8-acd2-eac155849b37
    ConsentType : Principal
    ExpiryTime  : 05/15/2017 07:02:39 AM
    ObjectId    : M2OxqB2F6EKs0urBVYSbN5d7PzhUZz1NlH25COvLocbJjoxkUFfRQauryBKwBWet
    PrincipalId : 648c8ec9-5750-41d1-abab-c812b00567ad
    ResourceId  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    Scope       : full_access_as_user
    StartTime   : 01/01/0001 12:00:00 AM

(**ClientId** é a ID de objeto da entidade de serviço do FabrikamMail (aquele recém-criado), **PrincipalId** é a ID de objeto de usuário (do usuário que deu consentimento), **ResourceId** é a ID de objeto da entidade de serviço do Exchange, Scope é a permissão no Exchange para a qual o consentimento foi dado).

Se os usuários não têm permissão para dar consentimento, eles verão uma tela que diz que essa permissão é necessária.

