---
title: 'Tutorial: Integração do Azure Active Directory ao Pega Systems | Microsoft Docs'
description: Neste tutorial você aprenderá a configurar o logon único entre o Azure Active Directory e o Pega Systems.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 013e477b66d2772698ce5c9cc61a59f8a5a04a5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094890"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Tutorial: Integração do Azure Active Directory ao Pega Systems

Neste tutorial, você aprenderá a integrar o Pega Systems ao Azure AD (Azure Active Directory).

Essa integração oferece estes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao Pega Systems.
* Você pode permitir que os usuários sejam conectados automaticamente ao Pega Systems (logon único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Pega Systems, você precisa ter:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, inscreva-se em uma [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura do Pega Systems que tenha o logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Pega Systems é compatível com SSO iniciado por SP e IdP.

## <a name="add-pega-systems-from-the-gallery"></a>Adicionar o Pega Systems por meio da galeria

Para configurar a integração do Pega Systems ao Azure AD, você precisa adicionar o Pega Systems por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Acesse **Aplicativos empresariais** > **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um aplicativo, selecione **Novo aplicativo** na parte superior da janela:

    ![Selecionar Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Pega Systems**. Selecione **Pega Systems** nos resultados da pesquisa e selecione **Adicionar**.

     ![Resultados da Pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Pega Systems usando um usuário de teste chamado Brenda Fernandes.
Para habilitar o logon único, você precisará estabelecer uma relação entre um usuário do Azure AD e o usuário correspondente no Pega Systems.

Para configurar e testar o logon único do Azure AD com o Pega Systems, você precisará concluir estas etapas:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** para habilitar o recurso para os usuários.
2. **[Configurar o logon único do Pega Systems](#configure-pega-systems-single-sign-on)** no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para habilitar o logon único do Azure AD para o usuário.
5. **[Criar um usuário de teste do Pega Systems](#create-a-pega-systems-test-user)** que esteja vinculado à representação do Azure AD do usuário.
6. **[Testar o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Pega Systems, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Pega Systems**, selecione **Logon único**:

    ![Selecione Logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único:

    ![Selecionar um método de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Configuração Básica de SAML**:

    ![Ícone Editar](common/edit-urls.png)

4. Na caixa de diálogo **Configuração Básica de SAML**, caso deseje configurar o aplicativo no modo iniciado por IdP, realize as seguintes etapas.

    ![Caixa de diálogo Configuração Básica de SAML](common/idp-intiated.png)

    1. Na caixa **Identificador**, digite uma URL neste padrão:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Na caixa **URL de Resposta**, digite uma URL neste padrão:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Caso deseje configurar o aplicativo no modo iniciado por SP, selecione **Definir URLs adicionais** e realize as seguintes etapas.

    ![Informações de logon único de Domínio e URLs do Pega Systems](common/both-advanced-urls.png)

    1. Na caixa **URL de logon**, digite o valor da URL de logon.

    1. Na caixa **Estado de Retransmissão**, digite uma URL neste padrão: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Os valores fornecidos aqui são espaços reservados. Você precisa usar o identificador, a URL de resposta, a URL de logon único e a URL de estado de retransmissão reais. Você pode obter os valores do identificador e da URL de resposta de um aplicativo Pega, conforme explicado mais adiante neste tutorial. Para obter o valor do estado de retransmissão, entre em contato com a [equipe de suporte do Pega Systems](https://www.pega.com/contact-us). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. O aplicativo Pega Systems precisa que as declarações do SAML estejam em um formato específico. Para obtê-las no formato correto, você precisa adicionar mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra os atributos padrão. Selecione o ícone **Editar** para abrir a caixa de diálogo **Atributos de usuário** :

    ![Atributos de usuário](common/edit-attribute.png)

7. Além dos atributos mostrados na captura de tela anterior, o aplicativo Pega Systems requer que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **Declarações do usuário** da caixa de diálogo **Atributos de usuário**, realize as seguintes etapas para adicionar esses atributos do token SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Esses valores são específicos para sua organização. Forneça os valores adequados.

    1. Selecione **Adicionar nova declaração** para abrir a caixa de diálogo **Gerenciar declarações do usuário**:

    ![Selecionar Adicionar nova declaração](common/new-save-attribute.png)

    ![Gerenciar a caixa de diálogo de declarações do usuário](common/new-attribute-details.png)

    1. Na caixa **Nome**, insira o nome do atributo mostrado para aquela linha.

    1. Deixe a caixa **Namespace** em branco.

    1. Em **Origem**, selecione **Atributo**.

    1. Na lista **Atributo de origem**, selecione o valor do atributo mostrado para essa linha.

    1. Selecione **Ok**.

    1. Clique em **Salvar**.

8. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione o link **Baixar** ao lado do **XML de Metadados de Federação**, de acordo com suas necessidades e salve o certificado no computador:

    ![Link de download do certificado](common/metadataxml.png)

9. Na seção **Configurar Pega Systems**, copie as URLs adequadas, de acordo com suas necessidades.

    ![Copiar as URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de Logon**.

    1. **Identificador do Azure AD**.

    1. **URL de Logoff**.

### <a name="configure-pega-systems-single-sign-on"></a>Configurar logon único do Pega Systems

1. Para configurar o logon único no lado do **Pega Systems**, entre no Portal do Pega com uma conta do administrador em outra janela do navegador.

2. Selecione **Criar** > **SysAdmin** > **Serviço de Autenticação**:

    ![Selecionar Serviço de Autenticação](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Conclua as etapas a seguir na tela **Criar Serviço de Autenticação**.

    ![Tela Criar Serviço de Autenticação](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Na lista **Tipo**, selecione **SAML 2.0**.

    1. Na caixa **Nome**, digite qualquer nome (por exemplo, **SSO do Azure AD**).

    1. Na caixa **Descrição resumida**, insira uma descrição.  

    1. Selecione **Criar e abrir**.
    
4. Na seção **Informações do IdP (Provedor de Identidade)** , selecione **Importar metadados do IdP** e procure o arquivo de metadados que você baixou do portal do Azure. Clique em **Enviar** para carregar os metadados:

    ![Seção de informações do IdP (Provedor de Identidade)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Essa importação preencherá os dados do IdP, conforme mostrado aqui:

    ![Dados do IdP importados](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Execute as seguintes etapas na seção **Configurações do SP (Provedor de Serviço)** .

    ![Configurações do provedor de serviço](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Copie o valor de **Identificação da Entidade** e cole-o na caixa **Identificador** na seção **Configuração Básica de SAML** do portal do Azure.

    1. Copie o valor **Localização do ACS (Serviço do Consumidor de Declaração)** e cole-o na caixa **URL de Resposta** da seção **Configuração Básica de SAML** do portal do Azure.

    1. Selecione **Desabilitar assinatura de solicitação**.

7. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste chamado Brenda Fernandes no portal do Azure.

1. No portal do Azure, selecione **Azure Active Directory** no painel esquerdo, **Usuários** e, em seguida, **Todos os usuários**:

    ![Selecionar Todos os usuários](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela:

    ![Selecionar Novo usuário](common/new-user.png)

3. Na caixa de diálogo **Usuário**, conclua as seguintes etapas.

    ![Caixa de diálogo Usuário](common/user-properties.png)

    a. Na caixa **Nome**, insira **BrendaFernandes**.
  
    b. Na caixa **Nome de usuário**, insira **brendafernandes@\<domíniodaempresa>.\<extensão>** . (Por exemplo, BrittaSimon@contoso.com.)

    c. Selecione **Mostrar senha** e, em seguida, anote o valor mostrado na caixa **Senha**.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela o acesso ao Pega Systems.

1. No portal do Azure, selecione **Aplicativos empresariais**, **Todos os aplicativos** e, em seguida, selecione **Pega Systems**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Pega Systems**.

    ![Lista de aplicativos](common/all-applications.png)

3. No painel esquerdo, selecione **Usuários e grupos**:

    ![Selecionar Usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Selecione Adicionar usuário](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários e, em seguida, clique no botão **Selecionar** na parte inferior da tela.

6. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-a-pega-systems-test-user"></a>Criar um usuário de teste do Pega Systems

Em seguida, você precisará criar um usuário chamado Brenda Fernandes no Pega Systems. Trabalhar com a [equipe de suporte do Pega Systems](https://www.pega.com/contact-us) para criar usuários.

### <a name="test-single-sign-on"></a>Testar logon único

Agora você precisará testar a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do Pega Systems no Painel de Acesso, você deverá ser conectado automaticamente à instância do Pega Systems para a qual você configurou o SSO. Para obter mais informações, confira [Acessar e usar aplicativos no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)