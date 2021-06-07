---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SSO do Prisma Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO do Prisma Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: d92ca345b0800523d86ce7b42220209a1f237103
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651386"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-prisma-cloud-sso"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o SSO do Prisma Cloud

Neste tutorial, você aprenderá a integrar o SSO do Prisma Cloud ao Azure Active Directory (Azure AD). Ao integrar o SSO do Prisma Cloud ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao SSO do Prisma Cloud.
* Permitir que os usuários sejam conectados automaticamente ao SSO do Prisma Cloud com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Prisma Cloud.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SSO do Prisma Cloud é compatível com SSO iniciado por **IDP**.

* O SSO do Prisma Cloud é compatível com provisionamento de usuário **Just-In-Time**.

## <a name="add-prisma-cloud-sso-from-the-gallery"></a>Adicionar o SSO do Prisma Cloud por meio da galeria

Para configurar a integração do SSO do Prisma Cloud ao Azure AD, você precisa adicionar o SSO do Prisma Cloud por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **SSO do Prisma Cloud** na caixa de pesquisa.
1. Selecione **SSO do Prisma Cloud** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-prisma-cloud-sso"></a>Configurar e testar o SSO do Azure AD para o SSO do Prisma Cloud

Configure e teste o SSO do Azure AD com o SSO do Prisma Cloud usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SSO do Prisma Cloud.

Para configurar e testar o SSO do Azure AD com o SSO do Prisma Cloud, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Prisma Cloud](#configure-prisma-cloud-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do SSO do Prisma Cloud](#create-prisma-cloud-sso-test-user)** – para ter um equivalente de B.Fernandes no SSO do Prisma Cloud que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **SSO do Prisma Cloud**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://app2.prismacloud.io/customer/<CUSTOMERID>`

    b. Os valores de **URL de Resposta** são fixos e já preenchidos previamente no portal do Azure. Você precisa selecionar a URL apropriada de acordo com seu requisito.

    > [!NOTE]
    > O valor do Identificador não é real. Atualize o valor com o identificador real. Contate a [equipe de suporte ao cliente do SSO do Prisma Cloud](mailto:support@paloaltonetworks.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o SSO do Prisma Cloud**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo-lhe acesso ao SSO do Prisma Cloud.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SSO do Prisma Cloud**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-prisma-cloud-sso"></a>Configurar SSO de nuvem do Prisma Cloud

Para configurar o logon único no lado do **SSO do Prisma Cloud**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do SSO do Prisma Cloud](mailto:support@paloaltonetworks.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-prisma-cloud-sso-test-user"></a>Criar usuário de teste do SSO do Prisma Cloud

Nesta seção, um usuário chamado B.Fernandes será criado no SSO do Prisma Cloud. O SSO do Prisma Cloud é compatível com o provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no SSO do Prisma Cloud, um será criado quando você tentar acessar o SSO do Prisma Cloud.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você será conectado automaticamente ao SSO do Prisma Cloud para o qual o SSO foi configurado.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco SSO do Prisma Cloud em Meus Aplicativos, você deverá ser conectado automaticamente ao SSO do Prisma Cloud no qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o SSO do Prisma Cloud, você poderá impor um controle de sessão, que protegerá contra a exfiltração e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).