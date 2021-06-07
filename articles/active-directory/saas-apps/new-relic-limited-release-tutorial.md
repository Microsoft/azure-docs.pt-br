---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao New Relic | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o New Relic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 29e19eea51b5ee55831bf1d694a9a6473a62d471
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97504042"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao New Relic

Neste tutorial, você aprenderá a integrar o New Relic ao Azure AD (Azure Active Directory). Ao integrar o New Relic ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao New Relic.
* Permitir que os usuários sejam conectados automaticamente ao New Relic com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa do seguinte:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do New Relic que esteja habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O New Relic dá suporte ao SSO que é iniciado pelo provedor de serviços ou pelo provedor de identidade.
* Depois de configurar o New Relic, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-from-the-gallery"></a>Adicionar o New Relic por meio da galeria

Para configurar a integração do New Relic ao Azure AD, você precisará adicionar o **New Relic (By Organization)** por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. Selecione o serviço **Azure Active Directory**.
1. Selecione **Aplicativos empresariais** > **Novo aplicativo**.
1. Na página **Procurar na Galeria do Azure AD**, digite **New Relic (By Organization)** na caixa de pesquisa.
1. Selecione **New Relic (By Organization)** nos resultados e escolha **Criar**. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Configurar e testar o SSO do Azure AD para o New Relic

Configure e teste o SSO do Azure AD com o New Relic usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do New Relic.

Para configurar e testar o SSO do Azure AD com o New Relic:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.
   1. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com B.Fernandes.
   1. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que B.Fernandes use o logon único do Azure AD.
1. [Configurar o SSO do New Relic](#configure-new-relic-sso): para definir as configurações de logon único no lado do New Relic.
   1. [Criar um usuário de teste do New Relic](#create-a-new-relic-test-user): para ter um equivalente de B.Fernandes no New Relic que esteja vinculado ao usuário do Azure AD.
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **New Relic by Organization**, localize a seção **Gerenciar**. Em seguida, selecione **Logon único**.

1. Na página **Selecionar um método de logon único**, escolha **SAML**.

1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

   ![Captura de tela da página Configurar o logon único com o SAML, com o ícone de lápis realçado.](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, preencha os valores para **Identificador** e **URL de Resposta**.

   * Recupere esses valores usando o aplicativo New Relic **My Organization**. Para usar esse aplicativo:
      1. [Entre](https://login.newrelic.com/) no New Relic.
      1. No menu superior, selecione **Aplicativos**.
      1. Na seção **Seus aplicativos**, selecione **Minha Organização** > **Domínios de autenticação**.
      1. Escolha o domínio de autenticação ao qual deseja conectar o SSO do Azure AD (caso você tenha mais de um domínio de autenticação). A maioria das empresas tem apenas um domínio de autenticação chamado **Padrão**. Se houver apenas um domínio de autenticação, você não precisará selecionar nada.
      1. Na seção **Autenticação**, a opção **URL do Consumidor de Declaração** contém o valor a ser usado em **URL de Resposta**.
      1. Na seção **Autenticação**, a opção **Nossa ID da entidade** contém o valor a ser usado em **Identificador**.

1. Na seção **Atributos e Declarações de Usuário**, verifique se o **Identificador de Usuário Exclusivo** é mapeado para um campo que contenha o endereço de email que está sendo usado no New Relic.

   * O campo padrão **user.userprincipalname** funcionará para você se os valores forem iguais aos endereços de email do New Relic.
   * O campo **user.mail** poderá funcionar melhor se **user.userprincipalname** não for o endereço de email do New Relic.

1. Na seção **Certificado de Autenticação SAML**, copie a **URL de Metadados de Federação do Aplicativo** e salve o valor para uso posterior.

1. Na seção **Configurar o New Relic by Organization**, copie a **URL de Logon** e salve o valor para uso posterior.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Veja como criar um usuário de teste de nome B.Fernandes no portal do Azure.

1. No portal do Azure, selecione **Azure Active Directory**.
1. Escolha **Usuários** > **Novo usuário**.
1. Na página **Novo usuário**:
   1. No campo **Nome de usuário**, insira `username@companydomain.extension`. Por exemplo, `b.simon@contoso.com`. Isso deverá corresponder ao endereço de email que será usado no lado do New Relic.
   1. No campo **Nome**, insira `B.Simon`.  
   1. Selecione **Mostrar senha** e salve o valor mostrado.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Veja como permitir que B.Fernandes use o logon único do Azure AD permitindo acesso ao aplicativo New Relic by Organization.

1. No portal do Azure, selecione **Azure Active Directory**.
1. Selecione **Aplicativos empresariais** > **New Relic by Organization**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![Captura de tela da seção Gerenciar, com a opção Usuários e grupos realçada.](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Em **Adicionar Atribuição**, selecione **Usuários e grupos** (ou **Usuários**, dependendo do nível do seu plano).

   ![Captura de tela da opção Adicionar usuário.](common/add-assign-user.png)

1. Em **Usuários e grupos** (ou **Usuários**), selecione **B.Fernandes** na lista **Usuários** e escolha **Selecionar** na parte inferior da tela.
1. Em **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-new-relic-sso"></a>Configurar o SSO do New Relic

Siga estas etapas para configurar o SSO no New Relic.

1. [Entre](https://login.newrelic.com/) no New Relic.

1. No menu superior, selecione **Aplicativos**.

1. Na seção **Seus aplicativos**, selecione **Minha Organização** > **Domínios de autenticação**.

1. Escolha o domínio de autenticação ao qual deseja conectar o SSO do Azure AD (caso você tenha mais de um domínio de autenticação). A maioria das empresas tem apenas um domínio de autenticação chamado **Padrão**. Se houver apenas um domínio de autenticação, você não precisará selecionar nada.

1. Na seção **Autenticação**, selecione **Configurar**.

   1. Em **Origem dos metadados do SAML**, insira o valor salvo anteriormente do campo **URL de Metadados de Federação do Aplicativo** do Azure AD.

   1. Em **URL de destino do SSO**, insira o valor salvo anteriormente do campo **URL de Logon** do Azure AD.

   1. Depois de verificar se as configurações estão corretas no lado do Azure AD e do New Relic, selecione **Salvar**. Se os dois lados não estiverem configurados corretamente, os usuários não poderão entrar no New Relic.

### <a name="create-a-new-relic-test-user"></a>Criar um usuário de teste do New Relic

Nesta seção, você criará um usuário chamado B.Fernandes no New Relic.

1. [Entre](https://login.newrelic.com/) no New Relic.

1. No menu superior, selecione **Aplicativos**.

1. Na seção **Seus aplicativos**, selecione **Gerenciamento de Usuários**.

1. Selecione **Adicionar usuário**.

   1. Em **Nome**, insira **B.Fernandes**.
   
   1. Em **Email**, insira o valor que será enviado pelo SSO do Azure AD.
   
   1. Escolha um **Tipo** e um **Grupo** de usuário para o usuário. Para um usuário de teste, **Usuário Básico** em Tipo e **Usuário** em Grupo são opções razoáveis.
   
   1. Para salvar o usuário, selecione **Adicionar Usuário**.

## <a name="test-sso"></a>Testar o SSO 

Veja como testar a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do **New Relic by Organization** no Painel de Acesso, você deverá ser conectado automaticamente ao New Relic. Para obter mais informações sobre o Painel de Acesso, confira [Entrar e iniciar aplicativos no portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o New Relic com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
