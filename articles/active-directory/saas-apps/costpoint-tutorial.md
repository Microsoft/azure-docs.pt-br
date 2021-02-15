---
title: 'Tutorial: Integração do Azure Active Directory ao Costpoint | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Costpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 16c9d64cc4dd49898245d74108cd6a4f2f0e4660
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455163"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Tutorial: Integrar o Costpoint com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Costpoint ao Azure AD (Azure AD). Quando você integra o Costpoint com o Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao Costpoint.
* Permitir que seus usuários se conectem automaticamente ao Costpoint usando as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do Costpoint.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Costpoint dá suporte ao SSO iniciado por **SP e IDP** .

## <a name="generate-costpoint-metadata"></a>Gerar metadados do Costpoint

A configuração de SSO do SAML do Costpoint é explicada no guia **DeltekCostpoint711Security.pdf** . Baixe este guia do site de suporte do Deltek Costpoint e consulte a seção **Instalação de Logon Único do SAML** > **Configurar Logon Único do SAML entre o Costpoint e o Microsoft Azure** . Siga as instruções e gere um arquivo **XML de Metadados de Federação do Costpoint SP** . 

![Captura de tela que mostra o "Utilitário de Configuração do Produto" com a guia "WebLogic – Segurança" selecionada.](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Adicionar o Costpoint da galeria

Para integrar o Costpoint ao Azure AD, primeiro adicione o Costpoint à sua lista de aplicativos SaaS gerenciados da galeria no portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.

1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .

   ![O botão Azure Active Directory](common/select-azuread.png)

1. Selecione **Aplicativos Empresariais** > **Todos os aplicativos** .

   ![A folha Aplicativos empresariais](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione **Novo aplicativo** .

   ![O botão Novo aplicativo](common/add-new-app.png)

1. Na seção **Adicionar da galeria** , digite **Costpoint** na caixa de pesquisa.

   ![Costpoint na lista de resultados](common/search-new-app.png)

1. Na lista de resultados, selecione **Costpoint** e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Configurar e testar o logon único do Azure AD

Configure e teste o SSO do Azure AD com o Costpoint usando uma usuária de teste chamada **B.Fernandes** . Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Costpoint.

Para configurar e testar o SSO do Azure AD com o Costpoint, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
1. **[Configurar o Costpoint](#configure-costpoint)** para definir as configurações de SSO do SAML no lado do aplicativo.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Fernandes.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B. Fernandes use o logon único do Azure AD.
1. **[Criar um usuário de teste do Costpoint](#create-a-costpoint-test-user)** para ter um equivalente de B.Fernandes no Costpoint que esteja vinculado à representação do usuário no Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure:

1. Na página de integração do aplicativo **Costpoint** , selecione **Logon único** .

   ![Configurar o link do logon único](common/select-sso.png)

1. Na seção **Configuração básica do SAML** , se você tiver o *arquivo de metadados do provedor de serviços* , conclua estas etapas:

   > [!NOTE]
   > Você obtém o arquivo de metadados do provedor de serviços em [Gerar metadados do Costpoint](#generate-costpoint-metadata). Como usar o arquivo é explicado posteriormente no tutorial.
 
   1. Selecione o botão **Fazer upload do arquivo de metadados** , em seguida, selecione o arquivo **XML de metadados de federação do Costpoint SP** gerado anteriormente pelo Costpoint e, em seguida, selecione o botão **Adicionar** para fazer upload do arquivo.

      ![Fazer upload do arquivo de metadados](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão populados automaticamente na seção do Costpoint.

      > [!NOTE]
      > Se os valores de **Identificador** e **URL de resposta** não forem populados automaticamente, insira-os manualmente de acordo com seus requisitos. Verifique se **ID (Identificador) da entidade** e **URL de Resposta (URL do Serviço do Consumidor de Asserção)** estão definidos corretamente e se a **URL do ACS** é uma URL válida do Costpoint que termina com **/LoginServlet.cps** .

   1. Selecione **Definir URLs adicionais** . Para **Estado de Retransmissão** , insira um valor usando o seguinte padrão: `system=[your system]` (por exemplo, **system = DELTEKCP** ).

1. Na página **Configurar logon único com SAML** , na seção **Certificado de Autenticação SAML** , selecione o ícone **Copiar** para a **URL de metadados de federação de aplicativos** e salve-a no Bloco de Notas.

   ![Certificado de assinatura de SAML](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Configurar Costpoint

1. Retorne ao Utilitário de Configuração do Costpoint. Na caixa de texto **XML de metadados de Federação IdP** , cole o conteúdo do arquivo *URL de Metadados de Federação* . 

   ![Utilitário de Configuração do Costpoint](./media/costpoint-tutorial/config-utility-idp.png)

1. Continue seguindo as instruções do guia **DeltekCostpoint711Security.pdf** para concluir a instalação do SAML do Costpoint.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado B. Fernandes.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários** .

   ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

1. Selecione **Novo usuário** .

   ![Botão Novo usuário](common/new-user.png)

1. Nas propriedades do **Usuário** , conclua estas etapas:

   ![A caixa de diálogo Usuário](common/user-properties.png)

   1. No campo **Nome** , insira **B.Fernandes** .
   
   1. No campo **Nome de usuário** , insira `b.simon\@yourcompanydomain.extension` (por exemplo, B.Simon@contoso.com).
   
   1. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido no campo **Senha** .
   
   1. Selecione **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo a ela acesso ao Costpoint.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos** .

1. Na lista de aplicativos, selecione **Costpoint** .

1. Na seção **Gerenciar** da página de visão geral do aplicativo, selecione **Usuários e grupos** .

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** . Na caixa de diálogo **Adicionar Atribuição** , selecione **Usuários e grupos** .

   ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** , na lista **Usuários** , selecione **B. Fernandes** . Em seguida, escolha **Selecionar** .

1. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função** , selecione a função apropriada para o usuário na lista e, em seguida, escolha **Selecionar** .

1. Na caixa de diálogo **Adicionar Atribuição** , selecione **Atribuir** .

### <a name="create-a-costpoint-test-user"></a>Criar um usuário de teste do Costpoint

Nesta seção, você criará um usuário no Costpoint. Suponha que a ID de usuário seja **B.FERNANDES** e o nome do usuário seja **B.Fernandes** . Trabalhe com a [equipe de suporte ao Cliente do Costpoint](https://www.deltek.com/about/contact-us) para adicionar o usuário à plataforma Costpoint. O usuário deve ser criado e ativado antes que possa usar o logon único.

Depois que o usuário é criado, a seleção de **Método de Autenticação** do usuário deve ser **Active Directory** , a caixa de seleção **Logon Único do SAML** deve ser selecionada e o nome de usuário do Azure Active Directory precisa ser **Active Directory ou ID do Certificado** (mostrado na captura de tela abaixo).

![Usuário do Costpoint](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Costpoint no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Costpoint, devido a ter configurado o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)