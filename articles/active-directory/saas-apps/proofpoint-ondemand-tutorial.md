---
title: 'Tutorial: integração do Azure Active Directory com o Proofpoint on Demand | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e Proofpoint on Demand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: jeedes
ms.openlocfilehash: e9beb2c9e265982a9e10f3e6abee6bc3cba4a4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648451"
---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Tutorial: integração do Azure Active Directory ao Proofpoint on Demand

Neste tutorial, você aprenderá a integrar o Proofpoint on Demand ao Azure AD (Azure Active Directory). Ao integrar o Proofpoint on Demand ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Proofpoint on Demand.
* Permitir que os usuários façam logon automaticamente no Proofpoint on Demand com as contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Proofpoint on Demand.

> [!NOTE]
> Se você estiver usando a autenticação sem senha ou MFA com o Azure AD, desative o valor AuthnContext na solicitação SAML. Caso contrário, o Azure AD vai gerar o erro na incompatibilidade de AuthnContext e não enviará o token de volta para o aplicativo.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Proofpoint on Demand dá suporte ao SSO iniciado por **SP**

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>Adicionar Proofpoint on Demand da galeria

Para configurar a integração do Proofpoint on Demand ao Azure AD, você precisa adicionar o Proofpoint on Demand da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Proofpoint on Demand** na caixa de pesquisa.
1. Selecione **Proofpoint on Demand** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-proofpoint-on-demand"></a>Configurar e testar o SSO do Azure AD para o Proofpoint on Demand

Configure e teste o SSO do Azure AD com o Proofpoint on Demand usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Proofpoint on Demand.

Para configurar e testar o SSO do Azure AD com o Proofpoint on Demand, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Proofpoint on Demand](#configure-proofpoint-on-demand-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Proofpoint on Demand](#create-proofpoint-on-demand-test-user)** – para ter um equivalente de B.Fernandes no Proofpoint on Demand que esteja vinculado à representação de usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Proofpoint on Demand**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Proofpoint on Demand](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<hostname>.pphosted.com/ppssamlsp_hostname`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://<hostname>.pphosted.com/ppssamlsp`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<hostname>.pphosted.com:portnumber/v1/samlauth/samlconsumer`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Contate a [equipe de suporte do cliente Proofpoint on Demand](https://www.proofpoint.com/us/support-services) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Proofpoint on Demand**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure, concedendo acesso ao Proofpoint on Demand.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Proofpoint on Demand**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="configure-proofpoint-on-demand-sso"></a>Configurar o SSO do Proofpoint on Demand

Para configurar o logon único no lado do **Proofpoint on Demand**, você precisará enviar o **Certificado (Base64)** baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe de suporte do Proofpoint on Demand](https://www.proofpoint.com/us/support-services). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-proofpoint-on-demand-test-user"></a>Criar um usuário de teste do Proofpoint on Demand

Nesta seção, você cria uma usuária chamada Brenda Fernandes no Proofpoint on Demand. Trabalhe com a [equipe de suporte do cliente Proofpoint on Demand](https://www.proofpoint.com/us/support-services) para adicionar usuários na plataforma Proofpoint on Demand.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Proofpoint on Demand, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Proofpoint on Demand e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Proofpoint on Demand em Meus Aplicativos, você deverá ser conectado automaticamente ao Proofpoint on Demand, para o qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Proofpoint on Demand, será possível impor o controle de sessão, que fornece proteção contra a infiltração e exfiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).