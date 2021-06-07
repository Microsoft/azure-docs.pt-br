---
title: 'Tutorial: Integração do Azure Active Directory ao Atlassian Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Atlassian Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: fe83a012cac68f705bc9fabc7748f5a7c7c61bbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94330491"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Tutorial: Integração do Atlassian Cloud com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Atlassian Cloud ao Azure Active Directory (Azure AD). Ao integrar o Atlassian Cloud com o Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao Atlassian Cloud.
* Permitir que os usuários sejam conectados automaticamente ao Atlassian Cloud com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura do Atlassian Cloud habilitada para logon único (SSO).
* Para habilitar logon único do SAML (Security Assertion Markup Language) para produtos Atlassian Cloud, é necessário configurar o Atlassian Access. Saiba mais sobre o [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 

* O Atlassian Cloud é compatível com SSO iniciado por **SP e IDP**
* O Atlassian Cloud é compatível com [provisionamento e desprovisionamento de usuário automatizados](atlassian-cloud-provisioning-tutorial.md)

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Adição do Atlassian Cloud da galeria

Para configurar a integração do Atlassian Cloud ao Azure AD, você precisará adicionar o Atlassian Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Atlassian Cloud** na caixa de pesquisa.
1. Selecione **Atlassian Cloud** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Configure e teste o SSO do Azure AD com o Atlassian Cloud usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Atlassian Cloud.

Para configurar e testar o SSO do Azure AD com o Atlassian Cloud, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD com o Atlassian Cloud](#configure-azure-ad-sso)** – para permitir que os usuários usem o SSO do SAML baseado no Azure AD com o Atlassian Cloud.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Criar um usuário de teste do Atlassian Cloud](#create-atlassian-cloud-test-user)** – para ter um equivalente de B.Fernandes no Atlassian Cloud que esteja vinculado à sua representação no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Para automatizar a configuração no Atlassian Cloud, você precisará instalar a **extensão de navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o Atlassian Cloud** para ser direcionado ao aplicativo Atlassian Cloud. No aplicativo, forneça as credenciais de administrador para entrar no Atlassian Cloud. A extensão do navegador configurará o aplicativo para você automaticamente.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o Atlassian Cloud manualmente, entre no seu site de empresa do Atlassian Cloud como administrador e execute as etapas a seguir.

1. Antes de começar, vá para a sua instância de produto do Atlassian e copie/salve a URL da Instância
   > [!NOTE]
   > A URL deve se ajustar ao padrão `https://<instancename>.atlassian.net`

   ![nome da instância](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. Abra o [Portal de Administração do Atlassian](https://admin.atlassian.com/) e clique no nome da sua organização

   ![organization](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. É necessário verificar o domínio antes de configurar logon único. Para obter mais informações, consulte o documento de [verificação de domínio do Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).
1. Na Tela do Portal de Administração do Atlassian, selecione **Segurança** na gaveta esquerda

   ![segurança](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. Na Tela de Segurança do Portal de Administração do Atlassian, selecione **Logon único do SAML** na gaveta esquerda

   ![sso do saml](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. Clique em **Adicionar Configuração do SAML** e mantenha a página aberta

   ![Adicionar Configuração do SAML](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![Adicionar Configuração do SAML 2](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. No portal do Azure, na página de integração de aplicativos do **Atlassian Cloud**, localize a seção **Gerenciar** e selecione **Configurar logon único**.

   ![configurar sso](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. Na página **Escolher um método de logon único**, escolha **SAML**.

   ![saml no azure](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. Na página **Configurar logon único com SAML**, role para baixo até **Configurar Atlassian Cloud**
   
   a. Clique em **URLs de Configuração**

   ![urls](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. Copie o valor **Identificador do Azure AD** do portal do Azure, cole-o na caixa de texto **ID da Entidade do Provedor de Identidade** em Atlassian
   
   c. Copie o valor **URL de Logon** do portal do Azure, cole-o na caixa de texto **URL de SSO do Provedor de Identidade** em Atlassian

   ![URL de SSO do Provedor de Identidade](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![id e sso da entidade](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para fazer o download do certificado e salvá-lo no computador.

   ![certificado de autenticação](./media/atlassian-cloud-tutorial/certificate.png)

   ![Certificado 1](./media/atlassian-cloud-tutorial/certificate-1.png)

1. **Adicionar/Salvar** a configuração de SAML no Atlassian

1. Se você quiser configurar o aplicativo no modo iniciado por **IDP**, edite a seção **Configuração de SAML Básica** da página **Configurar Logon Único com SAML** no Azure e abra a **página de logon único SAML** no portal de administração do Atlassian

   a. Copie o valor **ID da Entidade do SP** do Atlassian, cole-o na caixa **Identificador (ID da Entidade)** no Azure e defina-a como padrão
   
   b. Copie o valor **URL do Serviço do Consumidor de Declaração do SP** do Atlassian, cole-o na caixa **URL de Resposta (URL do Serviço do Consumidor de Asserção)** no Azure e defina-o como padrão
   
   c. Cole o valor **URL de Instância** que você copiou na etapa 1 na caixa **Estado de Retransmissão** no Azure

   ![copiar urls](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![botão editar](./media/atlassian-cloud-tutorial/edit-button.png)

   ![imagem de urls](./media/atlassian-cloud-tutorial/urls.png)
   
1. Se você quiser configurar o aplicativo no modo iniciado por **SP**, edite a seção **Configuração de SAML Básica** da página **Configurar Logon Único com SAML** no Azure. Copie a **URL da Instância** (da etapa 1) e cole-a na caixa **URL de Logon** no Azure

   ![botão editar em urls](./media/atlassian-cloud-tutorial/edit-button.png)

   ![url de logon](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. O aplicativo Atlassian Cloud espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizados para a configuração de atributos do token SAML. Você pode editar o mapeamento de atributo clicando no ícone **Editar**. 

   ![Atributos](./media/atlassian-cloud-tutorial/edit-attribute.png)
   
   1. Mapeamento de atributo para um locatário do Azure AD com uma licença do Microsoft 365
      
      a. Clique na declaração do **Identificador de Usuário Único (ID de Nome)**

      ![atributos e declarações](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. O Atlassian Cloud espera que o **nameidentifier** (**Identificador de Usuário Único**) seja mapeado para o email do usuário (**user.email**). Edite o **Atributo de origem** e altere-o para **user.mail**. Salve as alterações à declaração.

      ![ID de usuário único](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. Os mapeamentos de atributos finais devem ter a seguinte aparência.

      ![imagem 2](./media/atlassian-cloud-tutorial/default-attributes-1.png)
      
   1. Mapeamento de atributo para um locatário do Azure AD sem uma licença do Microsoft 365 

      a. Clique na declaração de `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

      ![imagem 3](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. O Azure não preenche o atributo **user.mail** para usuários criados em locatários do Azure AD sem licenças do Microsoft 365 e armazena o email para esses usuários no atributo **userPrincipalName**. O Atlassian Cloud espera que o **nameidentifier** (**Identificador de Usuário Único**) seja mapeado para o email do usuário (**user.userprincipalname**).  Edite o **Atributo de origem** e altere-o para **user.userprincipalname**. Salve as alterações à declaração.

      ![definir email](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. Os mapeamentos de atributos finais devem ter a seguinte aparência.

      ![image 4](./media/atlassian-cloud-tutorial/default-attributes-2.png)
     
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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ele acesso ao Atlassian Cloud.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Atlassian Cloud**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-atlassian-cloud-test-user"></a>Criar um usuário de teste do Atlassian Cloud

Para permitir que os usuários do Azure AD entrar no Atlassian Cloud, provisione as contas de usuário manualmente no Atlassian Cloud fazendo o seguinte:

1. No painel **Administração**, selecione **Usuários**.

    ![O link de Usuários do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Para criar um usuário no Atlassian Cloud, selecione **Convidar usuário**.

    ![Criar um usuário do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Na caixa **Endereço de email**, insira o endereço de email do usuário e atribua o acesso ao aplicativo.

    ![Usuário do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Para enviar um convite por email para o usuário, selecione **Convidar usuários**. Um convite por email é enviado ao usuário e, depois de aceitar o convite, o usuário está ativo no sistema.

> [!NOTE]
> Você também pode criar usuários em massa clicando no botão **Criar em Massa** na seção **Usuários**.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Atlassian Cloud na qual poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do Atlassian Cloud diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Atlassian Cloud para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco Atlassian Cloud no Painel de Acesso, se estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se estiver configurado no modo IDP, você será conectado automaticamente ao Atlassian Cloud para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Atlassian Cloud, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).