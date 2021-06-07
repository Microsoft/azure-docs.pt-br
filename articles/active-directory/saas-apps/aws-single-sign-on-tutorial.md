---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao AWS Single Sign-on | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AWS Single Sign-on.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: jeedes
ms.openlocfilehash: 2d0b9e45dc5de0cd4550cf4b9f944fd33ebd7e7e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720656"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-sign-on"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao AWS Single Sign-on

Neste tutorial, você aprenderá a integrar o AWS Single Sign-on ao Azure AD (Azure Active Directory). Ao integrar o AWS Single Sign-on ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao AWS Single Sign-on.
* Permitir que os usuários sejam conectados automaticamente ao AWS Single Sign-on com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do AWS Single Sign-on habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O AWS Single Sign-on dá suporte ao SSO iniciado por **SP e IdP**.

* O AWS Single Sign-on dá suporte ao [**Provisionamento de usuários automatizado**](./aws-single-sign-on-provisioning-tutorial.md).

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>Adicionando o AWS Single Sign-on por meio da galeria

Para configurar a integração do AWS Single Sign-on ao Azure AD, você precisará adicionar o AWS Single Sign-on da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **AWS Single Sign-on** na caixa de pesquisa.
1. Selecione **AWS Single Sign-on** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>Configurar e testar o SSO do Azure AD para o AWS Single Sign-on

Configure e teste o SSO do Azure AD com o AWS Single Sign-on usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no AWS Single Sign-on.

Para configurar e testar o SSO do Azure AD com o AWS Single Sign-on, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do AWS Single Sign-on](#configure-aws-single-sign-on-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do AWS Single Sign-on](#create-aws-single-sign-on-test-user)** : para ter um equivalente de B.Fernandes no AWS Single Sign-on que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **AWS Single Sign-on**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Se você tiver um **arquivo de metadados do Provedor de Serviços**, na seção **Configuração Básica de SAML**, execute as seguintes etapas:

    a. Clique em **Carregar arquivo de metadados**.

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados que você baixou da seção **Configurar o SSO do AWS Single Sign-on** (ponto 8) e clique em **Adicionar**.

    ![image2](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão preenchidos automaticamente na seção Configuração Básica do SAML:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Se os valores de **Identificador** e **URL de resposta** não forem preenchidos automaticamente, preencha-os manualmente de acordo com seus requisitos.

1. Se você não tiver um **arquivo de metadados do Provedor de Serviços**, execute as etapas a seguir na seção **Configuração SAML Básica**. Se desejar configurar o aplicativo no modo iniciado por **IDP**, digite os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do AWS Single Sign-on](mailto:aws-sso-partners@amazon.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo AWS Single Sign-on espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração dos atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/edit-attribute.png)


    > [!NOTE]
    > Se o ABAC estiver habilitado no SSO do AWS, os atributos adicionais poderão ser passados como marcas de sessão diretamente em contas do AWS.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o AWS Single Sign-on**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você habilita B.Fernandes a usar o logon único do Azure concedendo a ela acesso ao AWS Single Sign-on.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **AWS Single Sign-on**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-aws-single-sign-on-sso"></a>Configurar o SSO do AWS Single Sign-on

1. Para automatizar a configuração no AWS Single Sign-on, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o AWS Single Sign-on** para ser direcionado ao aplicativo AWS Single Sign-on. Nele, forneça as credenciais de administrador para entrar no AWS Single Sign-on. A extensão do navegador vai configurar automaticamente o aplicativo e automatizar as etapas de 3 a 10.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o AWS Single Sign-on manualmente, em outra janela do navegador da Web, entre no seu site de empresa do AWS Single Sign-on como administrador.

1. Acesse **Serviços -> Segurança, Identidade e Conformidade -> AWS Single Sign-On**.
2. No painel de navegação esquerdo, escolha **Configurações**.
3. Na página **Configurações**, localize **Origem da identidade** e clique em **Alterar**.

    ![Captura de tela do serviço de alteração de origem da identidade](./media/aws-single-sign-on-tutorial/settings.png)

4. Na página Alterar origem da identidade, escolha **Provedor de identidade externo**.

    
    ![Captura de tela da seção de escolha do provedor de identidade externo](./media/aws-single-sign-on-tutorial/external-identity-provider.png)


1. Execute as etapas abaixo na seção **Configurar provedor de identidade externo**:

    ![Captura de tela da seção de download e upload de metadados](./media/aws-single-sign-on-tutorial/upload-metadata.png)

    a. Na seção **Metadados do provedor de serviço**, localize **Metadados do SAML de SSO da AWS**, selecione **Baixar arquivo de metadados** para baixar o arquivo de metadados e salvá-lo no computador e use esse arquivo de metadados para carregá-lo no portal do Azure.

    b. Copie a **URL de Entrada do SSO da AWS** e cole esse valor na caixa de texto **URL de Logon** na seção **Configuração Básica do SAML** no portal do Azure.

    c. Na seção **Metadados do provedor de identidade**, escolha **Procurar** para carregar o arquivo de metadados que você baixou do portal do Azure.

    d. Escolha **Avançar: Examinar**.

8. Na caixa de texto, digite **ACEITAR** para alterar a origem da identidade.

    ![Captura de tela de confirmação da configuração](./media/aws-single-sign-on-tutorial/accept.png)

9. Clique em **Alterar origem da identidade**.

### <a name="create-aws-single-sign-on-test-user"></a>Criar um usuário de teste do AWS Single Sign-on

1. Abra o **console de SSO do AWS**.

2. No painel de navegação esquerdo, escolha **Usuários**.

3. Na página Usuários, escolha **Adicionar usuário**.

4. Na página Adicionar usuário, siga estas etapas:

    a. No campo **Nome de usuário**, insira B.Fernandes.

    b. No campo **Endereço de email**, insira o `username@companydomain.extension`. Por exemplo, `B.Simon@contoso.com`.

    c. No campo **Confirmar endereço de email**, insira novamente o endereço de email da etapa anterior.

    d. No campo Primeiro nome, digite `Jane`.

    e. No campo Sobrenome, digite `Doe`.

    f. No campo Nome de exibição, digite `Jane Doe`.

    g. Escolha **Avançar: Grupos**.

    > [!NOTE]
    > Certifique-se de que o nome de usuário inserido no SSO do AWS corresponda ao usuário de entrada no Azure AD. Isso ajudará a evitar problemas de autenticação.

5. Escolha **Adicionar usuário**.
6. Em seguida, você atribuirá o usuário à sua conta do AWS. Para fazer isso, no painel de navegação esquerdo do console de SSO do AWS, escolha **Contas do AWS**.
7. Na página de Contas do AWS, selecione a guia da organização do AWS e marque a caixa ao lado da conta do AWS que deseja atribuir ao usuário. Em seguida, escolha **Atribuir usuários**.
8. Na página Atribuir usuários, localize e marque a caixa ao lado do usuário B. Fernandes. Em seguida, escolha **Avançar: Conjuntos de permissões**.
9. Na seção Selecionar conjuntos de permissões, marque a caixa ao lado do conjunto de permissões que deseja atribuir ao usuário B. Fernandes. Se você não tiver um conjunto de permissões, escolha **Criar conjunto de permissões**.

    > [!NOTE]
    > Os conjuntos de permissões definem o nível de acesso que os usuários e grupos têm a uma conta do AWS. Para saber mais sobre os conjuntos de permissões, confira a página **Conjuntos de permissões** do SSO do AWS.
10. Escolha **Concluir**.

> [!NOTE]
> O AWS Single Sign-on também dá suporte ao provisionamento automático do usuário. É possível encontrar [aqui](./aws-single-sign-on-provisioning-tutorial.md) detalhes sobre como configurar o provisionamento automático do usuário.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do AWS Single Sign-on, na qual você poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do AWS Single Sign-on diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao AWS Single Sign-on, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do AWS Single Sign-on em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao AWS Single Sign-on, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o AWS Single Sign-on, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).