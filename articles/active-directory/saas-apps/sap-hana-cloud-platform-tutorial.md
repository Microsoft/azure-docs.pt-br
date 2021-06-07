---
title: 'Tutorial: Integração do Azure Active Directory com a SAP Cloud Platform | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Cloud Platform.
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
ms.openlocfilehash: b15c5a9f9f1e4e144caa2ddaa36d42a2a225b31b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97964041"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Tutorial: Integração do Active Directory do Azure com a SAP Cloud Platform

Neste tutorial, você aprenderá a integrar a SAP Cloud Platform ao Azure Active Directory (Azure AD). Ao integrar o SAP Cloud Platform ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao SAP Cloud Platform.
* Permitir que os usuários sejam conectados automaticamente ao SAP Cloud Platform com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD à SAP Cloud Platform, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura habilitada para logon único da SAP Cloud Platform

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu à SAP Cloud Platform poderão fazer logon único no aplicativo usando a [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

>[!IMPORTANT]
>Você precisa implantar seu próprio aplicativo ou assinar um aplicativo em sua conta da SAP Cloud Platform para testar o logon único. Neste tutorial, um aplicativo é implantado na conta.
> 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SAP Cloud Platform dá suporte a SSO iniciado por **SP**

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Adicionar a SAP Cloud Platform por meio da galeria

Para configurar a integração da SAP Cloud Platform ao Azure AD, você precisa adicionar a SAP Cloud Platform à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **SAP Cloud Platform** na caixa de pesquisa.
1. Selecione **SAP Cloud Platform** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>Configurar e testar o SSO do Azure AD para o SAP Cloud Platform

Configure e teste o SSO do Azure AD com o SAP Cloud Platform usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP Cloud Platform.

Para configurar e testar o SSO do Azure AD com o SAP Cloud Platform, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do SAP Cloud Platform](#configure-sap-cloud-platform-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste da SAP Cloud Platform](#create-sap-cloud-platform-test-user)** – para ter um equivalente de Brenda Fernandes no SAP Cloud Platform que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **SAP Cloud Platform**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    ![Informações de logon único de Domínio e URLs da SAP Cloud Platform](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo **SAP Cloud Platform**. Esta é a URL específica da conta de um recurso protegido em seu aplicativo SAP Cloud Platform. A URL é baseada no seguinte padrão: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >Esta é a URL em seu aplicativo SAP Cloud Platform que exige que o usuário seja autenticado.
    > 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`

    b. Na caixa de texto **Identificador** que você irá fornecer à sua SAP Cloud Platform digite uma URL usando um dos seguintes padrões: 

    - `https://hanatrial.ondemand.com/<instancename>`
    - `https://hana.ondemand.com/<instancename>`
    - `https://us1.hana.ondemand.com/<instancename>`
    - `https://ap1.hana.ondemand.com/<instancename>`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>`

    > [!NOTE] 
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com [equipe de suporte do cliente da SAP Cloud Platform](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) para obter a URL de logon e o Identificador. A URL de resposta você pode obter da seção de gerenciamento de confiança que é explicada no tutorial posteriormente.
    > 
4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

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

Nesta seção, você permite que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao SAP Cloud Platform.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SAP Cloud Platform**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sap-cloud-platform-sso"></a>Configurar SSO do SAP Cloud Platform

1. Em uma janela diferente do navegador, inicie uma sessão na ferramenta cockpit da SAP Cloud Platform em `https://account.<landscape host>.ondemand.com/cockpit`(por exemplo: https://account.hanatrial.ondemand.com/cockpit).

2. Clique na guia **Confiar** .
   
    ![Confiança](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Confiar")

3. Na seção Gerenciamento de confiança, em **Provedor de serviços local**, execute as seguintes etapas:

    ![Captura de tela que mostra a seção "Gerenciamento de Confiança" com a guia "Provedor de Serviço Local" selecionada e todas as caixas de texto realçadas.](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Gerenciamento de Confiança")
   
    a. Clique em **Editar**.

    b. Para **Tipo de Configuração**, selecione **Personalizado**.

    c. Para **Nome do Provedor Local**, deixe o valor padrão. Copie esse valor e cole-o no campo **Identificador** na configuração do Azure AD para a SAP Cloud Platform.

    d. Para gerar um par de chaves **Chave de Assinatura** e um **Certificado de Assinatura**, clique em **Gerar Par de Chaves**.

    e. Para **Propagação de Entidade**, selecione **Desabilitado**.

    f. Para **Forçar Autenticação**, selecione **Desabilitado**.

    g. Clique em **Save** (Salvar).

4. Depois de salvar as configurações do **Provedor de serviços Llocal**, faça o seguinte para obter a URL de resposta:
   
    ![Obter Metadados](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Obter Metadados")

    a. Baixe o arquivo de metadados da SAP Cloud Platform, clicando em **Obter metadados**.

    b. Abra o arquivo XML de metadados da SAP Cloud Platform baixado e localize a marca **ns3:AssertionConsumerService** .
 
    c. Copie o valor do atributo **Location** e cole-o no campo **URL de Resposta** na configuração do Azure AD da SAP Cloud Platform.

5. Clique na guia **Provedor de Identidade Confiável** e em **Adicionar Provedor de Identidade Confiável**.
   
    ![Captura de tela que mostra a página "Gerenciamento de Confiança" com a guia "Provedor de Identidade Confiável" selecionada.](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Gerenciamento de Confiança")
   
    >[!NOTE]
    >Para gerenciar a lista de provedores de identidade confiáveis, será necessário ter escolhido o Tipo de configuração personalizado na seção Provedor de Serviço Local. Para o tipo de configuração Padrão, você terá uma confiança implícita e não editável para o Serviço de ID do SAP. Para Nenhum, não há configurações de confiança.
    > 
    > 

6. Clique na guia **Geral** e em **Procurar** para carregar o arquivo de metadados baixado.
    
    ![Gerenciamento de Confiança](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Gerenciamento de Confiança")
    
    >[!NOTE]
    >Depois de carregar o arquivo de metadados, os valores de **URL de Logon Único**, **URL de Logoff Único** e o **Certificado de Autenticação** serão automaticamente populados.
    > 
     
7. Clique na guia **Atributos** .

8. Na guia **Atributos**, execute a seguinte etapa:
    
    ![Atributos](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Atributos") 

    a. Clique em **Adicionar Atributo Baseado em Declaração** e adicione os seguintes atributos baseados em declaração:
       
    | Atributo de Asserção | Atributo de Entidade |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |nome |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |sobrenome |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
     >[!NOTE]
     >A configuração dos Atributos depende de como os aplicativos na HCP são desenvolvidos, isto é, quais atributos eles esperam ter na resposta do SAML e por qual nome (Atributo de Entidade de Segurança) eles acessam esse atributo no código.
     > 
    
    b. O **Atributo Padrão** na captura de tela serve apenas para fins de ilustração. Ele não é necessário para que o cenário funcione.  
 
    c. Os nomes e valores do **Atributo de Entidade** mostrados na captura de tela dependerão de como o aplicativo foi desenvolvido. É possível que o seu aplicativo precise de mapeamentos diferentes.

### <a name="assertion-based-groups"></a>Grupos baseados em asserção

Como uma etapa opcional, você pode configurar grupos com base na asserção para seu Provedor de Identidade do Azure Active Directory.

Usar grupos na SAP Cloud Platform permite que você atribua dinamicamente um ou mais usuários a uma ou mais funções em seus aplicativos da SAP Cloud Platform, determinados pelos valores de atributos na asserção SAML 2.0. 

Por exemplo, se a declaração contém o atributo "*contract=temporary*", talvez seja conveniente que todos os usuários afetados sejam adicionados ao grupo "*TEMPORARY*". O grupo “*TEMPORARY*” pode conter uma ou mais funções de um ou mais aplicativos implantados em sua conta da SAP Cloud Platform.
 
Use os grupos baseados em declarações se desejar atribuir vários usuários simultaneamente a uma ou mais funções de aplicativos em sua conta da SAP Cloud Platform. Se você quer atribuir apenas um único usuário ou alguns deles a funções específicas, recomendamos atribuí-los diretamente na guia "**Autorizações**" da ferramenta cockpit da SAP Cloud Platform.

### <a name="create-sap-cloud-platform-test-user"></a>Criar um usuário da SAP Cloud Platform

Para permitir que os usuários do Azure AD façam logon na SAP Cloud Platform, atribua funções a eles na SAP Cloud Platform.

**Para atribuir uma função a um usuário, execute as seguintes etapas:**

1. Faça logon em sua ferramenta cockpit da **SAP Cloud Platform** .

2. Realize o que é descrito a seguir:
   
    ![Autorizações](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Autorizações")
   
    a. Clique em **Autorização**.

    b. Clique na guia **Usuários** .

    c. Na caixa de texto **Usuário** , digite o endereço de email do usuário.

    d. Clique em **Atribuir** para atribuir uma função ao usuário.

    e. Clique em **Save** (Salvar).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de logon do SAP Cloud Platform, na qual você poderá iniciar o fluxo de logon. 

* Acesse a URL de logon do SAP Cloud Platform diretamente e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do SAP Cloud Platform em Meus Aplicativos, você deve ser conectado automaticamente ao aplicativo SAP Cloud Platform para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o SAP Cloud Platform, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).