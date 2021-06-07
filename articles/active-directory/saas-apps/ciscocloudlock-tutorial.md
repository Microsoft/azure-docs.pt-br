---
title: 'Tutorial: Integração do Azure Active Directory ao The Cloud Security Fabric | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o The Cloud Security Fabric.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: jeedes
ms.openlocfilehash: 7025b948615efcf6ace3ca0fb6a2daecdd75c702
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456030"
---
# <a name="tutorial-integrate-the-cloud-security-fabric-with-azure-active-directory"></a>Tutorial: Integrar o The Cloud Security Fabric ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o The Cloud Security Fabric ao Azure AD (Azure Active Directory). Ao integrar o The Cloud Security Fabric ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao The Cloud Security Fabric.
* Habilitar os usuários a serem conectados automaticamente ao The Cloud Security Fabric por meio de suas respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do The Cloud Security Fabric habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O The Cloud Security Fabric é compatível com SSO iniciado por **SP**

## <a name="adding-the-cloud-security-fabric-from-the-gallery"></a>Adição do The Cloud Security Fabric da galeria

Para configurar a integração do The Cloud Security Fabric com o Azure AD, é necessário adicionar o The Cloud Security Fabric da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **The Cloud Security Fabric** na caixa de pesquisa.
1. Selecione **The Cloud Security Fabric** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o The Cloud Security Fabric usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do The Cloud Security Fabric.

Para configurar e testar o SSO do Azure AD com o The Cloud Security Fabric, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do The Cloud Security Fabric](#configure-the-cloud-security-fabric-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do The Cloud Security Fabric](#create-the-cloud-security-fabric-test-user)** – para ter um equivalente de B.Fernandes no The Cloud Security Fabric que esteja vinculado à representação de usuário no Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **The Cloud Security Fabric**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de logon**, digite uma URL: 

      ```http
      https://platform.cloudlock.com
      https://app.cloudlock.com
      ```

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão:

      ```http
      https://platform.cloudlock.com/gate/saml/sso/<subdomain>
      https://app.cloudlock.com/gate/saml/sso/<subdomain>
      ```

    > [!NOTE]
    > O valor do Identificador não é real. Atualize o valor com o identificador real. Entre em contato com a [equipe de suporte ao cliente do The Cloud Security Fabric](mailto:support@cloudlock.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

5. Para modificar as opções de **Assinatura** de acordo com seus requisitos, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Resposta SAML](./media/ciscocloudlock-tutorial/saml.png)

    a. Selecione a opção **Assinar resposta SAML e declaração** para **Opção de Assinatura**.

    b. Selecione a opção **SHA-256** para o **Algoritmo de Autenticação**.

    c. Clique em **Save** (Salvar).  

6. Na seção **Configurar o The Cloud Security Fabric**, copie as URLs apropriadas com base em seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-the-cloud-security-fabric-sso"></a>Configurar o SSO do The Cloud Security Fabric

Para configurar o logon único no lado do **The Cloud Security Fabric**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do The Cloud Security Fabric](mailto:support@cloudlock.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.
### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que o usuário B.Fernandes use o logon único do Azure concedendo a ele acesso ao The Cloud Security Fabric.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **The Cloud Security Fabric**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-the-cloud-security-fabric-test-user"></a>Criar usuário de teste do The Cloud Security Fabric

Nesta seção, você cria um usuário chamado B.Fernandes no The Cloud Security Fabric. Trabalhe com a [equipe de suporte do The Cloud Security Fabric](mailto:support@cloudlock.com) para adicionar os usuários à plataforma do The Cloud Security Fabric. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do The Cloud Security Fabric, no Painel de Acesso, você deverá ser conectado automaticamente ao The Cloud Security Fabric para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)