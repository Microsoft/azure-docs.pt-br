---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EBSCO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e EBSCO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.openlocfilehash: 2f7f017d773e7708b2206029769df7977b12e6f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454325"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EBSCO

Neste tutorial, você aprenderá a integrar o EBSCO ao Azure AD (Azure Active Directory). Quando integrar o EBSCO ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao EBSCO.
* Permitir que os usuários sejam conectados automaticamente ao EBSCO com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do EBSCO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O EBSCO dá suporte ao SSO iniciado por **SP e IDP**
* O EBSCO dá suporte ao provisionamento de usuário **just-in-time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-ebsco-from-the-gallery"></a>Adicionando EBSCO da galeria

Para configurar a integração de EBSCO no Azure AD, você precisa adicionar EBSCO da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **EBSCO** na caixa de pesquisa.
1. Selecione **EBSCO** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>Configurar e testar logon único do Azure AD para o EBSCO

Configure e teste o SSO do Azure AD com o EBSCO usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do EBSCO.

Para configurar e testar o SSO do Azure AD com o EBSCO, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do EBSCO](#configure-ebsco-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do EBSCO](#create-ebsco-test-user)** – para ter um equivalente de B.Fernandes no EBSCO que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **EBSCO**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    Na caixa de texto **Identificador**, digite uma URL: `pingsso.ebscohost.com`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do EBSCO](mailto:support@ebsco.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

    o   **Elementos exclusivos:**  

    o   **Custid** = Inserir ID de cliente exclusivo do EBSCO 

    o   **Profile** = Clientes podem personalizar o link para direcionar usuários a um perfil específico (dependendo do que adquirirem do EBSCO). Eles podem digitar uma ID de perfil específica. As IDs principais são eds (Serviço de descoberta de EBSCO) e ehost (bancos de dados do EBSOCOhost). Instruções para isso são fornecidas [aqui](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

1. O aplicativo EBSCO espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

    > [!Note]
    > O atributo **nome** é obrigatório e é mapeado com o **valor Identificador de Nome** no aplicativo EBSCO. Isso é adicionado por padrão, então você não precisa adicioná-lo manualmente.

1. Além do indicado acima, o aplicativo EBSCO espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem|
    | ---------------| --------------- |
    | Nome   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o EBSCO**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao EBSCO.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **EBSCO**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-ebsco-sso"></a>Configurar o SSO do EBSCO

Para configurar o logon único no lado do **EBSCO**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe de suporte do EBSCO](mailto:support@ebsco.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-ebsco-test-user"></a>Criar um usuário de teste do EBSCO

No caso de EBSCO, o provisionamento de usuários é automático.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

O Azure AD passa os dados necessários para o aplicativo EBSCO. O provisionamento de usuário do EBSCO pode ser automático OU exige uma única forma. Depende de se o cliente possui muitas contas de EBSCOhost pré-existentes com configurações pessoais salvadas. O mesmo pode ser discutido com a [equipe de suporte EBSCO](mailto:support@ebsco.com) durante a implementação. De qualquer forma, o cliente não precisa criar contas de EBSCOhost antes de testar.

   > [!Note]
   > Você pode automatizar o provisionamento/a personalização de usuário de EBSCOhost. Entre em contato com a [equipe de suporte EBSCO](mailto:support@ebsco.com) sobre o provisionamento just in time do usuário.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

1. Quando você clicar no bloco EBSCO no Painel de acesso, você deve ser conectado automaticamente ao seu aplicativo EBSCO.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

1. Depois que fizer logon no aplicativo, clique no botão **entrar** no canto superior direito.

    ![As credenciais do EBSCO na lista Aplicativos](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. Você receberá uma solicitação única para emparelhar o logon institucional/SAML com um **Vincular sua conta existente do MyEBSCOhost à sua conta institucional agora** OU **Criar uma nova conta de MyEBSCOhost e vinculá-la à sua conta institucional**. A conta é usada para personalização no aplicativo EBSCOhost. Selecione a opção **Criar uma nova conta** e você verá que o formulário de personalização é previamente concluído com os valores da resposta saml como mostrado na captura de tela abaixo. Clique em **'Continuar'** para salvar essa seleção.
    
     ![O usuário EBSCO na Lista de aplicativos](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Depois de concluir a configuração acima, limpe cookies/cache e faça logon novamente. Você não precisará entrar manualmente mais uma vez e as configurações de personalização serão lembradas.

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o EBSCO com o Azure AD](https://aad.portal.azure.com/)