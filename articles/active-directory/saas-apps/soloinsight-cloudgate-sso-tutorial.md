---
title: 'Tutorial: Integração do Azure Active Directory ao Soloinsight-CloudGate SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Soloinsight-CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2dc6dcbcdee8df93f34cf4d68b5e08453554bc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090005"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Tutorial: Integração do Soloinsight-CloudGate SSO ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Soloinsight-CloudGate SSO ao Azure AD (Azure Active Directory). Ao integrar o Soloinsight CloudGate SSO ao Azure AD, você pode:

* Controlar no Azure AD quem terá acesso ao Soloinsight-CloudGate SSO.
* Permitir que os usuários sejam conectados automaticamente ao Soloinsight-CloudGate SSO com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para SSO (logon único) do Soloinsight-CloudGate SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Soloinsight-CloudGate SSO dá suporte ao SSO iniciado por **SP**.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Adicionando o Soloinsight-CloudGate SSO por meio da galeria

Para configurar a integração do Soloinsight-CloudGate SSO ao Azure AD, você precisará adicionar o Soloinsight-CloudGate SSO por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Soloinsight-CloudGate SSO** na caixa de pesquisa.
1. Selecione **Soloinsight CloudGate SSO** do painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Soloinsight-CloudGate SSO usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Soloinsight-CloudGate SSO.

Para configurar e testar o SSO do Azure AD com o Soloinsight-CloudGate SSO, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configurar o Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Soloinsight-CloudGate SSO](#create-soloinsight-cloudgate-sso-test-user)** para ter um equivalente de Brenda Fernandes no Soloinsight-CloudGate SSO que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Soloinsight-CloudGate SSO**, encontre a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, selecione **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    1. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.sigateway.com/login`

    1. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Esses valores não são reais. Atualize esses valores com a URL de Logon e o Identificador reais, o que é explicado mais adiante na seção **Configurar o logon único do Soloinsight-CloudGate SSO** do tutorial.

1. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Soloinsight-CloudGate SSO**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Configurar o Soloinsight-CloudGate SSO

1. Para automatizar a configuração no Soloinsight-CloudGate SSO, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, clicar em **Configurar Soloinsight-CloudGate SSO** direcionará você ao aplicativo Soloinsight-CloudGate SSO. No aplicativo, forneça as credenciais de administrador para entrar no Soloinsight-CloudGate SSO. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 8.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Soloinsight-CloudGate SSO manualmente, abra uma nova janela do navegador da Web, entre no site da empresa Soloinsight-CloudGate SSO como administrador e execute as seguintes etapas:

4. Para obter os valores a serem colados no portal do Azure durante a Configuração Básica do SAML, entre no portal da Web do CloudGate usando suas credenciais e, em seguida, acesse as configurações de SSO, que podem ser encontradas no caminho **Página Inicial > Administração > Configurações do sistema > Geral**.

    ![Configurações de SSO do CloudGate](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **URL do Consumidor do SAML**

    * Copie os links disponíveis nos campos **URL do Consumidor do SAML** e **URL de Redirecionamento** e cole-os na seção **Configuração Básica do SAML** no portal do Azure nos campos **Identificador (ID da Entidade)** e **URL de Resposta**, respectivamente.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **Certificado de Autenticação SAML**

    * Acesse a origem do arquivo de Certificado (Base64) baixado nas listas Certificado de Autenticação SAML do portal do Azure e clique com o botão direito do mouse nela. Escolha a opção **Editar com o Bloco de notas++** na lista. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Copie o conteúdo no arquivo de Certificado (Base64) do Bloco de notas++.

        ![Cópia do certificado](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Cole o conteúdo no campo **Certificado** das configurações de SSO do portal da Web do CloudGate e clique no botão Salvar.

        ![Portal de certificado](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Grupo Padrão**

    * Selecione **Administrador de Negócios** na lista suspensa da opção **Grupo Padrão** no portal da Web do CloudGate

        ![Grupo padrão](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **Identificador do AD e URL de Logon**

    * A **URL de Logon** copiada das configurações **Configurar o Soloinsight-CloudGate SSO** do portal do Azure deve ser inserida na seção de configurações de SSO do portal da Web do CloudGate.

    * Cole o link da **URL de Logon** do portal do Azure no campo **URL de Logon do AD** do portal da Web do CloudGate.

    * Cole o link do **Identificador do Azure AD** do portal do Azure no campo **Identificador do AD** do portal da Web do CloudGate

        ![Logon do AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará a usuária de teste Brenda Fernandes no portal do Azure.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `Britta Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela o acesso ao Soloinsight-CloudGate SSO.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Soloinsight-CloudGate SSO**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Criar um usuário de teste do Soloinsight-CloudGate SSO

Para criar um usuário de teste, selecione **Funcionários** no menu principal do portal da Web do CloudGate e preencha o formulário Adicionar Novo Funcionário. O Nível de Autoridade que deve ser atribuído ao usuário de teste é **Administrador de Negócios**. Clique em **Criar** depois que todos os campos obrigatórios forem preenchidos.

![Teste de funcionário](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Soloinsight-CloudGate SSO no Painel de Acesso, você deverá ser conectado automaticamente ao Soloinsight-CloudGate SSO, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)