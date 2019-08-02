---
title: 'Tutorial: Integração do Azure Active Directory ao EBSCO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: b6f1198334d8bd4ba58b4fbba02942fc8c54c578
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442855"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Tutorial: Integração do Azure Active Directory ao EBSCO

Neste tutorial, você aprenderá como integrar EBSCO com o Azure Active Directory (Azure AD).
Integrar EBSCO com o Azure AD fornece os seguintes benefícios:

* Você pode controlar no Azure AD que tenha acesso ao EBSCO.
* Você pode permitir que os usuários sejam conectados automaticamente ao EBSCO (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com EBSCO, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do EBSCO

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O EBSCO dá suporte ao SSO iniciado por **SP** e **IdP**

* O EBSCO dá suporte ao provisionamento de usuário **just-in-time**

## <a name="adding-ebsco-from-the-gallery"></a>Adicionando EBSCO da galeria

Para configurar a integração de EBSCO no Azure AD, você precisa adicionar EBSCO da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar EBSCO da galeria, execute as seguintes etapas:**

1. No **[portal do Microsoft Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **EBSCO**, selecione **EBSCO** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![EBSCO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o EBSCO, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do EBSCO.

Para configurar e testar o logon único do Azure AD com EBSCO, você precisa concluir os blocos de construção a seguir:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do EBSCO](#configure-ebsco-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do EBSCO](#create-ebsco-test-user)** – para ter um equivalente de Brenda Fernandes no EBSCO que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o EBSCO, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **EBSCO**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute a seguinte etapa:

    ![Informações de logon único de domínio e URLs do EBSCO](common/idp-identifier.png)

    Na caixa de texto **Identificador**, digite uma URL: `pingsso.ebscohost.com`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![image](common/both-preintegrated-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do EBSCO](mailto:sso@ebsco.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

    o   **Elementos exclusivos:**  

    o   **Custid** = Inserir ID de cliente exclusivo do EBSCO 

    o   **Profile** = Clientes podem personalizar o link para direcionar usuários a um perfil específico (dependendo do que adquirirem do EBSCO). Eles podem digitar uma ID de perfil específica. As IDs principais são eds (Serviço de descoberta de EBSCO) e ehost (bancos de dados do EBSOCOhost). Instruções para isso são fornecidas [aqui](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

6. O aplicativo EBSCO espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo **Atributos de Usuário** .

    ![image](common/edit-attribute.png)

     > [!Note]
    > O atributo **nome** é obrigatório e é mapeado com o **valor Identificador de Nome** no aplicativo EBSCO. Isso é adicionado por padrão, então você não precisa adicioná-lo manualmente.

7. Além do indicado acima, o aplicativo EBSCO espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo: 

    | NOME | Atributo de Origem|
    | ---------------| --------------- |    
    | Nome   | user.givenname |
    | Sobrenome   | user.surname |
    | Email   | user.mail |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Save** (Salvar).

8. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

9. Na seção **Configurar o EBSCO**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-ebsco-single-sign-on"></a>Configurar o logon único do EBSCO

Para configurar o logon único no lado do **EBSCO**, é necessário enviar o **XML de Metadados** baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe de suporte do EBSCO](mailto:sso@ebsco.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

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

Nesta seção, você habilitará Brenda Fernandes para usar logon único do Azure, concedendo acesso ao EBSCO.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **EBSCO**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **EBSCO**.

    ![O link EBSCO na Lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-ebsco-test-user"></a>Criar um usuário de teste do EBSCO

No caso de EBSCO, o provisionamento de usuários é automático.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

O Azure AD passa os dados necessários para o aplicativo EBSCO. O provisionamento de usuário do EBSCO pode ser automático OU exige uma única forma. Depende de se o cliente possui muitas contas de EBSCOhost pré-existentes com configurações pessoais salvadas. O mesmo pode ser discutido com a [equipe de suporte EBSCO](mailto:sso@ebsco.com) durante a implementação. De qualquer forma, o cliente não precisa criar contas de EBSCOhost antes de testar.

   >[!Note]
   >Você pode automatizar o provisionamento/a personalização de usuário de EBSCOhost. Entre em contato com a [equipe de suporte EBSCO](mailto:sso@ebsco.com) sobre o provisionamento just in time do usuário. 

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

1. Quando você clicar no bloco EBSCO no Painel de acesso, você deve ser conectado automaticamente ao seu aplicativo EBSCO.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

2. Depois que fizer logon no aplicativo, clique no botão **entrar** no canto superior direito.

    ![As credenciais do EBSCO na lista Aplicativos](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Você receberá uma solicitação única para emparelhar o logon institucional/SAML com um **Vincular sua conta existente do MyEBSCOhost à sua conta institucional agora** OU **Criar uma nova conta de MyEBSCOhost e vinculá-la à sua conta institucional**. A conta é usada para personalização no aplicativo EBSCOhost. Selecione a opção **Criar uma nova conta** e você verá que o formulário de personalização é previamente concluído com os valores da resposta saml como mostrado na captura de tela abaixo. Clique em **'Continuar'** para salvar essa seleção.
    
     ![O usuário EBSCO na Lista de aplicativos](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Depois de concluir a configuração acima, limpe cookies/cache e faça logon novamente. Você não precisará entrar manualmente mais uma vez, e as configurações de personalização serão lembradas

## <a name="additional-sesources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

