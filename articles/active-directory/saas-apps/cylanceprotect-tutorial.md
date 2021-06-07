---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao CylancePROTECT | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o CylancePROTECT.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/27/2020
ms.author: jeedes
ms.openlocfilehash: 2fd21731513d0b32a96a74a822e38075ad1d8eb2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454959"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cylanceprotect"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao CylancePROTECT

Neste tutorial, você aprenderá a integrar o CylancePROTECT ao Azure AD (Azure Active Directory). Ao integrar o CylancePROTECT ao Azure AD, você poderá:

* Controlar quem tem acesso ao CylancePROTECT no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao CylancePROTECT com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do CylancePROTECT.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O CylancePROTECT dá suporte ao SSO iniciado por **IDP**

## <a name="adding-cylanceprotect-from-the-gallery"></a>Adicionar o CylancePROTECT da Galeria

Para configurar a integração do CylancePROTECT ao Azure AD, você precisa adicionar o CylancePROTECT por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **CylancePROTECT** na caixa de pesquisa.
1. Selecione **CylancePROTECT** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cylanceprotect"></a>Configurar e testar o logon único do Azure AD para o CylancePROTECT

Configure e teste o SSO do Azure AD com o CylancePROTECT usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no CylancePROTECT.

Para configurar e testar o SSO do Azure AD com o CylancePROTECT, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do CylancePROTECT](#configure-cylanceprotect-sso)** – para configurar o logon único no lado do aplicativo.
    * **[Criar usuário de teste do CylancePROTECT](#create-cylanceprotect-test-user)** – para ter um equivalente de B.Fernandes no CylancePROTECT que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **CylancePROTECT**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, digite a URL: 
    
    | Região | Valor de URL |
    |----------|---------|
    | Nordeste da Ásia-Pacífico (APNE1)| `https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Sudeste da Ásia-Pacífico (APNE1) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | Europa Central (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | América do Norte|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | América do Sul (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

    b. Na caixa de texto **URL de Resposta**, digite a URL: 
    
    | Região | Valor de URL |
    |----------|---------|
    | Nordeste da Ásia-Pacífico (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Sudeste da Ásia-Pacífico (APNE1)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Europa Central (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | América do Norte|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | América do Sul (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

1. O aplicativo CylancePROTECT espera as declarações de SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo CylancePROTECT espera que **nameidentifier** seja mapeado com **user.mail** e que remova todas as outras declarações restantes. Portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o CylancePROTECT**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

> [!NOTE]
> Abra o certificado codificado em Base64 baixado em um editor de texto e copie SOMENTE o texto entre as marcas **START** e **END** para colar no portal de administração do Cylance.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao CylancePROTECT.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **CylancePROTECT**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-cylanceprotect-sso"></a>Configurar o SSO do CylancePROTECT

Para configurar o logon único no lado do **CylancePROTECT**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do CylancePROTECT](https://www.cylance.com/en-us/resources/support/support-overview.html). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados. Para obter mais informações, use a documentação do Cylance: [https://support.cylance.com/s/](https://support.cylance.com/s/).

### <a name="create-cylanceprotect-test-user"></a>Criar um usuário de teste do CylancePROTECT

Nesta seção, você criará uma usuária chamada Brenda Fernandes no CylancePROTECT. Trabalhe com o administrador do console para adicionar os usuários na plataforma CylancePROTECT. O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do CylancePROTECT no Painel de Acesso, você deverá ser conectado automaticamente ao CylancePROTECT, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o CylancePROTECT com o Azure AD](https://aad.portal.azure.com/)