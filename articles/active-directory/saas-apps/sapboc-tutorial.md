---
title: 'Tutorial: Integração do Azure Active Directory ao SAP Analytics Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Analytics Cloud.
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
ms.openlocfilehash: 19bdb6d2a586dcb279687673c7fa4e302dc4928b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652633"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Tutorial: integração do SAP Analytics Cloud com o Azure Active Directory

Neste tutorial, você aprenderá como integrar o SAP Analytics Cloud ao Azure AD (Azure Active Directory). Ao integrar o SAP Analytics Cloud com o Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao SAP Analytics Cloud.
* Permitir que os usuários sejam conectados automaticamente ao SAP Analytics Cloud com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do SAP Analytics Cloud habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SAP Analytics Cloud dá suporte ao SSO iniciado por **SP**.

## <a name="add-sap-analytics-cloud-from-the-gallery"></a>Adicionar o SAP Analytics Cloud por meio da galeria

Para configurar a integração do SAP Analytics Cloud ao Azure AD, você precisa adicionar o SAP Analytics Cloud da galeria para sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **SAP Analytics Cloud** na caixa de pesquisa.
1. Selecione **SAP Analytics Cloud** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-sap-analytics-cloud"></a>Configurar e testar o SSO do Azure AD para o SAP Analytics Cloud

Configure e teste o SSO do Azure AD com o SAP Analytics Cloud usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP Analytics Cloud.

Para configurar e testar o SSO do Azure AD com o SAP Analytics Cloud, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SAP Analytics Cloud](#configure-sap-analytics-cloud-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SAP Analytics Cloud](#create-sap-analytics-cloud-test-user)** – para ter um equivalente do usuário B.Fernandes no SAP Analytics Cloud, que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **SAP Analytics Cloud**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando um dos seguintes padrões:

    - `https://<sub-domain>.sapanalytics.cloud/`
    - `https://<sub-domain>.sapbusinessobjects.cloud/`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando um dos seguintes padrões:

    - `<sub-domain>.sapbusinessobjects.cloud`
    - `<sub-domain>.sapanalytics.cloud`

    > [!NOTE] 
    > Os valores nessas URLs são apenas para demonstração. Atualize os valores com a verdadeira URL de entrada e a URL do identificador. Para obter a URL de entrada, entre em contato com a [equipe de Suporte ao cliente do SAP Analytics Cloud](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Você pode obter a URL do identificador ao baixar os metadados do SAP Analytics Cloud do console do administrador. Isso é explicado mais adiante no tutorial.

4. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o SAP Analytics Cloud**, copie as URLs apropriadas com base em seus requisitos.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ele acesso ao SAP Analytics Cloud.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SAP Analytics Cloud**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sap-analytics-cloud-sso"></a>Configurar o SSO do SAP Analytics Cloud

1. Em outra janela do navegador da Web, entre no site da empresa do seu SAP Analytics Cloud como administrador.

2. Selecione **Menu** > **Sistema** > **Administração**.
    
    ![Selecionar Menu, Sistema e Administração](./media/sapboc-tutorial/configure-1.png)

3. Na guia **Segurança**, selecione o ícone **Editar** (caneta).
    
    ![Na guia Segurança, selecionar o ícone Editar](./media/sapboc-tutorial/configure-2.png)  

4. Para **Método de Autenticação**, selecione **SSO (Logon Único) do SAML**.

    ![Selecionar Logon Único do SAML para o método de autenticação](./media/sapboc-tutorial/configure-3.png)  

5. Para baixar os metadados do provedor de serviços (Etapa 1), selecione **Baixar**. No arquivo de metadados, localize e copie o valor **entityID**. No portal do Azure, na caixa de diálogo **Configuração Básica de SAML**, cole o valor na caixa **Identificador**.

    ![Copiar e colar o valor de entityID](./media/sapboc-tutorial/configure-4.png)  

6. Para fazer upload dos metadados do provedor de serviços (Etapa 2) no arquivo que você baixou do portal do Azure, em **Fazer upload dos metadados do Provedor de Identidade**, selecione **Fazer Upload**.  

    ![Em Fazer upload dos metadados do Provedor de Identidade, selecionar Fazer Upload](./media/sapboc-tutorial/configure-5.png)

7. Na lista **Atributo de Usuário**, selecione o atributo de usuário (Etapa 3) que você quer usar na sua implementação. Esse atributo de usuário é mapeado para o provedor de identidade. Para inserir um atributo personalizado na página do usuário, use a opção **Mapeamento de SAML Personalizado**. Ou você pode selecionar o **Email** ou a **ID DE USUÁRIO** como o atributo de usuário. Em nosso exemplo, selecionamos **Email** porque mapeamos a declaração do identificador de usuário com o atributo **userprincipalname** na seção **Atributos e Declarações do Usuário** do portal do Azure. Isso fornece um email de usuário exclusivo, que é enviado ao aplicativo do SAP Analytics Cloud em toda resposta bem-sucedida do SAML.

    ![Selecionar Atributo de Usuário](./media/sapboc-tutorial/configure-6.png)

8. Para verificar a conta com o provedor de identidade (Etapa 4), na caixa **Credencial de Logon (Email)**, insira o endereço de email do usuário. Em seguida, selecione **Verificar Conta**. O sistema adiciona credenciais de entrada à conta de usuário.

    ![Inserir email e selecionar Verificar Conta](./media/sapboc-tutorial/configure-7.png)

9. Selecione o ícone **Salvar**.

    ![Ícone Salvar](./media/sapboc-tutorial/save.png)

### <a name="create-sap-analytics-cloud-test-user"></a>Criar usuário de teste do SAP Analytics Cloud

Os usuários do Azure AD precisam ser provisionados no SAP Analytics Cloud para que possam entrar no SAP Analytics Cloud. No SAP Analytics Cloud, o provisionamento é uma tarefa manual.

Para provisionar uma conta de usuário:

1. Entre no site da empresa do seu SAP Analytics Cloud como administrador.

2. Selecione **Menu** > **Segurança** > **Usuários**.

    ![Adicionar Funcionário](./media/sapboc-tutorial/user-1.png)

3. Na página **Usuários**, para adicionar detalhes do novo usuário, selecione **+**. 

    ![Página Adicionar Usuários](./media/sapboc-tutorial/user-4.png)

    Em seguida, conclua as seguintes etapas:

    1. Na caixa **ID DE USUÁRIO**, insira a ID do usuário, como **B**.

    1. Na caixa **NOME**, insira o nome do usuário, como **B**.

    1. Na caixa **SOBRENOME**, insira o sobrenome do usuário, como **Fernandes**.

    1. Na caixa **NOME DE EXIBIÇÃO**, insira o nome completo do usuário, como **B.Fernandes**.

    1. Na caixa **EMAIL**, insira o endereço de email do usuário, como `b.simon@contoso.com`.

    1. Na página **Selecionar Funções**, selecione a função apropriada para o usuário e selecione **OK**.

        ![Selecione a função](./media/sapboc-tutorial/user-3.png)

    1. Selecione o ícone **Salvar**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado à URL de Logon do SAP Analytics Cloud, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do SAP Analytics Cloud e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do SAP Analytics Cloud em Meus Aplicativos, você será redirecionado à URL de Logon do SAP Analytics Cloud. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o SAP Analytics Cloud, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).