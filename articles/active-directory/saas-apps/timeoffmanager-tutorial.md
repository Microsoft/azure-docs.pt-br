---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao TimeOffManager | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TimeOffManager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 849236b9ac33cec92cc145bb32b4271b73476057
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97608809"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeoffmanager"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao TimeOffManager

Neste tutorial, você aprenderá a integrar o TimeOffManager ao Azure AD (Azure Active Directory). Ao integrar o TimeOffManager ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao TimeOffManager.
* Permitir que os usuários sejam conectados automaticamente ao TimeOffManager com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do TimeOffManager habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.


* O TimeOffManager é compatível com SSO iniciado por **IDP**

* O TimeOffManager é compatível com o provisionamento de usuário **Just-In-Time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.


## <a name="adding-timeoffmanager-from-the-gallery"></a>Adicionar o TimeOffManager da galeria

Para configurar a integração do TimeOffManager ao Azure AD, você precisará adicionar o TimeOffManager da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **TimeOffManager** na caixa de pesquisa.
1. Selecione **TimeOffManager** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-timeoffmanager"></a>Configurar e testar logon único do Azure AD para o TimeOffManager

Configure e teste o SSO do Azure AD com o TimeOffManager usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do TimeOffManager.

Para configurar e testar o SSO do Azure AD com o TimeOffManager, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do TimeOffManager](#configure-timeoffmanager-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
    1. **[Criar usuário de teste do TimeOffManager](#create-timeoffmanager-test-user)** – para ter um equivalente de B.Fernandes no TimeOffManager que esteja vinculado à declaração de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **TimeOffManager**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE]
    > Esse valor não é real. Atualize esse valor com a URL de Resposta real. Você pode obter esse valor de **página Configurações de logon único** que é explicada posteriormente no tutorial ou entrar em contato com a [equipe de suporte do TimeOffManager](https://www.purelyhr.com/contact-us). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo TimeOffManager espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo TimeOffManager espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Atributo de Origem|
    | --- | --- |
    | Firstname |User.givenname |
    | Sobrenome |User.surname |
    | Email |User.mail |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o TimeOffManager**, copie a URL apropriada conforme com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao TimeOffManager.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **TimeOffManager**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-timeoffmanager-sso&quot;></a>Configurar o SSO do TimeOffManager

1. Em outra janela do navegador da Web, entre em seu site de empresa TimeOffManager como um administrador.

2. Vá para **Conta \> Opções da Conta \> Configurações de Logon Único**.
   
    ![A captura de tela mostra a opção Configurações de Logon Único selecionada nas Opções de Conta.](./media/timeoffmanager-tutorial/ic795917.png &quot;Configurações de logon único")

3. Na seção **Configurações de Logon Único** , realize as seguintes etapas:
   
    ![A captura de tela mostra a seção Configurações de Logon Único, na qual você pode inserir os valores descritos.](./media/timeoffmanager-tutorial/ic795918.png "Configurações de logon único")
   
    a. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole todo o Certificado na caixa de texto **Certificado X.509** .
   
    b. Na caixa de texto **Emissor do IdP**, cole o valor de **Identificador do Azure AD** copiado do portal do Azure.
   
    c. Na caixa de texto **URL do Ponto de Extremidade do IdP**, cole o valor da **URL de Logon** copiado do portal do Azure.
   
    d. Para **Impor SAML**, selecione **Não**.
   
    e. Para **Criação Automática de Usuários**, selecione **Sim**.
   
    f. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado no portal do Azure.
   
    g. Clique em **Salvar Alterações**.

4. Na página **Configurações de logon único**, copie o valor da **URL do Serviço do Consumidor de Declaração** e cole-a na caixa de texto **URL de Resposta** na seção **Configuração Básica de SAML** no Portal do Azure. 

    ![A captura de tela mostra o link da URL do Serviço do Consumidor de Declaração.](./media/timeoffmanager-tutorial/ic795915.png "Configurações de logon único")

### <a name="create-timeoffmanager-test-user"></a>Criar usuário de teste do TimeOffManager

Nesta seção, um usuário chamado Brenda Fernandes será criado no TimeOffManager. O TimeOffManager é compatível cm provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no TimeOffManager, um novo será criado após a autenticação.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do TimeOffManager ou as APIs fornecidas pelo TimeOffManager para provisionar as contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco TimeOffManager Manager no Painel de Acesso, você deverá ser conectado automaticamente ao TimeOffManager para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o TimeOffManager com o Azure AD](https://aad.portal.azure.com/)