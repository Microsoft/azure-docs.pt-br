---
title: 'Tutorial: Integração do Azure Active Directory ao LinkedIn Learning | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ff3748213d8449704f7b1001469eb8f7a224498
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098064"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Tutorial: Integração do Azure Active Directory ao LinkedIn Learning

Neste tutorial, você aprenderá a integrar o LinkedIn Learning ao Azure AD (Azure Active Directory).
A integração do LinkedIn Learning com o Azure AD oferece os seguintes benefícios:

* É possível controlar, no Azure AD, quem tem acesso ao LinkedIn Learning.
* Você pode permitir que seus usuários façam logon automaticamente no LinkedIn Learning (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o LinkedIn Learning, serão necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do LinkedIn Learning habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O LinkedIn Learning é compatível com SSO iniciado por **SP e IDP**
* O LinkedIn Learning dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-linkedin-learning-from-the-gallery"></a>Adicionando o LinkedIn Learning da galeria

Para configurar a integração do LinkedIn Learning com o Azure AD, é necessário adicionar o LinkedIn Learning da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o LinkedIn Learning da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **LinkedIn Learning**, selecione **LinkedIn Learning** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![LinkedIn Learning na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o LinkedIn Learning, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do LinkedIn Learning.

Para configurar e testar o logon único do Azure AD com o LinkedIn Learning, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do LinkedIn Learning](#configure-linkedin-learning-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criando um usuário de teste do LinkedIn Learning](#create-linkedin-learning-test-user)** – para ter um equivalente de Brenda Fernandes no LinkedIn Learning que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o LinkedIn Learning, siga as etapas abaixo:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativo **LinkedIn Learning**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **Iniciado por IdP**:

    ![Informações de logon único em Domínio e URLs do LinkedIn Learning](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, insira a **ID da Entidade** copiada do Portal do LinkedIn. 

    b. Na caixa de texto **URL de resposta**, insira o **URL do serviço de consumidor de afirmações (ACS)** copiado do portal do LinkedIn.

    c. Se você quiser configurar o aplicativo no modo **iniciado por SP**, clique na opção **Definir URLs adicionais** na seção **Configuração Básica do SAML** em que você especificará a URL de logon. Para criar sua URL de login, copie o **URL do Serviço de consumidor de afirmações (ACS)** e substitua / saml / por / login /. Depois disso, o URL de conexão deve ter o seguinte padrão:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Informações de logon único em Domínio e URLs do LinkedIn Learning](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Esses valores não são reais. Você atualizará esses valores com o identificador e a URL de resposta reais, a qual é explicada mais adiante na seção **Configurar Logon Único do LinkedIn Learning** do tutorial.

5. Seu aplicativo LinkedIn Learning espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributos personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo LinkedIn Learning espera que **nameidentifier** seja mapeado com **user.mail**. Portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

6. Além do indicado acima, o aplicativo LinkedIn Learning espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:
    
    | NOME | Atributo de Origem |
    | ---------------| --------------- |
    | email  | user.mail  |
    | department  | user.department  |
    | nome  | user.givenname  |
    | sobrenome  | user.surname  |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

7. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

### <a name="configure-linkedin-learning-single-sign-on"></a>Configurar o logon único do LinkedIn Learning

1. Em uma janela diferente do navegador da Web, faça logon em seu locatário do LinkedIn Learning como administrador.

2. Em **Centro de Contas**, clique em **Configurações Globais** em **Configurações**. Além disso, selecione **Learning – Padrão** na lista suspensa.

    ![Configurar o logon único](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Clique em **OU clique aqui para carregar e copiar campos individuais do formulário** e copie a **ID da Entidade** e a **URL do ACS (Serviço do Consumidor de Declaração)** e cole-as na seção **Configuração Básica do SAML** no portal do Azure.

    ![Configurar o logon único](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Vá para a seção **Configurações de administração do LinkedIn**. Carregue o arquivo XML que você baixou do Portal do Azure clicando na opção **Carregar o arquivo XML**.

    ![Configurar o logon único](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Clique em **Ativar** para habilitar o SSO. O status do SSO é alterado de **Não Conectado** para **Conectado**

    ![Configurar o logon único](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure ao conceder acesso ao LinkedIn Learning.

1. No portal do Azure, escolha **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **LinkedIn Learning**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **LinkedIn Learning**.

    ![O link do LinkedIn Learning na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-linkedin-learning-test-user"></a>Criar um usuário de teste do LinkedIn Learning

O aplicativo LinkedIn Learning dá suporte ao provisionamento de usuário just in time e, após a autenticação, os usuários serão criados automaticamente no aplicativo. Na página de configurações de administração no portal do LinkedIn Learning, coloque a opção **Atribuir licenças automaticamente** na posição de ativar o provisionamento just in time, o que também atribuirá uma licença ao usuário.

   ![Criação de um usuário de teste do AD do Azure](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do LinkedIn Learning no Painel de Acesso, você deverá ser conectado automaticamente ao LinkedIn Learning no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

