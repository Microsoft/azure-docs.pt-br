---
title: Opções de logon único no Azure AD
description: Saiba mais sobre as opções disponíveis para SSO (logon único) no Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.openlocfilehash: 2bf84a22a384e6079c2d85c833b34ba37eecaa46
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99252348"
---
# <a name="single-sign-on-options-in-azure-ad"></a>Opções de logon único no Azure AD

O logon único oferece muitos benefícios em relação aos métodos de logon tradicionais.

- **Com o logon único**, os usuários entram uma vez com uma conta para acessar os dispositivos ingressados no domínio, recursos da empresa, aplicativos de SaaS (software como serviço) e aplicativos Web. Depois de se conectar, o usuário pode iniciar os aplicativos no portal do Office 365 ou em Meus Aplicativos. Os administradores podem centralizar o gerenciamento da conta de usuário e automaticamente adicionar ou remover o acesso de usuário para aplicativos com base na associação de grupo.

- **Sem o logon único**, os usuários devem se lembrar de senhas específicas do aplicativo e entrar em cada aplicativo. A equipe de TI precisa criar e atualizar as contas de usuário para cada aplicativo, como o Microsoft 365, o Box e o Salesforce. Os usuários precisam se lembrar de suas senhas, além de gastar tempo para entrar em cada aplicativo.

Para saber mais sobre o logon único, consulte [O que é logon único?](what-is-single-sign-on.md).

## <a name="choosing-a-single-sign-on-method"></a>Escolhendo um método de logon único

Há várias maneiras de configurar um aplicativo para logon único. A escolha de um método de logon único depende de como o aplicativo está configurado para autenticação.

- Os aplicativos de nuvem podem usar os métodos OpenID Connect, OAuth, SAML, baseado em senha, vinculado ou desabilitado para o logon único. 
- Os aplicativos locais podem usar os métodos baseado em senha, de Autenticação Integrada do Windows, baseado em cabeçalho, vinculado ou desabilitado para o logon único. As opções locais funcionam quando os aplicativos são configurados para Proxy de Aplicativo.

Este fluxograma ajuda você a decidir qual método de logon único é melhor para sua situação.

![Fluxograma de decisão para o método de logon único](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

A tabela a seguir resume os métodos de logon único e os links para obter mais detalhes.

| Método de logon único | Tipos de aplicativos | Quando usar |
| :------ | :------- | :----- |
| [OpenID Connect e OAuth](#openid-connect-and-oauth) | nuvem e local | Use o OpenID Connect e o OAuth ao desenvolver um novo aplicativo. Esse protocolo simplifica a configuração de aplicativo, tem SDKs fáceis de usar e permite que seu aplicativo use o MS Graph.
| [SAML](#saml-sso) | nuvem e local | Escolha SAML sempre que possível para aplicativos existentes que não usam o OpenID Connect nem o OAuth. O SAML funciona para aplicativos que são autenticados usando um dos protocolos SAML.|
| [Baseado em senha](#password-based-sso) | nuvem e local | Escolha o método baseado em senha quando o aplicativo é autenticado com o nome de usuário e senha. O logon único baseado em senha permite o armazenamento e a reprodução segura de senhas do aplicativo usando uma extensão de navegador da Web ou aplicativo móvel. Esse método utiliza o processo de entrada existente fornecido pelo aplicativo, mas permite que um administrador gerencie as senhas. |
| [Vinculado](#linked-sign-on) | nuvem e local | Escolha o logon vinculado quando o aplicativo estiver configurado para o logon único em outro serviço de provedor de identidade. Essa opção não adiciona o logon único ao aplicativo. No entanto, o aplicativo pode já ter o logon único implementado usando outro serviço, como Serviços de Federação do Active Directory (AD FS).|
| [Desabilitado](#disabled-sso) | nuvem e local | Escolha o logon único desabilitado quando o aplicativo não estiver pronto para ser configurado para logon único. Esse modo é o padrão quando você cria o aplicativo.|
| [IWA (Autenticação Integrada do Windows)](#integrated-windows-authentication-iwa-sso) | apenas local | Escolha esse logon único de IWA para aplicativos que usam a [IWA (Autenticação Integrada do Windows)](/aspnet/web-api/overview/security/integrated-windows-authentication) ou aplicativos com reconhecimento de declaração. Para IWA, os conectores de Proxy de Aplicativo usam a KCD (Delegação Restrita de Kerberos) para autenticar os usuários no aplicativo. |
| [Baseado em cabeçalho](#header-based-sso) | apenas local | Use o logon único baseado em cabeçalho quando o aplicativo usar cabeçalhos para autenticação. O Proxy de Aplicativo usa o Azure AD para autenticar o usuário e, em seguida, passa o tráfego pelo serviço de conector.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect e OAuth

Ao desenvolver novos aplicativos, use protocolos modernos, como OpenID Connect e OAuth, para obter a melhor experiência de logon único para seu aplicativo em várias plataformas de dispositivo. O OAuth permite que os usuários ou administradores [deem o consentimento](configure-user-consent.md) para recursos protegidos, como [Microsoft Graph](/graph/overview). Fornecemos [SDKs](../develop/reference-v2-libraries.md) de fácil adoção para o seu aplicativo, além de deixá-lo pronto para usar o [Microsoft Graph](/graph/overview).

Para obter mais informações, consulte:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Guia do desenvolvedor da plataforma de identidade da Microsoft](../develop/index.yml).

## <a name="saml-sso"></a>SSO do SAML

Com o **logon único SAML**, o Azure AD realiza a autenticação para o aplicativo usando a conta do Azure AD do usuário. O Azure AD comunica as informações do logon para o aplicativo por meio de um protocolo de conexão. Com o logon único baseado em SAML, você pode mapear os usuários para funções de aplicativo específicas com base nas regras definidas nas suas declarações SAML.

Escolha o logon único baseado em SAML quando o aplicativo der suporte a ele.

O logon único baseado em SAML é compatível com aplicativos que usam qualquer um desses protocolos:

- SAML 2.0
- O certificado do provedor de identidade do Web Services Federation

Para configurar um aplicativo SaaS para o logon único baseado em SAML, confira [Configurar o logon único baseado em SAML](configure-saml-single-sign-on.md). Além disso, muitos aplicativos SaaS (Software como Serviço) têm um [tutorial específico de aplicativo](../saas-apps/tutorial-list.md) que explica a configuração para logon único baseado em SAML.

Para configurar um aplicativo para o Web Services Federation, siga as mesmas diretrizes usadas para configurar o aplicativo para logon único baseado em SAML. Na etapa de configuração do aplicativo para usar o Azure AD, você precisará substituir a URL de logon do Azure AD para o ponto de extremidade do Web Services Federation `https://login.microsoftonline.com/<tenant-ID>/wsfed`.

Para configurar um aplicativo local para logon único baseado em SAML, confira [Logon único de SAML para aplicativos locais com o Proxy de Aplicativo](application-proxy-configure-single-sign-on-on-premises-apps.md).

Para saber mais sobre o protocolo SAML, confira [Protocolo SAML de logon único](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>SSO baseado em senha

Com o logon baseado em senha, os usuários entram no aplicativo com um nome de usuário e uma senha na primeira vez que o acessam. Após o primeiro logon, o Azure AD fornece o nome de usuário e a senha ao aplicativo.

O logon único baseado em senha usa os processos de autenticação existentes fornecidos pelo aplicativo. Quando você habilita o logon único de senha para um aplicativo, o Azure AD coleta e armazena de forma segura os nomes de usuário e senhas do aplicativo. As credenciais do usuário são armazenadas em um estado criptografado no diretório.

Escolha o logon único baseado em senha quando:

- Um aplicativo não for compatível com o protocolo de logon único SAML.
- Um aplicativo for autenticado com um nome de usuário e senha em vez de tokens de acesso e cabeçalhos.

>[!NOTE]
>Você não pode aplicar políticas de acesso condicional ou autenticação multifator para SSO baseado em senha.

O logon único baseado em senha for compatível com qualquer aplicativo baseado em nuvem que tenha uma página de entrada baseada em HTML. O usuário pode usar qualquer um dos seguintes navegadores:

- Internet Explorer 11 no Windows 7 ou posterior
   > [!NOTE]
   > O Internet Explorer está com suporte limitado e já não recebe mais atualizações de software. O Microsoft Edge é o navegador recomendado.

- O Microsoft Edge na Edição de Aniversário do Windows 10 ou posterior
- Microsoft Edge para iOS e Android
- Intune Managed Browser
- Chrome no Windows 7 ou posterior e no macOS X ou posterior
- Firefox 26.0 ou posterior no Windows XP SP2 ou posterior e no macOS X 10.6 ou posterior

Para configurar um aplicativo em nuvem para o logon único baseado em senha, confira [Configurar o logon único com senha](configure-password-single-sign-on-non-gallery-applications.md).

Para configurar um aplicativo local para logon único por meio do Proxy de Aplicativo, confira [Cofre para senhas para logon único com o Proxy de Aplicativo](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Como a autenticação funciona para SSO baseado em senha

Para autenticar um usuário em um aplicativo, o Azure AD recupera as credenciais do usuário do diretório e as insere na página de entrada do aplicativo.  O Azure AD passa com segurança as credenciais do usuário por meio de uma extensão de navegador da Web ou aplicativo móvel. Esse processo permite que um administrador gerencie as credenciais do usuário e não exige que os usuários se lembrem de sua senha.

> [!IMPORTANT]
> As credenciais são ocultadas do usuário durante o processo de logon automatizado. No entanto, as credenciais são detectáveis usando ferramentas de depuração da Web. Os usuários e administradores precisam seguir as mesmas políticas de segurança como se as credenciais fossem inseridas diretamente pelo usuário.

### <a name="managing-credentials-for-password-based-sso"></a>Gerenciando credenciais para o SSO baseado em senha

As senhas de cada aplicativo podem ser gerenciadas pelo administrador do Azure AD ou pelos usuários.

Quando o administrador do Azure AD gerencia as credenciais:  

- O usuário não precisa redefinir ou lembrar o nome de usuário e senha. O usuário pode acessar o aplicativo clicando nele em Meus Aplicativos ou por meio de um link fornecido.
- O administrador pode realizar tarefas de gerenciamento nas credenciais. Por exemplo, o administrador pode atualizar o acesso de aplicativo de acordo com as associações de grupo do usuário e o status do funcionário.
- O administrador pode usar as credenciais administrativas para fornecer acesso aos aplicativos compartilhados entre vários usuários. Por exemplo, o administrador pode permitir que qualquer pessoa que pode acessar um aplicativo tenha acesso a uma mídia social ou o aplicativo de compartilhamento de documentos.

Quando o usuário final gerencia as credenciais:

- Os usuários podem gerenciar as senhas atualizando-as ou excluindo-as, conforme necessário.
- Os administradores ainda são capazes de definir novas credenciais para o aplicativo.

## <a name="linked-sign-on"></a>Logon vinculado
O logon vinculado permite que o Azure AD forneça o logon único para um aplicativo que já está configurado para um logon único em outro serviço. O aplicativo vinculado pode aparecer para os usuários finais no portal do Office 365 ou portal MyApps do Azure AD. Por exemplo, um usuário pode iniciar um aplicativo que está configurado para logon único no Serviços de Federação do Active Directory (AD FS) 2.0 no portal do Office 365. Relatórios adicionais também estão disponíveis para aplicativos vinculados que são iniciados no portal do Office 365 ou no portal MyApps do Azure AD. Para configurar um aplicativo para logon vinculado, confira [Configurar logon vinculado](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Logon vinculado para migração de aplicativo

O logon vinculado pode fornecer uma experiência de usuário consistente enquanto você migra aplicativos durante um período. Se você estiver migrando aplicativos para o Azure Active Directory, poderá usar o logon vinculado para publicar rapidamente links para todos os aplicativos que pretende migrar.  Os usuários podem encontrar todos os links no [portal MyApps](../user-help/my-apps-portal-end-user-access.md) ou no [iniciador de aplicativos do Microsoft 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Os usuários não saberão que estão acessando um aplicativo vinculado ou migrado.  

Depois que um usuário for autenticado com um aplicativo vinculado, um registro de conta precisa ser criado antes que o usuário final receba o acesso de logon único. O provisionamento deste registro de conta pode ocorrer automaticamente ou manualmente por um administrador.

>[!NOTE]
>Você não pode aplicar políticas de acesso condicional ou autenticação multifator para um aplicativo vinculado. Isso ocorre porque um aplicativo vinculado não fornece funcionalidades de logon único por meio do Azure AD. Ao configurar um aplicativo vinculado, basta adicionar um link que aparecerá no inicializador de aplicativos ou no portal MyApps. 

## <a name="disabled-sso"></a>SSO desabilitado

O modo desabilitado significa que o logon único não é usado para o aplicativo. Quando o logon único está desabilitado, pode ser necessário que os usuários se autentiquem duas vezes. Primeiro, os usuários se autenticam no Azure AD e, em seguida, entram no aplicativo.

Use o modo de logon único desabilitado:

- Se você não estiver pronto para integrar este aplicativo ao logon único do Azure AD,
- Se você estiver testando outros aspectos do aplicativo ou
- Como uma camada de segurança para um aplicativo local que não exige que os usuários se autentiquem. Com logon único desabilitado, o usuário precisa se autenticar.

Observe que, se você tiver configurado o aplicativo para logon único baseado em SAML iniciado por SP e alterar o modo SSO para desabilitado, ele não impedirá que os usuários se conectem ao aplicativo fora do portal MyApps. Para conseguir isso, você precisa [desabilitar a capacidade de conexão dos usuários](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>SSO de IWA (Autenticação Integrada do Windows)

O [Proxy de Aplicativo](application-proxy.md) oferece SSO (logon único) para aplicativos que usam a [IWA (Autenticação Integrada do Windows)](/aspnet/web-api/overview/security/integrated-windows-authentication) ou aplicativos com reconhecimento de declaração. Se o aplicativo usar a IWA, o Proxy de Aplicativo autenticará o aplicativo usando a KCD (Delegação Restrita de Kerberos). Para um aplicativo com reconhecimento de declaração que confia no Azure Active Directory, o logon único funciona porque o usuário já foi autenticado usando o Azure AD.

Escolha o modo de logon único da Autenticação Integrada do Windows para fornecer logon único a um aplicativo local que é autenticado com o IWA.

Para configurar um aplicativo local para IWA, confira [Delegação restrita de Kerberos para logon único em seus aplicativos com o Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Como funciona o logon único com a KCD
Este diagrama explica o fluxo de quando um usuário acessa um aplicativo local que usa IWA.

![Diagrama de fluxo de autenticação do Microsoft Azure AD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. O usuário insere a URL para acessar o aplicativo local por meio do Proxy de Aplicativo.
1. O Proxy de Aplicativo redireciona a solicitação para serviços de autenticação do AD do Azure para pré-autenticação. Neste ponto, o AD do Azure se aplica a qualquer política de autenticação e autorização aplicável, tal como autenticação multifator. Se o usuário for validado, o AD do Azure cria um token e o envia para o usuário.
1. O usuário passa o token para o Proxy de Aplicativo.
1. O Proxy de Aplicativo valida o token e recupera o nome UPN dele. Em seguida, ele envia a solicitação, o nome UPN e o SPN (nome da entidade de serviço) para o conector por meio de um canal seguro duplamente autenticado.
1. O conector usa a negociação da KCD (Delegação Restrita de Kerberos) com o AD local, representando o usuário para obter um token Kerberos para o aplicativo.
1. O Active Directory envia o token Kerberos do aplicativo para o conector.
1. O conector envia a solicitação original para o servidor de aplicativos usando o token Kerberos recebido do AD.
1. O aplicativo envia a resposta para o conector, que é retornada para o serviço de Proxy de Aplicativo e, finalmente, para o usuário.

## <a name="header-based-sso"></a>SSO baseado em cabeçalho

O logon único baseado em cabeçalho funciona para aplicativos que usam cabeçalhos de HTTP para autenticação.

Escolha logon único baseado em cabeçalho quando o Proxy de Aplicativo estiver configurado para o aplicativo local.

Para saber mais sobre a autenticação baseada em cabeçalho, confira [SSO baseado em cabeçalho](application-proxy-configure-single-sign-on-with-headers.md).


## <a name="next-steps"></a>Próximas etapas
* [Série de Início Rápido no Gerenciamento de Aplicativos](view-applications-portal.md)
* [Planejar uma implantação de logon único](plan-sso-deployment.md)
* [Logon único com aplicativos locais](application-proxy-config-sso-how-to.md)