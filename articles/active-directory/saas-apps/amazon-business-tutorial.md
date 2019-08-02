---
title: 'Tutorial: Integração do Azure Active Directory ao Amazon Business | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Amazon Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2597a66-afd9-4f11-b14b-646b597bb6c2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7ac085beaa85a7ddf3a6c3bfc61820e8e5a63ea
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68496561"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Tutorial: integrar o Amazon Business ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Amazon Business ao Azure AD (Azure Active Directory). Quando integrar o [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Amazon Business.
* Permitir que os usuários sejam conectados automaticamente ao Amazon Business com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para SSO (logon único) do Amazon Business. Acesse a página do [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) para criar uma conta do Amazon Business.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em uma conta existente do Amazon Business.

* O Amazon Business é compatível com SSO iniciado por **SP e IDP**
* O Amazon Business dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-amazon-business-from-the-gallery"></a>Adicionando o Amazon Business por meio da galeria

Para configurar a integração do Amazon Business ao Azure AD, é necessário adicionar o Amazon Business à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Amazon Business** na caixa de pesquisa.
1. Selecione **Amazon Business** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Amazon Business usando um usuário de teste chamado **B.Fernandes**.

Para configurar e testar o SSO do Azure AD com o Amazon Business, conclua as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do Amazon Business](#configure-amazon-business-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Amazon Business](#create-amazon-business-test-user)** para ter um equivalente de B.Fernandes no Amazon Business que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Amazon Business**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar no modo iniciado por **IDP**:

    1. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando um dos seguintes padrões:
    
       | | |
       |-|-|
       | `https://www.amazon.com`|
       | `https://www.amazon.co.jp`|
       | `https://www.amazon.de`|

    1. Na caixa de texto **URL de Resposta**, digite uma URL nos seguintes padrões:
    
       | | |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`|

       > [!NOTE]
       > O valor de URL de Resposta não é real. Atualize esse valor com a URL de Resposta real. Você obterá o valor `<idpid>` da seção de configuração de SSO do Amazon Business, explicada posteriormente no tutorial. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite uma URL: `https://www.amazon.com/`

1. A captura de tela a seguir mostra a lista de atributos padrão. Edite os atributos clicando no ícone **Editar** na seção **Atributos e Declarações do Usuário**.

    ![Atributos](media/amazon-business-tutorial/map-attribute3.png)

1. Edite os atributos e copie o valor do **Namespace** desses atributos no Bloco de notas.

    ![Atributos](media/amazon-business-tutorial/map-attribute4.png)

1. Além do indicado acima, o aplicativo Amazon Business espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Atributos e Declarações de Usuário** na caixa de diálogo **Declarações de Grupo**, execute as seguintes etapas:

    a. Clique na **caneta** ao lado de **Grupos retornados na declaração**.

    ![image](./media/amazon-business-tutorial/config04.png)

    ![image](./media/amazon-business-tutorial/config05.png)

    b. Selecione **Todos os Grupos** na lista de opções.

    c. Selecione **ID do Grupo** como o **Atributo de origem**.

    d. Marque a caixa de seleção **Personalizar o nome da declaração de grupo** e insira o nome do grupo de acordo com as necessidades de sua organização.

    e. Clique em **Save** (Salvar).

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Assinatura SAML**, localize **XML de Metadados** e selecione **Baixar** para baixar o certificado e salvá-lo em seu computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Amazon Business**, copie as URLs apropriadas com base em suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Configurar o SSO do Amazon Business

1. Em outra janela do navegador da Web, entre como um administrador em seu site da empresa do Amazon Business.

1. Clique em **Perfil do Usuário** e selecione **Configurações de Negócios**.

    ![Perfil do Usuário](media/amazon-business-tutorial/user-profile.png)

1. No assistente de **Integrações do sistema**, selecione **SSO (Logon Único)** .

    ![SSO (Logon único)](media/amazon-business-tutorial/sso-settings.png)

1. No assistente **Configurar o SSO**, selecione o provedor de acordo com os requisitos organizacionais e clique em **Avançar**.

    ![Grupo padrão](media/amazon-business-tutorial/default-group1.png)

1. No assistente **Padrões para novas contas de usuário**, selecione o **Grupo Padrão** e, em seguida, selecione a **Função de Compra Padrão** de acordo com a função do usuário na organização e clique em **Avançar**.

    ![Grupo padrão](media/amazon-business-tutorial/dafault-group2.png)

1. No assistente **Carregar o arquivo de metadados**, clique em **Procurar** para carregar o arquivo **XML de metadados** que você baixou do portal do Azure e clique em **Carregar**.

    ![Dados de conexão](media/amazon-business-tutorial/connection-data1.png)

1. Após o upload do arquivo de metadados baixado, os campos na seção **Dados de conexão** serão populados automaticamente. Depois disso, clique em **Avançar**.

    ![Dados de conexão](media/amazon-business-tutorial/connection-data2.png)

1. No assistente **Carregar sua instrução de atributo**, clique em **Ignorar**.

    ![Atributos](media/amazon-business-tutorial/map-attribute1.png)

1. No assistente **Mapeamento de atributos**, adicione os campos de requisito clicando na opção **+ Adicionar um campo**. Adicione os valores de atributo (incluindo o namespace que você copiou da seção **Atributos e Declarações do Usuário** do portal do Azure) para o campo **AttributeName SAML** e clique em **Avançar**.

    ![Atributos](media/amazon-business-tutorial/map-attribute2.png)

1. No assistente **Dados de conexão do Amazon**, clique em **Avançar**.

    ![Conexão](media/amazon-business-tutorial/amazon-connect.png)

1. Verifique o **Status** das etapas que foram configuradas e clique em **Iniciar testes**.

    ![Conexão](media/amazon-business-tutorial/sso-connection1.png)

1. No assistente **Testar Conexão de SSO**, clique em **Testar**.

    ![Conexão](media/amazon-business-tutorial/sso-connection2.png)

1. No assistente **URL iniciada por IDP**, antes de clicar em **Ativar**, copie o valor atribuído a **idpid** e cole-o no parâmetro **idpid** na **URL de Resposta** na seção de **Configuração Básica do SAML** no portal do Azure.

    ![Conexão](media/amazon-business-tutorial/sso-connection3.png)

1. No assistente **Você está pronto para alternar para o SSO ativo?** , marque a caixa de seleção **Testei integralmente o SSO e estou pronto para colocá-lo no ar** e clique **Alternar para ativo**.

    ![Conexão](media/amazon-business-tutorial/sso-connection4.png)

1. Por fim, na seção **Detalhes da Conexão de SSO**, o **Status** é mostrado como **Ativo**.

    ![Conexão](media/amazon-business-tutorial/sso-connection5.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

> [!NOTE]
> Os administradores precisam criar os usuários de teste no locatário deles, se necessário. As etapas a seguir mostram como criar um usuário de teste.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Criar um grupo de segurança do Microsoft Azure Active Directory no portal do Azure

1. Clique em **Azure Active Directory > Todos os grupos**.

    ![Criar um grupo de segurança do Azure AD](./media/amazon-business-tutorial/all-groups-tab.png)

1. Clique em **Novo grupo**:

    ![Criar um grupo de segurança do Azure AD](./media/amazon-business-tutorial/new-group-tab.png)

1. Preencha **Tipo de grupo**, **Nome do grupo**, **Descrição do grupo**, **Tipo de associação**. Clique na seta para selecionar membros; em seguida, pesquise ou clique no membro que deseja adicionar ao grupo. Clique em **Selecionar** para adicionar os membros selecionados e, em seguida, clique em **Criar**.

    ![Criar um grupo de segurança do Azure AD](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Amazon Business.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Amazon Business**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

    >[!NOTE]
    > Se você não atribuir os usuários no Azure AD, obterá o erro a seguir.

    ![O link Adicionar Usuário](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Atribuir o grupo de segurança do Microsoft Azure Active Directory no portal do Azure

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, selecione **Amazon Business**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Amazon Business**.

    ![O link do Amazon Business na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Clique em **Adicionar usuário**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Pesquise o grupo de segurança que você deseja usar e clique no grupo para adicioná-lo à seção Selecionar membros. Clique em **Selecionar** e, em seguida, clique em **Atribuir**.

    ![Pesquisar grupo de segurança](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Verifique as notificações na barra de menus para ser avisado de que o grupo foi atribuído com êxito ao aplicativo empresarial no portal do Azure.

### <a name="create-amazon-business-test-user"></a>Criar um usuário de teste do Amazon Business

Nesta seção, será criado um usuário chamado B.Fernandes no Amazon Business. O Amazon Business dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Amazon Business, será criado um após a autenticação.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Amazon Business no Painel de Acesso, você deverá ser conectado automaticamente ao Amazon Business para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
