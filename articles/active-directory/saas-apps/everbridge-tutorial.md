---
title: 'Tutorial: Integração do Azure Active Directory ao Everbridge | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Everbridge.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 6e281931eb4646e09bb9aa3226ed7d0735c84e3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643772"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Tutorial: Integração do Azure Active Directory ao Everbridge

Neste tutorial, você aprenderá a integrar o Everbridge ao Azure AD (Azure Active Directory).
Ao integrar o Everbridge ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Everbridge.
* Permitir que seus usuários entrem automaticamente no Everbridge usando suas contas do Azure AD. Esse controle de acesso é chamado de logon único (SSO).
* Gerenciar suas contas em um local central usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Everbridge, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Everbridge que usa logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Everbridge dá suporte ao SSO iniciado por IDP.

## <a name="add-everbridge-from-the-gallery"></a>Adicionar o EverBridge por meio da galeria

Para configurar a integração do Everbridge ao Azure AD, você precisará adicionar o Everbridge da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Everbridge** na caixa de pesquisa.
1. Selecione **Everbridge** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-everbridge"></a>Configurar e testar o SSO do Azure AD para o Everbridge

Configure e teste o SSO do Azure AD com o Everbridge usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Everbridge.

Para configurar o SSO do Azure AD com o Everbridge, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Everbridge](#configure-everbridge-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Everbridge](#create-everbridge-test-user)** – para ter um equivalente de B.Fernandes no Everbridge que esteja vinculado à representação desse usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Everbridge**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

    >[!NOTE]
    >Configure o aplicativo como o portal do gerente *ou* como o portal do membro no portal do Azure e no portal do Everbridge.

4. Para configurar o aplicativo **Everbridge** como o **Portal do gerente do Everbridge**, na seção **Configuração Básica do SAML** execute as seguintes etapas:

    ![Informações de logon único do domínio e URLs do Everbridge](common/idp-intiated.png)

    a. Na caixa **Identificador**, insira uma URL que siga o padrão.
    `https://sso.everbridge.net/<API_Name>`

    b. Na caixa **URL de Resposta**, insira uma URL que siga o padrão.
    `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com os valores reais de Identificador e URL de Resposta. Para obter esses valores, entre em contato com a equipe de suporte do [Everbridge](mailto:support@everbridge.com). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Para configurar o aplicativo **Everbridge** como o **Portal de membro do Everbridge**, na seção **Configuração Básica do SAML**, siga as etapas:

  * Se você quiser configurar o aplicativo no modo iniciado pelo IDP, siga estas etapas:

     ![Domínio Everbridge e informações de logon único de URLs para o modo iniciado pelo IDP](common/idp-intiated.png)

    a. Na caixa **Identificador**, insira uma URL que siga o padrão `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Na caixa **URL de Resposta**, insira uma URL que siga o padrão `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Caso queira configurar o aplicativo no modo iniciado por SP, selecione **Definir URLs adicionais** e siga esta etapa:

     ![Domínio Everbridge e informações de logon único de URLs para o modo iniciado pelo SP](common/both-signonurl.png)

     a. Na caixa **URL de Entrada**, insira uma URL que siga o padrão `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Esses valores não são reais. Atualize esses valores com os valores do Identificador, da URL de Resposta e da URL de Logon reais. Para obter esses valores, entre em contato com a equipe de suporte do [Everbridge](mailto:support@everbridge.com). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, selecione **Download** para baixar o **XML de metadados de Federação**. Salve-o no computador.

    ![Link de download do certificado](common/metadataxml.png)

7. Na seção **Configurar o Everbridge**, copie as URLs necessárias para seus requisitos:

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo acesso ao Everbridge.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Everbridge**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="configure-everbridge-sso"></a>Configurar o SSO do Everbridge

Para configurar o SSO no **Everbridge** como um aplicativo do **portal do gerente Everbridge**, siga estas etapas.
 
1. Em uma janela do navegador da Web diferente, entre no Everbridge como administrador.

1. No menu na parte superior, selecione a guia **Configurações**. Em **Segurança**, selecione **Logon Único**.
   
     ![Configurar o logon único](./media/everbridge-tutorial/sso.png)
   
     a. Na caixa **Nome**, insira o nome do provedor do identificador. Um exemplo é o nome da sua empresa.
   
     b. Na caixa **Nome da API**, insira o nome da API.
   
     c. Selecione **Escolher Arquivo** para carregar o arquivo de metadados que você baixou do portal do Azure.
   
     d. Para **Local de Identidade do SAML**, selecione **A identidade está no elemento NameIdentifier da declaração Subject**.
   
     e. Na caixa **URL de Logon do Provedor de Identidade**, cole a **URL de Logon** que você copiou do portal do Azure.
   
     f. Para **Associação de Solicitação Iniciada do Provedor de Serviço**, selecione **Redirecionamento HTTP**.

     g. Selecione **Salvar**.

### <a name="configure-everbridge-as-everbridge-member-portal-sso"></a>Configurar o Everbridge como SSO do portal do membro Everbridge

Para configurar o logon único no **Everbridge** como um **Portal de membro do Everbridge**, envie o **XML de Metadados de Federação** baixado para a [equipe de suporte do Everbridge](mailto:support@everbridge.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-everbridge-test-user"></a>Criar usuário de teste do Everbridge

Nesta seção, você cria o usuário de teste Brenda Fernandes no Everbridge. Para adicionar usuários na plataforma Everbridge, trabalhe com a equipe de suporte do [Everbridge](mailto:support@everbridge.com). Os usuários devem ser criados e ativados no Everbridge para você poder usar o logon único. 

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você deverá ser conectado automaticamente ao Everbridge, para o qual configurou o SSO.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Everbridge em Meus Aplicativos, você deverá ser conectado automaticamente ao Everbridge, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Everbridge, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).