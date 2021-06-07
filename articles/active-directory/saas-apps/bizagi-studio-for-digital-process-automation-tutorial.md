---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory ao Bizagi for Digital Process Automation | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Bizagi for Digital Process Automation.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.openlocfilehash: efbb8a9ca0d475939d7713fa6a6a4a8245aead90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92457054"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-for-digital-process-automation"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory ao Bizagi for Digital Process Automation

Neste tutorial, você aprenderá a integrar o Bizagi for Digital Process Automation ao Azure AD (Azure Active Directory). Ao integrar o Bizagi for Digital Process Automation ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso a um projeto do Bizagi for Digital Process Automation Services ou Server.
* Habilitar seus usuários a serem conectados automaticamente a um projeto do Bizagi for Digital Process Automation Services ou Server com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Um projeto do Bizagi usando o Automation Services ou Server. 
* Ter seus próprios certificados para assinaturas de declaração SAML. Esses certificados devem ser gerados no formato p12 ou pfx.
* Ter um arquivo de metadados no formato XML gerado por meio do projeto do Bizagi. 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa o SSO do Azure AD em um projeto do Bizagi usando os Serviços ou o Servidor de Automação.

* O Bizagi for Digital Process Automation dá suporte ao SSO iniciado por **SP**
* Após configurar o Bizagi for Digital Process Automation, você poderá impor controles de sessão, que protegem o vazamento e a infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-bizagi-for-digital-process-automation-from-the-gallery"></a>Como adicionar o Bizagi for Digital Process Automation da Galeria

Para configurar a integração do Bizagi for Digital Process Automation no Azure AD, você precisa adicionar o Bizagi for Digital Process Automation da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Bizagi for Digital Process Automation** na caixa de pesquisa.
1. Selecione **Bizagi for Digital Process Automation** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-for-digital-process-automation"></a>Configurar e testar o logon único do Azure AD para o Bizagi for Digital Process Automation

Configure e teste o SSO do Azure AD com o Bizagi for Digital Process Automation usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no projeto do Bizagi.

Para configurar e testar o SSO do Azure AD com o Bizagi for Digital Process Automation, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Bizagi for Digital Process Automation](#configure-bizagi-for-digital-process-automation-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar o Bizagi for Digital Process Automation](#create-bizagi-for-digital-process-automation-test-user)** – para ter um equivalente de B.Fernandes no Bizagi for Digital Process Automation que esteja vinculado à declaração do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Bizagi for Digital Process Automation**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Carregue o arquivo de metadados do Bizagi na opção **Carregar arquivo de metadados**.
1. Examine a configuração. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite a URL de seu projeto do Bizagi: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite a URL de seu projeto do Bizagi: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)
    
    Essa URL de metadados precisa estar registrada nas opções de autenticação de seu projeto do Bizagi.
    
1. Na página **Configurar logon único com SAML**, clique no ícone de edição/caneta de **Atributos e Declarações do Usuário** to editar o Identificador Exclusivo do Usuário.
    
    Defina o Identificador Exclusivo do Usuário como user.mail.

### <a name="create-an-azure-ad-test"></a>Criar um teste do Azure AD 

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará B.Fernandes a usar o logon único do Azure concedendo acesso ao Bizagi for Digital Process Automation.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Bizagi for Digital Process Automation**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-bizagi-for-digital-process-automation-sso"></a>Configurar o SSO do Bizagi for Digital Process Automation

Para configurar o logon único no lado do **Bizagi for Digital Process Automation**, envie a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-bizagi-for-digital-process-automation-test-user"></a>Criar usuário de teste do Bizagi for Digital Process Automation

Nesta seção, você criará um usuário chamado Brenda Fernandes no Bizagi for Digital Process Automation. Trabalhe com a [equipe de suporte do Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com) para adicionar os usuários na plataforma do Bizagi for Digital Process Automation. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Bizagi for Digital Process Automation no Painel de Acesso, você deverá ser conectado automaticamente ao portal do Bizagi for Digital Process Automation, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Bizagi for Digital Process Automation com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)