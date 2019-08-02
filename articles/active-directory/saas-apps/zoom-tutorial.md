---
title: 'Tutorial: Integração do Azure Active Directory ao Zoom | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zoom.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 597c05e7018b1eb9dd20555458e67b515113ca2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086373"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Tutorial: Integração do Azure Active Directory ao Zoom

Neste tutorial, você aprenderá a integrar o Zoom ao Azure AD (Azure Active Directory).
A integração do Zoom ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Zoom.
* Você pode permitir que seus usuários entrem automaticamente no Zoom (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Zoom, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Zoom habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Zoom dá suporte ao SSO iniciado por **SP**

## <a name="adding-zoom-from-the-gallery"></a>Adicionar o Zoom da galeria

Para configurar a integração do Zoom com o Azure AD, você precisará adicionar o Zoom da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zoom da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zoom**, selecione **Zoom** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Ampliar a lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Zoom, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zoom.

Para configurar e testar o logon único do Azure AD com o Zoom, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Zoom](#configure-zoom-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Zoom](#create-zoom-test-user)** – para ter um equivalente de Brenda Fernandes no Zoom vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Zoom, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Zoom**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Zoom](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.zoom.us`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `<companyname>.zoom.us`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do Zoom](https://support.zoom.us/hc/en-us) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo Zoom espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo **Atributos de Usuário**.

    ![image](common/edit-attribute.png)

6. Além do indicado acima, o aplicativo Zoom espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:
    
    | NOME | Namespace  |  Atributo de Origem|
    | ---------------| --------------- | --------- |
    | Endereço de email  | user.mail  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail` |
    | Nome  | user.givenname  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |
    | Sobrenome  | user.surname  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |
    | Número de telefone  | user.telephonenumber  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone` |
    | department  | user.department  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department` |
    | função |    user.assignedrole |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/role` |

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) para saber como configurar a Função no Azure AD

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Escolha Origem como **Atributo**.

    d. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    e. Clique em **Ok**

    f. Clique em **Save** (Salvar).

    > [!NOTE]
    > O Zoom pode esperar uma declaração de grupo no conteúdo SAML, portanto, se você tiver criado algum grupo, contate a [equipe de suporte ao cliente do Zoom](https://support.zoom.us/hc/en-us) com as informações de grupo para que eles também possam configurar essas informações de grupo. Você também precisará fornecer a ID do Objeto para a [equipe de suporte ao cliente do Zoom](https://support.zoom.us/hc/en-us) para que eles possam configurar. Siga o [documento](https://support.zoom.us/hc/en-us/articles/115005887566) para obter a ID de Objeto.

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

8. Na seção **Configurar o Zoom**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-zoom-single-sign-on"></a>Configurar logon único do Zoom

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Zoom como administrador.

2. Clique na guia **Logon Único** .

    ![Guia Logon único](./media/zoom-tutorial/ic784700.png "Logon único")

3. Clique na guia **Controle de Segurança** e vá para as configurações de **Logon Único**.

4. Na seção de Configurações de Logon Único, execute as seguintes etapas:

    ![Seção Logon único](./media/zoom-tutorial/ic784701.png "Logon único")

    a. Na caixa de texto **URL da página de entrada**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Para o valor de **URL de página de logout**, você precisa acessar o portal do Azure e clicar em **Azure Active Directory** à esquerda e, em seguida, navegar até **Registros de aplicativo**.

    ![O botão Azure Active Directory](./media/zoom-tutorial/appreg.png)

    c. Clique em **Pontos de extremidade**

    ![O botão Ponto de extremidade](./media/zoom-tutorial/endpoint.png)

    d. Copie o **PONTO DE EXTREMIDADE DE SAÍDA DO SAML-P** e cole-o na caixa de texto da **URL de página de saída**.

    ![O botão Copiar ponto de extremidade](./media/zoom-tutorial/endpoint1.png)

    e. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do provedor de identidade** .

    f. Na caixa de texto **Emissor**, cole o valor de **Identificador do Azure AD**, copiado do portal do Azure. 

    g. Clique em **Save** (Salvar).

    > [!NOTE]
    > Para obter mais informações, visite a documentação de zoom [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Zoom.

1. No portal do Azure, escolha **Aplicativos empresariais**, escolha **Todos os aplicativos** e, em seguida, escolha **Zoom**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e escolha **Zoom**.

    ![O link do Zoom na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-zoom-test-user"></a>Criar um usuário de teste do Zoom

Para permitir que os usuários do Azure AD façam logon no Zoom, eles devem estar provisionados no Zoom. No caso do Zoom, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Faça logon em seu site de empresa do **Zoom** como administrador.

2. Clique na guia **Gerenciamento de Contas** e, em seguida, clique em **Gerenciamento de Usuários**.

3. Na seção Gerenciamento de Usuários, clique em **Adicionar usuários**.

    ![Gerenciamento de usuário](./media/zoom-tutorial/ic784703.png "Gerenciamento de usuário")

4. Na página **Adicionar usuários** , realize as seguintes etapas:

    ![Adicionar Usuários](./media/zoom-tutorial/ic784704.png "Adicionar Usuários")

    a. Para **Tipo de Usuário**, selecione **Básico**.

    b. Na caixa de texto **Emails**, digite o endereço de email de uma conta de Azure AD válida que você deseja provisionar.

    c. Clique em **Adicionar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário do Zoom ou as APIs fornecidas pelo Zoom para provisionar contas de usuário do Azure Active Directory.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Zoom no Painel de Acesso, você deverá ser conectado automaticamente ao Zoom no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
