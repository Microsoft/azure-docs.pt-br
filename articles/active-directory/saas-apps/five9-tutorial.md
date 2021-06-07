---
title: 'Tutorial: Integração do Azure Active Directory ao Five9 Plus Adapter (CTI, Contact Center Agents) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Five9 Plus Adapter (CTI, Contact Center Agents).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 85e953951d5368dc97312e7810f3c356bda7c6b6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218712"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Tutorial: Integração do Azure Active Directory ao Five9 Plus Adapter (CTI, Contact Center Agents)

Neste tutorial, você aprenderá a integrar o Five9 Plus Adapter (CTI, Contact Center Agents) ao Azure AD (Azure Active Directory). Ao integrar o Five9 Plus Adapter (CTI, Contact Center Agents) ao Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso ao Five9 Plus Adapter (CTI, Contact Center Agents).
* Permitir que os usuários sejam conectados automaticamente ao Five9 Plus Adapter (CTI, Contact Center Agents) com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Five9 Plus Adapter (CTI, Contact Center Agents), você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único do Five9 Plus Adapter (CTI, Contact Center Agents).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Five9 Plus Adapter (CTI, Contact Center Agents) dá suporte ao SSO iniciado por **IdP**.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Adicionar o Five9 Plus Adapter (CTI, Contact Center Agents) por meio da galeria

Para configurar a integração do Five9 Plus Adapter (CTI, Contact Center Agents) ao Azure AD, é necessário adicionar o Five9 Plus Adapter (CTI, Contact Center Agents) à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Five9 Plus Adapter (CTI, Contact Center Agents)** na caixa de pesquisa.
1. Selecione **Five9 Plus Adapter (CTI, Contact Center Agents)** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-five9-plus-adapter-cti-contact-center-agents"></a>Configurar e testar o SSO do Azure AD para Five9 Plus Adapter (CTI, Contact Center Agents)

Configure e teste o SSO do Azure AD com o Five9 Plus Adapter (CTI, Contact Center Agents) usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Five9 Plus Adapter (CTI, Contact Center Agents).

Para configurar e testar o SSO do Azure AD com o Five9 Plus Adapter (CTI, Contact Center Agents), execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Five9 Plus Adapter (CTI, Contact Center Agents)](#configure-five9-plus-adapter-cti-contact-center-agents-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Five9 Plus Adapter (CTI, Contact Center Agents)](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** – para ter um equivalente de B.Fernandes no Five9 Plus Adapter (CTI, Contact Center Agents) que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Five9 Plus Adapter (CTI, Contact Center Agents)** , localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na página **Configurar Logon Único com SAML**, execute as seguintes etapas:

    a. Na caixa de texto **Identificador**, digite uma das seguintes URLs:
    
    |    Ambiente      |       URL      |
    | :-- | :-- |
    | Para o “Five9 Plus Adapter for Microsoft Dynamics CRM” | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Para o “Five9 Plus Adapter for Zendesk” | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Para o “Five9 Plus Adapter for Agent Desktop Toolkit” | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. Na caixa de texto **URL de Resposta**, digite uma das seguintes URLs:

    |      Ambiente     |      URL      |
    | :--                  | :--           |
    | Para o “Five9 Plus Adapter for Microsoft Dynamics CRM” | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Para o “Five9 Plus Adapter for Zendesk” | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Para o “Five9 Plus Adapter for Agent Desktop Toolkit” | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o Five9 Plus Adapter (CTI, Contact Center Agents)**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao Five9 Plus Adapter (CTI, Contact Center Agents).

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Five9 Plus Adapter (CTI, Contact Center Agents)**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-five9-plus-adapter-cti-contact-center-agents-sso"></a>Configurar o SSO do Five9 Plus Adapter (CTI, Contact Center Agents)

1. Para configurar o logon único no lado do **Five9 Plus Adapter (CTI, Contact Center Agents)**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas para a [equipe de suporte do Five9 Plus Adapter (CTI, Contact Center Agents)](https://www.five9.com/about/contact). Além disso, para configurar o SSO adicional, siga as etapas abaixo de acordo com o adaptador:

    a. Guia do Administrador “Five9 Plus Adapter for Agent Desktop Toolkit”: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Guia do administrador “Five9 Plus Adapter for Microsoft Dynamics CRM”: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Guia do administrador“Five9 Plus Adapter for Zendesk”: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Criar um usuário de teste do Five9 Plus Adapter (CTI, Contact Center Agents)

Nesta seção, você cria um usuário chamado Brenda Fernandes no Five9 Plus Adapter (CTI, Contact Center Agents). Trabalhe com a [equipe de suporte do Five9 Plus Adapter (CTI, Contact Center Agents)](https://www.five9.com/about/contact) para adicionar os usuários à plataforma Five9 Plus Adapter (CTI, Contact Center Agents). Os usuários devem ser criados e ativados antes de usar o logon único. 

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você entrará automaticamente no Five9 Plus Adapter (CTI, Contact Center Agents), para o qual configurou o SSO.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Five9 Plus Adapter (CTI, Contact Center Agents) nos Meus Aplicativos, você deverá ser conectado automaticamente ao Five9 Plus Adapter (CTI, Contact Center Agents), para o qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Five9 Plus Adapter (CTI, Contact Center Agents), você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
