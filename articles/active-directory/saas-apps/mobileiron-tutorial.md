---
title: 'Tutorial: Integração do Azure Active Directory com MobileIron | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o MobileIron.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3e4bbd5b-290e-4951-971b-ec0c1c11aaa2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 800db36f8432a8d8cf99028643f91269ffb648e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096873"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Tutorial: Integração do Azure Active Directory com MobileIron

Neste tutorial, você aprenderá a integrar o MobileIron ao Azure AD (Azure Active Directory).
A integração do MobileIron ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao MobileIron.
* Você pode permitir que os usuários sejam conectados automaticamente ao MobileIron (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao MobileIron, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do MobileIron

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O MobileIron dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-mobileiron-from-the-gallery"></a>Adicionando MobileIron da galeria

Para configurar a integração do MobileIron ao Azure AD, você precisará adicionar o MobileIron da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o MobileIron da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **MobileIron**, selecione **MobileIron** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![MobileIron na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o MobileIron, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do MobileIron.

Para configurar e testar o logon único do Azure AD com o MobileIron, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do MobileIron](#configure-mobileiron-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do MobileIron](#create-mobileiron-test-user)** – para ter um equivalente de Brenda Fernandes no MobileIron que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o MobileIron, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **MobileIron**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único em Domínio e URLs do MobileIron](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.mobileiron.com/<key>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<host>.mobileiron.com/saml/SSO/alias/<key>`

    c. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único em Domínio e URLs do MobileIron](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<host>.mobileiron.com/user/login.html`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Você obterá os valores de chave e host do portal administrativo do MobileIron, o que é explicada mais adiante no tutorial.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

### <a name="configure-mobileiron-single-sign-on"></a>Configurar o Logon Único do MobileIron

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do MobileIron como administrador.

2. Acesse **Administrador** > **Identidade** e selecione a opção **AAD** no campo **Informações sobre a Configuração do IDP de Nuvem**.

    ![Botão Configurar administrador de logon único](./media/mobileiron-tutorial/tutorial_mobileiron_admin.png)

3. Copie os valores de **Chave** e **Host** e cole-os para completar as URLs na seção **Configuração Básica do SAML** no portal do Azure.

    ![Botão Configurar administrador de logon único](./media/mobileiron-tutorial/key.png)

4. Em **Exportar arquivo de metadados do AAD e importar para o Campo de Nuvem MobileIron**, clique **Escolher Arquivo** para carregar os metadados baixados do portal do Azure. Clique em **Concluído** depois de carregar.

    ![Botão Configurar metadados do administrador de Logon Único](./media/mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao MobileIron.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **MobileIron**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **MobileIron**.

    ![O link do MobileIron na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-mobileiron-test-user"></a>Criar um usuário de teste do MobileIron

Para permitir que os usuários do Azure AD façam logon no MobileIron, eles devem ser provisionados no MobileIron.  
No caso do MobileIron, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do MobileIron como administrador.

1. Vá para **Usuários** e clique em **Adicionar** > **Usuário Único**.

    ![Botão Configurar usuário do Logon Único](./media/mobileiron-tutorial/tutorial_mobileiron_user.png)

1. Na página do diálogo **“Usuário Único”** , execute as seguintes etapas:

    ![Botão Configurar adicionar usuário de Logon Único](./media/mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. Na caixa de texto **Endereço de Email**, insira o email do usuário como brittasimon@contoso.com.

    b. Na caixa de texto **Nome**, insira o nome do usuário como Brenda.

    c. Na caixa de texto **Sobrenome**, insira o nome do usuário como Fernandes.

    d. Clique em **Concluído**.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do MobileIron no Painel de Acesso, você deverá ser conectado automaticamente ao MobileIron, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

