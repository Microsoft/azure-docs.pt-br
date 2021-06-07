---
title: 'Tutorial: Integração do Azure Active Directory ao SAP Business ByDesign | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Business ByDesign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 6db863f43deb6eb2787cda60650a267a62076aad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654327"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Tutorial: integração do Azure Active Directory ao SAP Business ByDesign

Neste tutorial, você aprenderá a integrar o SAP Business ByDesign ao Azure AD (Azure Active Directory). Ao integrar o SAP Business ByDesign ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao SAP Business ByDesign.
* Permitir que os usuários sejam conectados automaticamente ao SAP Business ByDesign com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do SAP Business ByDesign habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SAP Business ByDesign é compatível com o SSO iniciado por **SP**

## <a name="add-sap-business-bydesign-from-the-gallery"></a>Adicionar o SAP Business ByDesign da galeria

Para configurar a integração do SAP Business ByDesign ao Azure AD, você precisará adicionar o SAP Business ByDesign da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **SAP Business ByDesign** na caixa de pesquisa.
1. Escolha **SAP Business ByDesign** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Configure e teste o SSO do Azure AD com o SAP Business ByDesign usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP Business ByDesign.

Para configurar e testar o SSO do Azure AD com o SAP Business ByDesign, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SAP Business ByDesign](#configure-sap-business-bydesign-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SAP Business ByDesign](#create-sap-business-bydesign-test-user)** – para ter um equivalente de Brenda Fernandes no SAP Business ByDesign vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **SAP Business ByDesign**, localize a seção **Gerenciar** e escolha **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<servername>.sapbydesign.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo SAP Business ByDesign espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image1](common/edit-attribute.png)

6. Clique no ícone **Editar** para editar o valor de **Valor do identificador de nome**.

    ![image2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Na seção **Gerenciar declarações do usuário**, realize as seguintes etapas:

    ![image3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Selecione **Transformação** como uma **Origem**.

    b. Na lista suspensa **Transformação**, selecione **ExtractMailPrefix()** .

    > [!NOTE]
    > Por padrão, o ByD usa o formato NameID **não especificado** para mapeamento de usuário. O ByD mapeia a NameID das asserções SAML no alias do usuário do ByD. Além disso, o ByD dá suporte ao formato de ID de nome **emailAddress**. Nesse caso, o ByD mapeia a NameID da asserção SAML no endereço de email do usuário do ByD dos dados de contato do funcionário do ByD. Para obter mais detalhes, você pode conferir [este blog do SAP](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/).

8. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

9. Na seção **Configurar o SAP Business ByDesign**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao SAP Business ByDesign.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SAP Business ByDesign**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.

1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sap-business-bydesign-sso"></a>Configurar o SSO do SAP Business ByDesign

1. Faça logon no portal do SAP Business ByDesign com direitos de administrador.

2. Navegue até **Tarefa Comum de Gerenciamento de Aplicativos e Usuários** e clique na guia **Provedor de Identidade**.

3. Clique em **Novo Provedor de Identidade** e selecione o arquivo XML de metadados baixado no Portal do Azure. Com a importação dos metadados, o sistema carrega automaticamente o certificado de assinatura e o certificado de criptografia necessários.

    ![Configurar o logon único1](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Para incluir a **URL de Serviço de Consumidor de Declaração** na solicitação SAML, selecione **Incluir URL de Serviço de Consumidor de Declaração**.

5. Clique em **Ativar Logon Único**.

6. Salve suas alterações.

7. Clique na guia **Meu Sistema** .

    ![Configurar o logon único2](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Na caixa de texto **URL de Logon do Azure AD**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    ![Configurar o logon único3](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Especifique se o funcionário pode escolher manualmente entre fazer logon com uma ID de usuário e senha ou SSO, selecionando **Seleção de Provedor de Identidade Manual**.

10. Na seção **URL de SSO**, especifique a URL que deve ser usada pelo funcionário para fazer logon no sistema.
    Na lista suspensa URL Enviada ao Funcionário, você pode escolher entre as seguintes opções:

    **URL não SSO**

    O sistema envia apenas a URL normal do sistema ao funcionário. O funcionário não pode fazer logon usando o SSO e deve usar uma senha ou um certificado em vez disso.

    **URL de SSO**

    O sistema envia apenas a URL de SSO ao funcionário. O funcionário pode fazer logon usando o SSO. A solicitação de autenticação é redirecionada por meio do IdP.

    **Seleção Automática**

    Se o SSO não estiver ativo, o sistema enviará a URL normal do sistema ao funcionário. Se o SSO estiver ativo, o sistema verificará se o funcionário tem uma senha. Se uma senha estiver disponível, a URL de SSO e a URL não SSO serão enviadas ao funcionário. No entanto, se o funcionário não tiver uma senha, apenas a URL de SSO será enviada ao funcionário.

11. Salve suas alterações.

### <a name="create-sap-business-bydesign-test-user"></a>Criar um usuário de teste do SAP Business ByDesign

Nesta seção, você criará uma usuária chamada Brenda Fernandes no SAP Business ByDesign. Trabalhe com a [equipe de suporte ao cliente do SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para adicionar os usuários à plataforma SAP Business ByDesign. 

> [!NOTE]
> Verifique se o valor de NameID corresponde ao campo de nome de usuário na plataforma SAP Business ByDesign.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do SAP Business ByDesign na qual poderá iniciar o fluxo de logon. 

2. Acesse a URL de Logon do SAP Business ByDesign diretamente e inicie o fluxo de logon nela.

3. Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do SAP Business ByDesign em Meus Aplicativos, isso redirecionará para a URL de logon do SAP Business ByDesign. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

* Depois de configurar o SAP Business ByDesign, você poderá impor controles de sessão, que fornecem proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).