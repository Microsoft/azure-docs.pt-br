---
title: Guia de administração do Confluence/Atlassian Jira - Azure Active Directory| Microsoft Docs
description: Guia de administração para usar o Atlassian Jira e Confluence com Azure AD (Azure Active Directory).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2f06b884cb1213e9d2cabff4e6e2b97a60339a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935749"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Guia de administração do Atlassian Jira e Confluence para Azure Active Directory

## <a name="overview"></a>Visão geral

O plug-in de logon único (SSO) do Azure Active Directory (AD do Azure) permite que os clientes do Microsoft Azure Active Directory usem sua conta corporativa ou de estudante para entrar no Atlassian Jira e produtos com base em Confluence Server. Ele implementa SSO baseado em SAML 2.0.

## <a name="how-it-works"></a>Como ele funciona

Quando os usuários desejam entrar no aplicativo Atlassian Jira ou Confluence, eles veem o botão **Logon com Microsoft Azure AD** na página de entrada. Quando eles o selecionam, precisam entrar usando a página de entrada da organização do Microsoft Azure AD (ou seja, sua conta corporativa ou de estudante).

Depois que os usuários forem autenticados, eles poderão entrar no aplicativo. Se eles já estão autenticados com a ID e a senha de sua conta corporativa ou de estudante, então eles entram diretamente no aplicativo. 

A entrada funciona no Jira e no Confluence. Se os usuários entrarem no aplicativo JIRA e o Confluence também estiver aberto na mesma janela do navegador, eles não precisarão fornecer as credenciais novamente para outro aplicativo. 

Os usuários também podem obter o produto Atlassian em Meus aplicativos com a conta corporativa ou de estudante. Eles devem conseguir entrar sem terem que fornecer credenciais.

> [!NOTE]
> O provisionamento de usuários não é feito por meio do plug-in.

## <a name="audience"></a>Público-alvo

Administradores de Jira e Confluence podem usar o plug-in para habilitar o SSO usando o Microsoft Azure Active Directory.

## <a name="assumptions"></a>Suposições

* Instâncias de Jira e Confluence são habilitadas para HTTPS.
* Usuários já estão criados no Jira ou no Confluence.
* Usuários já têm funções no Jira ou no Confluence.
* Os administradores têm acesso às informações necessárias para configurar o plug-in.
* O Jira e o Confluence também estão disponíveis fora da rede da empresa.
* O plug-in funciona com a versão local do Jira e do Confluence.

## <a name="prerequisites"></a>Pré-requisitos

Considere as seguintes informações antes de instalar o plug-in:

* O Jira e o Confluence estão instalados em uma versão do Windows de 64 bits.
* As versões do Jira e do Confluence estão habilitadas para HTTPS.
* O Jira e o Confluence estão disponíveis na Internet.
* Credenciais de administrador estão em vigor para o Jira e o Confluence.
* Credenciais de administrador estão em vigor para o Microsoft Azure Active Directory.
* O WebSudo está desabilitado no Jira e no Confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Versões do Jira e do Confluence com suporte

O plug-in é compartível com as versões a seguir do Jira e do Confluence:

* JIRA Core e Software: 6.0 a 7.12
* Jira Service Desk: 3.0.0 a 3.5.0
* O JIRA também fornece suporte para 5.2. Para obter mais detalhes, clique em [Logon único do Microsoft Azure Active Directory para JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluence: 5.0 a 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

## <a name="installation"></a>Instalação

Para instalar o plug-in, siga estas etapas:

1. Entre em sua instância do Jira ou do Confluence como administrador.

2. Vá para o console de administração do Jira/Confluence e selecione **Complementos**.

3. No Microsoft Download Center, baixe o [Microsoft SAML SSO Plugin para Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ [Microsoft SAML SSO Plugin para Confluence](https://www.microsoft.com/download/details.aspx?id=56503).

   A versão apropriada do plug-in é exibida nos resultados da pesquisa.

4. Selecione o plug-in e o Gerenciador de plug-in Universal (UPM) o instalará.

Depois que o plug-in for instalado, ele será exibido na seção **Complementos Instalados pelo Usuário** da seção **Gerenciar Complementos**.

## <a name="plug-in-configuration"></a>Configuração de plug-in

Antes de começar a usar o plug-in, você deve configurá-lo. Selecione o plug-in, selecione o botão **Configurar** e, em seguida, forneça os detalhes de configuração.

A imagem a seguir mostra a tela de configuração no JIRA e no Confluence:

![Tela de configuração de plug-in](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **URL de metadados**: a URL para obter metadados de federação do Microsoft Azure AD.

* **Identificadores**: a URL utilizada pelo Azure AD para validar a origem da solicitação. Ela mapeia para o elemento **Identificador** no Microsoft Azure AD. O plug-in deriva automaticamente esta URL como https://*\<domínio: porta >*/.

* **URL de Resposta**: a URL de resposta no seu IdP (provedor de identidade) que inicia a entrada SAML. Ela mapeia para o elemento **URL de Resposta** no Microsoft Azure AD. O plug-in deriva automaticamente esta URL como https://*\<domínio: porta >*/plugins/servlet/saml/auth.

* **URL de logon**: a URL de logon no IdP que inicia a entrada SAML. Ela mapeia para o elemento **Logon** no Microsoft Azure AD. O plug-in deriva automaticamente esta URL como https://*\<domínio: porta >*/plugins/servlet/saml/auth.

* **ID da entidade do IdP**: a ID da entidade que o IdP usa. Essa caixa é preenchida quando a URL de metadados é resolvida.

* **URL de logon**: a URL de entrada do IdP. Essa caixa é preenchida no Microsoft Azure AD quando a URL de metadados é resolvida.

* **URL de logoff**: a URL de logoff do IdP. Essa caixa é preenchida no Microsoft Azure AD quando a URL de metadados é resolvida.

* **Certificado X.509**: certificado X.509 do IdP. Essa caixa é preenchida no Microsoft Azure AD quando a URL de metadados é resolvida.

* **Nome do botão Logon**: o nome do botão de entrada que sua organização deseja que os usuários vejam na página de entrada.

* **Locais da ID de usuário SAML**: o local onde a ID de usuário Jira ou Confluence é esperada na resposta SAML. Ele pode estar em **NameID** ou em um nome de atributo personalizado.

* **Nome do atributo**: nome do atributo em que a ID de usuário é esperada.

* **Habilitar a descoberta de realm inicial**: a seleção a ser feita caso a empresa esteja usando entrada com base no AD FS (Serviços de Federação do Active Directory).

* **Nome de Domínio**: o nome de domínio se a entrada for baseada em AD FS.

* **Habilitar Saída Única**: a seleção a ser feita caso você deseje sair do Azure AD quando um usuário sair do Jira ou do Confluence.

## <a name="troubleshooting"></a>solução de problemas

* **Você está recebendo vários erros de certificado**: entre no Azure AD e remova os vários certificados disponíveis no aplicativo. Certifique-se de que apenas um certificado esteja presente.

* **Um certificado está prestes a expirar no Azure AD**: os complementos encarregam-se da sobreposição automática do certificado. Quando um certificado estiver prestes a expirar, um novo certificado deverá ser marcado como ativo e os certificados não utilizados deverão ser excluídos. Quando um usuário tenta entrar no Jira nesse cenário, o plug-in efetua fetch e salva o novo certificado.

* **Você deseja desabilitar o WebSudo (desabilite a sessão de administrador segura)**:

  * Para Jira, sessões de administrador seguras (ou seja, confirmação de senha antes de acessar as funções de administração) são habilitadas por padrão. Se você deseja remover esse recurso em sua instância de Jira, especifique a seguinte linha no arquivo jira-config: `ira.websudo.is.disabled = true`

  * Para Confluence, siga as etapas no [site de suporte do Confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Campos que devem ser preenchidos pela URL de metadados não estão sendo preenchidos**:

  * Verifique se a URL está correta. Verifique se o locatário e a ID do aplicativo estão mapeados corretamente.

  * Insira a URL em um navegador e verifique se você está recebendo o XML de metadados de federação.

* **Erro interno de servidor**: examine os logs no diretório de logs da instalação. Se você estiver recebendo o erro quando o usuário estiver tentando entrar utilizando o SSO do Microsoft Azure AD, é possível compartilhar os logs com a equipe de suporte.

* **Há um erro de "ID de usuário não encontrado" quando o usuário tenta entrar**: Crie a ID de usuário no Jira ou no Confluence.

* **Há um erro de "Aplicativo não encontrado" no Azure AD**: Verifique se a URL apropriada está mapeada para o aplicativo no Microsoft Azure AD.

* **Você precisa de suporte**: Alcance a [Equipe de integração de SSO do Microsoft Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). A equipe responde entre 24 e 48 horas dentro do horário comercial.

  Também é possível criar um ticket de suporte com a Microsoft por meio do canal do Portal do Azure.

## <a name="plug-in-faq"></a>Perguntas frequentes sobre o plug-in

Confira as Perguntas frequentes abaixo se tiver dúvidas relacionadas a este plug-in.

### <a name="what-does-the-plug-in-do"></a>O que o plug-in faz?

O plug-in oferece recurso de logon único (SSO) para Atlassian Jira (incluindo Jira Core, Jira Software, Jira Service Desk) e software local do Confluence. O plug-in funciona com o Microsoft Azure Active Directory (Azure AD) como provedor de identidade (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Com quais produtos Atlassian o plug-in funciona?

O plug-in funciona apenas com as versões locais do Jira e Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>O plug-in funciona em versões da nuvem?

Não. O plug-in é compatível apenas com as versões locais do Jira e Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Com quais versões locais do Jira e do Confluence o plug-in é compatível?

O plug-in é compatível com estas versões:

* JIRA Core e Software: 6.0 a 7.12
* Jira Service Desk: 3.0.0 a 3.5.0
* O JIRA também fornece suporte para 5.2. Para obter mais detalhes, clique em [Logon único do Microsoft Azure Active Directory para JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluence: 5.0 a 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>O plug-in é gratuito ou pago?

Trata-se de um complemento gratuito.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>É necessário reiniciar o Jira ou Confluence depois de implantar o plug-in?

Uma reinicialização não é necessária. Você pode começar a usar o plug-in imediatamente.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Como fazer para obter suporte para o plug-in?

Você pode entrar em contato com a [Equipe de Integração de SSO do Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) se precisar de suporte com o plug-in. A equipe responde entre 24 e 48 horas dentro do horário comercial.

Também é possível criar um ticket de suporte com a Microsoft por meio do canal do Portal do Azure.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Este plug-in funcionaria em uma instalação do Jira e Confluence em Mac ou Ubuntu?

Nós testamos este plug-in apenas em Jira e Confluence de instalações em servidores do Windows de 64 bits.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>O plug-in funciona com IdPs diferente do Microsoft Azure Active Directory?

Não. Ele funciona apenas com o Microsoft Azure Active Directory.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Com qual versão do SAML o plug-in funciona?

Funciona com SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>O plug-in realiza provisionamento de usuário?

Não. O plug-in fornece apenas SSO baseado em SAML 2.0. O usuário deve ser provisionado no aplicativo antes de entrar com SSO.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>O plug-in é compartível com versões de cluster do Jira e do Confluence o plug-in é compatível?

Não. O plug-in funciona apenas com as versões locais do Jira e Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>O plug-in funciona com versões HTTP do Jira e do Confluence?

Não. O plug-in funciona apenas com instalações habilitadas para HTTPS.
