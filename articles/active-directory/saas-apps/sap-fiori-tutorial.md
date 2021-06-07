---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SAP Fiori | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Fiori.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 547d96a9591b99318a74977106e99511c9c80507
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687100"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SAP Fiori

Neste tutorial, você aprenderá a integrar o SAP Fiori ao Azure AD (Azure Active Directory). Com a integração do SAP Fiori ao Azure AD, você pode:

* Controlar no Azure AD que tem acesso ao SAP Fiori.
* Permitir que os usuários sejam conectados automaticamente ao SAP Fiori usando suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do SAP Fiori habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SAP Fiori dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> Para a Autenticação de iFrame iniciada pelo SAP Fiori, é recomendável usar o parâmetro **IsPassive** no AuthnRequest SAML para autenticação silenciosa. Para obter mais detalhes sobre o parâmetro **IsPassive**, confira as informações sobre o [Logon único de SAML do Azure AD](../develop/single-sign-on-saml-protocol.md).

## <a name="adding-sap-fiori-from-the-gallery"></a>Como adicionar o SAP Fiori por meio da galeria

Para configurar a integração do SAP Fiori ao Azure AD, você precisará adicionar o SAP Fiori por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da por meio da galeria**, digite **SAP Fiori** na caixa de pesquisa.
1. Selecione **SAP Fiori** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-sap-fiori"></a>Configurar e testar o SSO do Azure AD para o SAP Fiori

Configure e teste o SSO do Azure AD com o SAP Fiori com um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP Fiori.

Para configurar e testar o SSO do Azure AD com o SAP Fiori, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SAP Fiori](#configure-sap-fiori-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SAP Fiori](#create-sap-fiori-test-user)** – para ter um equivalente de B. Fernandes no SAP Fiori que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Abra uma nova janela do navegador da Web e entre em seu site de empresa do SAP Fiori como administrador.

1. Certifique-se de que os serviços **http** e **https** estão ativos e as portas relevantes são atribuídas ao código de transação **SMICM**.

1. Entre no cliente do SAP Business para o sistema SAP **T01**, em que o logon único é necessário. Em seguida, ative o gerenciamento de Sessão de Segurança HTTP.

    1. Vá para o código de transação **SICF_SESSIONS**. Todos os parâmetros de perfil relevantes com os valores atuais são exibidos. A aparência deles é similar à do exemplo a seguir:

        ```
        login/create_sso2_ticket = 2
        login/accept_sso2_ticket = 1
        login/ticketcache_entries_max = 1000
        login/ticketcache_off = 0  login/ticket_only_by_https = 0 
        icf/set_HTTPonly_flag_on_cookies = 3
        icf/user_recheck = 0  http/security_session_timeout = 1800
        http/security_context_cache_size = 2500
        rdisp/plugin_auto_logout = 1800
        rdisp/autothtime = 60
        ```

        >[!NOTE]
        > Ajuste os parâmetros com base nos requisitos da sua organização. Os parâmetros anteriores são fornecidos apenas como um exemplo.

    1. Se necessário, ajuste os parâmetros no perfil/padrão da instância do sistema SAP e reinicie esse sistema.

    1. Clique duas vezes no cliente relevante para habilitar uma sessão de segurança HTTP.

        ![Os valores atuais da página Parâmetros de Perfil Relevantes são exibidos](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Ative os seguintes serviços SICF:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Vá para o código de transação **SAML2** no cliente de negócios do sistema SAP [**T01/122**]. A interface do usuário de configuração é aberta em uma nova janela do navegador. Neste exemplo, usamos o Cliente Business para o sistema SAP 122.

    ![A página de entrada do Cliente Business do SAP Fiori](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Insira seu nome de usuário e senha e selecione **Fazer logon**.

    ![A página de Configuração de SAML 2.0 do sistema ABAP T01/122 no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. Na caixa **Nome do Provedor**, substitua **T01122** com **http:\//T01122** e, em seguida, selecione **Salvar**.

    > [!NOTE]
    > Por padrão, o nome do provedor está no formato \<sid>\<client>. O Azure AD espera o nome no formato \<protocol>://\<name>. Recomendamos que você mantenha o nome do provedor como https\://\<sid>\<client> para que você possa configurar vários mecanismos de ABAP do SAP Fiori no Azure AD.

    ![O nome do provedor atualizado na página de Configuração de SAML 2.0 do sistema ABAP T01/122 no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Selecione a **guia Provedor Local** > **Metadados**.

1. Na caixa de diálogo **Metadados do SAML 2.0**, baixe o arquivo XML de metadados gerado e salve-o em seu computador.

    ![O link Baixar Metadados na caixa de diálogo de metadados do SAP SAML 2.0](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. No portal do Azure, na página de integração de aplicativos do **SAP Fiori**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    a. Clique em **Carregar arquivo de metadados**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão populados automaticamente no painel **Configuração Básica do SAML**. Na caixa **URL de Entrada**, digite uma URL com o seguinte padrão: `https://<your company instance of SAP Fiori>`.

    > [!NOTE]
    > Alguns erros de relatório de clientes relacionados a valores de **URL de resposta** configurados incorretamente. Se vir esse erro, você poderá usar o seguinte script do PowerShell para definir a URL de Resposta correta para a instância:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Você pode definir a ID do objeto `ServicePrincipal` por conta própria antes de executar o script ou você pode passá-lo aqui.

1. O aplicativo SAP Fiori espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Para gerenciar esses valores de atributo, no painel **Configurar o Logon Único com o SAML**, selecione **Editar**.

    ![O painel Atributos de usuário](common/edit-attribute.png)

1. No painel **Atributos e Declarações do Usuário**, configure os atributos do token SAML, conforme mostrado na imagem anterior. Em seguida, conclua as seguintes etapas:

    1. Selecione **Editar** para abrir o painel **Gerenciar declarações do usuário**.

    1. Na lista **Transformação**, selecione **ExtractMailPrefix()** .

    1. Na lista **Parâmetro 1**, selecione **user.userprincipalname**.

    1. Clique em **Salvar**.

       ![O painel Gerenciar declarações de usuário](./media/sapfiori-tutorial/nameidattribute.png)

       ![A seção Transformação no painel Gerenciar declarações de usuário](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o SAP Fiori**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo a ela o acesso ao SAP Fiori.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SAP Fiori**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sap-fiori-sso"></a>Configurar o SSO do SAP Fiori

1. Entre no sistema SAP e acesse o código de transação **SAML2**. Uma nova janela do navegador é aberta com a página de configuração do SAML.

1. Para configurar pontos de extremidade para um provedor de identidade confiável (Azure AD), selecione a guia **Provedores Confiáveis**.

    ![A guia Provedores confiáveis no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Selecione **Adicionar** e selecione **Carregar Arquivo de Metadados** no menu de contexto.

    ![As opções Adicionar e Carregar Arquivo de Metadados no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Carregue o arquivo de metadados que você baixou do portal do Azure. Selecione **Avançar**.

    ![Selecionar o arquivo de metadados a carregar no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Na página seguinte, na caixa **Alias**, digite o nome do alias. Por exemplo, **aadsts**. Selecione **Avançar**.

    ![A caixa de Alias no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Verifique se o valor na caixa **Algoritmo Digest** é **SHA-256**. Selecione **Avançar**.

    ![Verifique o valor do Algoritmo Digest no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Em **Pontos de Extremidade de Logon Único**, selecione **HTTP POST** e, em seguida, selecione **Avançar**.

    ![Opções de pontos de extremidade de logon único no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Em **Pontos de Extremidade de Logoff Único**, selecione **Redirecionamento HTTP** e, em seguida, selecione **Avançar**.

    ![Opções de pontos de extremidade de logoff único no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Em **Pontos de Extremidade de Artefato**, selecione **Avançar** para continuar.

    ![Opções de Pontos de Extremidade de Artefato no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. Em **Requisitos de Autenticação**, selecione **Concluir**.

    ![Opções de Requisitos de Autenticação e a opção de Término no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Selecione **Provedor Confiável** > **Federação de Identidades** (na parte inferior da página). Selecione **Editar**.

    ![As guias Provedor Confiável e Federação de Identidade no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Selecione **Adicionar**.

    ![A opção Adicionar na guia de Federação de Identidade](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. Na caixa de diálogo **Formatos de NameID compatíveis**, selecione **Não especificado**. Selecione **OK**.

    ![A caixa de diálogo Formatos de NameID compatíveis e opções no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Os valores de **Fonte de ID de usuário** e **Modo de Mapeamento de ID de Usuário** determinam o vínculo entre o usuário do SAP e a declaração do Azure AD.  

    **Cenário 1**: Usuário do SAP para o mapeamento de usuário do Azure AD

    1. No SAP, em **Detalhes do Formato de NameID "Não especificado"** , observe os detalhes:

        ![Captura de tela que mostra a caixa de diálogo 'Detalhes do Formato de NameID "Não especificado"' no SAP.](./media/sapfiori-tutorial/nameiddetails.png)

    1. No portal do Azure, em **Atributos e Declarações do Usuário**, observe as declarações necessárias do Azure AD.

        ![Captura de tela que mostra a caixa de diálogo "Atributos e Declarações do Usuário".](./media/sapfiori-tutorial/claimsaad1.png)

    **Cenário 2**: Selecione a ID de usuário do SAP com base no endereço de email configurado no SU01. Nesse caso, a ID de email deve ser configurada no SU01 para cada usuário que precisar do SSO.

    1.  No SAP, em **Detalhes do Formato de NameID "Não especificado"** , observe os detalhes:

        ![A caixa de diálogo Detalhes do Formato de NameID "Não especificado" no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. No portal do Azure, em **Atributos e Declarações do Usuário**, observe as declarações necessárias do Azure AD.

       ![A caixa de diálogo Atributos e Declarações do Usuário no portal do Azure](./media/sapfiori-tutorial/claimsaad2.png)

1. Selecione **Salvar** e, em seguida, selecione **Habilitar** para habilitar o provedor de identidade.

    ![As opções Salvar e Habilitar no SAP](./media/sapfiori-tutorial/configuration1.png)

1. Quando solicitado, selecione **OK**.

    ![A opção OK na caixa de diálogo de configuração do SAML 2.0 no SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>Criar um usuário de teste do SAP Fiori

Nesta seção, você criará um usuário chamado Brenda Fernandes no SAP Fiori. Trabalhe com sua equipe interna de especialistas SAP ou com o parceiro SAP de sua organização para adicionar os usuários à plataforma SAP Fiori.

## <a name="test-sso"></a>Testar o SSO

1. Depois de ativar o provedor de identidade do Azure AD no SAP Fiori, tente acessar uma das URLs a seguir para testar o logon único (não deve ser solicitado que você forneça um nome de usuário e senha):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Substitua *sapurl* pelo nome de host real do SAP.

1. A URL de teste deve levar à página de aplicativo de teste a seguir no SAP. Se a página abrir, o logon único do Azure AD terá sido configurado com êxito.

    ![A página de aplicativo de teste padrão no SAP](./media/sapfiori-tutorial/testingsso.png)

1. Se for solicitado que você forneça um nome de usuário e senha, habilite o rastreamento para ajudar a diagnosticar o problema. Use a seguinte URL para o rastreamento: https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o SAP Fiori, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).