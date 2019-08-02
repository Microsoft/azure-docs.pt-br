---
title: 'Tutorial: Integração do Azure Active Directory com o Alibaba Cloud Service (SSO baseado em função) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Alibaba Cloud Service (SSO baseado em função).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdfd19d9a0e928e26ad6f01ba4b9c3f493aacb0c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107157"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>Tutorial: Integração do Azure Active Directory com o Alibaba Cloud Service (SSO baseado em função)

Neste tutorial, você aprenderá a integrar o Alibaba Cloud Service (SSO baseado em função) ao Azure AD (Azure Active Directory).
A integração do Alibaba Cloud Service (SSO baseado em função) com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Alibaba Cloud Service (SSO baseado em função).
* Você pode permitir que os usuários sejam conectados automaticamente ao Alibaba Cloud Service (SSO baseado em função) (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Alibaba Cloud Service (SSO baseado em função), você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único (SSO baseado em função) do Alibaba Cloud Service

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Alibaba Cloud Service dá suporte a SSO iniciado por **IDP** (SSO baseado em função)

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Adicionando o Alibaba Cloud Service (SSO baseado em função) da Galeria

Para configurar a integração do Alibaba Cloud Service (SSO baseado em função) ao Azure AD, você precisará adicionar o Alibaba Cloud Service (SSO baseado em função) da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Alibaba Cloud Service (SSO baseado em função) por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Alibaba Cloud Service (SSO baseado em função)** , selecione **Alibaba Cloud Service (SSO baseado em função)** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Alibaba Cloud Service (SSO baseado em função) na lista de resultados](common/search-new-app.png)

5. Na página **Alibaba Cloud Service (SSO baseado em função)** , clique em **Propriedades** no painel de navegação à esquerda, copie a **ID de objeto** e salve-a no computador para uso posterior.

    ![Configuração de propriedades](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)
    
## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o Alibaba Cloud Service (SSO baseado em função) com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Alibaba Cloud Service (SSO baseado em função).

Para configurar e testar o logon único do Azure AD com o Alibaba Cloud Service (SSO baseado em função), você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único baseado em função no Alibaba Cloud Service](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o logon único do Alibaba Cloud Service (SSO baseado em função)](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Alibaba Cloud Service (SSO baseado em função)](#create-alibaba-cloud-service-role-based-sso-test-user)** – para ter um equivalente de Brenda Fernandes no Alibaba Cloud Service (SSO baseado em função) que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Alibaba Cloud Service (SSO baseado em função), execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativo do **Alibaba Cloud Service (SSO baseado em função)** , selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    >[!NOTE]
    >Você obterá os metadados do Provedor de Serviços nesta [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Clique em **Carregar arquivo de metadados**.

    ![image](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![image](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão populados automaticamente na caixa de texto da seção do Alibaba Cloud Service (SSO baseado em função):

    ![image](common/idp-intiated.png)

    > [!Note]
    > Se os valores de **Identificador** e **URL de Resposta** não forem populados automaticamente, preencha-os manualmente de acordo com suas necessidades.

5. O aplicativo Alibaba Cloud Service (SSO baseado em função) espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizados para a configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo **Atributos de Usuário**.

    ![image](common/edit-attribute.png)

6. Além disso, como acima, o aplicativo Alibaba Cloud Service (SSO baseado em função) espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | NOME | Namespace | Atributo de Origem|
    | ---------------| ------------| --------------- |
    | Função | https:\//www.aliyun.com/SAML-Role/Attribute | user.assignedroles |
    | RoleSessionName | https:\//www.aliyun.com/SAML-Role/Attribute | user.userprincipalname |

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) para saber como configurar a **Função** no Azure AD

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Save** (Salvar).

7. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

8. Na seção **Alibaba Cloud Service (SSO baseado em função)** , copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Configurar o logon único baseado em função no Alibaba Cloud Service

1. Entre no [console de RAM](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) do Alibaba Cloud usando Account1.

2. No painel de navegação à esquerda, selecione **SSO**.

3. Na guia **SSO baseado em função**, clique em **Criar IdP**.

4. Na página exibida, insira `AAD` no campo Nome do IdP, insira uma descrição no campo **Observação**, clique em **Carregar** para carregar o arquivo de metadados de federação baixado antes e, em seguida, clique em **OK**.

5. Depois que o IdP for criado com êxito, clique em **Criar Função de RAM**.

6. No campo **Nome da Função de RAM**, insira `AADrole`, selecione `AAD` na lista suspensa **Selecionar IdP** e clique em OK.

    >[!NOTE]
    >Conceda permissão à função conforme necessário. Depois que você criar o IdP e a função correspondente, recomendamos que você salve os ARNs do IdP e a função para uso posterior. Obtenha os ARNs na página de informações do IdP e na página de informações da função.

7. Associe a função de RAM do Alibaba Cloud (AADrole) ao usuário do Azure AD (u2): Para associar a função de RAM ao usuário do Azure AD, você precisará criar uma função no Azure AD seguindo estas etapas:

    a. Entre no [Explorador do Graph do Azure AD](https://developer.microsoft.com/graph/graph-explorer?spm=a2c63.p38356.879954.9.7d904e167h6Yg9).

    b. Clique em **Modificar Permissões** para obter as permissões necessárias para a criação de uma função.

    ![Configuração do Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Selecione as permissões a seguir na lista e clique em **Modificar Permissões**, conforme mostrado na figura a seguir.

    ![Configuração do Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Depois que as permissões forem concedidas, faça logon no Explorador do Graph novamente.

    d. Na página do Explorador do Graph, selecione **GET** na primeira lista suspensa e **beta** na segunda lista suspensa. Em seguida, insira `https://graph.microsoft.com/beta/servicePrincipals` no campo ao lado das listas suspensas e clique em **Executar Consulta**.

    ![Configuração do Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Se você estiver usando vários diretórios, insira `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` no campo da consulta.

    e. Na seção **Visualização da Resposta**, extraia a propriedade appRoles da 'Entidade de Serviço' para uso posterior.

    ![Configuração do Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

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

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>Configurar o logon único do Alibaba Cloud Service (SSO baseado em função)

Para configurar o logon único no **Alibaba Cloud Service (SSO baseado em função)** , é necessário enviar o **XML de Metadados de Federação** baixado e as URLs copiadas adequadas do portal do Azure para a [equipe de suporte do Alibaba Cloud Service (SSO baseado em função)](https://www.aliyun.com/service/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Alibaba Cloud Service (SSO baseado em função).

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Alibaba Cloud Service (SSO baseado em função)** .

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Alibaba Cloud Service (SSO baseado em função)** .

    ![O link do Alibaba Cloud Service (SSO baseado em função) na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na guia **Usuários e grupos**, selecione u2 na lista de usuários e clique em **Selecionar**. Em seguida, clique em **Atribuir**.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

6. Exiba a função atribuída e teste o Alibaba Cloud Service (SSO baseado em função).

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Depois de atribuir o usuário (u2), a função criada será anexada automaticamente ao usuário. Se você tiver criado várias funções, precisará anexar a função apropriada ao usuário, conforme necessário. Caso deseje implementar o SSO baseado em função do Azure AD para várias contas do Alibaba Cloud, repita as etapas anteriores.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Criar um usuário de teste do Alibaba Cloud Service (SSO baseado em função)

Nesta seção, você criará um usuário chamado Brenda Fernandes no Alibaba Cloud Service (SSO baseado em função). Trabalhe com a [equipe de suporte do Alibaba Cloud Service (SSO baseado em função)](https://www.aliyun.com/service/) para adicionar os usuários à plataforma Alibaba Cloud Service (SSO baseado em função). Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único 

Depois que as configurações anteriores forem concluídas, teste o Alibaba Cloud Service (SSO baseado em função) seguindo estas etapas:

1. No portal do Azure, acesse a página **Alibaba Cloud Service (SSO baseado em função)** , selecione **Logon único** e clique em **Testar**.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Clique em **Entrar como o usuário atual**.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Na página de seleção de conta, selecione u2.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. A página a seguir é exibida, indicando que o SSO baseado em função foi bem-sucedido.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

