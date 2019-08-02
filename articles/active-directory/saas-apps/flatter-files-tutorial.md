---
title: 'Tutorial: Integração do Azure Active Directory com o Flatter Files | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Flatter Files.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 9e4ba987393628af07f8a8a507f635047eb18cc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102556"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Tutorial: Integração do Azure Active Directory com o Flatter Files

Neste tutorial, você aprenderá a integrar o Flatter Files ao Azure AD (Azure Active Directory).
A integração do Flatter Files ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Flatter Files.
* Você pode permitir que os usuários sejam conectados automaticamente ao Flatter Files (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure ao Flatter Files, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Flatter Files

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Flatter Files dá suporte ao SSO iniciado por **IDP**

## <a name="adding-flatter-files-from-the-gallery"></a>Adicionando Flatter Files da galeria

Para configurar a integração do Flatter Files ao AD do Azure, você precisará adicionar o Flatter Files da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Flatter Files da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Flatter Files**, selecione **Flatter Files** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Flatter Files na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Flatter Files, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Flatter Files.

Para configurar e testar o logon único do AD do Azure com o Flatter Files, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Flatter Files](#configure-flatter-files-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Flatter Files](#create-flatter-files-test-user)** – para ter um equivalente de Brenda Fernandes no Flatter Files que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Flatter Files, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Flatter Files**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

    ![Informações de logon único de Domínio e URLs do Flatter Files](common/preintegrated.png)

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Flatter Files**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-flatter-files-single-sign-on"></a>Configurar o logon único do Flatter Files

1. Faça logon no aplicativo Flatter Files como administrador.

2. Clique em **PAINEL**. 
   
    ![Configurar o logon único](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Clique em **Configurações** e execute as etapas a seguir na guia **Empresa**: 
   
    ![Configurar o logon único](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Selecione **Usar SAML 2.0 para Autenticação**.
    
    b. Clique em **Configurar SAML**.

4. No diálogo **Configuração de SAML** , execute as seguintes etapas: 
   
    ![Configurar o logon único](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. Na caixa de texto **Domínio**, digite seu domínio registrado.
   
   > [!NOTE]
   > Se não tiver um domínio registrado, entre em contato com a equipe de suporte do Flatter Files pelo email [support@flatterfiles.com](mailto:support@flatterfiles.com). 
    
    b. Na caixa de texto **URL do Provedor de Identidade**, cole o valor da **URL de Logon** copiado do portal do Azure.
   
    c.  Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do Provedor de Identidade**.

    d. Clique em **Atualizar**.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Flatter Files.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Flatter Files**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Flatter Files**.

    ![O link do Flatter Files na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-flatter-files-test-user"></a>Criar um usuário de teste do Flatter Files

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Flatter Files.

**Para criar um usuário chamado Brenda Fernandes no Flatter Files, execute as seguintes etapas:**

1. Faça logon no site da empresa **Flatter Files** como administrador.

2. No painel de navegação à esquerda, clique em **Configurações** e, em seguida, clique na guia **Usuários**.
   
    ![Criar um usuário do Flatter Files](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Clique em **Adicionar Usuário**. 

4. No diálogo **Adicionar Usuário** , realize as seguintes etapas:
   
    ![Criar um usuário do Flatter Files](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**. 
   
    c. Na caixa de texto **Endereço de Email**, digite o endereço de email de Brenda no portal do Azure.
   
    d. Clique em **Enviar**.   


### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Flatter Files no Painel de Acesso, você deverá ser conectado automaticamente ao Flatter Files, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

