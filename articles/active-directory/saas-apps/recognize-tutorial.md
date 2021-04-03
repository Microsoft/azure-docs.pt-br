---
title: 'Tutorial: Integração do Azure Active Directory com o Recognize | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Recognize.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 7195a126938deabf542e6fa3e52b72c2efdddac2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515212"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Tutorial: integração do Azure Active Directory com o Recognize

Neste tutorial, você aprende a integrar o Recognize ao Azure AD (Azure Active Directory).
A integração do Recognize ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Recognize.
* Você pode permitir que seus usuários entrem automaticamente no Recognize (logon único) com a conta do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao Recognize, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Recognize

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Recognize é compatível com o SSO iniciado por **SP**

## <a name="adding-recognize-from-the-gallery"></a>Adicionando o Recognize da galeria

Para configurar a integração do Recognize ao Azure AD, você precisará adicionar o Recognize da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Recognize da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Recognize**, selecione **Recognize** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![Recognize na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Recognize, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Recognize.

Para configurar e testar o logon único do Azure AD com o Recognize, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Recognize](#configure-recognize-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Recognize](#create-recognize-test-user)** – para ter um equivalente de Brenda Fernandes no Recognize que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Recognize, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Recognize**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    >[!NOTE]
    >Você obterá o **arquivo de metadados do provedor de serviço**, da seção **Configurar o logon único do Recognize** do tutorial.

    a. Clique em **Carregar arquivo de metadados**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, o valor de **Identificador** será preenchido automaticamente na seção "Configuração Básica do SAML".

    ![Informações de logon único de Domínio e URLs do Recognize](common/sp-identifier.png)

     Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Se o valor de **Identificador** não for preenchido automaticamente, você o receberá ao abrir a URL de metadados do provedor de serviços na seção "Configurações de SSO", explicada posteriormente na seção **Configurar o logon único do Recognize** do tutorial. O valor da URL de logon não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte ao cliente do Recognize](mailto:support@recognizeapp.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Recognize**, copie as URLs corretas, de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-recognize-single-sign-on"></a>Configurar o logon único do Recognize

1. Em uma janela diferente do navegador da Web, entre no locatário do Recognize como um administrador.

2. No canto superior direito, clique em **Menu**. Vá para **Administrador da Empresa**.
   
    ![Captura de tela que mostra a opção Administrador da Empresa selecionada no menu Configurações.](./media/recognize-tutorial/tutorial_recognize_000.png)

3. No painel de navegação esquerdo, clique em **Configurações**.
   
    ![Captura de tela que mostra a opção Configurações selecionada na página de navegação.](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Realize as seguintes etapas na seção **Configurações do SSO**.
   
    ![Captura de tela que mostra a opção Configurações do SAML, na qual você pode inserir os valores descritos.](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Em **Habilitar SSO**, selecione **ATIVADO**.

    b. Na caixa de texto **ID da Entidade de IdP**, cole o valor do **Identificador do Azure AD** que você copiou do portal do Azure.
    
    c. Na caixa de texto **URL de Destino de SSO**, cole o valor da **URL de Logon** que você copiou do portal do Azure.
    
    d. Na caixa de texto **URL de Destino de SLO**, cole o valor da **URL de Logon** que você copiou do portal do Azure. 
    
    e. Abra o arquivo de **Certificado (Base64)** baixado no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado**.
    
    f. Clique no botão **Salvar configurações**. 

5. Ao lado da seção **Configurações de SSO**, copie a URL em **URL de metadados do provedor de serviço**.
   
    ![Captura de tela que mostra a opção Observações, na qual você pode copiar os Metadados do Provedor de Serviços.](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Abra o **link da URL de metadados** em um navegador em branco para baixar o documento de metadados. Em seguida, copie o value(entityID) do EntityDescriptor do arquivo e cole-o na caixa de texto **Identificador** em **Configuração Básica do SAML** do portal do Azure.
    
    ![Captura de tela que mostra uma caixa de texto com XML de texto sem formatação, na qual você pode obter a ID da entidade.](./media/recognize-tutorial/tutorial_recognize_004.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Recognize.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Recognize**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Recognize**.

    ![O link do Recognize na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-recognize-test-user"></a>Criar um usuário de teste do Recognize

Para permitir que os usuários do Azure AD façam logon no Recognize, eles deverão ser provisionados no Recognize. No caso do Recognize, o provisionamento é uma tarefa manual.

Este aplicativo não dá suporte ao provisionamento de SCIM, mas tem uma sincronização de usuário alternativa que provisiona usuários. 

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no site de empresa do Recognize como um administrador.

2. No canto superior direito, clique em **Menu**. Vá para **Administrador da Empresa**.

3. No painel de navegação esquerdo, clique em **Configurações**.

4. Execute as seguintes etapas na seção **Sincronização de usuário**.
   
    ![Novo usuário](./media/recognize-tutorial/tutorial_recognize_005.png "Novo Usuário")
   
    a. Em **Sincronização habilitada**, selecione **ATIVADO**.
   
    b. Em **Escolher o provedor de sincronização**, selecione **Microsoft / Office 365**.
   
    c. Clique em **Executar Sincronização de Usuário**.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Recognize no Painel de Acesso, você deverá ser conectado automaticamente ao Recognize no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)