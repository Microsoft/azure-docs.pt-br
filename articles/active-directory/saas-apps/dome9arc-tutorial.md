---
title: 'Tutorial: Integração do Azure Active Directory ao Check Point CloudGuard Dome9 Arc | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Check Point CloudGuard Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdaaab8257d3a79130902e1ba0466f9cf15484f4
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147177"
---
# <a name="tutorial-integrate-check-point-cloudguard-dome9-arc-with-azure-active-directory"></a>Tutorial: Integrar o Check Point CloudGuard Dme9 Arc ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Check Point CloudGuard Dome9 Arc ao Azure Active Directory (Azure AD). Ao integrar o Check Point CloudGuard Dome9 Arc ao Azure AD, você pode:

* Controlar no Azure AD quem terá acesso ao Check Point CloudGuard Dome9 Arc.
* Permitir que os usuários sejam conectados automaticamente ao Check Point CloudGuard Dome9 Arc com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Check Point CloudGuard Dome9 Arc.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Check Point CloudGuard Dome9 Arc dá suporte ao SSO iniciado por **SP e IDP**.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Como adicionar o Check Point CloudGuard Dome9 Arc da galeria

Para configurar a integração do Check Point CloudGuard Dome9 Arc ao Azure AD, será necessário adicionar o Check Point CloudGuard Dome9 Arc da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Check Point CloudGuard Dome9 Arc** na caixa de pesquisa.
1. Selecione **Check Point CloudGuard Dome9 Arc** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Check Point CloudGuard Dome9 Arc usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Check Point CloudGuard Dome9 Arc.

Para configurar e testar o SSO do Azure AD com o Check Point CloudGuard Dome9 Arc, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configure o Check Point CloudGuard Dome9 Arc](#configure-check-point-cloudguard-dome9-arc)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Crie um usuário de teste do Check Point CloudGuard Dome9 Arc](#create-check-point-cloudguard-dome9-arc-test-user)** para ter um equivalente de B.Fernandes no Check Point CloudGuard Dome9 Arc vinculado à representação de usuário do Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Check Point CloudGuard Dome9 Arc**, encontre a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://secure.dome9.com/`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Você selecionará o valor do nome da empresa no portal de administração do dome9, que será explicado mais adiante no tutorial.

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao cliente do Check Point CloudGuard Dome9 Arc](mailto:Dome9@checkpoint.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. O aplicativo Check Point CloudGuard Dome9 Arc espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone  **Editar** para abrir a caixa de diálogo Atributos do usuário.

    ![image](common/edit-attribute.png)

7. Além do indicado acima, o aplicativo Check Point CloudGuard Dome9 Arc espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo: 

    | NOME |  Atributo de Origem|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

1. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para fazer o download do certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Check Point CloudGuard Dome9 Arc**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-check-point-cloudguard-dome9-arc"></a>Configurar o Check Point CloudGuard Dome9 Arc

1. Em outra janela do navegador da Web, faça logon no site da empresa do seu Check Point CloudGuard Dome9 Arc como administrador.

2. Clique nas **Configurações de Perfil** no canto superior direito e depois em **Configurações de Conta**. 

    ![Configuração do Check Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure1.png)

3. Navegue até **SSO** e, em seguida, clique em **HABILITAR**.

    ![Configuração do Check Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure2.png)

4. Na seção de Configuração de SSO, execute as seguintes etapas:

    ![Configuração do Check Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure3.png)

    a. Digite o nome da empresa na caixa de texto **ID da Conta**. Esse valor deve ser usado na URL de Resposta mencionada na seção **Configuração básica de SAML** do portal do Azure.

    b. Na caixa de texto **Emissor**, cole o valor de **Identificador do Azure AD** que você copiou do portal do Azure.

    c. Na caixa de texto **URL do ponto de extremidade IDP**, cole o valor de **URL de Logon** que você copiou do portal do Azure.

    d. Abra seu certificado codificado com Base64 baixado no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado X.509**.

    e. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Check Point CloudGuard Dome9 Arc.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Check Point CloudGuard Dome9 Arc**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Criar o usuário de teste do Check Point CloudGuard Dome9 Arc

Para permitir que os usuários do Azure AD entrem no Check Point CloudGuard Dome9 Arc, eles devem ser provisionados no aplicativo. O Check Point CloudGuard Dome9 Arc é compatível com provisionamento just-in-time, mas, para que ele funcione corretamente, o usuário deve selecionar uma **Função** específica e atribuir o mesmo ao usuário.

   >[!Note]
   >Para saber sobre a criação da **Função** e outros detalhes, contate a [equipe de suporte ao cliente do Check Point CloudGuard Dome9 Arc](mailto:Dome9@checkpoint.com).

**Para provisionar uma conta de usuário manualmente, execute as seguintes etapas:**

1. Entre no site da empresa do seu Check Point CloudGuard Dome9 Arc como administrador.

2. Clique em **Usuários e Funções** e, em seguida, clique em **Usuários**.

    ![Adicionar Funcionário](./media/dome9arc-tutorial/user1.png)

3. Clique em **ADICIONAR USUÁRIO**.

    ![Adicionar Funcionário](./media/dome9arc-tutorial/user2.png)

4. Na seção **Criar Usuário** , realize as seguintes etapas:

    ![Adicionar Funcionário](./media/dome9arc-tutorial/user3.png)

    a. Na caixa de texto **Email**, digite o email do usuário como B.Simon@contoso.com.

    b. Na caixa de texto **Nome**, digite o nome do usuário, como Brenda.

    c. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como Fernandes.

    d. Faça o **Usuário do SSO** como **On**.

    e. Clique em **CRIAR**.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco Check Point CloudGuard Dome9 Arc no Painel de Acesso, você deve ser conectado automaticamente ao Check Point CloudGuard Dome9 Arc para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)