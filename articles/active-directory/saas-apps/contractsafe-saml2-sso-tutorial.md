---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ContractSafe Saml2 SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ContractSafe Saml2 SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.openlocfilehash: 2d236b9910e2eda8e574d020544d625a68aefb81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455299"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Tutorial: Integrar o SSO (logon único) do Azure Active Directory ao ContractSafe Saml2 SSO

Neste tutorial, você aprende a integrar o ContractSafe Saml2 SSO ao Azure AD (Azure Active Directory). Ao integrar o ContractSafe Saml2 SSO ao Azure AD, você pode:

* Controlar quem tem acesso ao ContractSafe Saml2 SSO no Azure AD.
* Permitir que os usuários entrem automaticamente no ContractSafe Saml2 SSO com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa do seguinte:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do ContractSafe Saml2 SSO com SSO habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O ContractSafe Saml2 SSO é compatível com SSO iniciado por **IDP**.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>Adicionar o ContractSafe Saml2 SSO da galeria

Para configurar a integração do ContractSafe Saml2 SSO ao Azure AD, é necessário adicionar o ContractSafe Saml2 SSO à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **ContractSafe Saml2 SSO** na caixa de pesquisa.
1. Selecione **ContractSafe Saml2 SSO** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Configurar e testar o SSO do Azure AD para o ContractSafe Saml2 SSO

Configure e teste o SSO do Azure AD com o ContractSafe Saml2 SSO usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ContractSafe Saml2 SSO.

Para configurar e testar o SSO do Azure AD com o ContractSafe Saml2 SSO, conclua os seguintes blocos de construção:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.
   * [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o SSO do Azure AD usando a conta de **B.Fernandes**.
   * [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) – para permitir que **B.Fernandes** use o SSO do Azure AD.

1. [Configurar o ContractSafe Saml2 SSO](#configure-contractsafe-saml2-sso) para definir as configurações de SSO no lado do aplicativo.
   * [Criar um usuário de teste do ContractSafe Saml2 SSO](#create-a-contractsafe-saml2-sso-test-user) para ter um equivalente de **B.Fernandes** no ContractSafe Saml2 SSO que esteja vinculado à declaração da usuária no Azure AD.
2. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ContractSafe Saml2 SSO**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone editar (caneta) da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os seguintes valores nos campos correspondentes:

    a. Na caixa de texto **Identificador**, insira uma URL usando o seguinte formato: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. Na caixa de texto **URL de Resposta**, insira uma URL usando o seguinte formato: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [Equipe de suporte ao cliente do ContractSafe Saml2 SSO](mailto:support@contractsafe.com) para obter esses valores. Veja também os formatos mostrados na seção **Configuração Básica do SAML** no portal do Azure.

1. O ContractSafe Saml2 SSO espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizados para a configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![Atributos padrão comuns](common/default-attributes.png)

1. Além dos atributos padrão, o aplicativo ContractSafe Saml2 SSO espera que mais alguns atributos sejam passados novamente na resposta SAML. Esses atributos são populados previamente, mas você pode analisá-los de acordo com suas necessidades. A lista a seguir mostra os atributos adicionais.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | emailname | user.userprincipalname |
    | email | user.onpremisesuserprincipalname |

1. Na página **Configurar logon único com SAML** na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação**. Selecione **Baixar** para baixar o certificado e salvá-lo em seu computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o ContractSafe Saml2 SSO**, copie as URLs apropriadas com base em suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada **B.Fernandes**.

1. No painel esquerdo no portal do Azure, selecione **Azure Active Directory**. Selecione **Usuários** e, em seguida, selecione **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira um endereço de email no formato: `username@companydomain.extension`. Um exemplo é `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Selecione **Criar**.

## <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que **B.Fernandes** use o SSO do Azure permitindo acesso ao ContractSafe Saml2 SSO.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **ContractSafe Saml2 SSO**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

   ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista **Usuários**. Em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione o botão **Atribuir**.

## <a name="configure-contractsafe-saml2-sso"></a>Configurar o ContractSafe Saml2 SSO

Para configurar o SSO no lado do **ContractSafe Saml2 SSO**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do ContractSafe Saml2 SSO](mailto:support@contractsafe.com). A equipe é responsável por configurar a conexão de SSO de SAML corretamente em ambos os lados.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>Criar um usuário de teste do ContractSafe Saml2 SSO

Crie uma usuária chamada B. Fernandes no ContractSafe Saml2 SSO. Trabalhe com a [equipe de suporte do ContractSafe Saml2 SSO](mailto:support@contractsafe.com) para adicionar os usuários à plataforma do ContractSafe Saml2 SSO. Os usuários devem ser criados e ativados antes de você usar o SSO.

## <a name="test-sso"></a>Testar o SSO

Teste sua configuração de SSO do Microsoft Azure Active Directory usando o Painel de Acesso. Ao selecionar o bloco do ContractSafe Saml2 SSO no Painel de Acesso, você deverá ser conectado automaticamente ao ContractSafe Saml2 SSO para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS ao Azure Active Directory](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o ContractSafe Saml2 SSO com o Azure AD](https://aad.portal.azure.com/)