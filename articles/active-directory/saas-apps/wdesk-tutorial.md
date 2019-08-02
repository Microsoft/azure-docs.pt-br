---
title: 'Tutorial: Integração do Azure Active Directory com o Wdesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 0f614838ae44b5c4263bc9eac81e43fd13f87baa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67087297"
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Tutorial: Integração do Azure Active Directory com o Wdesk

Neste tutorial, você aprenderá a integrar o Wdesk ao Azure AD (Azure Active Directory).
A integração do Wdesk ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Wdesk.
* Você pode permitir que os usuários sejam conectados automaticamente ao Wdesk (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Wdesk, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Wdesk

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Wdesk dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-wdesk-from-the-gallery"></a>Adicionar o Wdesk da galeria

Para configurar a integração do Wdesk ao Azure AD, você precisará adicionar o Wdesk a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Wdesk por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Wdesk**, selecione **Wdesk** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Wdesk na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Wdesk, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Wdesk.

Para configurar e testar o logon único do Azure AD com o Wdesk, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Wdesk](#configure-wdesk-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Wdesk](#create-wdesk-test-user)** – para ter um equivalente de Brenda Fernandes no Wdesk que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Wdesk, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Wdesk**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Wdesk](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do Wdesk](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Você pode obter esses valores no portal do WDesk ao configurar o SSO.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Wdesk**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-wdesk-single-sign-on"></a>Configurar o logon único do Wdesk

1. Em outra janela do navegador da Web, entre no Wdesk como Administrador de segurança.

2. No canto inferior esquerdo, clique em **Administrador** e escolha **Conta de Administrador**:
 
     ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Na Administração do Wdesk, navegue até **Segurança**, **SAML** > **Configurações de SAML**:

    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. Em **Configurações Gerais**, marque **Habilitar Logon Único do SAML**:

    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. Em **Detalhes do Provedor de Serviço**, execute as seguintes etapas:

    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Copie a **URL de Logon** e cole-a na caixa de texto **Url de Logon** no Portal do Azure.
   
      b. Copie a **URL de Metadados** e cole-a na caixa de texto **Identificador** no Portal do Azure.
       
      c. Copie a **URL de Logon** e cole-a na caixa de texto **Url de Resposta** no Portal do Azure.
   
      d. Clique em **Salvar** no Portal do Azure para salvar as alterações.      

6. Clique em **Definir Configurações do IdP** para abrir a caixa de diálogo **Editar Configurações do IdP**. Clique em **Escolher Arquivo** para localizar o arquivo **Metadata.xml** salvo no Portal do Azure, depois carregue-o.
    
    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Clique em **Salvar alterações**.

    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Wdesk.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Wdesk**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Wdesk**.

    ![O link do Wdesk na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-wdesk-test-user"></a>Criar um usuário de teste do Wdesk

Para permitir que os usuários do Azure AD entrem no Wdesk, eles precisam ser provisionados no Wdesk. No Wdesk, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Wdesk como Administrador de segurança.

2. Navegue até **Administrador** > **Conta de Administrador**.

     ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Clique em **Membros**, sob **Pessoas**.

4. Agora clique em **Adicionar Membro** para abrir a caixa de diálogo **Adicionar Membro**. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/wdesk-tutorial/createuser1.png)  

5. Na caixa de texto **Usuário**, insira o nome do usuário, como brittasimon@contoso.com e clique no botão **Continuar**.

    ![Criação de um usuário de teste do AD do Azure](./media/wdesk-tutorial/createuser3.png)

6.  Insira os detalhes, conforme mostrado abaixo:
  
    ![Criação de um usuário de teste do AD do Azure](./media/wdesk-tutorial/createuser4.png)
 
    a. Na caixa de texto **Email**, insira o email do usuário como brittasimon@contoso.com.

    b. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda**.

    c. Na caixa de texto **Sobrenome**, insira o nome do usuário como **Fernandes**.

7. Clique no botão **Salvar Membro**.  

    ![Criação de um usuário de teste do AD do Azure](./media/wdesk-tutorial/createuser5.png)

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Wdesk no Painel de Acesso, você deverá ser conectado automaticamente ao Wdesk, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

