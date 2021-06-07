---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SSO da Discovery Benefits | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO da Discovery Benefits.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.openlocfilehash: 0e5d67e00ee56b5c4006a8422c713e3cabb32bfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454755"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SSO da Discovery Benefits

Neste tutorial, você aprenderá a integrar o SSO da Discovery Benefits ao Azure AD (Azure Active Directory). Ao integrar o SSO da Discovery Benefits no Azure AD, você pode:

* Controlar no Azure AD que tem acesso ao SSO da Discovery Benefits.
* Permitir que os usuários sejam conectados automaticamente ao SSO da Discovery Benefits com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do SSO da Discovery Benefits.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SSO da Discovery Benefits é compatível com SSO iniciado por **IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>Adicionando o SSO da Discovery Benefits por meio da Galeria

Para configurar a integração do SSO da Discovery Benefits ao Azure AD, é necessário adicionar o SSO da Discovery Benefits por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **SSO da Discovery Benefits** na caixa de pesquisa.
1. Selecione **SSO da Discovery Benefits** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>Configurar e testar o logon único do Azure AD para o SSO da Discovery Benefits

Configure e teste o SSO do Azure AD com o SSO da Discovery Benefits usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SSO da Discovery Benefits.

Para configurar e testar o SSO do Azure AD com o SSO da Discovery Benefits, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SSO da Discovery Benefits](#configure-discovery-benefits-sso-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do SSO da Discovery Benefits](#create-discovery-benefits-sso-test-user)** – para ter um equivalente de B.Fernandes no SSO da Discovery Benefits que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SSO da Discovery Benefits**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado no modo iniciado do **IDP** e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.

1. O aplicativo do SSO da Discovery Benefits espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizados para a configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo Atributos de usuário.

    ![image](common/edit-attribute.png)

    a. Clique no ícone **Editar** para abrir a caixa de diálogo **Identificador Exclusivo do Usuário (ID do Nome)** .

    ![Captura de tela que mostra a seção "Atributos e Declarações do Usuário" com as reticências "Declaração necessária" no lado direito selecionadas.](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![Configuração de SSO da Discovery Benefits](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. Clique no ícone **Editar** para abrir a caixa de diálogo **Gerenciar transformação**.

    c. Na caixa de texto **Transformação**, digite a **ToUppercase()** mostrada para essa linha.

    d. Na caixa de texto **Parâmetro 1**, digite o parâmetro como `<Name Identifier value>`.

    e. Clique em **Adicionar**.

    > [!NOTE]
    > O SSO da Discovery Benefits exige que um valor de cadeia de caracteres fixo seja passado no campo **Identificador Exclusivo do Usuário (ID do Nome)** para que essa integração funcione. Atualmente, o Azure AD não é compatível com esse recurso, portanto, como uma solução alternativa, você pode usar transformações de NameID **ToUpper** ou **ToLower** para definir um valor de cadeia de caracteres fixo, conforme mostrado acima na captura de tela.

    f. Nós preenchemos automaticamente as declarações adicionais que são necessárias para a configuração de SSO (`SSOInstance` e `SSOID`). Use o ícone **Editar** para mapear os valores de acordo com sua organização.

    ![Captura de tela que mostra "Atributos e Declarações do Usuário" com os valores "Instância de SSO" e "ID de SSO" realçados.](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o SSO da Discovery Benefits**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao SSO da Discovery Benefits.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SSO da Discovery Benefits**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-discovery-benefits-sso-sso"></a>Configurar SSO do SSO da Discovery Benefits

Para configurar o logon único no lado do **SSO da Discovery Benefits**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do SSO da Discovery Benefits](mailto:Jsimpson@DiscoveryBenefits.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-discovery-benefits-sso-test-user"></a>Criar usuário de teste do SSO da Discovery Benefits

Nesta seção, você criará uma usuária chamada Brenda Fernandes no SSO da Discovery Benefits. Trabalhe com a [equipe de suporte do Discovery Benefits SSO](mailto:Jsimpson@DiscoveryBenefits.com) para adicionar os usuários na plataforma do Discovery Benefits SSO. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SSO da Discovery Benefits no Painel de Acesso, você deverá ser conectado automaticamente ao SSO da Discovery Benefits, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o SSO da Discovery Benefits com o Azure AD](https://aad.portal.azure.com/)