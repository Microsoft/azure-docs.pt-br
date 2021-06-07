---
title: 'Tutorial: Integração do Azure Active Directory ao moconavi | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o moconavi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: f4ddb41528357d2f1b5c9e671ec2dd1308f9bf11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516811"
---
# <a name="tutorial-azure-active-directory-integration-with-moconavi"></a>Tutorial: Integração do Azure Active Directory ao moconavi

Neste tutorial, você aprenderá a integrar o moconavi ao Azure AD (Azure Active Directory).
A integração do moconavi ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao moconavi.
* Você pode permitir que os usuários sejam conectados automaticamente ao moconavi (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD com o moconavi, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do moconavi

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O moconavi é compatível com o SSO iniciado por **SP**

## <a name="adding-moconavi-from-the-gallery"></a>Adicionando o moconavi da Galeria

Para configurar a integração do moconavi ao Azure AD, você precisará adicionar o moconavi da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o moconavi da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **moconavi**, selecione **moconavi** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![moconavi na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o moconavi, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do moconavi.

Para configurar e testar o logon único do Azure AD com o moconavi, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do moconavi](#configure-moconavi-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do moconavi](#create-moconavi-test-user)** – para ter um equivalente de Brenda Fernandes no moconavi que esteja vinculado à representação de usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o moconavi, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **moconavi**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do moconavi](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<yourserverurl>/moconavi-saml2/saml/login`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://<yourserverurl>/moconavi-saml2`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<yourserverurl>/moconavi-saml2/saml/SSO`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do moconavi](mailto:support@recomot.co.jp) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o moconavi**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-moconavi-single-sign-on"></a>Configurar o logon único do moconavi

Para configurar o logon único no lado do **moconavi**, é necessário enviar o **XML de metadados de federação** transferido por download e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do moconavi](mailto:support@recomot.co.jp). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

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

Nesta seção, você permitirá que Britta Simon use o logon único do Azure concedendo a ela acesso ao moconavi.

1. No portal do Azure, selecione **Aplicativos empresariais**, **Todos os aplicativos** e, em seguida, **moconavi**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **moconavi**.

    ![O link do moconavi na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-moconavi-test-user"></a>Criar usuário de teste do moconavi

Nesta seção, você criará uma usuária chamada Britta Simon no moconavi. Trabalhe com a [equipe de suporte do moconavi](mailto:support@recomot.co.jp) para adicionar os usuários na plataforma do moconavi. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único 

1. Instale o moconavi da Microsoft Store.

2. Inicie o moconavi.

3. Clique no botão **Configuração de conexão**.

    ![A captura de tela mostra moconavi com o botão de configuração Conexão.](./media/moconavi-tutorial/testing1.png)

4. Digite `https://mcs-admin.moconavi.biz/gateway` na caixa de texto **Conectar-se à URL** e clique no botão **Concluído**.

    ![A captura de tela mostra a caixa Conectar-se à URL e o botão Concluído.](./media/moconavi-tutorial/testing2.png)

5. Na captura de tela a seguir, execute as seguintes etapas:

    ![A captura de tela mostra a página moconavi em que você pode inserir os valores descritos.](./media/moconavi-tutorial/testing3.png)

    a. Digite **Chave de Autenticação de Entrada**:`azureAD` na caixa de texto **Chave de Autenticação de Entrada**.

    b. Digite **ID de Usuário de Entrada**: `your ad account` na caixa de texto **ID de Usuário de Entrada**.

    c. Clique em **LOGON**.

6. Insira sua senha do Azure AD n caixa de texto **Senha** e clique no botão **Logon**.

    ![A captura de tela mostra onde inserir sua senha do Azure AD.](./media/moconavi-tutorial/testing4.png)

7. A autenticação do Azure AD tem êxito quando o menu é exibido.

    ![A captura de tela mostra o ícone de telefone no moconavi.](./media/moconavi-tutorial/testing5.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)