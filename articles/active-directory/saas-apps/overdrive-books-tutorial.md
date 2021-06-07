---
title: 'Tutorial: Integração do Azure Active Directory com o Overdrive | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Overdrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: 390ccfa828ebaa0d3dec15990ffeb60cbb24360e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648565"
---
# <a name="tutorial-azure-active-directory-integration-with-overdrive"></a>Tutorial: Integração do Azure Active Directory com o Overdrive

Neste tutorial, você aprenderá a integrar o Overdrive ao Azure AD (Azure Active Directory). Ao integrar o Overdrive ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Overdrive.
* Permitir que os usuários sejam conectados automaticamente ao Overdrive com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:
 
* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Overdrive habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Overdrive é compatível com o SSO iniciado por **SP**.

* O Overdrive é compatível com o provisionamento de usuário **Just-In-Time**.

## <a name="add-overdrive-from-the-gallery"></a>Adicionar o Overdrive por meio da galeria

Para configurar a integração do Overdrive ao Azure AD, adicione o Overdrive por meio da galeria à lista de aplicativos SaaS gerenciados fazendo o seguinte:
 
1. Entre no portal do Azure com uma conta corporativa ou de estudante ou uma conta pessoal Microsoft.
1. No painel esquerdo, selecione o serviço **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Overdrive** na caixa de pesquisa.
1. No painel de resultados, selecione **Overdrive** e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-overdrive"></a>Configurar e testar o SSO do Azure AD para o Overdrive

Configure e teste o SSO do Azure AD com o Overdrive usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Overdrive.

Para configurar e testar o SSO do Azure AD com o Overdrive, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Overdrive](#configure-overdrive-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Overdrive](#create-overdrive-test-user)** : para ter um equivalente de B.Fernandes no Overdrive que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Overdrive**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `http://<subdomain>.libraryreserve.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte ao cliente do Overdrive](https://help.overdrive.com/) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Overdrive**, copie a URL apropriada, de acordo com suas necessidades.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Overdrive.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **Overdrive**.

2. Na lista de aplicativos, selecione **Overdrive**.

3. No menu à esquerda, selecione **Usuários e grupos**.

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-overdrive-sso"></a>Configurar o SSO do Overdrive

Para configurar o logon único no **Overdrive**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Overdrive](https://help.overdrive.com/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-overdrive-test-user"></a>Criar um usuário de teste do Overdrive

Nesta seção, um usuário chamado Brenda Fernandes é criado no Overdrive. O Overdrive é compatível com o provisionamento de usuário Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se ainda não existir um usuário no Overdrive, ele será criado após a autenticação.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do OverDrive ou as APIs fornecidas pelo OverDrive para provisionar as contas de usuário do Azure AD.
>

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Overdrive, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Overdrive e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Overdrive em Meus Aplicativos, você será redirecionado para a URL de Logon do Overdrive. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Overdrive, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).