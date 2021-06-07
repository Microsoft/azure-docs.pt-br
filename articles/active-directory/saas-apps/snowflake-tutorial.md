---
title: 'Tutorial: Integração do Active Directory do Azure com o Snowflake | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Snowflake.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 1af0209265ec4945950120e80a83e19c8ab2eb4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726214"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Tutorial: Integração do Active Directory do Azure com o Snowflake

Neste tutorial, você aprenderá a integrar o Snowflake ao Azure AD (Azure Active Directory). Ao integrar o Snowflake ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Snowflake.
* Permitir que seus usuários sejam automaticamente conectados ao Snowflake com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Snowflake, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Snowflake

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar o logon único do Azure AD em um ambiente de teste.

- O Snowflake dá suporte ao SSO iniciado por **SP e IDP**
- O Snowflake é compatível com [provisionamento e desprovisionamento de usuário automatizados](snowflake-provisioning-tutorial.md) (recomendado)

## <a name="adding-snowflake-from-the-gallery"></a>Adicionando floco de neve da galeria

Para configurar a integração do Snowflake ao Azure AD, você precisa adicionar o Floco de Neve da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Snowflake** na caixa de pesquisa.
1. Selecione **Snowflake** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-snowflake"></a>Configurar e testar o SSO do Azure AD para o Snowflake

Configure e teste o SSO do Azure AD com o Snowflake usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Snowflake.

Para configurar e testar o SSO do Azure AD com o Snowflake, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Snowflake](#configure-snowflake-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Snowflake](#create-snowflake-test-user)** – para ter um equivalente a B.Fernandes no Snowflake que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos **Snowflake**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, execute as seguintes etapas caso deseje configurar o aplicativo no modo iniciado por **IDP**:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

1. Clique em **Definir URLs adicionais** e execute a seguinte etapa caso deseje configurar o aplicativo no modo iniciado por SP:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`
    
    b. Na caixa de texto **URL de Logoff**, digite uma URL usando o seguinte padrão: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/logout`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com [Equipe de suporte ao cliente de floco de neve](https://support.snowflake.net/s/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Snowflake**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Snowflake.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **floco de neve**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-snowflake-sso"></a>Configurar o SSO do Snowflake

1. Em uma janela diferente do navegador da Web, efetue login no Snowflake como um administrador de segurança.

1. **Alterne a função** à **ACCOUNTADMIN**, clicando no **perfil** no lado superior direito da página.

    > [!NOTE]
    > Isso é separado do contexto selecionado no canto superior direito em seu nome de usuário.
    
    ![O administrador de Snowflake](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

1. Abra o **certificado de Base 64 baixado** no bloco de notas. Copie o valor insira "---BEGIN CERTIFICATE---" e "---END CERTIFICATE---" e cole em aspas lado **certificado** abaixo. Em **ssoUrl**, cole **URL de logon** valor que você copiou do portal do Azure. Selecione o **Todas as consultas** e clique em **Executar**.

   ![Sql Snowflake](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

   ```
   use role accountadmin;
   alter account set saml_identity_provider = '{
   "certificate": "<Paste the content of downloaded certificate from Azure portal>",
   "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
   "type":"custom",
   "label":"AzureAD"
   }';
   alter account set sso_login_page = TRUE;
   ```


### <a name="create-snowflake-test-user"></a>Criar um usuário de teste do Snowflake

Para habilitar usuários do Microsoft Azure Active Directory para fazer logon no Snowflake, eles devem ser provisionados no Snowflake. No Snowflake, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no Snowflake como Administrador de Segurança.

2. **Alterne a função** à **ACCOUNTADMIN**, clicando no **perfil** no lado superior direito da página.  

    ![O administrador de Snowflake](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. Crie o usuário executando o abaixo de consulta SQL, garantindo que o "Nome de logon" é definido como o nome de usuário do Microsoft Azure Active Directory na planilha conforme mostrado abaixo.

    ![O adminsql Snowflake](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Snowflake, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Snowflake e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você deverá ser conectado automaticamente ao Snowflake, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do Snowflake em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao Snowflake, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Snowflake, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)