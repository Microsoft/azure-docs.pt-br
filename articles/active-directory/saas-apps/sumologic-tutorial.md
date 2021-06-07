---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o SumoLogic | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SumoLogic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: 2dcc52688cabebaa6eb813e3240150ea8774e716
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521884"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SumoLogic

Neste tutorial, você aprenderá a integrar o SumoLogic ao Azure AD (Azure Active Directory). Ao integrar o SumoLogic ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao SumoLogic.
* Permitir que os usuários, usando as respectivas contas do Azure AD, sejam conectados automaticamente ao SumoLogic.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do SumoLogic habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SumoLogic é compatível com o SSO iniciado por **IDP**

## <a name="adding-sumologic-from-the-gallery"></a>Adicionando SumoLogic pela galeria

Para configurar a integração do SumoLogic ao Azure AD, você precisará adicionar o SumoLogic pela galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **SumoLogic** na caixa de pesquisa.
1. Selecione **SumoLogic** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>Configurar e testar o logon único do Azure AD para o SumoLogic

Configure e teste o SSO do Azure AD com o SumoLogic usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SumoLogic.

Para configurar e testar o SSO do Azure AD com o SumoLogic, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SumoLogic](#configure-sumologic-sso)** – para configurar o logon único no lado do aplicativo.
    * **[Criar um usuário de teste do SumoLogic](#create-sumologic-test-user)** – para que haja um equivalente de B. Fernandes no SumoLogic que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SumoLogic**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    - `https://service.sumologic.com`
    - `https://<tenantname>.us2.sumologic.com`
    - `https://<tenantname>.us4.sumologic.com`
    - `https://<tenantname>.eu.sumologic.com`
    - `https://<tenantname>.jp.sumologic.com`
    - `https://<tenantname>.de.sumologic.com`
    - `https://<tenantname>.ca.sumologic.com`

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:

    - `https://service.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte do Cliente do SumoLogic](https://www.sumologic.com/contact-us/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo SumoLogic espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo SumoLogic espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    |  Nome | Atributo de Origem |
    | ---------------| --------------- |
    | Nome | user.givenname |
    | LastName | user.surname |
    | Funções | user.assignedroles |

    > [!NOTE]
    > Clique [aqui](../develop/active-directory-enterprise-app-role-management.md) para saber como configurar a **Função** no Azure AD.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o SumoLogic**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure ao conceder a ela o acesso ao SumoLogic.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SumoLogic**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sumologic-sso"></a>Configurar o SSO do SumoLogic

1. Em outra janela do navegador da Web, entre no site da empresa do SumoLogic como administrador.

1. Vá para **Gerenciar \> Segurança**.

    ![Gerenciar](./media/sumologic-tutorial/ic778556.png "Gerenciar")

1. Clique em **SAML**.

    ![Configurações de globais de segurança](./media/sumologic-tutorial/ic778557.png "Configurações de globais de segurança")

1. Na lista **Selecionar uma configuração ou criar uma nova**, selecione **Azure AD** e clique em **Configurar**.

    ![A captura de tela mostra Configurar SAML 2.0, em que você pode selecionar o Azure A D.](./media/sumologic-tutorial/ic778558.png "Configurar SAML 2.0")

1. No diálogo **Configurar SAML 2.0** , realize as seguintes etapas:

    ![A captura de tela mostra a caixa de diálogo Configurar SAML 2.0, em que você pode inserir os valores descritos.](./media/sumologic-tutorial/ic778559.png "Configurar SAML 2.0")

    a. Na caixa de texto **Nome da Configuração**, digite **Azure AD**.

    b. Selecione **Modo de Depuração**.

    c. Na caixa de texto **Issuer** (Emissor), cole o valor de **Identificador do Azure AD**, copiado do portal do Azure.

    d. Na caixa de texto **URL de Solicitação do Authn**, cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole todo o Certificado na caixa de texto **Certificado X.509** .

    f. Para **Atributo de Email**, selecione **Usar entidade do SAML**.  

    g. Selecione **Configuração de Logon iniciada pelo SP**.

    h. Na caixa de texto **Caminho de Logon**, digite **Azure** e clique em **Salvar**.

### <a name="create-sumologic-test-user"></a>Criar um usuário de teste do SumoLogic

Para que os usuários do Azure AD possam entrar no SumoLogic, eles precisam ser provisionados no SumoLogic. No caso do SumoLogic, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. entre no seu locatário do **SumoLogic**.

1. Vá para **Gerenciar \> Usuários**.

    ![A captura de tela mostra a opção Usuários selecionada no menu Gerenciar.](./media/sumologic-tutorial/ic778561.png "Usuários")

1. Clique em **Adicionar**.

    ![A captura de tela mostra o botão Adicionar para Usuários.](./media/sumologic-tutorial/ic778562.png "Usuários")

1. No diálogo **Novo Usuário** , realize as seguintes etapas:

    ![Novo usuário](./media/sumologic-tutorial/ic778563.png "Novo Usuário")

    a. Digite as informações relacionadas da conta do Azure AD que você deseja provisionar nas caixas de texto **Nome**, **Sobrenome** e **Email**.
  
    b. Selecione uma função.
  
    c. Para **Status**, selecione **Ativo**.
  
    d. Clique em **Save** (Salvar).

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do SumoLogic ou as APIs fornecidas pelo SumoLogic para provisionar as contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SumoLogic no Painel de Acesso, você será conectado automaticamente ao InTime para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o SumoLogic com o Azure AD](https://aad.portal.azure.com/)