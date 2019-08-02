---
title: 'Tutorial: Integração do Azure Active Directory ao Riskware | Microsoft Docs'
description: Saiba como configurar logon único entre o Azure Active Directory e o Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b2bfbed33433521fd086d474ea4b754f5435f5e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092917"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Tutorial: Integração do Azure Active Directory ao Riskware

Neste tutorial, você aprenderá a integrar o Riskware com Azure AD (Azure Active Directory).
A integração do Riskware com Azure AD fornece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Riskware.
* Você pode permitir que seus usuários entrem automaticamente no Riskware (logon único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Riskware, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do Riskware habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Riskware é compatível com SSO iniciado por **SP**

## <a name="adding-riskware-from-the-gallery"></a>Adição do Riskware da galeria

Para configurar a integração do Riskware ao Azure AD, você precisa adicionar o Riskware da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar Riskware da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Riskware**, selecione **Riskware** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Riskware na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Riskware, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Riskware.

Para configurar e testar o logon único do Azure AD com o Riskware, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Riskware](#configure-riskware-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Riskware](#create-riskware-test-user)** – para ter um equivalente de Brenda Fernandes no Riskware vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com Riskware, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Riskware**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Riskware](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:
    
    | Ambiente| Padrão de URL|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite a URL:
    
    | Ambiente| Padrão de URL|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > O valor da URL de Entrada não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente Riskware](mailto:support@pansoftware.com.au) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar Riskware**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-riskware-single-sign-on"></a>Configurar logon único do Riskware

1. Em outra janela do navegador da Web, entre em seu site empresarial do Riskware como um administrador.

1. No canto superior direito, clique em **Manutenção** para abrir a página de manutenção.

    ![Configurações de manutenção do Riskware](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na página de manutenção, clique em **Autenticação**.

    ![Configuração de autenticação do Riskware](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Na página **Configuração de Autenticação**, execute as seguintes etapas:

    ![Configuração authenconfig do Riskware](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Selecione **Digite** como **SAML** para autenticação.

    b. In the **Code** textbox, type your code like AZURE_UAT.

    c. Na caixa de texto **Descrição**, digite sua descrição como AZURE Configuration for SSO.

    d. Na caixa de texto **Página de Logon Único**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    e. Na caixa de texto **Página de Saída**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    f. Na caixa de texto **Campo Postagem do Formulário**, digite o nome do campo presente em Postar Resposta que contém SAML, como SAMLResponse

    g. Na caixa de texto **Nome da Marca de Identidade XML**, digite o atributo que contém o identificador exclusivo na resposta SAML, como NameID.

    h. Abra o **XML de Metadados** baixado do portal do Azure no Bloco de notas, copie o certificado do arquivo de Metadados e cole-o na caixa de texto **Certificado**

    i. Na caixa de texto **URL do Consumidor**, cole o valor da **URL de Resposta**, que você recebe da equipe de suporte.

    j. Na caixa de texto **Emissor**, cole o valor do **Identificador**, que você recebe da equipe de suporte.

    > [!Note]
    > Contate a [equipe de suporte ao Cliente Riskware](mailto:support@pansoftware.com.au) para obter esses valores

    k. Selecione a caixa de seleção **Usar POST**.

    l. Selecione a caixa de seleção **Usar solicitação de SAML**.

    m. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Riskware.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Riskware**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Riskware**.

    ![O link do Riskware na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-riskware-test-user"></a>Criar um usuário de teste do Riskware

Para permitir que os usuários do Microsoft Azure Active Directory entre no Riskware, eles devem ser provisionados no Riskware. No Riskware, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Riskware como Administrador de Segurança.

1. No canto superior direito, clique em **Manutenção** para abrir a página de manutenção. 

    ![Configuração de manutenções do Riskware](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na página de manutenção, clique em **Pessoas**.

    ![Configuração Pessoas do Riskware](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Na guia **Detalhes**, execute as seguintes etapas:

    ![Detalhes da configuração do Riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Selecione **Tipo de Pessoa**, como Funcionário.

    b. Na caixa de texto **Nome**, digite o nome do usuário, como **Brenda**.

    c. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como **Brenda**.

1. Na guia **Segurança** , realize as seguintes etapas:

    ![Configuração de segurança do Riskware](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Na seção **Autenticação**, selecione o modo **Autenticação**, que você configurou como Configuração do AZURE para SSO.

    b. Na seção **Detalhes do Logon**, na caixa de texto **ID de Usuário**, insira o email do usuário como `brittasimon@contoso.com`.

    c. Na caixa de texto **Senha**, insira a senha do usuário.

1. Na guia **Organização**, execute as seguintes etapas:

    ![Configuração da Organização do Riskware](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Selecione a opção como organização **Level1**.

    b. Na seção **Local de Trabalho Principal da Pessoa**, na caixa de texto **Local**, digite o local.

    c. Na seção **Funcionário**, selecione **Status do Funcionário** como Casual.

    d. Clique em **Save** (Salvar).

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Riskware no Painel de Acesso, você deverá ser conectado automaticamente ao Riskware para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
