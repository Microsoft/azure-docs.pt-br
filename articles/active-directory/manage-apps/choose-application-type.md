---
title: Como escolher o tipo de aplicativo a ser usado ao adicionar um aplicativo | Microsoft Docs
description: Entenda os tipos de aplicativos com suporte que você pode integrar ao Azure AD e suas opções de configuração relacionadas
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: fb2c49d6436a14e9b6cbb0a92eb0dfba077c8e4d
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424254"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Escolhendo o tipo de aplicativo ao adicionar um aplicativo no Active Directory Domain Services do Azure

Conheça os quatro tipos de aplicativos que você pode adicionar ao Microsoft Azure Active Directory (Azure Active Directory). Ao adicionar um aplicativo no Azure Active Directory Domain Services, você será solicitado a escolher um dos quatro tipos de aplicativo.

## <a name="what-are-the-types-of-applications"></a>Quais são os tipos de aplicativos?

O Azure AD oferece suporte a quatro tipos de aplicativos principais que você pode adicionar usando o recurso **Adicionar** encontrado em **Aplicativos Empresariais**. Elas incluem:

- **Aplicativos da galeria do Azure AD** – Um aplicativo que foi previamente integrado para logon único com o Azure AD.

- **Aplicativos do Proxy de Aplicativo** – um aplicativo em execução em seu ambiente local para o qual você deseja fornecer logon único seguro externamente.

- **Aplicativos desenvolvidos de forma personalizada** – um aplicativo que sua organização deseja desenvolver na Plataforma de Desenvolvimento de Aplicativos do Azure AD, mas que talvez ainda não exista.

- **Aplicativos inexistentes na Galeria** – traga seus aplicativos! Qualquer link da Web desejado, ou qualquer aplicativo que renderiza um campo de nome de usuário e senha, dá suporte aos protocolos SAML ou OpenID Connect ou dá suporte ao SCIM que você deseja integrar para logon único com o Azure Active Directory.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Recursos e capacidades suportados pelos tipos de aplicativo

Os seguintes recursos são suportados por qualquer um dos quatro tipos de aplicativos anteriores no Azure Active Directory:

- **Início rápido** – comece a usar um aplicativo rapidamente seguindo [as etapas de implantação simples](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

- **Gerenciamento de propriedades gerais** – obtenha um [deeplink direto](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) para um aplicativo, [personalize a marca](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) de um aplicativo ou [desabilite o aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) para todos os usuários.

- **Gerenciamento de usuário e grupo** – [atribua](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) ou [remova](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) usuários e grupos de um aplicativo e, como opção, atribua as funções específicas de aplicativo as quais esses usuários e grupos têm acesso

- **Acesso de autoatendimento ao aplicativo** – permita que os usuários solicitem o [acesso de autoatendimento ao aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) de seus Painéis de acesso do aplicativo por meio da adição direta de um aplicativo ou [ingressando em um grupo habilitado para autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), podendo exigir a aprovação comercial durante o processo

- **Logs de entrada** – confira [todas as entradas em um aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins), ou em todos os seus aplicativos

- **Logs de auditoria** – confira [logs detalhados de auditoria sobre modificações feitas em um aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), ou em todos os seus aplicativos

- **Acesso condicional e com base em risco** – defina [regras avançadas de acesso com base em condição](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) que são aplicadas quando os usuários tentam entrar em um aplicativo específico

- **Exibição de permissões** – veja qualquer uma das [permissões de OAuth2](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) as quais um aplicativo tem acesso em seu diretório de um único local

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Modos de logon único e provisionamento com suporte de tipos específicos de aplicativos

A tabela a seguir descreve os diferentes modos de logon único e de provisionamento com suporte de cada um dos tipos de aplicativos anteriores. Use essa tabela como um auxílio para entender qual aplicativo você precisa adicionar a fim de dar suporte a um objetivo específico.

  ![Tabela: Diferentes modos de provisionamento e SSO com suporte de cada tipo de aplicativo](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Como escolher um modo de logon único

A seguir, são apresentados os modos de **logon único** para aplicativos do Microsoft Azure AD.

- **Logon único do Azure AD desabilitado** – escolha o **modo de logon único** desabilitado do Azure AD se você ainda não estiver pronto para integrar esse aplicativo com logon único ao Azure AD ou se apenas o estiver testando

- **Logon vinculado** – escolha o **modo de logon único** [Logon vinculado](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) se você tiver um aplicativo que já está conectado a uma solução de logon único existente, ou se quiser apenas publicar um link simples para seus usuários no [Painel de Acesso do Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) ou no [Inicializador de aplicativos do Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

- **Logon baseado em senha** – Escolha o **modo de logon único** [Logon baseado em senha](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) se seu aplicativo renderizar um campo de nome de usuário e senha em HTML e você quiser armazenar esse nome de usuário e senha com segurança para reprodução no aplicativo mais tarde

- **Logon baseado em SAML** – Escolha o modo de logon único [Logon baseado em SAML](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) se o seu aplicativo der suporte aos protocolos SAML ou OpenID Connect, ou se você quiser mapear usuários a funções de aplicativo específicas com base em regras que você define em suas declarações SAML *

  >[!NOTE]
  >Essa opção não está disponível quando o proxy de aplicativo está configurado para um aplicativo.

- **logon baseado em cabeçalho** – Escolha este modo de logon único [Logon baseado em cabeçalho](application-proxy-configure-single-sign-on-with-ping-access.md) se você tiver um aplicativo usando PingAccess que oferece suporte à autenticação baseada em cabeçalho HTTP no qual deseja realizar logon único

  >[!NOTE]
  >Essa opção está disponível apenas quando o proxy de aplicativo e o PingAccess estiverem configurados para um aplicativo.

- **Autenticação integrada do Windows** – Escolha o modo de logon único [Autenticação integrada do Windows](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) ao expor um aplicativo WIA local no qual você deseja realizar o logon único

  >[!NOTE]
  >Essa opção só está disponível quando o proxy de aplicativo estiver configurado para um aplicativo.

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Modos de logon único para aplicativos personalizados

Aplicativos desenvolvidos de forma personalizada por meio da experiência Aplicativo desenvolvido de forma personalizada também oferecem suporte a modos de logon único não listados anteriormente, que incluem:

- Logon baseado em [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)

- Logon baseado em [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)

- Logon baseado em [WS-Federation 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)

- Logon baseado em [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)

Leia o [Guia do Desenvolvedor do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) para saber mais sobre como criar um aplicativo desenvolvido de forma personalizada que dá suporte a esses modos de logon único.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Como definir o modo de logon único de um aplicativo

Para definir o modo de logon único de um aplicativo, siga estas instruções:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**
1. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.
1. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
1. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.
1. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

1. Selecione o aplicativo para o qual você deseja configurar o logon único.
1. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

## <a name="how-to-choose-a-provisioning-mode"></a>Como escolher um modo de provisionamento

- **Provisionamento Manual** – Escolha o modo de provisionamento [Manual](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) se você tiver contas ou se quiser gerenciar as contas desse aplicativo fora do Azure AD.

- **Provisionamento Automático** – Escolha o **modo de provisionamento** [Automático](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) se você quiser habilitar o provisionamento automático baseado em API e/ou o desprovisionamento de contas de usuário para esse aplicativo 

  >[!NOTE]
  >Essa opção está disponível somente para aplicativos dentro da categoria **em destaque** da [Galeria de Aplicativos do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal).

- **Provisionamento Automático baseado em SCIM** – Use o [Provisionamento Automático baseado em SCIM](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) se o seu aplicativo der suporte ao protocolo SCIM para detecção de alterações em usuários e grupos, que são emitidas automaticamente para alterações em qualquer aplicativo integrado ao Azure AD 

  >[!NOTE]
  >Essa opção não está listada como um modo de provisionamento específico, mas está habilitada por padrão para todos os aplicativos integradas ao Azure AD.

## <a name="how-to-set-an-applications-provisioning-mode"></a>Como configurar o modo de provisionamento de um aplicativo

Para definir o modo de **provisionando** de um aplicativo, siga as instruções abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**
1. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.
1. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
1. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.
1. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

1. Selecione o aplicativo para o qual você deseja configurar o provisionamento.
1. Após o carregamento do aplicativo, clique em **Provisionamento** no menu de navegação à esquerda do aplicativo.

## <a name="next-steps"></a>Próximas etapas

[Gerenciando aplicativos com o Azure Active Directory](what-is-application-management.md)
