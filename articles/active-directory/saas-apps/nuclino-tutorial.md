---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Nuclino | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Nuclino.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: 4307838df56fb2dc1cdf6ee4044b8ce8de164227
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518595"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nuclino"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Nuclino

Neste tutorial, você aprenderá a integrar o Nuclino ao Azure AD (Azure Active Directory). Ao integrar o Nuclino ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Nuclino.
* Permitir que os usuários sejam conectados automaticamente ao Nuclino com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Nuclino.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Nuclino dá suporte ao SSO iniciado por **SP e IDP**
* O Nuclino dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-nuclino-from-the-gallery"></a>Adicionando o Nuclino da galeria

Para configurar a integração do Nuclino ao Azure AD, você precisa adicionar o Nuclino da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Nuclino** na caixa de pesquisa.
1. Selecione **Nuclino** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nuclino"></a>Configurar e testar o logon único do Azure AD para o Nuclino

Configure e teste o SSO do Azure AD com o Nuclino usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Nuclino.

Para configurar e testar o SSO do Azure AD com o Nuclino, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Nuclino](#configure-nuclino-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Nuclino](#create-nuclino-test-user)** – para ter um equivalente de B.Fernandes no Nuclino que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Nuclino**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais da seção **Autenticação**, que será explicada mais adiante neste tutorial.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://app.nuclino.com/<UNIQUE-ID>/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao cliente do Nuclino](mailto:contact@nuclino.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. O aplicativo Nuclino espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/edit-attribute.png)

7. Além do indicado acima, o aplicativo Nuclino espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de Origem|
    | ---------------| --------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Nuclino**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Nuclino.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Nuclino**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-nuclino-sso"></a>Configurar o SSO do Nuclino

1. Para automatizar a configuração no Nuclino, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após a adição da extensão ao navegador, um clique em **Configurar o Nuclino** direcionará você ao aplicativo Nuclino. Nele, forneça as credenciais de administrador para entrar no Nuclino. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

3. Caso deseje configurar o Nuclino manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do Nuclino como administrador e execute as seguintes etapas:

4. Clique no **ÍCONE**.

    ![Captura de tela que mostra o ícone "Menu" selecionado ao lado de "SSO do Azure AD".](./media/nuclino-tutorial/configure1.png)

5. Clique em **SSO do Azure AD** e selecione **Configurações de equipe** na lista suspensa.

    ![Captura de tela que mostra a lista suspensa "SSO do Azure AD" com a opção "Configurações de equipe" selecionada.](./media/nuclino-tutorial/configure2.png)

6. Selecione **Autenticação** no painel de navegação à esquerda.

    ![Captura de tela que mostra a opção "Autenticação" selecionada.](./media/nuclino-tutorial/configure3.png)

7. Na seção **Autenticação**, execute as seguintes etapas:

    ![Configuração do Nuclino](./media/nuclino-tutorial/configure4.png)

    a. Selecione **SSO (logon único) baseado em SAML**.

    b. Copie o valor da **URL do ACS (é necessário copiar e colar isso no provedor de SSO)** e cole-o na caixa de texto **URL de Resposta** da seção **Configuração Básica do SAML** no portal do Azure.

    c. Copie o valor da **ID da Entidade (é necessário copiar e colar isso no provedor de SSO)** e cole-o na caixa de texto **Identificador** da seção **Configuração Básica do SAML** no portal do Azure.

    d. Na caixa de texto **URL de SSO**, cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Na caixa de texto **ID da Entidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    f. Abra o arquivo **Certificate (Base64)** baixado no Bloco de Notas. Copie o conteúdo dele para sua área de transferência e, em seguida, cole-o na caixa de texto **Certificado público**.

    g. Clique em **SALVAR ALTERAÇÕES**.

### <a name="create-nuclino-test-user"></a>Criar um usuário de teste do Nuclino

Nesta seção, um usuário chamado B.Fernandes será criado no Nuclino. O Nuclino dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Nuclino, um novo será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Nuclino](mailto:contact@nuclino.com).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Nuclino no Painel de Acesso, você deverá ser conectado automaticamente ao Nuclino, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Nuclino com o Azure AD](https://aad.portal.azure.com/)