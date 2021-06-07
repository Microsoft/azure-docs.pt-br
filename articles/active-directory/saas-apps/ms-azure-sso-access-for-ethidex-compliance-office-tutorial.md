---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Acesso de SSO do MS Azure para Ethidex Compliance Office™ | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Acesso de SSO do MS Azure para Ethidex Compliance Office™.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
ms.openlocfilehash: 7ef219ca147fe96fc65f14bbf3ba6a565adc95ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520907"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Acesso de SSO do MS Azure para Ethidex Compliance Office™

Neste tutorial, você aprenderá a integrar o Acesso de SSO do MS Azure para Ethidex Compliance Office™ com o Azure AD (Azure Active Directory). Com a integração do Acesso SSO do MS Azure para Ethidex Compliance Office™ ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Acesso de SSO do MS Azure para Ethidex Compliance Office™.
* Permitir que os usuários sejam conectados automaticamente ao Acesso de SSO do MS Azure para Ethidex Compliance Office™ usando suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Acesso de SSO do MS Azure para Ethidex Compliance Office™ habilitado para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Acesso de SSO do MS Azure para Ethidex Compliance Office™ tem suporte para SSO iniciado por **IDP**

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>Adicionar o Acesso de SSO do MS Azure para Ethidex Compliance Office™ da galeria

Para configurar a integração do Acesso de SSO do MS Azure para Ethidex Compliance Office™ ao Azure AD, você precisa adicionar o Acesso de SSO do MS Azure para Ethidex Compliance Office™ da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Acesso de SSO do MS Azure para Ethidex Compliance Office™** na caixa de pesquisa.
1. Selecione **Acesso de SSO do MS Azure para Ethidex Compliance Office™** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Configurar e testar o logon único do Azure AD para Acesso de SSO do MS Azure para Ethidex Compliance Office™

Configure e teste o SSO do Azure AD com o Acesso de SSO do MS Azure para Ethidex Compliance Office™ usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Acesso de SSO do MS Azure para Ethidex Compliance Office™.

Para configurar e testar o SSO do Azure AD com o Acesso de SSO do MS Azure para Ethidex Compliance Office™, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o Acesso de SSO do MS Azure para SSO do Ethidex Compliance Office](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Acesso de SSO do MS Azure para Ethidex Compliance Office](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** – para ter um equivalente de B. Fernandes no Acesso de SSO do MS Azure para Ethidex Compliance Office™ que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Acesso de SSO do MS Azure para Ethidex Compliance Office™**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `com.ethidex.prod.<CLIENTID>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.ethidex.com/saml2/sp/acs/<CLIENTID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [Equipe de suporte do Acesso de SSO do MS Azure para Ethidex Compliance Office™](mailto:support@ethidex.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Acesso de SSO do MS Azure para Ethidex Compliance Office™ espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizados para a configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo Acesso de SSO do MS Azure para Ethidex Compliance Office™ espera que o **nameidentifier** seja mapeado com **user.mail**, portanto, é necessário editar o mapeamento de atributos, clicando no ícone **Editar** e alterar o mapeamento de atributos.

    ![image](common/edit-attribute.png)

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Bruto)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificateraw.png)

1. Na seção **Configurar o Acesso de SSO do MS Azure para Ethidex Compliance Office™**, copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo acesso ao Acesso de SSO do MS Azure para Ethidex Compliance Office™.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Acesso de SSO do MS Azure para Ethidex Compliance Office™**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>Configurar o SSO do Acesso de SSO do MS Azure para Ethidex Compliance Office

Para configurar o logon único no lado do **Acesso de SSO do MS Azure para Ethidex Compliance Office™**, você precisa enviar o **Certificado (Bruto)** baixado e as URLs adequadas copiadas do portal do Azure para a [equipe de suporte do Acesso de SSO do MS Azure para Ethidex Compliance Office™](mailto:support@ethidex.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>Criar o usuário de teste do Acesso de SSO do MS Azure para Ethidex Compliance Office

Nesta seção, você cria um usuário chamado B. Fernandes no Acesso de SSO do MS Azure para Ethidex Compliance Office™. Trabalhe com a [equipe de suporte do Acesso de SSO do MS Azure para Ethidex Compliance Office™](mailto:support@ethidex.com) para adicionar os usuários na plataforma do Acesso de SSO do MS Azure para Ethidex Compliance Office™. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Acesso de SSO do MS Azure para Ethidex Compliance Office™ no Painel de Acesso, você deverá ser conectado automaticamente ao Acesso de SSO do MS Azure para Ethidex Compliance Office™ para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Acesso SSO do MS Azure para Ethidex Compliance Office™ com o Azure AD](https://aad.portal.azure.com/)