---
title: 'Tutorial: Integração do Azure Active Directory com o Mitel Connect | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Mitel Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: e03870322df94a4c9587a3395c70925d2d2e838d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588216"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Tutorial: Integração do Azure Active Directory ao Mitel MiCloud Connect

Neste tutorial, você aprenderá a integrar o Mitel MiCloud Connect ao Azure AD (Azure Active Directory). A integração do Mitel MiCloud Connect ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso aos aplicativos MiCloud Connect usando suas credenciais da empresa.
* Você pode permitir que os usuários em sua conta sejam conectados automaticamente ao MiCloud Connect (Logon Único) com suas contas do Azure AD.


Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao MiCloud Connect, você precisará dos seguintes itens:

* Uma assinatura do Azure AD

  Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma conta do Mitel MiCloud Connect

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO (logon único) do Azure AD.

* O Mitel Connect é compatível com SSO iniciado por **SP**

## <a name="adding-mitel-connect-from-the-gallery"></a>Adicionando o Mitel Connect da Galeria

Para configurar a integração do Mitel Connect ao Azure AD, você precisará adicionar o Mitel Connect por meio da galeria à lista de aplicativos SaaS gerenciados no portal do Azure.

**Para adicionar o Mitel Connect por meio da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, clique em **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Clique em **Aplicativos Empresariais** e, em seguida, em **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique em **Novo aplicativo**.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Digite **Mitel Connect** no campo de pesquisa, clique em **Mitel Connect** no painel de resultados e, em seguida, clique em **Adicionar**.

     ![Mitel Connect na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o MiCloud Connect, com base em um usuário de teste chamado **Brenda Fernandes**. Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do MiCloud Connect.

Para configurar e testar o logon único do Azure AD com o MiCloud Connect, você precisará concluir as seguintes etapas:

1. **[Configurar o MiCloud Connect para o SSO com o Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** – para permitir que os usuários usem esse recurso e definam as configurações de SSO no lado do aplicativo.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
4. **[Criar um usuário de teste do Mitel MiCloud Connect](#create-a-mitel-micloud-connect-test-user)** – para ter um equivalente de Brenda Fernandes em sua conta do MiCloud Connect que esteja vinculado à representação do usuário do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Configurar o MiCloud Connect para o SSO com o Azure AD

Nesta seção, você habilitará o logon único do Azure AD para o MiCloud Connect no portal do Azure e configurará sua conta do MiCloud Connect para permitir o SSO usando o Azure AD.

Para configurar o MiCloud Connect com o SSO do Azure AD, é mais fácil abrir o portal do Azure e o portal da conta do Mitel lado a lado. Você precisará copiar algumas informações do portal do Azure para o portal da conta do Mitel e algumas do portal da conta do Mitel para o portal do Azure.


1. Para abrir a página de configuração no [portal do Azure](https://portal.azure.com/), faça o seguinte:

    a. Na página de integração de aplicativos do **Mitel Connect**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

    b. Na caixa de diálogo **Selecionar um método de Logon único**, clique em **SAML**.

    ![Modo de seleção de logon único](common/select-saml-option.png)
    
    A página de entrada baseada no SAML será exibida.

2. Para abrir a caixa de diálogo de configuração no portal da conta do Mitel, faça o seguinte:

    a. No menu **Sistema de Telefonia**, clique em **Recursos Complementares**.

    b. À direita de **Logon Único**, clique em **Ativar** ou **Configurações**.
    
    A caixa de diálogo Configurações de Logon Único do Connect será exibida.
    
3. Marque a caixa de seleção **Habilitar Logon Único**.
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. No portal do Azure, clique no ícone **Editar** na seção **Configuração Básica do SAML**.
    ![image](common/edit-urls.png)

    A caixa de diálogo Configuração Básica do SAML será exibida.

5.  Copie a URL do campo **Mitel Identifier (Entity ID)** (Identificador do Mitel [ID da Entidade]) no portal da conta do Mitel e cole-a no campo **Identificador (ID da Entidade)** no portal do Azure.

6. Copie a URL do campo **Reply URL (Assertion Consumer Service URL)** (URL de Resposta [URL do Serviço do Consumidor de Declaração]) no portal da conta do Mitel e cole-a no campo **URL de Resposta (URL do Serviço do Consumidor de Declaração)** no portal do Azure.  
   ![image](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. Na caixa de texto **URL de Logon**, digite uma das seguintes URLs:

    * **https://portal.shoretelsky.com** – para usar o portal da conta do Mitel como o aplicativo padrão do Mitel
    * **https://teamwork.shoretel.com** – para usar o Teamwork como o aplicativo padrão do Mitel

    **OBSERVAÇÃO**: O aplicativo padrão do Mitel é o aplicativo acessado quando um usuário clica no bloco do Mitel Connect no Painel de Acesso. Esse também é o aplicativo acessado ao fazer uma configuração do teste por meio do Azure AD.

8. Clique em **Salvar** na caixa de diálogo **Configuração Básica do SAML** no portal do Azure.

9. Na seção **Certificado de Autenticação SAML** na página **Logon baseado em SAML** do portal do Azure, clique em **Baixar** ao lado de **Certificado (Base64)** para baixar o **Certificado de Autenticação** e salvá-lo no computador.
    ![image](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Abra o arquivo de Certificado de Autenticação em um editor de texto, copie todos os dados no arquivo e, em seguida, cole os dados no campo **Certificado de Autenticação** no portal da conta do Mitel. 
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. Na seção **Configurar o Mitel Connect** da página **Logon baseado em SAML** do portal do Azure, faça o seguinte:

    a. Copie a URL do campo **URL de Logon** e cole-a no campo **URL de Entrada** no portal da conta do Mitel.

    b. Copie a URL do campo **Identificador do Azure AD** e cole-a no campo **ID da Entidade** no portal da conta do Mitel.
    ![image](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Clique em **Salvar** na caixa de diálogo **Configurações de Logon Único do Connect** no portal da conta do Mitel.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Nesta seção, você criará um usuário de teste chamado Brenda Fernandes no portal do Azure.

1. No portal do Azure, no painel esquerdo, clique em **Azure Active Directory**, em **Usuários** e, em seguida, em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Clique em **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Na caixa de diálogo Propriedades do usuário, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, digite **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite brendafernandes@\<domíniodaempresa\>.\<extensão\>.  
Por exemplo, BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Mitel Connect.

1. No portal do Azure, clique em **Aplicativos Empresariais** e, em seguida, em **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, clique em **Mitel Connect**.

    ![O link do Mitel Connect na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Clique em **Adicionar usuário** e, em seguida, clique em **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista **Usuários** e, em seguida, clique em **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, selecione a função de usuário apropriada na lista na caixa de diálogo **Selecionar Função** e, em seguida, clique em **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar Atribuição**, clique em **Atribuir**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Criar um usuário de teste do Mitel MiCloud Connect

Nesta seção, você criará um usuário chamado Brenda Fernandes em sua conta do MiCloud Connect. Os usuários precisam ser criados e ativados para usarem o logon único.

Para obter detalhes sobre como adicionar usuários no portal da conta do Mitel, confira o artigo [Adding a User](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) (Como adicionar um usuário) da Base de Dados de Conhecimento da Mitel.

Crie um usuário em sua conta do MiCloud Connect com os seguintes detalhes:

  * **Nome:** Brenda Fernandes

* **Endereço de Email Empresarial:** `brittasimon@<yourcompanydomain>.<extension>`   
(Exemplo: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Nome de usuário:** `brittasimon@<yourcompanydomain>.<extension>`  
(Exemplo: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); o nome de usuário do usuário normalmente é o mesmo que o endereço de email empresarial do usuário)

**OBSERVAÇÃO:** O nome de usuário do MiCloud Connect do usuário precisa ser idêntico ao endereço de email do usuário no Azure.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Mitel Connect no Painel de Acesso, você deverá ser redirecionado automaticamente para a entrada no aplicativo MiCloud Connect configurado como o padrão no campo **URL de Logon**. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
