---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao LinkedIn Sales Navigator | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LinkedIn Sales Navigator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: 3f4d4d1e0fc44b9fb031ce5e3e45219fa8169562
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458499"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao LinkedIn Sales Navigator

Neste tutorial, você aprenderá a integrar o LinkedIn Sales Navigator ao Azure AD (Azure Active Directory). Ao integrar o LinkedIn Sales Navigator ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao LinkedIn Sales Navigator.
* Permitir que os usuários sejam conectados automaticamente ao LinkedIn Sales Navigator com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do LinkedIn Sales Navigator habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O LinkedIn Sales Navigator é compatível com SSO iniciado por **SP e IDP**
* O LinkedIn Sales Navigator é compatível com o provisionamento de usuário **Just-In-Time**
* O LinkedIn Sales Navigator é compatível com o [provisionamento de usuário **Automatizado**](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Adicionar LinkedIn Sales Navigator da galeria

Para configurar a integração do LinkedIn Sales Navigator com o Azure AD, é necessário adicionar o LinkedIn Sales Navigator da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **LinkedIn Sales Navigator** na caixa de pesquisa.
1. Selecione **LinkedIn Sales Navigator** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-sales-navigator"></a>Configurar e testar o logon único do Azure AD para o LinkedIn Sales Navigator

Configure e teste o SSO do Azure AD com o LinkedIn Sales Navigator com um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no LinkedIn Sales Navigator.

Para configurar e testar o SSO do Azure AD com o LinkedIn Sales Navigator, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar SSO do LinkedIn Sales Navigator](#configure-linkedin-sales-navigator-sso)** – para configurar as definições de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do LinkedIn Sales Navigator](#create-linkedin-sales-navigator-test-user)** – para ter um equivalente de B.Fernandes no LinkedIn Sales Navigator que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **LinkedIn Sales Navigator**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador**, insira o valor **ID da Entidade**, você copiará o valor da ID da Entidade do Portal do LinkedIn explicado posteriormente neste tutorial.

    b. Na caixa de texto **URL de Resposta**, insira o valor da **URL do ACS (Serviço do Consumidor de Declaração)** , você copiará o valor da URL do ACS (Serviço do Consumidor de Declaração) do Portal do LinkedIn explicado posteriormente neste tutorial.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. O aplicativo LinkedIn Sales Navigator espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributos personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo LinkedIn Sales Navigator espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem|
    | --- | --- |
    | email| user.mail |
    | department| user.department |
    | nome| user.givenname |
    | sobrenome| user.surname |
    | Identificador Exclusivo do Usuário | user.mail |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o LinkedIn Sales Navigator**, copie a URL apropriada conforme seus requisitos.

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

Nesta seção, ao conceder acesso ao LinkedIn Sales Navigator a B.Fernandes, você permitirá que ela use o logon único do Azure.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **LinkedIn Sales Navigator**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-linkedin-sales-navigator-sso"></a>Configurar SSO do LinkedIn Sales Navigator

1. Em uma janela diferente do navegador da Web, faça logon em seu site do **LinkedIn Sales Navigator** como administrador.

1. Em **Centro de Contas**, clique em **Configurações Globais** em **Configurações**. Além disso, selecione **Sales Navigator** na lista suspensa.

    ![A captura de tela mostra as Configurações de Autenticação em que você pode selecionar Sales Navigator.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Clique em **OU Clique aqui para carregar e copiar campos individuais do formulário** e execute as seguintes etapas:

    ![A captura de tela mostra o Logon Único em que você pode inserir os valores descritos.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Copie a **ID da Entidade** e cole-a na caixa de texto **Identificador** na **Configuração Básica de SAML** no portal do Azure.

    b. Copie a **URL do ACS (Serviço do Consumidor de Declaração)** e cole-a na caixa de texto **URL e Resposta** na **Configuração Básica de SAML** no portal do Azure.

1. Vá para a seção **Configurações de administração do LinkedIn**. Carregue o arquivo XML que você baixou do portal do Azure clicando na opção **Carregar arquivo XML**.

    ![A captura de tela mostra Definir as configurações de SSO do provedor de serviços do LinkedIn em que você pode carregar um arquivo XML.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Clique em **Ativar** para habilitar o SSO. O status do SSO é alterado de **Não Conectado** para **Conectado**

    ![A captura de tela mostra o Logon Único em que você pode habilitar Autenticar usuários com SSO.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>Criar um usuário de teste do LinkedIn Sales Navigator

O aplicativo LinkedIn Sales Navigator dá suporte ao provisionamento de usuário JIT (Just-In-Time) e, após a autenticação, os usuários são automaticamente criados no aplicativo. Ative **atribuir licenças automaticamente** para atribuir uma licença ao usuário.

   ![Criação de um usuário de teste do AD do Azure](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do LinkedIn Sales Navigator no Painel de Acesso, deverá ser conectado automaticamente ao LinkedIn Sales Navigator para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o LinkedIn Sales Navigator com o Azure AD](https://aad.portal.azure.com/)