---
title: 'Tutorial: integração do Azure Active Directory com o BitaBIZ | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o BitaBIZ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e52b5478bc4b483b793f155eab2f41246552d49
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106363"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Tutorial: integração do Azure Active Directory com o BitaBIZ

Neste tutorial, você aprenderá a integrar o BitaBIZ ao Azure Active Directory (Azure AD).
A integração do BitaBIZ ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao BitaBIZ.
* Você pode permitir que os usuários sejam conectados automaticamente ao BitaBIZ (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao BitaBIZ, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do BitaBIZ

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O BitaBIZ dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-bitabiz-from-the-gallery"></a>Adicionar o BitaBIZ a partir da galeria

Para configurar a integração do BitaBIZ ao Azure AD, você precisará adicionar o Bynder a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o BitaBIZ a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **BitaBIZ**, selecione **BitaBIZ** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![BitaBIZ na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o BitaBIZ, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do BitaBIZ.

Para configurar e testar o logon único do Azure AD com o BitaBIZ, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do BitaBIZ](#configure-bitabiz-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do BitaBIZ](#create-bitabiz-test-user)** – para ter um equivalente de Brenda Fernandes no BitaBIZ que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o BitaBIZ, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **BitaBIZ**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo iniciado por **IDP**:

    ![Informações de logon único de Domínio e URLs do BitaBIZ](common/idp-identifier.png)

    Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > O valor na URL anterior é apenas para demonstração. Você atualiza o valor com o identificador real, o que é explicado no tutorial posteriormente.

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![image](common/both-preintegrated-signon.png)

    Na caixa de texto **URL de Logon**, digite a URL: `https://www.bitabiz.com/dashboard`

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o BitaBIZ**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-bitabiz-single-sign-on"></a>Configurar o logon único do BitaBIZ

1. Em uma janela diferente do navegador da Web, faça logon em seu locatário do BitaBIZ como um administrador.

2. Clique em **CONFIGURAR ADMIN**.

    ![Configuração do BitaBIZ](./media/bitabiz-tutorial/settings1.png)

3. Clique em **Integrações Microsoft** na seção **Adicionar valor**.

    ![Configuração do BitaBIZ](./media/bitabiz-tutorial/settings2.png)

4. Role para baixo até a seção **Microsoft Azure AD (Habilitar logon único)** e execute as seguintes etapas:

    ![Configuração do BitaBIZ](./media/bitabiz-tutorial/settings3.png)

    a. Copie o valor da caixa de texto **ID da Entidade (“Identificador” no Azure AD)** e cole-o na caixa e texto **Identificador** na seção **Configuração Básica do SAML** do portal do Azure. 

    b. Na caixa de texto **URL de Serviço de Logon Único do Azure AD**, cole a **URL de Logon** copiada do portal do Azure.

    c. Na caixa de texto **ID da Entidade do SAML do Azure AD**, cole o **Identificador do Azure AD** copiado do portal do Azure.

    d. Abra o arquivo de **Certificado (Base64)** baixado no bloco de notas, copie o conteúdo dele para a área de transferência e, em seguida, cole-o na caixa de texto **Certificado de autenticação do Azure AD (codificação Base64)** .

    e. Adicione seu nome de domínio de email corporativo, ou seja, minhaempresa.com na caixa de texto **Nome de domínio** para atribuir o SSO aos usuários em sua empresa com este domínio de email (OPCIONAL).

    f. Marque **Habilitado para SSO** para a conta do BitaBIZ.

    g. Clique em **Salvar a configuração do Azure AD** para salvar e ativar a configuração de SSO.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao BitaBIZ.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **BitaBIZ**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **BitaBIZ**.

    ![O link do BitaBIZ na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-bitabiz-test-user"></a>Criar um usuário de teste do BitaBIZ

Para permitir que os usuários do Azure AD façam logon no BitaBIZ, eles devem ser provisionados no BitaBIZ.  
No caso do BitaBIZ, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do BitaBIZ como administrador.

2. Clique em **CONFIGURAR ADMIN**.

    ![Adicionar usuário no BitaBIZ](./media/bitabiz-tutorial/settings1.png)

3. Clique em **Adicionar usuários** na seção **Organização**.

    ![Adicionar usuário no BitaBIZ](./media/bitabiz-tutorial/user1.png)

4. Clique em **Adicionar novo funcionário**.

    ![Adicionar usuário no BitaBIZ](./media/bitabiz-tutorial/user2.png)

5. Na página da caixa de diálogo **Adicionar novo funcionário**, execute as seguintes etapas:

    ![Adicionar usuário no BitaBIZ](./media/bitabiz-tutorial/user3.png)

    a. Na caixa de texto **Nome**, digite o nome do usuário, como Brenda.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como Fernandes.

    c. Na caixa de texto **Email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.

    d. Selecione uma data em **Data de admissão**.

    e. Existem outros atributos de usuário opcionais, que podem ser configurados para o usuário. Consulte o [Documento de configuração do usuário](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) para obter mais detalhes.

    f. Clique em **Salvar funcionário**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe um email e segue um link para confirmar sua conta antes que ela se torne ativa.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do BitaBIZ no Painel de Acesso, você deverá ser conectado automaticamente ao BitaBIZ, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
