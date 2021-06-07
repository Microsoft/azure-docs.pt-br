---
title: 'Tutorial: Integração do Azure Active Directory ao Costpoint | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Costpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 781d1509b69ecb09189772f4a31d4872201d0b2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652922"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Tutorial: Integrar o Costpoint com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Costpoint ao Azure AD (Azure AD). Quando você integra o Costpoint com o Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao Costpoint.
* Permitir que seus usuários se conectem automaticamente ao Costpoint usando as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do Costpoint.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 

* O Costpoint dá suporte ao SSO iniciado por **SP e IDP**.

## <a name="generate-costpoint-metadata"></a>Gerar metadados do Costpoint

A configuração de SSO do SAML do Costpoint é explicada no guia **DeltekCostpoint711Security.pdf**. Baixe este guia do site de suporte do Deltek Costpoint e consulte a seção **Instalação de Logon Único do SAML** > **Configurar Logon Único do SAML entre o Costpoint e o Microsoft Azure**. Siga as instruções e gere um arquivo **XML de Metadados de Federação do Costpoint SP**. 

![Captura de tela que mostra o "Utilitário de Configuração do Produto" com a guia "WebLogic – Segurança" selecionada.](./media/costpoint-tutorial/configuration-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Adicionar o Costpoint da galeria

Para configurar a integração do Costpoint ao Azure AD, você precisa adicionar o Costpoint da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Costpoint** na caixa de pesquisa.
1. Selecione **Costpoint** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-costpoint"></a>Configurar e testar o SSO do Azure AD para o Costpoint

Configure e teste o SSO do Azure AD com o Costpoint usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Costpoint.

Para configurar e testar o SSO do Azure AD com o Costpoint, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Costpoint](#configure-costpoint-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Costpoint](#create-costpoint-test-user)** : para ter um equivalente de B.Fernandes no Costpoint que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure:

1. Na página de integração do aplicativo **Costpoint**, selecione **Logon único**.

1. Na seção **Configuração básica do SAML**, se você tiver o **arquivo de metadados do provedor de serviços**, conclua estas etapas:

   > [!NOTE]
   > Você obtém o arquivo de metadados do provedor de serviços em [Gerar metadados do Costpoint](#generate-costpoint-metadata). Como usar o arquivo é explicado posteriormente no tutorial.
 
   1. Selecione o botão **Fazer upload do arquivo de metadados**, em seguida, selecione o arquivo **XML de metadados de federação do Costpoint SP** gerado anteriormente pelo Costpoint e, em seguida, selecione o botão **Adicionar** para fazer upload do arquivo.

      ![Fazer upload do arquivo de metadados](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão populados automaticamente na seção do Costpoint.

      > [!NOTE]
      > Se os valores de **Identificador** e **URL de resposta** não forem populados automaticamente, insira-os manualmente de acordo com seus requisitos. Verifique se **ID (Identificador) da entidade** e **URL de Resposta (URL do Serviço do Consumidor de Asserção)** estão definidos corretamente e se a **URL do ACS** é uma URL válida do Costpoint que termina com **/LoginServlet.cps**.

   1. Selecione **Definir URLs adicionais**. Para **Estado de Retransmissão**, insira um valor usando o seguinte padrão: `system=[your system]` (por exemplo, **system = DELTEKCP**).

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione o ícone **Copiar** para a **URL de metadados de federação de aplicativos** e salve-a no Bloco de Notas.

   ![Certificado de assinatura de SAML](common/copy-metadataurl.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Costpoint.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Costpoint**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-costpoint-sso"></a>Configurar o SSO do Costpoint

1. Retorne ao Utilitário de Configuração do Costpoint. Na caixa de texto **XML de metadados de Federação IdP**, cole o conteúdo do arquivo *URL de Metadados de Federação*. 

   ![Utilitário de Configuração do Costpoint](./media/costpoint-tutorial/configuration-utility-metadata.png)

1. Continue seguindo as instruções do guia **DeltekCostpoint711Security.pdf** para concluir a instalação do SAML do Costpoint.

### <a name="create-costpoint-test-user"></a>Criar usuário de teste do Costpoint

Nesta seção, você criará um usuário no Costpoint. Suponha que a ID de usuário seja **B.FERNANDES** e o nome do usuário seja **B.Fernandes**. Trabalhe com a [equipe de suporte ao Cliente do Costpoint](https://www.deltek.com/about/contact-us) para adicionar o usuário à plataforma Costpoint. O usuário deve ser criado e ativado antes que possa usar o logon único.

Depois que o usuário é criado, a seleção de **Método de Autenticação** do usuário deve ser **Active Directory**, a caixa de seleção **Logon Único do SAML** deve ser selecionada e o nome de usuário do Azure Active Directory precisa ser **Active Directory ou ID do Certificado** (mostrado na captura de tela abaixo).

![Usuário do Costpoint](./media/costpoint-tutorial/costpoint-user.png)

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado à URL de Logon do Costpoint, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Costpoint e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no Costpoint, para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Costpoint, em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você entrará automaticamente no Costpoint, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Costpoint, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).