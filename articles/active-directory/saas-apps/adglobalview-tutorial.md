---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ADP Globalview | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ADP Globalview.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.openlocfilehash: c11dd5ae3bc312cfea1a047c2db4396ba2cb50da
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309026"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp-globalview"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ADP Globalview

Neste tutorial, você aprenderá a integrar o ADP Globalview ao Azure AD (Azure Active Directory). Ao integrar o ADP Globalview ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao ADP Globalview.
* Permitir que os usuários sejam conectados automaticamente ao ADP Globalview com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do ADP Globalview habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O ADP Globalview é compatível com SSO iniciado por **IDP**

## <a name="adding-adp-globalview-from-the-gallery"></a>Adicionando o ADP Globalview por meio da galeria

Para configurar a integração do ADP Globalview ao Azure AD, você precisa adicionar o ADP Globalview à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **ADP Globalview** na caixa de pesquisa.
1. Selecione **ADP Globalview** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp-globalview"></a>Configurar e testar logon único do Azure AD para o ADP Globalview

Configure e teste o SSO do Azure AD com o ADP Globalview usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ADP Globalview.

Para configurar e testar o SSO (logon único) do Azure AD com o ADP GlobalView, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do ADP Globalview](#configure-adp-globalview-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Crie um usuário de teste do ADP Globalview](#create-adp-globalview-test-user)** – para ter um equivalente de B.Fernandes no ADP Globalview vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ADP Globalview**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    
    Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    ```https
    https://<subdomain>.globalview.adp.com/federate
    https://<subdomain>.globalview.adp.com/federate2
    ```


    > [!NOTE]
    > Esse valor não é real. Atualize o valor com o identificador real. Contate a [Equipe de suporte ao cliente do ADP Globalview](https://www.adp.com/contact-us/overview.aspx) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o ADP Globalview**, copie as URLs apropriadas com base em seus requisitos.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao ADP Globalview.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **ADP Globalview**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-adp-globalview-sso"></a>Configurar o SSO do ADP GlobalView

Para configurar o logon único no lado do **ADP Globalview**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do ADP Globalview](https://www.adp.com/contact-us/overview.aspx). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-adp-globalview-test-user"></a>Criar usuário de teste do ADP Globalview

Nesta seção, você criará um usuário chamado B.Fernandes no ADP Globalview. Trabalhe com a [equipe de suporte do ADP Globalview](https://www.adp.com/contact-us/overview.aspx) para adicionar os usuários na plataforma do ADP Globalview. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ADP Globalview no Painel de Acesso, você deverá ser conectado automaticamente ao ADP Globalview no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o ADP GlobalView com o Azure AD](https://aad.portal.azure.com/)