---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Cornerstone OnDemand | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cornerstone OnDemand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 0994126c4e31d880a792904b0c086a56f6bd90fb
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98727702"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-ondemand"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Cornerstone OnDemand

Neste tutorial, você aprenderá a integrar o Cornerstone OnDemand ao Azure Active Directory (Azure AD). Ao integrar o Cornerstone OnDemand ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Cornerstone OnDemand.
* Permitir que os usuários façam logon automaticamente no Cornerstone OnDemand com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Cornerstone OnDemand.

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Cornerstone OnDemand dá suporte a SSO iniciado por **SP**
* O Cornerstone OnDemand dá suporte ao [provisionamento automatizado de usuários](cornerstone-ondemand-provisioning-tutorial.md)


## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Adicionando Cornerstone OnDemand da galeria

Para configurar a integração do Cornerstone OnDemand ao Azure AD, você precisa adicionar o Cornerstone OnDemand da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da por meio da galeria**, digite **Cornerstone OnDemand** na caixa de pesquisa.
1. Selecione **Cornerstone OnDemand** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-cornerstone-ondemand"></a>Configurar e testar o SSO do Azure AD para o Cornerstone OnDemand

Configure e teste o SSO do Azure AD com o Cornerstone OnDemand usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cornerstone OnDemand.

Para configurar e testar o SSO do Azure AD com o Cornerstone OnDemand, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Cornerstone OnDemand](#configure-cornerstone-ondemand-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Cornerstone OnDemand](#create-cornerstone-ondemand-test-user)** – para ter um equivalente de B.Fernandes no Cornerstone OnDemand vinculado à representação de usuário do Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Cornerstone OnDemand**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company>.csod.com/samldefault.aspx?ouid=2`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<company>.csod.com/samldefault.aspx?ouid=2`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte ao cliente do Cornerstone OnDemand](mailto:moreinfo@csod.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Cornerstone OnDemand**, copie a URL apropriada conforme seus requisitos.

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

Nesta seção, você permite que B.Fernandes use o logon único do Azure concedendo acesso ao Cornerstone OnDemand.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Cornerstone OnDemand**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-cornerstone-ondemand-sso"></a>Configurar SSO do Cornerstone OnDemand

Para configurar o logon único no lado do **Cornerstone OnDemand** é necessário enviar as URLS copiadas apropriadas e o **Certificado (Base64)** que você baixou do portal do Azure para a [equipe de suporte do Cornerstone OnDemand](mailto:moreinfo@csod.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-cornerstone-ondemand-test-user"></a>Criar um usuário de teste do Cornerstone OnDemand

O objetivo desta seção é criar um usuário chamado B.Fernandes no Cornerstone OnDemand. O Cornerstone OnDemand dá suporte ao provisionamento automático do usuário, que está habilitado por padrão. Você pode encontrar mais detalhes [ aqui ](./cornerstone-ondemand-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuários.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

Para configurar o provisionamento de usuário, envie as informações (por exemplo: Nome, Email) sobre o usuário do Azure AD que você deseja provisionar para a [equipe de suporte do Cornerstone OnDemand](mailto:moreinfo@csod.com).

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Cornerstone OnDemand ou APIs fornecidas pelo Cornerstone OnDemand para provisionar as contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Cornerstone OnDemand, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Cornerstone OnDemand e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clica no bloco do Cornerstone OnDemand nos Meus Aplicativos, isso redireciona você para a URL de logon do Cornerstone OnDemand. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o Cornerstone OnDemand, você poderá impor controles de sessão, o que protege contra o vazamento e a infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)