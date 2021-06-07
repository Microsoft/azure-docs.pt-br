---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao WhosOnLocation | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o WhosOnLocation.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: jeedes
ms.openlocfilehash: d0e5134da9083e97b3977b05d601c2cfba25f5d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92636673"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-whosonlocation"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao WhosOnLocation

Neste tutorial, você aprenderá a integrar o WhosOnLocation ao Azure AD (Azure Active Directory). Ao integrar o WhosOnLocation ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao WhosOnLocation.
* Permitir que os usuários sejam conectados automaticamente ao WhosOnLocation com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do WhosOnLocation.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O WhosOnLocation dá suporte ao SSO **iniciado por SP**

* Depois de configurar o WhosOnLocation, você poderá impor controles de sessão, que fornecem proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-whosonlocation-from-the-gallery"></a>Como adicionar o WhosOnLocation por meio da galeria

Para configurar a integração do WhosOnLocation ao Azure AD, você precisará adicionar o WhosOnLocation por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **WhosOnLocation** na caixa de pesquisa.
1. Selecione **WhosOnLocation** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-whosonlocation"></a>Configurar e testar o SSO do Azure AD para o WhosOnLocation

Configure e teste o SSO do Azure AD com o WhosOnLocation usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do WhosOnLocation.

Para configurar e testar o SSO do Azure AD com o WhosOnLocation, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do WhosOnLocation](#configure-whosonlocation-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do WhosOnLocation](#create-whosonlocation-test-user)** : para ter um equivalente de B.Fernandes no WhosOnLocation que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **WhosOnLocation**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://login.whosonlocation.com/saml/login/<CUSTOM_ID>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://login.whosonlocation.com/saml/metadata/<CUSTOM_ID>`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://login.whosonlocation.com/saml/acs/<CUSTOM_ID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon, a URL de Resposta e o Identificador reais. Entre em contato com a [equipe de suporte ao cliente do WhosOnLocation](mailto:support@whosonlocation.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o WhosOnLocation**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao WhosOnLocation.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **WhosOnLocation**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-whosonlocation-sso"></a>Configurar o SSO do WhosOnLocation

1. Em outra janela do navegador, entre no seu site de empresa do WhosOnLocation como administrador.

2. Clique em **Ferramentas** -> **Conta**.

    ![A captura de tela mostra a opção Conta selecionada no menu Ferramentas no site do WhosOnLocation.](./media/WhosOnLocation-tutorial/config1.png)

3. No navegador do lado esquerdo, selecione **Acesso de Funcionário**.

    ![Captura de tela mostra a opção Acesso do Funcionário selecionada no Perfil da Conta.](./media/WhosOnLocation-tutorial/config2.png)

4. Execute as etapas abaixo na página a seguir.

    ![A captura de tela mostra a guia de Acesso do Funcionário, na qual você pode inserir dados do usuário.](./media/WhosOnLocation-tutorial/config3.png)

    a. Altere **Logon único com SAML** para **Sim**.

    b. Na caixa de texto **URL do Emissor**, cole o valor da **ID da Entidade** copiada do portal do Azure.

    c. Na caixa de texto **Ponto de Extremidade de SSO**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Certificado**.

    e. Clique em **Salvar Configuração do SAML**.

### <a name="create-whosonlocation-test-user"></a>Criar um usuário de teste do WhosOnLocation

Nesta seção, você criará um usuário chamado B.Fernandes no WhosOnLocation. Trabalhe com a [equipe de suporte do WhosOnLocation](mailto:support@whosonlocation.com) para adicionar os usuários na plataforma do WhosOnLocation. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do WhosOnLocation no Painel de Acesso, você deverá ser conectado automaticamente ao WhosOnLocation, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o WhosOnLocation com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o WhosOnLocation com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)