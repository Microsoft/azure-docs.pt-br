---
title: 'Tutorial: integração do Azure Active Directory ao FreshDesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o FreshDesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: e1394eafdfd733b5d69a4d4abbb6b218b4c8c10d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651897"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: integração do Azure Active Directory ao FreshDesk

Neste tutorial, você aprenderá a integrar o FreshDesk ao Azure AD (Azure Active Directory). Ao integrar o FreshDesk ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao FreshDesk.
* Permitir que os usuários sejam conectados automaticamente ao FreshDesk com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:
 
* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do FreshDesk habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O FreshDesk dá suporte ao SSO iniciado por **SP**

## <a name="add-freshdesk-from-the-gallery"></a>Adicionar o FreshDesk por meio da galeria

Para configurar a integração do FreshDesk ao Azure AD, você precisa adicionar o FreshDesk da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **FreshDesk** na caixa de pesquisa.
1. Escolha **FreshDesk** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>Configurar e testar o SSO do Azure AD com o FreshDesk

Configure e teste o SSO do Azure AD com o FreshDesk usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do FreshDesk.

Para configurar e testar o SSO do Azure AD com o FreshDesk, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do FreshDesk](#configure-freshdesk-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do FreshDesk](#create-freshdesk-test-user)** – para ter um equivalente de Brenda Fernandes no FreshDesk que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

1. No portal do Azure, na página de integração de aplicativos do **FreshDesk**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de lápis de **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o padrão `https://<tenant-name>.freshdesk.com` ou qualquer outro valor sugerido pelo FreshDesk.

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o padrão `https://<tenant-name>.freshdesk.com` ou qualquer outro valor sugerido pelo FreshDesk.
     
    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<tenant-name>.freshdesk.com/login/saml`
    
    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon reais. Entre em contato com [equipe de suporte do cliente FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo FreshDesk espera as asserções de SAML em um formato específico, que exige a inclusão de mapeamentos de atributos personalizados na configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão em que **Identificador de Usuário Único** é mapeado com **user.userprincipalname**, mas o FreshDesk espera que essa declaração seja mapeada com **user.mail**, de modo que você precisa editar o mapeamento de atributos clicando no ícone Editar para então alterá-lo.

    ![image](common/edit-attribute.png)

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o FreshDesk**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao FreshDesk.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **FreshDesk**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name=&quot;configure-freshdesk-sso&quot;></a>Configurar o SSO do FreshDesk

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Freshdesk como administrador.

2. Selecione o **Ícone de Segurança** e, na seção **Segurança**, realize as seguintes etapas:

    ![Logon único](./media/freshdesk-tutorial/configure-1.png &quot;Logon Único")
  
    a. Para **Logon Único**, selecione **Ativado**.

    b. No **Método de Logon**, selecione **SSO SAML**.

    c. Na caixa de texto **ID da Entidade fornecida pelo IdP**, cole o valor da **ID da Entidade** copiado do portal do Azure.

    d. Na caixa de texto **URL de SSO SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Nas **Opções de Assinatura**, selecione **Apenas Asserções Assinadas** na lista suspensa.

    f. Na caixa de texto **URL de logoff**, cole o **valor do URL de logout**, copiado do portal do Azure.

    g. Na caixa de texto **Certificado de Segurança**, cole o valor **Certificado (Base64)** que você obteve anteriormente.
  
    h. Clique em **Save** (Salvar).

## <a name="create-freshdesk-test-user"></a>Criar um usuário de teste do FreshDesk

Para permitir que os usuários do Azure AD façam logon no FreshDesk, eles devem ser provisionados no FreshDesk.  
No caso do FreshDesk, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Freshdesk** .

1. No menu à esquerda, clique em **Administração** e, na guia **Configurações Gerais**, clique em **Agentes**.
  
    ![Agentes](./media/freshdesk-tutorial/create-user-1.png "Agentes")

1. Clique em **Novo Agente**.

    ![Novo Agente](./media/freshdesk-tutorial/create-user-2.png "Novo Agente")

1. Na caixa de diálogo de Informações do Agente, insira os dados nos campos obrigatórios e clique em **Criar agente**.

    ![Informações sobre o Agente](./media/freshdesk-tutorial/create-user-3.png "Informações sobre o Agente")

    >[!NOTE]
    >O titular da conta do Azure AD receberá um email que inclui um link para confirmar a conta antes que ela se torne ativa.
    >
    >[!NOTE]
    >É possível usar qualquer outra ferramenta de criação da conta de usuário do Freshdesk ou APIs fornecidas pelo Freshdesk para provisionar as contas de usuário do Azure AD para o FreshDesk.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do FreshDesk, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do FreshDesk e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do FreshDesk em Meus Aplicativos, você será conectado automaticamente ao FreshDesk, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o FreshDesk, você poderá impor o controle de sessão, que protege contra a exportação e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).