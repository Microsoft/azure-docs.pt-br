---
title: 'Tutorial: Integração SSO (logon único) do Azure Active Directory com o Cisco Webex Meetings| Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cisco Webex Meetings.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: bb8ea637d0353e4efa0cb946f486d68639fc699d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592482"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Tutorial: Integração SSO (logon único) do Azure Active Directory com o Cisco Webex Meetings

Neste tutorial, você aprenderá como integrar o Cisco Webex Meetings ao Azure AD (Azure Active Directory). Com a integração do Cisco Webex Meetings ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao Cisco Webex Meetings.
* Permitir que os usuários entrem automaticamente no Cisco Webex Meetings com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Cisco Webex Meetings habilitada para SSO (logon único).

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Cisco Webex Meetings dá suporte ao SSO iniciado por **SP e IDP**.

* O Cisco Webex Meetings dá suporte ao provisionamento de usuário **Just-In-Time**.

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Adicionando o Cisco Webex Meetings da galeria

Para configurar a integração do Cisco Webex Meetings ao Azure AD, você precisa adicionar o Cisco Webex Meetings da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Cisco Webex Meetings** na caixa de pesquisa.
1. Selecione **Cisco Webex Meetings** no painel de resultados e, depois, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex-meetings"></a>Configurar e testar o SSO do Azure AD para o Cisco Webex Meetings

Configure e teste o SSO do Azure AD com o Cisco Webex Meetings usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cisco Webex Meetings.

Para configurar e testar o SSO do Azure AD com o Cisco Webex Meetings, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
   1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
   1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
   
1. **[Configurar o SSO do Cisco Webex Meetings](#configure-cisco-webex-meetings-sso)** – para definir as configurações de logon único no lado do aplicativo.
   * **[Criar um usuário de teste do Cisco Webex Meetings](#create-cisco-webex-meetings-test-user)** – para ter um equivalente de B. Fernandes no Cisco Webex Meetings que esteja vinculado à representação de usuário do Azure AD.
    
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Cisco Webex Meetings**, encontre a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar Logon Único com SAML**, você pode configurar o aplicativo no modo iniciado por **IDP** carregando o arquivo **Metadados do Provedor de Serviços** da seguinte maneira:
   1. Clique em **Carregar arquivo de metadados**.
   1. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.
   1. Após a conclusão bem-sucedida do upload do arquivo de metadados do Provedor de Serviços, os valores **Identificador** e **URL de Resposta** são preenchidos automaticamente na seção **Configuração Básica do SAML**.
   
      > [!Note]
      > Você obterá o arquivo de Metadados do Provedor de Serviços da seção **Configurar o SSO do Cisco Webex Meetings**, que será explicada posteriormente neste tutorial. 

1. Se desejar configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:  
   1. Na seção **Configuração básica do SAML**, clique no ícone editar/de caneta.

      ![Editar a Configuração Básica de SAML](common/edit-urls.png)

   1. Na caixa de texto **URL de Logon**, digite a URL usando o seguinte padrão: `https://<customername>.my.webex.com`

1. O aplicativo Cisco Webex Meetings espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo Atributos de usuário.

   ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo Cisco Webex Meetings espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção Declarações de Usuário da caixa de diálogo Atributos de Usuário, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo: 

   | Nome | Atributo de Origem|
   | ---------------|  --------- |
   |   nome    | user.givenname |
   |   sobrenome    | user.surname |
   |   email       | user.mail |
   |   uid    | user.mail |

   1. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.
   1. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
   1. Deixe o **Namespace** em branco.
   1. Escolha Origem como **Atributo**.
   1. Na lista **Atributo de origem**, selecione o valor do atributo mostrado para essa linha na lista suspensa.
   1. Clique em **Save** (Salvar).

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Cisco Webex Meetings**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Cisco Webex Meetings.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Cisco Webex Meetings**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-cisco-webex-meetings-sso"></a>Configurar o SSO do Cisco Webex Meetings

1. Entre no Cisco Webex Meetings com suas credenciais de administrador.
1. Acesse **Configurações Comuns de Site** e navegue até **Configuração de SSO**.

   ![Captura de tela que mostra Administração do Cisco WebEx com Configurações de Site Comuns e Configuração de SSO selecionadas.](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

1. Na página **Administração do Webex**, execute as seguintes etapas:

   ![Captura de tela que mostra a página Administração do WebEx com as informações descritas nesta etapa.](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

   1. Selecione **SAML 2.0** como o **Protocolo de Federação**.
   1. Clique no link **Importar metadados SAML** para carregar o arquivo de metadados que você baixou do portal do Azure.
   1. Selecione o **Perfil de SSO** como **Iniciado por IDP**  e clique no botão **Exportar** para baixar o arquivo de metadados do provedor de serviço e carregue-o na seção **Configuração SAML Básica** no portal do Azure.
   1. Na caixa de texto **AuthContextClassRef**, digite um dos seguintes valores:
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified`
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`
    
      Para habilitar a MFA usando o Azure AD, insira os dois valores desta forma: `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

   1. Selecione **Criação Automática de Conta**.
   
      > [!NOTE]
      > Para habilitar o provisionamento de usuário **Just-In-Time**, você precisa marcar a **Criação Automática de Conta**. Além disso, os atributos do token SAML precisam ser passados na resposta SAML.

   1. Clique em **Save** (Salvar).

      > [!NOTE]
      > Essa configuração destina-se apenas aos clientes que usam a ID de usuário do Webex no formato de email.
      > 
      > Para saber mais sobre como configurar o Cisco Webex Meetings, confira a página de [documentação do WebEx](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog).

### <a name="create-cisco-webex-meetings-test-user"></a>Criar um usuário de teste do Cisco Webex Meetings

O objetivo desta seção é criar um usuário chamado B. Fernandes no Cisco Webex Meetings. O Cisco Webex Meetings dá suporte ao provisionamento **Just-In-Time**, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Cisco Webex Meetings, um novo será criado quando você tentar acessar o Cisco Webex Meetings.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado à URL de logon do Cisco Webex Meetings, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de logon do Cisco Webex Meetings e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no Cisco Webex Meetings para o qual configurou o SSO.

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do Cisco Webex Meetings em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você entrará automaticamente no Cisco Webex Meetings para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Cisco Webex Meetings, você poderá aplicar o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O Controle de Sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)