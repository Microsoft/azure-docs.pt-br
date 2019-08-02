---
title: 'Tutorial: Integração do Azure Active Directory ao TOPdesk – Público | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TOPdesk – Public.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: a2a0ffd670a03aeaaa262b83127a385be9efc978
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088489"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Tutorial: Integração do Azure Active Directory ao TOPdesk – Público

Neste tutorial, você aprenderá a integrar o TOPdesk – Public ao Azure AD (Azure Active Directory).
A integração do TOPdesk – Public ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao TOPdesk – Public.
* Você pode permitir que os usuários se conectem automaticamente ao TOPdesk – Public (Logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao TOPdesk – Public, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do TOPdesk – Public habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O TOPdesk – Public é compatível com o SSO iniciado por **SP**

## <a name="adding-topdesk---public-from-the-gallery"></a>Adicionar o TOPdesk – Public da galeria

Para configurar a integração do TOPdesk – Public ao Azure AD, é necessário adicionar o TOPdesk – Public da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o TOPdesk – Public da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **TOPdesk – Public**, selecione **TOPdesk – Public** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![TOPdesk – Public na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o TOPdesk – Public, com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do TOPdesk – Public.

Para configurar e testar o logon único do Azure AD com o TOPdesk – Public, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do TOPdesk – Public](#configure-topdesk---public-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do TOPdesk – Public](#create-topdesk---public-test-user)** – para ter um equivalente de Brenda Fernandes no TOPdesk – Public vinculado à declaração do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o TOPdesk – Public, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **TOPdesk – Public**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4.  Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    >[!NOTE]
    >Você obterá o **arquivo de metadados do fornecedor de serviços**, que é explicado posteriormente na seção **Configurar o logon único do TOPdesk – Public** do tutorial.

    a. Clique em **Carregar arquivo de metadados**.
    
    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão preenchidos automaticamente na seção "Configuração Básica do SAML".

    ![Informações de logon único de Domínio e URLs do TOPdesk – Public](common/sp-identifier-reply.png)

    d. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<companyname>.topdesk.net`

    e. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<companyname>.topdesk.net/tas/public/login/verify`

    > [!NOTE] 
    > Se os valores **Identificador** e **URL de resposta** não forem preenchidos automaticamente, você precisará reinseri-los manualmente. Para o identificador, siga o padrão, conforme mencionado acima, e obtenha o valor da URL de resposta na seção **Configurar o logon único do TOPdesk – Public** que é explicada posteriormente no tutorial. O valor de **URL de logon** não é real, então é necessário atualizar o valor com a URL de logon real. Entre em contato com a [equipe de suporte ao cliente do TOPdesk – Public](https://help.topdesk.com/saas/enterprise/user/) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o TOPdesk – Public**, copie as URLs apropriadas de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-topdesk---public-single-sign-on"></a>Configurar o logon único do TOPdesk – Public

1. Faça logon em seu site de empresa do **TOPdesk - Public** como administrador.

2. No menu **TOPdesk**, clique em **Configurações**.
   
    ![Configurações](./media/topdesk-public-tutorial/ic790598.png "Configurações")

3. Clique em **Configurações de Logon**.
   
    ![Configurações de logon](./media/topdesk-public-tutorial/ic790599.png "Configurações de logon")

4. Expanda o menu **Configurações de Logon** e clique em **Geral**.
   
    ![Geral](./media/topdesk-public-tutorial/ic790600.png "Geral")

5. Na seção **Público** da seção de configuração de **Logon do SAML**, realize as seguintes etapas:
   
    ![Configurações técnicas](./media/topdesk-public-tutorial/ic790601.png "Configurações técnicas")
   
    a. Clique em **Baixar** para baixar o arquivo de metadados públicos e salve-o localmente no computador.
   
    b. Abra o arquivo de metadados baixado e localize o nó **AssertionConsumerService**.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Copie o valor **AssertionConsumerService**, cole-o na caixa de texto **URL de resposta** na seção **Configuração de SAML básica**.      
   
6. Execute as seguintes etapas para criar um arquivo de certificado:
    
    ![Certificado](./media/topdesk-public-tutorial/ic790606.png "Certificado")
    
    a. Abra o arquivo de metadados baixado do Portal do Azure.
    
    b. Expanda o nó **RoleDescriptor** que contém um **xsi:type** de **fed:ApplicationServiceType**.
    
    c. Copie o valor do nó **X509Certificate** .
    
    d. Salve o valor copiado de **X509Certificate** localmente no computador em um arquivo.

7. Na seção **Público**, clique em **Adicionar**.
    
    ![Logon SAML](./media/topdesk-public-tutorial/ic790625.png "logon SAML")

8. Na página do diálogo **Assistente de configuração do SAML** , realize as seguintes etapas:
    
    ![Assistente de configuração SAML](./media/topdesk-public-tutorial/ic790608.png "Assistente de configuração SAML")
    
    a. Para carregar o arquivo de metadados baixado do Portal do Azure, em **Metadados de Federação**, clique em **Procurar**.

    b. Para carregar o arquivo de certificado, em **Certificado (RSA)** , clique em **Procurar**.

    c. Para carregar o arquivo de logotipo que você recebeu da equipe de suporte do TOPdesk, em **Ícone do logotipo**, clique em **Procurar**.

    d. Na caixa de texto **Atributo de nome de usuário**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Na caixa de texto **Nome de exibição** , digite um nome para a sua configuração.

    f. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao TOPdesk – Public.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **TOPdesk – Public**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **TOPdesk – Public**.

    ![O link do TOPdesk – Public na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-topdesk---public-test-user"></a>Criar usuário de teste do TOPdesk – Public

Para permitir que os usuários do Azure AD se conectem ao TOPdesk – Public, eles deverão ser provisionados no TOPdesk – Public. No caso do TOPdesk - Público, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:

1. Faça logon em seu site de empresa do **TOPdesk - Public** como administrador.

2. No menu na parte superior, clique em **TOPdesk \> Novo \> Arquivos de Suporte \> Pessoa**.
   
    ![Pessoa](./media/topdesk-public-tutorial/ic790628.png "Pessoa")

3. Na caixa de diálogo Nova Pessoa, execute as seguintes etapas:
   
    ![Nova pessoa](./media/topdesk-public-tutorial/ic790629.png "Nova pessoa")
   
    a. Clique na guia Geral.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, tal como Fernandes
 
    c. Selecione um **Site** para a conta.
 
    d. Clique em **Save** (Salvar).

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do TOPdesk – Público ou APIs fornecidas pelo TOPdesk – Público para provisionar as contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do TOPdesk – Public no Painel de Acesso, você deverá ser conectado automaticamente ao TOPdesk – Public para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
