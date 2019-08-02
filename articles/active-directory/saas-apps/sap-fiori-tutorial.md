---
title: 'Tutorial: Integração do Azure Active Directory ao SAP Fiori | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Fiori.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeedes
ms.openlocfilehash: 897685eb967e03cfd30182eec6b237e27386496c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092161"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>Tutorial: Integração do Azure Active Directory ao SAP Fiori

Neste tutorial, você aprenderá a integrar o SAP Fiori ao Azure AD (Azure Active Directory).

A integração do SAP Fiori ao Azure AD oferece os seguintes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao SAP Fiori.
* Os usuários podem ser automaticamente conectados ao SAP Fiori com as contas do Azure AD (logon único).
* Você pode gerenciar suas contas em um único local, o portal clássico do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS (software como serviço) ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SAP Fiori, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura do Azure AD, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma assinatura do SAP Fiori com logon único habilitado.
* O SAP Fiori 7.20 ou posterior é obrigatório.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste e integrará o SAP Fiori ao Azure AD.

O SAP Fiori dá suporte aos seguintes recursos:

* **Logon único iniciado por SP**

## <a name="add-sap-fiori-in-the-azure-portal"></a>Adicionar o SAP Fiori no portal do Azure

Para integrar o SAP Fiori com o Azure AD, você deve adicionar SAP Fiori à sua lista de aplicativos SaaS gerenciados.

1. Entre no [Portal do Azure](https://portal.azure.com).

1. No menu esquerdo, selecione **Azure Active Directory**.

    ![A opção Azure Active Directory](common/select-azuread.png)

1. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

1. Para adicionar um aplicativo, selecione **Novo aplicativo**.

    ![A opção Novo aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, insira **SAP Fiori**. Nos resultados da pesquisa, selecione **SAP Fiori** e **Adicionar**.

    ![SAP Fiori na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SAP Fiori, com base em um usuário de teste chamado **Brenda Fernandes**. Para que o logon único funcione, estabeleça uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP Fiori.

Para configurar e testar o logon único do Azure AD com o SAP Fiori, você precisará concluir os seguintes blocos de construção:

| Tarefa | DESCRIÇÃO |
| --- | --- |
| **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** | Permite que os usuários usem esse recurso. |
| **[Configurar o logon único do SAP Fiori](#configure-sap-fiori-single-sign-on)** | Define as configurações de logon único no aplicativo. |
| **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** | Testa o logon único do Azure AD para um usuário chamado Brenda Fernandes. |
| **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** | Habilita Brenda Fernandes a usar o logon único do Azure AD. |
| **[Criar um usuário de teste do SAP Fiori](#create-an-sap-fiori-test-user)** | Cria um equivalente de Brenda Fernandes no SAP Fiori que é vinculado à representação do usuário no Azure AD. |
| **[Testar o logon único](#test-single-sign-on)** | Verifica se a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você configurará o logon único do Azure AD com o SAP Fiori no portal do Azure.

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
    > Por padrão, o nome do provedor está no formato \<sid>\<cliente>. O Azure AD espera o nome no formato \<protocolo>://\<nome>. Recomendamos que você mantenha o nome do provedor como https\://\<sid>\<cliente> para que você possa configurar vários mecanismos de ABAP do SAP Fiori no Azure AD.

    ![O nome do provedor atualizado na página de Configuração de SAML 2.0 do sistema ABAP T01/122 no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Selecione a **guia Provedor Local** > **Metadados**.

1. Na caixa de diálogo **Metadados do SAML 2.0**, baixe o arquivo XML de metadados gerado e salve-o em seu computador.

    ![O link Baixar Metadados na caixa de diálogo de metadados do SAP SAML 2.0](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. No [portal do Azure](https://portal.azure.com/), no painel de integração de aplicativos do **SAP Fiori**, selecione **Logon único**.

    ![A opção Logon único](common/select-sso.png)

1. No painel **Selecionar um método de logon único**, selecione o modo **SAML** ou **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

1. No painel **Configurar logon único com SAML**, selecione **Editar** (o ícone de lápis) para abrir o painel **Configuração Básica de SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, conclua as seguintes etapas:

    1. Selecione **Carregar o arquivo de metadados**.

        ![A opção Carregar o arquivo de metadados](common/upload-metadata.png)

   1. Para selecionar o arquivo de metadados, selecione o ícone de pasta e, em seguida, selecione **Carregar**.

       ![Selecione o arquivo de metadados e, em seguida, o botão Carregar](common/browse-upload-metadata.png)

1. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão populados automaticamente no painel **Configuração Básica do SAML**. Na caixa **URL de Logon**, insira uma URL que tenha o seguinte padrão: https:\//\<instância do SAP Fiori da sua empresa\>.

    ![Informações de logon único de domínio e URLs do SAP Fiori](common/sp-identifier-reply.png)

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

    1. Na lista **Parâmetro 1**, selecione **user.userprinicipalname**.

    1. Clique em **Salvar**.

       ![O painel Gerenciar declarações de usuário](./media/sapfiori-tutorial/nameidattribute.png)

       ![A seção Transformação no painel Gerenciar declarações de usuário](./media/sapfiori-tutorial/nameidattribute1.png)


1. No painel **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** ao lado de **XML de Metadados de Federação**. Selecione uma opção de download com base em seus requisitos. Salve o certificado no computador.

    ![A opção Download do certificado](common/metadataxml.png)

1. Na seção **Configurar o SAP Fiori**, copie as seguintes URLs com base em seus requisitos:

    * URL de logon
    * Identificador do Azure AD
    * URL de logoff

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-sap-fiori-single-sign-on"></a>Configurar o logon único do SAP Fiori

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

        ![A caixa de diálogo Detalhes do Formato de NameID "Não especificado" no SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. No portal do Azure, em **Atributos e Declarações do Usuário**, observe as declarações necessárias do Azure AD.

        ![A caixa de diálogo Atributos e Declarações do Usuário no portal do Azure](./media/sapfiori-tutorial/claimsaad1.png)

    **Cenário 2**: Selecione a ID de usuário do SAP com base no endereço de email configurado no SU01. Nesse caso, a ID de email deve ser configurada no SU01 para cada usuário que precisar do SSO.

    1.  No SAP, em **Detalhes do Formato de NameID "Não especificado"** , observe os detalhes:

        ![A caixa de diálogo Detalhes do Formato de NameID "Não especificado" no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. No portal do Azure, em **Atributos e Declarações do Usuário**, observe as declarações necessárias do Azure AD.

       ![A caixa de diálogo Atributos e Declarações do Usuário no portal do Azure](./media/sapfiori-tutorial/claimsaad2.png)

1. Selecione **Salvar** e, em seguida, selecione **Habilitar** para habilitar o provedor de identidade.

    ![As opções Salvar e Habilitar no SAP](./media/sapfiori-tutorial/configuration1.png)

1. Quando solicitado, selecione **OK**.

    ![A opção OK na caixa de diálogo de configuração do SAML 2.0 no SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada Brenda Fernandes.

1. No portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.

    ![As opções Usuários e Todos os usuários](common/users.png)

1. Selecione **Novo usuário**.

    ![A opção Novo usuário](common/new-user.png)

1. No painel **Usuário**, conclua as etapas a seguir:

    1. Na caixa **Nome**, insira **BrendaFernandes**.
  
    1. Na caixa **Nome de usuário**, insira **brendafernandes\@\<domíniodaempresa>.\<extensão>** . Por exemplo, **brendafernandes\@contoso.com**.

    1. Selecione a caixa de seleção **Mostrar Senha**. Anote o valor exibido na caixa **Senha**.

    1. Selecione **Criar**.

    ![O painel Usuário](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá a Brenda Fernandes acesso ao SAP Fiori para que ela possa usar o logon único do Azure.

1. No portal do Azure, selecione **Aplicativos empresariais** > **Todos os aplicativos** > **SAP Fiori**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **SAP Fiori**.

    ![O SAP Fiori na lista de aplicativos](common/all-applications.png)

1. No menu, selecione **Usuários e grupos**.

    ![A opção Usuários e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Em seguida, no painel **Adicionar atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

1. No painel **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários. Escolha **Selecionar**.

1. Se você estiver esperando um valor de função na declaração SAML, no painel **Selecionar função**, selecione a função relevante ao usuário na lista. Escolha **Selecionar**.

1. No painel **Adicionar atribuição**, selecione **Atribuir**.

### <a name="create-an-sap-fiori-test-user"></a>Criar um usuário de teste do SAP Fiori

Nesta seção, você criará um usuário chamado Brenda Fernandes no SAP Fiori. Trabalhe com sua equipe interna de especialistas SAP ou com o parceiro SAP de sua organização para adicionar os usuários à plataforma SAP Fiori.

### <a name="test-single-sign-on"></a>Testar logon único

1. Depois de ativar o provedor de identidade do Azure AD no SAP Fiori, tente acessar uma das URLs a seguir para testar o logon único (não deve ser solicitado que você forneça um nome de usuário e senha):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Substitua *sapurl* pelo nome de host real do SAP.

1. A URL de teste deve levar à página de aplicativo de teste a seguir no SAP. Se a página abrir, o logon único do Azure AD terá sido configurado com êxito.

    ![A página de aplicativo de teste padrão no SAP](./media/sapfiori-tutorial/testingsso.png)

1. Se for solicitado que você forneça um nome de usuário e senha, habilite o rastreamento para ajudar a diagnosticar o problema. Use a seguinte URL para o rastreamento: https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, examine estes artigos:

- [Lista de tutoriais de integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
