---
title: "Como determinar o método de logon único a ser usado | Microsoft Docs"
description: "Compreenda os modos de logon único com suporte do Azure AD e como escolher qual deles deve ser usado para o aplicativo em que você está interessado."
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 80f4a965920fec9cb578c1bee235c7857f37431e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>Como determinar o método de logon único a ser usado

Este artigo o ajuda a compreender os modos de logon único com suporte do Azure AD e como escolher qual deles deve ser usado para o aplicativo em que você está interessado.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Modos de logon único e provisionamento com suporte de tipos específicos de aplicativos

A tabela a seguir descreve os diferente modos de logon único e provisionamento com suporte de cada um dos tipos de aplicativos mencionados acima. Use essa tabela como um auxílio para entender qual aplicativo você precisa adicionar a fim de dar suporte a um objetivo específico.

  ![Tabela de tipos de aplicativo](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Como escolher um modo de logon único

Os modos de **logon único** com suporte para aplicativos do Azure AD estão listados abaixo.

-   **Logon único do Azure AD desabilitado** – escolha o **modo de logon único** desabilitado do Azure AD se você ainda não estiver pronto para integrar esse aplicativo com logon único ao Azure AD ou se apenas o estiver testando

-   **Logon vinculado** – escolha o **modo de logon único** [Logon vinculado](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) se você tiver um aplicativo que já está conectado a uma solução de logon único existente, ou se quiser apenas publicar um link simples para seus usuários no [Painel de Acesso do Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) ou no [Inicializador de aplicativos do Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Logon baseado em senha** – escolha o **modo de logon único** [Logon baseado em senha](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) se seu aplicativo renderizar um campo de nome de usuário e senha em HTML e você quiser armazenar esse nome de usuário e senha com segurança para reprodução no aplicativo mais tarde

-   **Logon baseado em SAML** – escolha o modo de logon único [Logon baseado em SAML](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) se seu aplicativo der suporte aos protocolos SAML ou OpenID Connect ou se você quiser mapear usuários a funções de aplicativo específicas com base em regras que você define em suas declarações SAML *(**Observação:** esta opção não fica disponível quando o proxy de aplicativo está configurado para um aplicativo)*

-   **Logon baseado em cabeçalho** – escolha o modo de logon único [Logon baseado em cabeçalho](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) se você tiver um aplicativo que usa PingAccess e que dá suporte à autenticação baseada em cabeçalho HTTP em que você deseja realizar logon único *(**Observação:** esta opção fica disponível somente quando o proxy de aplicativo e o PingAccess estão configurados para um aplicativo)*

-   **Autenticação integrada do Windows** – escolha o modo de logon único [Autenticação integrada do Windows](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) ao expor um aplicativo WIA local no qual você deseja realizar o logon único *(**Observação:** esta opção fica disponível apenas quando o proxy de aplicativo está configurado para um aplicativo)*

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Modos de logon único para aplicativos personalizados

Aplicativos desenvolvidos de forma personalizada por meio da experiência [Aplicativo desenvolvido de forma personalizada](#_Custom-Developed_Applications) também dão suporte a modos de logon único adicionais não listados acima. Estão incluídos:

-   Logon baseado em [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)

-   Logon baseado em [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)

-   Logon baseado em [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)

-   Logon baseado em [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)

Leia o [Guia do Desenvolvedor do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) para saber mais sobre como criar um aplicativo desenvolvido de forma personalizada que dá suporte a esses modos de logon único.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Como definir o modo de logon único de um aplicativo

Para definir o modo de **logon único** de um aplicativo, siga as instruções abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global** ou **Coadministrador.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Selecione o aplicativo para o qual deseja configurar o logon único

7.  Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

## <a name="next-steps"></a>Próximas etapas
[Fornecer logon único para seus aplicativos com Proxy de Aplicativo](active-directory-application-proxy-sso-using-kcd.md)

