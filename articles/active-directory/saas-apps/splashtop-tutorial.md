---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Splashtop | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Splashtop.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: b6dda20487caf6fe3ba49578cfdc0b65434a8dfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520550"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Splashtop

Neste tutorial, você aprenderá a integrar o Splashtop ao Azure AD (Azure Active Directory). Ao integrar o Splashtop ao Azure AD, você pode:

* Controlar no Azure AD quem terá acesso ao Splashtop.
* Permitir que os usuários sejam conectados automaticamente ao Splashtop com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Splashtop com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Splashtop é compatível com SSO iniciado por **SP**

* Após configurar o Splashtop, você poderá impor controles de sessão, que protegem contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-splashtop-from-the-gallery"></a>Adicionando o Splashtop por meio da Galeria

Para configurar a integração do Splashtop ao Azure AD, você precisará adicionar o Splashtop por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Splashtop** na caixa de pesquisa.
1. Selecione **Splashtop** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Configurar e testar o logon único do Azure AD para o Splashtop

Configure e teste o SSO do Azure AD com o Splashtop com um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Splashtop.

Para configurar e testar o SSO do Azure AD com o Splashtop, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Splashtop](#configure-splashtop-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Splashtop](#create-splashtop-test-user)** – para ter um equivalente de B.Fernandes no Splashtop que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Splashtop**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Logon**, digite a URL: `https://my.splashtop.com/login/sso`

1. O aplicativo Splashtop espera as declarações de SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo TicketManager espera que **nameidentifier** seja mapeado com **user.mail**. Portanto, é necessário editar o mapeamento de atributos clicando no ícone **Editar** e alterar o mapeamento de atributos.

    ![Captura de tela que mostra Atributos de usuário com o ícone Editar selecionado.](common/edit-attribute.png)

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Splashtop**, copie as URLs apropriadas conforme suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao Splashtop.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Splashtop**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-splashtop-sso"></a>Configurar SSO do Splashtop

Nesta seção, você precisará solicitar um novo método de SSO no [portal da Web do Splashtop](https://my.splashtop.com/login).
1. No portal da Web do Splashtop, acesse a guia **Informações da conta** / **Equipe** e role para baixo até encontrar a seção **Logon único**. Em seguida, clique em **Inscrever-se no novo método de SSO**.

    ![Captura de tela que mostra a página Logon Único, na qual você pode selecionar Inscrever-se no novo método de SSO.](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. Na janela de inscrição, forneça um **nome de SSO**. Por exemplo, Novo Azure, e, em seguida, selecione **Azure** como o tipo de IDP e insira a **URL de Logon** e o **identificador do Azure AD** copiado do aplicativo Splashtop no portal do Azure.

    ![Captura de tela que mostra a página Inscrever-se no novo método de SSO, na qual você pode inserir um nome e outras informações.](media/splashtop-tutorial/azure-sso-1.png)

1. Para obter informações sobre o certificado, clique com o botão direito do mouse no arquivo de certificado baixado do aplicativo Splashtop no portal do Azure, edite-o com o Bloco de notas e, em seguida, copie o conteúdo e cole-o no campo **Baixar Certificado (Base64)** .

    ![Captura de tela que mostra a seleção de um arquivo de certificado e a abertura dele com o Bloco de notas.](media/splashtop-tutorial/cert-1.png)
    ![Captura de tela que mostra o conteúdo do arquivo de certificado.](media/splashtop-tutorial/cert-2.png)
    ![Captura de tela que mostra a caixa de texto Baixar Certificado.](media/splashtop-tutorial/azure-sso-2.png)

1. É isso! Clique em **Salvar** e a equipe de validação de SSO do Splashtop entrará em contato com você para obter informações de verificação e ativar o método SSO.

### <a name="create-splashtop-test-user"></a>Criar usuário de teste do Splashtop

1. Após a ativação do método SSO, verifique o método SSO recém-criado para habilitá-lo na seção **Logon Único**.

    ![Captura de tela que mostra a página Logon Único, na qual você pode habilitar o novo método.](media/splashtop-tutorial/enable.png)

1. Convide o usuário de teste, por exemplo, `B.Simon@contoso.com`, para sua equipe do Splashtop com o método de SSO recém-criado.

    ![Captura de tela que mostra a página Convidar Usuários, na qual você pode selecionar o novo método.](media/splashtop-tutorial/invite.png)

1. Você também pode alterar uma conta existente do Splashtop para uma conta de SSO, confira as [instruções](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-).

1. É isso! Você pode usar a conta de SSO para fazer logon no portal da Web do Splashtop ou no aplicativo Splashtop Business.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Splashtop no Painel de Acesso, você deve ser conectado automaticamente ao Splashtop no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Splashtop com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Splashtop com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)