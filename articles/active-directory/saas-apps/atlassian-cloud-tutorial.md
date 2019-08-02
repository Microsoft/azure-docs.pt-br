---
title: 'Tutorial: Integração do Azure Active Directory ao Atlassian Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdbfb07b73d591bbab64f38e8c986fb1b7e072a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106582"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Tutorial: Integração do Atlassian Cloud com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Atlassian Cloud ao Azure Active Directory (Azure AD). Ao integrar o Atlassian Cloud com o Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao Atlassian Cloud.
* Permitir que os usuários sejam conectados automaticamente ao Atlassian Cloud com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura do Atlassian Cloud habilitada para logon único (SSO).
* Para habilitar logon único do SAML (Security Assertion Markup Language) para produtos Atlassian Cloud, é necessário configurar o Atlassian Access. Saiba mais sobre o [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Atlassian Cloud é compatível com SSO iniciado por **SP e IDP**

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Adição do Atlassian Cloud da galeria

Para configurar a integração do Atlassian Cloud ao Azure AD, você precisará adicionar o Atlassian Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Atlassian Cloud** na caixa de pesquisa.
1. Selecione **Atlassian Cloud** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Atlassian Cloud usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Atlassian Cloud.

Para configurar e testar o SSO do Azure AD com o Atlassian Cloud, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do Atlassian Cloud](#configure-atlassian-cloud-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Atlassian Cloud](#create-atlassian-cloud-test-user)** – para ter um equivalente de B.Fernandes no Atlassian Cloud que esteja vinculado à sua representação no Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Atlassian Cloud**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://auth.atlassian.com/saml/<unique ID>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Clique em **Definir URLs adicionais**.

    d. Na caixa de texto **Estado de Retransmissão**, digite uma URL usando o seguinte padrão: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Os valores anteriores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Você obterá esses valores reais da tela **Configuração SAML do Atlassian Cloud**, que é explicada posteriormente em **Configurar logon único do Atlassian Cloud** do tutorial.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > O valor da URL de Entrada não é real. Cole o valor da instância que você usa para se conectar ao portal de administrador do Atlassian Cloud.

    ![Configurar o logon único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. O aplicativo Atlassian Cloud espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizados para a configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo Atlassian Cloud espera que **nameidentifier** seja mapeado com **user.mail**. Portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

1. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para fazer o download do certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar Atlassian Cloud**, copie a URL apropriada de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-atlassian-cloud-sso"></a>Configurar o logon único do Atlassian Cloud

1. Para automatizar a configuração no Atlassian Cloud, você precisará instalar a **extensão de navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar extensão**.

    ![Extensão dos meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clicar em **Instalar Atlassian Cloud** direcionará você ao aplicativo Atlassian Cloud. No aplicativo, forneça as credenciais de administrador para entrar no Atlassian Cloud. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar manualmente o Atlassian Cloud, abra uma nova janela do navegador da Web, entre no site corporativo do Atlassian Cloud como administrador e execute as seguintes etapas:

4. É necessário verificar o domínio antes de configurar logon único. Para obter mais informações, consulte o documento de [verificação de domínio do Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

5. No painel à esquerda, selecione **Segurança** > **Logon único SAML**. Se você ainda não fez isso, assine o Identity Manager da Atlassian.

    ![Configurar o logon único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

6. Na janela de **Adicionar configuração SAML**, faça o seguinte:

    ![Configurar o logon único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. Na caixa **ID da entidade do provedor de identidade**, cole o **Identificador do Azure AD** que você copiou do portal do Azure.

    b. Na caixa **URL de SSO do provedor de identidade**, cole a **URL de logon** que você copiou do portal do Azure.

    c. Abra o certificado baixado do portal do Azure em arquivo .txt, copie o valor (sem as linhas *Begin Certificate* e *End Certificate*) e cole-os na caixa **Certificado X509 público**.

    d. Clique em **Salvar Configuração**.

7. Para garantir que você configurou as URLs corretas, atualize as configurações do Azure AD fazendo o seguinte:

    ![Configurar o logon único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. Na janela do SAML, copie a **ID de identidade SP** e, em seguida, no portal do Azure, na **Configuração de SAML básica** do Atlassian Cloud, cole-a na caixa **Identificador**.

    b. Na janela do SAML, copie a **URL do Serviço do Consumidor de Declaração SP** e, em seguida, no portal do Azure, na **Configuração de SAML básica** do Atlassian Cloud, cole-a na caixa **URL de resposta**. A URL de logon é a URL do locatário do seu Atlassian Cloud.

    > [!NOTE]
    > Se você for um cliente existente, depois de atualizar os valores da **ID de identidade SP** e **URL do Serviço de Consumidor de Declaração de SP** no portal do Azure, selecione **Sim, atualize a configuração**. Se você for um novo cliente, você pode ignorar esta etapa.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ele acesso ao Atlassian Cloud.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Atlassian Cloud**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-atlassian-cloud-test-user"></a>Criar um usuário de teste do Atlassian Cloud

Para permitir que os usuários do Azure AD entrar no Atlassian Cloud, provisione as contas de usuário manualmente no Atlassian Cloud fazendo o seguinte:

1. No painel **Administração**, selecione **Usuários**.

    ![O link de Usuários do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

2. Para criar um usuário no Atlassian Cloud, selecione **Convidar usuário**.

    ![Criar um usuário do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

3. Na caixa **Endereço de email**, insira o endereço de email do usuário e atribua o acesso ao aplicativo.

    ![Criar um usuário do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

4. Para enviar um convite por email para o usuário, selecione **Convidar usuários**. Um convite por email é enviado ao usuário e, depois de aceitar o convite, o usuário está ativo no sistema.

> [!NOTE]
> Você também pode criar usuários em massa clicando no botão **Criar em Massa** na seção **Usuários**.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Atlassian Cloud no Painel de Acesso, você deverá ser conectado automaticamente ao Atlassian Cloud para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)