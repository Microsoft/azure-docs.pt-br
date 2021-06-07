---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Moxtra | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Moxtra.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.openlocfilehash: be63ea7ebf905b825ffe73dfc2b37dcdc3bb88fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92507492"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Moxtra

Neste tutorial, você aprenderá a integrar o Moxtra ao Azure AD (Azure Active Directory). Quando integrar o Moxtra ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Moxtra.
* Permitir que os usuários sejam conectados automaticamente ao Moxtra com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do Moxtra.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Moxtra dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-moxtra-from-the-gallery"></a>Adicionando Moxtra da galeria

Para configurar a integração do Moxtra ao Azure AD, você precisará adicionar o Moxtra da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Moxtra** na caixa de pesquisa.
1. Escolha **Moxtra** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>Configurar e testar o logon único do Azure AD para o Moxtra

Configure e teste o SSO do Azure AD com o Moxtra com um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Moxtra.

Para configurar e testar o SSO do Azure AD com o Moxtra, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Moxtra](#configure-moxtra-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criação de um usuário de teste Moxtra](#create-moxtra-test-user)** : para ter um equivalente de B.Fernandes no Moxtra que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Moxtra**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Logon**, digite uma URL: `https://www.moxtra.com/service/#login`

1. Seu aplicativo do Moxtra espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo Atributos de usuário.

    ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo Moxtra espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção Declarações de Usuário da caixa de diálogo Atributos de Usuário, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome | Atributo de Origem|
    | ------------------- | -------------------- |    
    | nome | user.givenname |
    | sobrenome | user.surname |
    | idpid    | < Identificador do Azure AD >

    > [!Note]
    > O valor do atributo **idpid** não é real. Você pode obter o valor real da seção **Configurar o Moxtra** da etapa nº 8. 

    1. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    1. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    1. Deixe o **Namespace** em branco.

    1. Escolha Origem como **Atributo**.

    1. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    1. Clique em **Ok**

    1. Clique em **Save** (Salvar).

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Moxtra**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Moxtra.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Moxtra**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-moxtra-sso"></a>Configurar o SSO do Moxtra

1. Em outra janela do navegador, entre em seu site de empresa do Moxtra como administrador.

2. Na barra de ferramentas à esquerda, clique em **Console do Administrador > Logon Único do SAML** e clique em **Novo**.
   
    ![A captura de tela mostra a página de Logon Único do SAML com a opção de criar um Logon Único do SAML.](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Na página **SAML** , execute as seguintes etapas:
   
    ![A captura de tela mostra a página do SAML em que você pode inserir os valores descritos.](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo, *SAML*). 
  
    b. Na caixa de texto **ID da Entidade de IdP**, cole o valor do **Identificador do Azure AD** que você copiou do portal do Azure. 
 
    c. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure. 
 
    d. Na caixa de texto **AuthnContextClassRef**, digite **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**. 
 
    e. Na caixa de texto **Formato da NameID**, digite **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**. 
 
    f. Abra o certificado baixado no bloco de notas do Portal do Azure, copie o conteúdo e cole-o na caixa de texto **Certificado**.    
 
    g. Na caixa de texto de domínio de email SAML email, digite seu domínio de email SAML.    
  
    >[!NOTE]
    >Para ver as etapas de verificação do domínio, clique no “**i**” abaixo.

    h. Clique em **Atualizar**.

### <a name="create-moxtra-test-user"></a>Criar um usuário de teste Moxtra

O objetivo desta seção é criar um usuário chamado B.Fernandes no Moxtra.

**Para criar um usuário chamado B.Fernandes no Moxtra, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Moxtra como administrador.

1. Na barra de ferramentas à esquerda, clique em **Console do Administrador > Gerenciamento de Usuário** e em **Adicionar Usuário**.
   
    ![A captura de tela mostra a página Gerenciamento do Usuário com a opção Adicionar Usuário selecionada.](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. No diálogo **Adicionar Usuário** , realize as seguintes etapas:
  
    a. Na caixa de texto **Nome**, digite **B**.
  
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  
    c. Na caixa de texto **Email**, digite o endereço de email de B.Fernandes no Portal do Azure.
  
    d. Na caixa de texto **Divisão**, digite **Dev**.
  
    e. Na caixa de texto **Departamento**, digite **TI**.
  
    f. Selecione **Administrator**.
  
    g. Clique em **Adicionar**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Moxtra no Painel de Acesso, você deverá ser conectado automaticamente ao Moxtra no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Avaliar o Moxtra com o Azure AD](https://aad.portal.azure.com/)