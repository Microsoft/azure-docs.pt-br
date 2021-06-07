---
title: 'Tutorial: Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SAP NetWeaver | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP NetWeaver.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/11/2020
ms.author: jeedes
ms.openlocfilehash: b6b8dab3472473082562f1e4c0216886191e4a59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97962798"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SAP NetWeaver

Neste tutorial, você aprenderá a integrar o SAP NetWeaver ao Azure AD (Azure Active Directory). Ao integrar o SAP NetWeaver ao Azure AD, você poderá:

* Controlar no Microsoft Azure AD que tem acesso ao SAP NetWeaver.
* Permitir que os usuários sejam conectados automaticamente ao SAP NetWeaver com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SAP NetWeaver.
* SAP NetWeaver V7.20 necessário pelo menos

## <a name="scenario-description"></a>Descrição do cenário

* O SAP NetWeaver dá suporte a **SAML** (**SSO iniciado com SP**) e **OAuth**. Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

> [!NOTE]
> Configure o aplicativo no SAML ou no OAuth de acordo com os requisitos de sua organização. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>Adicionar SAP NetWeaver da galeria

Para configurar a integração do SAP NetWeaver ao Azure AD, você precisará adicionar o SAP NetWeaver da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da por meio da galeria**, digite **SAP NetWeaver** na caixa de pesquisa.
1. Selecione **SAP NetWeaver** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-sap-netweaver"></a>Configurar e testar o SSO do Azure AD para o SAP NetWeaver

Configure e teste o SSO do Azure AD com o SAP NetWeaver com um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP NetWeaver.

Para configurar e testar o SSO do Azure AD com o SAP NetWeaver, execute as seguintes etapas:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B. Fernandes use o logon único do Azure AD.
1. **[Configurar o SAP NetWeaver usando SAML](#configure-sap-netweaver-using-saml)** para definir as configurações de SSO no lado do aplicativo.
    1. **[Criar um usuário de teste do SAP NetWeaver](#create-sap-netweaver-test-user)** para ter um equivalente de B. Fernandes no SAP NetWeaver que esteja vinculado à representação de usuário do Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.
1. **[Configurar o SAP NetWeaver para OAuth](#configure-sap-netweaver-for-oauth)** para definir as configurações de OAuth no lado do aplicativo.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SAP NetWeaver, execute as seguintes etapas:

1. Abra uma nova janela do navegador da Web e entre no site da empresa do SAP NetWeaver como administrador

1. Certifique-se de que os serviços **http** e **https** estão ativos e as portas apropriadas são atribuídas no código T **SMICM**.

1. Entre no cliente de negócios do Sistema SAP (T01) em que o SSO é necessário e ative o Gerenciamento de sessão de segurança HTTP.

    a. Vá para o código de transação **SICF_SESSIONS**. Ele exibe todos os parâmetros de perfil relevante com os valores atuais. Serão apresentados como abaixo:-
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
    > Ajuste acima dos parâmetros de acordo com os requisitos da sua organização, acima dos parâmetros são fornecidos aqui como indicação apenas.

    b. Se for necessário, ajuste os parâmetros no perfil/padrão da instância do sistema SAP e reinicie o sistema SAP.

    c. Clique duas vezes no cliente relevante para habilitar a sessão de segurança HTTP.

    ![A sessão de segurança HTTP ](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Ative os serviços SICF abaixo:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. Vá para o código de transação **SAML2** no cliente de negócios do sistema SAP [T01/122]. Isso abrirá uma interface do usuário em um navegador. Neste exemplo, supomos 122 como cliente do SAP Business.

    ![Código da transação](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Forneça seu nome de usuário e senha para entrar na interface do usuário e clique em **Editar**.

    ![nome de usuário e senha](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. Substitua **Nome do Provedor** de T01122 para `http://T01122` e clique em **Salvar**.

    > [!NOTE]
    > Por padrão, o nome do provedor vem no formato `<sid><client>`, mas o Azure AD espera o nome no formato `<protocol>://<name>`, recomendando manter o nome do provedor como `https://<sid><client>` para permitir que vários mecanismos SAP NetWeaver ABAP sejam configurados no Azure AD.

    ![Os vários mecanismos ABAP do SAP NetWeaver](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Geração de Metadados do Provedor de Serviço**: - uma vez que terminamos de configurar as configurações do **Provedor Local** e **Provedores Confiáveis** na Interface de usuário do SAML 2.0, a próxima etapa seria gerar arquivo de metadados do provedor de serviços (que contém todas as configurações, contextos de autenticação e outras configurações no SAP). Depois que esse arquivo é gerado, precisamos carregá-lo no Microsoft Azure Active Directory.

    ![Como gerar metadados de provedor de serviços](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Vá para a **guia do Provedor Local**.

    b. Clique em **Metadados**.

    c. Salve o **arquivo XML de Metadados** gerado no computador e carregue-o na seção **Configuração Básica do SAML** para popular automaticamente os valores de **Identificador** e **URL de Resposta** no portal do Azure.

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **SAP NetWeaver**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute a seguinte etapa:

    a. Clique em **Carregar arquivo de metadados** para carregar o **Arquivo de metadados do provedor de serviços** que você obteve anteriormente.

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão populados automaticamente na caixa de texto da seção **Configuração Básica do SAML**, conforme mostrado abaixo:

    d. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Já observamos que alguns clientes relataram um erro de URL de Resposta incorreta configurada para suas instâncias. Se você receber um erro desse tipo, use o seguinte script do PowerShell como uma solução alternativa para definir a URL de Resposta correta para a instância:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > A ID de Objeto da ServicePrincipal deve ser definida primeiro por você ou também pode ser passada aqui.

1. O aplicativo SAP NetWeaver espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo Atributos de usuário.

    ![editar atributo](common/edit-attribute.png)

1. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    a. Clique no **ícone Editar** para abrir a caixa de diálogo **Gerenciar declarações do usuário**.

    ![ícone editar](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Na lista **Transformação**, selecione **ExtractMailPrefix()** .

    c. Na lista **Parâmetro 1**, selecione **user.userprincipalname**.

    d. Clique em **Save** (Salvar).

1. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, localize **XML de Metadados de Federação** e escolha **Download** para fazer o download do certificado e salve-o em seu computador.

   ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o SAP NetWeaver**, copie as URLs apropriadas com base em seus requisitos.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo a ela o acesso ao SAP NetWeaver.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SAP NetWeaver**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sap-netweaver-using-saml"></a>Configurar o SAP NetWeaver usando SAML

1. Entre no sistema SAP e acesse o código de transação SAML2. Isso abre a nova janela do navegador com a tela de configuração do SAML.

2. Para configurar pontos de extremidade para provedor de Identidade confiável (Microsoft Azure Active Directory) para a guia **Provedores Confiáveis**.

    ![Configurar provedores confiáveis de logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Pressione **Add** e selecione **Carregar arquivo de metadados** no menu de contexto.

    ![Configurar Logon Único 2](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Carregue o arquivo de metadados que você fez o download do portal do Azure.

    ![Configurar Logon Único 3](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Na próxima tela, digite o nome do alias. Por exemplo, adicione sts e pressione **Avançar** para continuar.

    ![Configurar Logon Único 4](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Certifique-se de que seu **Algoritmo de código hash** deve ser **SHA-256** e não requer alterações e pressione **Próxima**.

    ![Configurar Logon Único 5](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. Nos **pontos de extremidade de logon único**, use **HTTP POST** e clique em **Próxima** para continuar.

    ![Configurar Logon Único 6](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Nos **pontos de extremidade de logout único**, use **HTTP POST** e clique em **Próxima** para continuar.

    ![Configurar Logon Único 7](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Nos **pontos de extremidade de artefato**, aperte **Próxima** para continuar.

    ![Configurar Logon Único 8](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Em **Requisitos de Autenticação**, clique em **Concluir**.

    ![Configurar Logon Único 9](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Vá para a guia **Provedor Confiável** > **Federação de Identidades** (na parte inferior da tela). Clique em **Editar**.

    ![Configurar Logon Único 10](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Clique em **Adicionar** na guia **Federação de Identidades** (janela inferior).

    ![Configurar Logon Único 11](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. Na janela pop-up, selecione **Não especificado** em **Formatos de NameID com Suporte** e clique em OK.

    ![Configurar Logon Único 12](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

1. Forneça o valor da **Origem da ID de Usuário** como um **Atributo de Declaração**, o valor de **Modo de mapeamento da ID de usuário** como **Email** e o **Nome do Atributo de Declaração** como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    ![Configurar o logon único ](./media/sapnetweaver-tutorial/nameid-format.png)

14. Observe que os valores de **Fonte de ID de Usuário** e **Modo de mapeamento da ID de usuário** determinam o vínculo entre o usuário do SAP e a declaração do Azure AD.

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Cenário: o usuário do SAP para o mapeamento de usuário do Azure Active Directory.

    a. Captura de tela de detalhes da NameID do SAP.

    ![Configurar Logon Único 13](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Captura de tela mencionando as declarações Obrigatórias do Azure AD.

    ![Configurar Logon Único 14](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Cenário: Selecione a ID de usuário do SAP com base no endereço de email configurado no SU01. Nesse caso, a ID de email deve ser configurada no su01 para cada usuário que precisar do SSO.

    a.  Captura de tela de detalhes da NameID do SAP.

    ![Configurar Logon Único 15](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. captura de tela mencionando as declarações Obrigatórias do Azure AD.

    ![Configurar Logon Único 16](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Clique em **Salvar** e, em seguida, clique em **Habilitar** para habilitar o provedor de identidade.

    ![Configurar Logon Único 17](./media/sapnetweaver-tutorial/configuration1.png)

16. Clique em **OK** quando solicitado.

    ![Configurar Logon Único 18](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>Criar um usuário de teste do SAP NetWeaver

    Nesta seção, você criará uma usuária chamada B. Fernandes no SAP NetWeaver. Trabalhe sua equipe de especialistas SAP internamente ou trabalhe com seu parceiro do SAP de organização para adicionar os usuários à plataforma SAP NetWeaver.

## <a name="test-sso"></a>Testar o SSO

1. Depois que o provedor de identidade do Microsoft Azure Active Directory tiver sido ativado, tente acessar abaixo da URL para verificar o SSO (não haverá nenhum prompt de nome de usuário e senha)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (ou) use a URL abaixo

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Substitua sapurl pelo nome de host real do SAP.

2. A URL acima deve levar você para a tela mencionada abaixo. Se você for capaz de alcançar a página abaixo, a instalação do SSO do Microsoft Azure Active Directory é feita com êxito.

    ![testar Logon Único](./media/sapnetweaver-tutorial/testingsso.png)

3. Se ocorrer o prompt de nome de usuário e senha, diagnostique o problema ap habilitar o rastreamento usando a URL abaixo

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>Configurar o SAP NetWeaver para OAuth

1. O processo documentado do SAP está disponível no local: [Habilitação do serviço de gateway do NetWeaver e criação de escopo do OAuth 2.0](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Vá até SPRO e localize **Ativar e manter serviços**.

    ![Ativar e manter serviços](./media/sapnetweaver-tutorial/oauth01.png)

3. Neste exemplo, queremos conectar o serviço OData: `DAAG_MNGGRP` com OAuth ao SSO do Azure AD. Use a pesquisa de nome de serviço técnico para o serviço `DAAG_MNGGRP` e ative-o se ainda não estiver ativo (procure o status `green` na guia de nós do ICF). Verifique se o alias do sistema (o sistema de back-end conectado, onde o serviço está de fato em execução) está correto.

    ![Serviço Odata](./media/sapnetweaver-tutorial/oauth02.png)

    * Em seguida, clique no botão **OAuth** na barra de botões superior e atribua `scope` (mantenha o nome padrão como oferecido).

4. Para nosso exemplo, o escopo é `DAAG_MNGGRP_001`, gerado com base no nome do serviço adicionando automaticamente um número. O relatório `/IWFND/R_OAUTH_SCOPES` pode ser usado para alterar o nome do escopo ou criar manualmente.

    ![Configurar OAuth](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Mensagem `soft state status is not supported` – pode ser ignorada, sem problemas. Para obter mais detalhes, consulte [aqui](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true).

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Criar um usuário de serviço para o cliente OAuth 2.0

1. OAuth2 usa um `service ID` para obter o token de acesso para o usuário final em seu nome. Restrição importante do design de OAuth: o `OAuth 2.0 Client ID` deve ser idêntico ao `username` que o cliente OAuth 2.0 usa para fazer logon ao solicitar um token de acesso. Portanto, para nosso exemplo, vamos registrar um cliente OAuth 2.0 com o nome CLIENT1 e, como pré-requisito, um usuário com o mesmo nome (CLIENT1) deve existir no sistema SAP, e configuraremos esse usuário para ser usado pelo aplicativo referenciado. 

2. Ao registrar um cliente OAuth, usamos o `SAML Bearer Grant type`.

    >[!NOTE]
    >Para obter mais detalhes, veja o Registro de clientes do OAuth 2.0 para conhecer o tipo de concessão de portador SAML [aqui](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type).

3. tcod: SU01 / create user CLIENT1 como `System type` e atribua a senha, salve conforme necessário para fornecer a credencial ao programador da API, que deve gravá-la com o nome de usuário para o código de chamada. Nenhum perfil ou função deve ser atribuído.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Registrar a nova ID de cliente de OAuth 2.0 com o assistente de criação

1. Para registrar um novo **cliente OAuth 2.0**, inicie a transação **SOAUTH2**. A transação exibirá uma visão geral dos clientes OAuth 2.0 que já foram registrados. Escolha **Criar** para iniciar o assistente para o novo cliente OAuth chamado CLIENT1 neste exemplo.

2. Vá até T-Code: **SOAUTH2** e forneça a descrição e, em seguida, clique em **avançar**.

    ![SOAUTH2](./media/sapnetweaver-tutorial/oauth04.png)

    ![ID do cliente do OAuth 2.0](./media/sapnetweaver-tutorial/oauth05.png)

3. Selecione o **SAML2 IdP – Azure AD** já adicionado na lista suspensa e salve.

    ![IdP SAML2 – Azure AD 1](./media/sapnetweaver-tutorial/oauth06.png)

    ![IdP SAML2 – Azure AD 2](./media/sapnetweaver-tutorial/oauth07.png)

    ![IdP SAML2 – Azure AD 3](./media/sapnetweaver-tutorial/oauth08.png)

4. Clique em **Adicionar** na atribuição de escopo para adicionar o escopo criado anteriormente: `DAAG_MNGGRP_001`

    ![Escopo](./media/sapnetweaver-tutorial/oauth09.png)

    ![atribuição de escopo](./media/sapnetweaver-tutorial/oauth10.png)

5. Clique em **concluir**.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Azure AD SAP NetWeaver, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O Controle de Sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
