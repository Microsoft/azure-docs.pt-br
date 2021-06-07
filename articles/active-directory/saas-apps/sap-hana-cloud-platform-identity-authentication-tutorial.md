---
title: 'Tutorial: Integração do Azure Active Directory com o SAP Cloud Platform Identity Authentication | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Cloud Platform Identity Authentication.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: dc0cd57eb32baaeac0850337bbead3a73dec9292
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897319"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SAP Cloud Platform Identity Authentication

Neste tutorial, você aprenderá como integrar o SAP Cloud Platform Identity Authentication ao Azure AD (Azure Active Directory). Ao integrar o SAP Cloud Platform Identity Authentication ao Azure AD, você poderá:

* Controlar quem tem acesso ao SAP Cloud Platform Identity Authentication no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao SAP Cloud Platform Identity Authentication com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SAP Cloud Platform Identity Authentication.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* A SAP Cloud Platform Identity Authentication é compatível com SSO iniciado por **SP** e **IDP**

Antes de se aprofundar nos detalhes técnicos, é essencial entender os conceitos que serão examinados. Os AD FS (Serviços de Federação do Active Directory) e a Autenticação de Identidade da SAP Cloud Platform permitem que você implemente o SSO em aplicativos ou serviços protegidos pelo Azure AD (como um IdP) com aplicativos SAP e serviços protegidos pela Autenticação de Identidade da SAP Cloud Platform.

Atualmente, a Autenticação de Identidade da SAP Cloud Platform atua como um provedor de identidade de proxy para aplicativos SAP. O Azure Active Directory por sua vez atua como o principal provedor de identidade nessa configuração. 

O diagrama a seguir ilustra esse relacionamento:

![Criação de um usuário de teste do AD do Azure](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Com essa configuração, seu locatário de Autenticação de Identidade da SAP Cloud Platform é configurado como um aplicativo confiável no Azure Active Directory.

Todos os serviços e aplicativos SAP que você deseja proteger dessa maneira são posteriormente configurados no console de gerenciamento de Autenticação de Identidade da SAP Cloud Platform.

Portanto, a autorização para conceder acesso aos serviços e aplicativos SAP precisa ocorrer na Autenticação de Identidade da SAP Cloud Platform (diferentemente do Azure Active Directory).

Ao configurar a autenticação de identidade da SAP Cloud Platform como um aplicativo por meio do Azure Active Directory Marketplace, você não precisa configurar declarações individuais ou declarações SAML.

> [!NOTE]
> Atualmente, somente o SSO da Web foi testado por ambas as partes. Os fluxos necessários para a comunicação de aplicativo a API ou de API para API devem funcionar, mas ainda não foram testados. Eles serão testados durante as atividades subsequentes.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adicionar SAP Cloud Platform Identity Authentication a partir da galeria

Para configurar a integração da Autenticação de Identidade da SAP Cloud Platform no Azure AD, você precisa adicionar a Autenticação de Identidade da SAP Cloud Platform da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **SAP Cloud Platform Identity Authentication** na caixa de pesquisa.
1. Selecione **SAP Cloud Platform Identity Authentication** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform-identity-authentication"></a>Configurar e testar o SSO do Azure AD para o SAP Cloud Platform Identity Authentication

Configure e teste o SSO do Azure AD com o SAP Cloud Platform Identity Authentication usando um usuário de teste com o nome **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP Cloud Platform Identity Authentication.

Para configurar e testar o SSO do Azure AD com o SAP Cloud Platform Identity Authentication, realize as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SAP Cloud Platform Identity Authentication](#configure-sap-cloud-platform-identity-authentication-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SAP Cloud Platform Identity Authentication](#create-sap-cloud-platform-identity-authentication-test-user)** – para ter um equivalente de B.Fernandes no SAP Cloud Platform Identity Authentication que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **SAP Cloud Platform Identity Authentication**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica de SAML**, execute as seguintes etapas caso deseje configurar no modo iniciado por **IDP**:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `<IAS-tenant-id>.accounts.ondemand.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte do Cliente de SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) para obter esses valores. Se você não entender esse valor de identificador, leia a documentação do SAP Cloud Platform Identity Authentication sobre [Configuração do SAML 2.0 do Locatário](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Clique em **Definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado por **SP**:

    ![Informações sobre logon único de Domínio e URLs do SAP Cloud Platform Identity Authentication](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Esse valor não é real. Atualize esse valor com o URL de Logon real. Use a URL de logon do aplicativo comercial específica. Entre em contato com a [equipe de suporte do Cliente de SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) se você tiver qualquer dúvida.

1. O aplicativo SAP Cloud Platform Identity Authentication espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo SAP Cloud Platform Identity Authentication espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem|
    | ---------------| --------------- |
    | firstName | user.givenname |

8. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Download** para baixar o **XML de metadados** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

9. Na seção **Configurar a SAP Cloud Platform Identity Authentication**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao SAP Cloud Platform Identity Authentication.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SAP Cloud Platform Identity Authentication**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.

1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Configurar o SSO do SAP Cloud Platform Identity Authentication

1. Para automatizar a configuração no SAP Cloud Platform Identity Authentication, você precisa instalar a **extensão do navegador de Entrada Segura dos Meus Aplicativos** clicando em **Instalar extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, clique em **Configurar o SAP Cloud Platform Identity Authentication**, o que direcionará você para o aplicativo SAP Cloud Platform Identity Authentication. Nele, forneça as credenciais de administrador para entrar no SAP Cloud Platform Identity Authentication. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

3. Se quiser configurar o SAP Cloud Platform Identity Authentication manualmente, em outra janela do navegador da Web, vá para o console de administração do SAP Cloud Platform Identity Authentication. A URL tem o seguinte padrão: `https://<tenant-id>.accounts.ondemand.com/admin`. Em seguida, leia a documentação sobre autenticação de identidade da SAP Cloud Platform em [Integração com o Microsoft Azure AD](https://developers.sap.com/tutorials/cp-ias-azure-ad.html).

2. No portal do Azure, selecione o botão **Salvar**.

3. Continue com o seguinte somente se quiser adicionar e habilitar o SSO para outro aplicativo SAP. Repita as etapas na seção **Adicionar Autenticação de Identidade da SAP Cloud Platform por meio da galeria**.

4. No portal do Azure, na página de integração de aplicativos de **SAP Cloud Platform Identity Authentication**, selecione **Logon vinculado**.

    ![Configurar o logon vinculado](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked-sign-on.png)

5. Salve a configuração.

> [!NOTE]
> O novo aplicativo aproveita a configuração de logon único do aplicativo SAP anterior. Certifique-se de usar os mesmos Provedores de Identidade Corporativa no Console de Administração de Autenticação de Identidade da SAP Cloud Platform.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Criar um usuário de teste da SAP Cloud Platform Identity Authentication

Você não precisa criar um usuário na Autenticação de Identidade da SAP Cloud Platform. Os usuários que estão no repositório de usuários do Azure AD podem usar a funcionalidade de SSO.

O SAP Cloud Platform Identity Authentication é compatível com a opção de Federação de Identidades. Essa opção permite que o aplicativo verifique se os usuários autenticados pelo provedor de identidade corporativa existem no repositório de usuários da Autenticação de Identidade da SAP Cloud Platform.

A opção de Federação de Identidades é desabilitada por padrão. Se a Federação de Identidades estiver habilitada, somente os usuários que são importados na Autenticação de Identidade da SAP Cloud Platform poderão acessar o aplicativo.

Para obter mais informações sobre como habilitar ou desabilitar a Federação de Identidades com o SAP Cloud Platform Identity Authentication, consulte Habilitar a Federação de Identidades com o SAP Cloud Platform Identity Authentication em [Configurar a Federação de Identidades com o repositório de usuários do SAP Cloud Platform Identity Authentication](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/c029bbbaefbf4350af15115396ba14e2.html).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do SAP Cloud Platform Identity Authentication, na qual poderá iniciar o fluxo de logon.

* Acesse diretamente a URL de Logon do SAP Cloud Platform Identity Authentication, na qual você iniciará o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao SAP Cloud Platform Identity Authentication, para o qual configurou o SSO

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do SAP Cloud Platform Identity Authentication em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao SAP Cloud Platform Identity Authentication, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o SAP Cloud Platform Identity Authentication, você poderá impor controles de sessão, que protegem contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)