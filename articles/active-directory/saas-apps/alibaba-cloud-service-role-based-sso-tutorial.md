---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Alibaba Cloud Service (SSO baseado em função) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Alibaba Cloud Service (SSO baseado em função).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/08/2020
ms.author: jeedes
ms.openlocfilehash: 6e4cb3c372b696b203d2441c74a6cafff175af47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736123"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Alibaba Cloud Service (SSO baseado em função)

Neste tutorial, você aprenderá a integrar o Alibaba Cloud Service (SSO baseado em função) ao Azure AD (Azure Active Directory). Ao integrar o Alibaba Cloud Service (SSO baseado em função) ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Alibaba Cloud Service (SSO baseado em função).
* Permitir que os usuários sejam conectados automaticamente ao Alibaba Cloud Service (SSO baseado em função) com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Alibaba Cloud Service (SSO baseado em função) habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Alibaba Cloud Service dá suporte a SSO iniciado por **IDP** (SSO baseado em função)

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Adicionando o Alibaba Cloud Service (SSO baseado em função) da Galeria

Para configurar a integração do Alibaba Cloud Service (SSO baseado em função) ao Azure AD, você precisará adicionar o Alibaba Cloud Service (SSO baseado em função) da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Alibaba Cloud Service (SSO baseado em função)** na caixa de pesquisa.
1. Selecione **Alibaba Cloud Service (SSO baseado em função)** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.
5. Na página **Alibaba Cloud Service (SSO baseado em função)** , clique em **Propriedades** no painel de navegação à esquerda, copie a **ID de objeto** e salve-a no computador para uso posterior.

    ![Configuração de propriedades](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-sso-for-alibaba-cloud-service-role-based-sso"></a>Configurar e testar o SSO do Azure AD para o Alibaba Cloud Service (SSO baseado em função)

Configure e teste o SSO do Azure AD com o Alibaba Cloud Service (SSO baseado em função) usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Alibaba Cloud Service (SSO baseado em função).

Para configurar e testar o SSO do Azure AD com o Alibaba Cloud Service (SSO baseado em função), execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o logon único baseado em função no Alibaba Cloud Service](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** – para permitir que os usuários usem esse recurso.
    1. **[Configurar o SSO do Alibaba Cloud Service (SSO baseado em função)](#configure-alibaba-cloud-service-role-based-sso-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Alibaba Cloud Service (SSO baseado em função)](#create-alibaba-cloud-service-role-based-sso-test-user)** – para ter um equivalente de Brenda Fernandes no Alibaba Cloud Service (SSO baseado em função) que esteja vinculado à representação de usuário do Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Alibaba Cloud Service (SSO baseado em função)** , localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    a. Clique em **Carregar arquivo de metadados**.

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.


    >[!NOTE]
    >1. Para o site internacional do Alibaba Cloud, baixe os metadados do provedor de serviços [deste](https://signin.alibabacloud.com/saml-role/sp-metadata.xml) link.
    > 1. Para o site do Alibaba Cloud Service (CN), baixe os metadados do provedor de serviços [deste](https://signin.aliyun.com/saml-role/sp-metadata.xml) link.

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão populados automaticamente na caixa de texto da seção do Alibaba Cloud Service (SSO baseado em função):

    > [!Note]
    > Se os valores de **Identificador** e **URL de Resposta** não forem populados automaticamente, preencha-os manualmente de acordo com suas necessidades.

1. O Alibaba Cloud Service (SSO baseado em função) exige que as funções sejam configuradas no Azure AD. A declaração de função é pré-configurada, portanto, você não precisa configurá-la, mas ainda precisará criá-la no Azure AD usando este [artigo](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Alibaba Cloud Service (SSO baseado em função)** , copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Alibaba Cloud Service (SSO baseado em função).

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Alibaba Cloud Service (SSO baseado em função)** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na guia **Usuários e grupos**, selecione u2 na lista de usuários e clique em **Selecionar**. Em seguida, clique em **Atribuir**.

    ![Atribuir o usuário de teste do Azure AD1](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Exiba a função atribuída e teste o Alibaba Cloud Service (SSO baseado em função).

    ![Atribuir o usuário de teste do Azure AD2](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Depois de atribuir o usuário (u2), a função criada será anexada automaticamente ao usuário. Se você tiver criado várias funções, precisará anexar a função apropriada ao usuário, conforme necessário. Caso deseje implementar o SSO baseado em função do Azure AD para várias contas do Alibaba Cloud, repita as etapas anteriores.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Configurar o logon único baseado em função no Alibaba Cloud Service

1. Entre no [console de RAM](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) do Alibaba Cloud usando Account1.

2. No painel de navegação à esquerda, selecione **SSO**.

3. Na guia **SSO baseado em função**, clique em **Criar IdP**.

4. Na página exibida, insira `AAD` no campo Nome do IdP, insira uma descrição no campo **Observação**, clique em **Carregar** para carregar o arquivo de metadados de federação baixado antes e, em seguida, clique em **OK**.

5. Depois que o IdP for criado com êxito, clique em **Criar Função de RAM**.

6. No campo **Nome da Função de RAM**, insira `AADrole`, selecione `AAD` na lista suspensa **Selecionar IdP** e clique em OK.

    >[!NOTE]
    >Conceda permissão à função conforme necessário. Depois que você criar o IdP e a função correspondente, recomendamos que você salve os ARNs do IdP e a função para uso posterior. Obtenha os ARNs na página de informações do IdP e na página de informações da função.

7. Associe a função de RAM do Alibaba Cloud (AADrole) ao usuário do Azure AD (u2): Para associar a função de RAM ao usuário do Azure AD, você precisará criar uma função no Azure AD seguindo estas etapas:

    a. Entre no [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

    b. Clique em **Modificar Permissões** para obter as permissões necessárias para a criação de uma função.

    ![Configuração do Graph1](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Selecione as permissões a seguir na lista e clique em **Modificar Permissões**, conforme mostrado na figura a seguir.

    ![Configuração do Graph2](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Depois que as permissões forem concedidas, faça logon no Explorador do Graph novamente.

    d. Na página do Explorador do Graph, selecione **GET** na primeira lista suspensa e **beta** na segunda lista suspensa. Em seguida, insira `https://graph.microsoft.com/beta/servicePrincipals` no campo ao lado das listas suspensas e clique em **Executar Consulta**.

    ![Configuração do Graph3](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Se você estiver usando vários diretórios, insira `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` no campo da consulta.

    e. Na seção **Visualização da Resposta**, extraia a propriedade appRoles da 'Entidade de Serviço' para uso posterior.

    ![Configuração do Graph4](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Localize a propriedade appRoles inserindo `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` no campo da consulta. Observe que a `objectID` é a ID de objeto que você copiou da página **Propriedades** do Azure AD.

    f. Volte ao Explorador do Graph, altere o método de **GET** para **PATCH**, cole o seguinte conteúdo na seção **Corpo da Solicitação** e, em seguida, clique em **Executar Consulta**:
    ```
    { 
      "appRoles": [
        { 
          "allowedMemberTypes":[
            "User"
          ],
          "description": "msiam_access",
          "displayName": "msiam_access",
          "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
          "isEnabled": true,
          "origin": "Application",
          "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
      ]
    }
    ```
    > [!NOTE]
    > O `value` são os ARNs do IdP e a função que você criou no console de RAM. Aqui, você pode adicionar várias funções conforme necessário. O Azure AD enviará o valor dessas funções como o valor da declaração na resposta SAML. No entanto, você só pode adicionar novas funções após o componente `msiam_access` da operação de patch. Para simplificar o processo de criação, recomendamos que você use um gerador de ID, como o Gerador de GUID para gerar IDs em tempo real.

    g. Depois que o patch da 'Entidade de Serviço' for aplicado com a função necessária, anexe a função com o usuário do Azure AD (u2) seguindo as etapas da seção **Atribuir o usuário de teste do Azure AD** do tutorial.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Configurar o SSO do Alibaba Cloud Service (SSO baseado em função)

Para configurar o logon único no **Alibaba Cloud Service (SSO baseado em função)** , é necessário enviar o **XML de Metadados de Federação** baixado e as URLs copiadas adequadas do portal do Azure para a [equipe de suporte do Alibaba Cloud Service (SSO baseado em função)](https://www.aliyun.com/service/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Criar um usuário de teste do Alibaba Cloud Service (SSO baseado em função)

Nesta seção, você criará um usuário chamado Brenda Fernandes no Alibaba Cloud Service (SSO baseado em função). Trabalhe com a [equipe de suporte do Alibaba Cloud Service (SSO baseado em função)](https://www.aliyun.com/service/) para adicionar os usuários à plataforma Alibaba Cloud Service (SSO baseado em função). Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Depois que as configurações anteriores forem concluídas, teste o Alibaba Cloud Service (SSO baseado em função) seguindo estas etapas:

1. No portal do Azure, acesse a página **Alibaba Cloud Service (SSO baseado em função)** , selecione **Logon único** e clique em **Testar**.

    ![Configuração de teste1](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Clique em **Entrar como o usuário atual**.

    ![Configuração de teste2](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Na página de seleção de conta, selecione u2.

    ![Configuração de teste3](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. A página a seguir é exibida, indicando que o SSO baseado em função foi bem-sucedido.

    ![Configuração de teste4](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Alibaba Cloud Service (SSO baseado em função), você poderá impor um controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
