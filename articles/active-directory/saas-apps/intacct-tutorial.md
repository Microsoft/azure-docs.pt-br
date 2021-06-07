---
title: 'Tutorial: Integração do Azure Active Directory ao Sage Intacct | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Sage Intacct.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 5a216e39ca32b16de405c7924d08da52c6eae4c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736944"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Tutorial: Integrar o Sage Intacct ao Azure Active Directory

Neste tutorial, você aprenderá como integrar o Sage Intacct ao Azure AD (Azure Active Directory). Com a integração do Sage Intacct ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Sage Intacct.
* Permitir que os usuários sejam conectados automaticamente ao Sage Intacct com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Sage Intacct habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Sage Intacct é compatível com SSO iniciado por **IDP**

## <a name="adding-sage-intacct-from-the-gallery"></a>Adicionar o Sage Intacct da galeria

Para configurar a integração do Sage Intacct ao Azure AD, você precisa adicionar o Sage Intacct por meio da galeria à sua lista de aplicativos de SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Sage Intacct** na caixa de pesquisa.
1. Selecione **Sage Intacct** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-sage-intacct"></a>Configurar e testar o SSO do Azure AD para o Sage Intacct

Configure e teste o SSO do Azure AD com o Sage Intacct usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Sage Intacct.

Para configurar e testar o SSO do Azure AD com o Sage Intacct, conclua as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
2. **[Configurar o SSO do Sage Intacct](#configure-sage-intacct-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Sage Intacct](#create-sage-intacct-test-user)** – para que haja um equivalente de B. Fernandes no Sage Intacct que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Sage Intacct**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL: `https://www.intacct.com/ia/acct/sso_response.phtml`

1. Seu aplicativo Sage Intacct espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados para sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo Atributos de Usuário.

    ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo Sage Intacct espera que mais alguns atributos sejam passados novamente na resposta SAML. Na caixa de diálogo **Declarações e Atributos do Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome do atributo  |  Atributo de Origem|
    | ---------------| --------------- |
    | Nome da empresa | **ID da empresa do Sage Intacct** |
    | name | O valor deve ser o mesmo que a **ID de Usuário** do Sage Intacct, que você insere na seção **Criar usuário de teste do Sage Intacct**, que é explicada posteriormente no tutorial |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite ou selecione o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Sage Intacct**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Sage Intacct.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Sage Intacct**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name=&quot;configure-sage-intacct-sso&quot;></a>Configurar o SSO do Sage Intacct

1. Em outra janela do navegador da Web, entre em seu site da empresa do Sage Intacct como administrador.

1. Clique na guia **Empresa** e em **Informações da Empresa**.

    ![Empresa](./media/intacct-tutorial/ic790037.png &quot;Empresa")

1. Clique na guia **Segurança** e em **Editar**.

    ![Segurança](./media/intacct-tutorial/ic790038.png "Segurança")

1. Na seção **SSO (logon único)** , realize as seguintes etapas:

    ![Logon único](./media/intacct-tutorial/ic790039.png "logon único")

    a. Selecione **Habilitar logon único**.

    b. Para **Tipo de provedor de identidade**, selecione **SAML 2.0**.

    c. Na caixa de texto **URL do Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    d. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Abra seu certificado codificado em **Base 64** no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa **Certificado**.

    f. Clique em **Save** (Salvar).

### <a name="create-sage-intacct-test-user"></a>Criar um usuário de teste do Sage Intacct

Para configurar os usuários do Azure AD para que possam entrar no Sage Intacct, eles devem ser provisionados no Sage Intacct. Para o Sage Intacct, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Entre em seu locatário do **Sage Intacct**.

1. Clique na guia **Empresa** e em **Usuários**.

    ![Usuários](./media/intacct-tutorial/ic790041.png "Usuários")

1. Clique na guia **Adicionar**.

    ![Adicionar](./media/intacct-tutorial/ic790042.png "Adicionar")

1. Na seção **Informações do Usuário** , realize as seguintes etapas:

    ![A captura de tela mostra a seção Informações do usuário em que você pode inserir as informações nesta etapa.](./media/intacct-tutorial/ic790043.png "Informações do Usuário")

    a. Insira a **ID de Usuário**, o **Sobrenome**, o **Nome**, o **Endereço de email**, o **Título** e o **Telefone** de uma conta do Azure AD que você deseja provisionar na seção **Informações do Usuário**.

    > [!NOTE]
    > Verifique se a **ID de Usuário** na captura de tela acima e o valor do **Atributo de Origem** que está mapeado para o atributo **nome** na seção **Atributos do Usuário** no portal do Azure são os mesmos.

    b. Selecione os **privilégios de Administrador** de uma conta do Azure AD que você deseja provisionar.

    c. Clique em **Save** (Salvar). 
    
    d. O titular da conta do Azure AD receberá um email e um link para confirmar sua conta antes de se tornar ativo.

1. Clique na guia **Logon único** e verifique se a **ID de usuário do SSO Federado** na captura de tela abaixo e o valor do **Atributo de Origem** mapeado para o `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` na seção **Atributos do Usuário** no portal do Azure são os mesmos.

    ![A captura de tela mostra a seção Informações do Usuário em que você pode inserir a ID do usuário de SSO Federado.](./media/intacct-tutorial/ic790044.png "Informações do Usuário")

> [!NOTE]
> Para provisionar as contas de usuário do Azure AD, é possível usar qualquer outra ferramenta de criação da conta de usuário do Sage Intacct ou APIs fornecidas pelo Sage Intacct.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você será conectado automaticamente ao Sage Intacct, para o qual configurou o SSO

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Sage Intacct em Meus Aplicativos, você será conectado automaticamente ao aplicativo Sage Intacct, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Sage Intacct, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).