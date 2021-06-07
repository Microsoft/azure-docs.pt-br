---
title: 'Tutorial: Integração do Azure Active Directory a um GitHub Enterprise Cloud – Organização | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o GitHub Enterprise Cloud – Organização.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: c87f66e48aa7b39a7f3fc5f9b8572dd6925abbc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732133"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-a-github-enterprise-cloud-organization"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory ao GitHub Enterprise Cloud – Organização

Neste tutorial, você aprenderá a integrar o GitHub Enterprise Cloud – **Organização** ao Azure AD (Azure Active Directory). Ao integrar o GitHub Enterprise Cloud – Organização ao Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso à sua organização do GitHub Enterprise Cloud.
* Gerenciar o acesso à sua organização do GitHub Enterprise Cloud em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD a um GitHub Enterprise Cloud – Organização, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma organização do GitHub criada na [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), que requer o [plano de cobrança do GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O GitHub é compatível com SSO iniciado por **SP**

* O GitHub é compatível com o [provisionamento **automatizado** de usuários (convites de organização)](github-provisioning-tutorial.md)


## <a name="adding-github-from-the-gallery"></a>Adicionar o GitHub da galeria

Para configurar a integração do GitHub ao Azure AD, você precisará adicionar o GitHub da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **GitHub** na caixa de pesquisa.
1. Selecione **GitHub Enterprise Cloud – Organização** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-github"></a>Configurar e testar o SSO do Azure AD para o GitHub

Configure e teste o SSO do Azure AD com o GitHub usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do GitHub.

Execute as seguintes etapas para configurar e testar o SSO do Azure AD com o GitHub, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do GitHub](#configure-github-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do GitHub](#create-github-test-user)** – para ter um equivalente de B.Fernandes no GitHub que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **GitHub**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

   a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://github.com/orgs/<Organization ID>/sso`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://github.com/orgs/<Organization ID>`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://github.com/orgs/<Organization ID>/saml/consume`


    > [!NOTE]
    > Observe que esses não são os valores reais. Você precisa atualizar esses valores com a URL de Entrada, o Identificador e a URL de Resposta reais. Aqui, sugerimos que você use o valor exclusivo de cadeia de caracteres no Identificador. Acesse a seção Administração do GitHub para recuperar esses valores.

5. Seu aplicativo GitHub espera as declarações de SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que o **Identificador de Usuário Único (ID de nome)** é mapeada com **user.userprincipalname**. O aplicativo GitHub espera que o **Identificador de Usuário Único (ID de nome)** seja mapeado com **user.mail**. Portanto, você precisa editar o mapeamento de atributos clicando no ícone **Editar** e alterando-o.

    ![Captura de tela que mostra a seção "Atributos do Usuário" com o ícone "Editar" selecionado.](common/edit-attribute.png)

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o GitHub**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao GitHub.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **GitHub**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.

    ![função de usuário](./media/github-tutorial/user-role.png)

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-github-sso"></a>Configurar o SSO do GitHub

1. Em outra janela do navegador da Web, faça logon em seu site de organização do GitHub como administrador.

2. Navegue até **Configurações** e clique em **Segurança**.

    ![Captura de tela que mostra o menu "Configurações da organização" do GitHub com "Segurança" selecionado.](./media/github-tutorial/security.png)

3. Marque a caixa **Habilitar autenticação SAML**, revelando os campos de configuração de Logon Único e execute as seguintes etapas:

    ![Captura de tela que mostra a seção "Logon único de SAML" com a "Habilitar autenticação SAML" com as caixas de texto de URL realçadas.](./media/github-tutorial/saml-sso.png)

    a. Copie o valor da **URL de logon único** e cole esse valor na caixa de texto **URL de Logon** na seção **Configuração Básica de SAML** no portal do Azure.
    
    b. Copie o valor da **URL do serviço do consumidor de declaração** e cole-o na caixa de texto **URL de Resposta** da **Configuração Básica de SAML** do portal do Azure.

4. Configure os seguintes campos:

    ![Captura de tela que mostra as caixas de texto "URL de Logon", "Emissor" e Certificado Público".](./media/github-tutorial/configure.png)

    a. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    b. Na caixa de texto **Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Abra o certificado baixado do portal do Azure no bloco de notas e cole o conteúdo na caixa de texto **Certificado Público**.

    d. Clique no ícone **Editar** para editar o **Método de Assinatura** e **Método de Resumo** do **RSA-SHA1** e **SHA1** para **RSA-SHA256** e **SHA256** conforme mostrado abaixo.
    
    e. Atualize a **URL do serviço do consumidor de declaração (URL de Resposta)** da URL padrão para que a URL no GitHub corresponda à URL no registro do aplicativo do Azure.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Clique em **Testar configuração de SAML** para confirmar que não há falhas de validação ou erros durante o SSO.

    ![Configurações](./media/github-tutorial/test.png)

6. Clique em **Salvar**

> [!NOTE]
> O logon único no GitHub se autentica em uma organização específica no GitHub e não substitui a autenticação do GitHub em si. Portanto, se a sessão do github.com do usuário expirou, você poderá ser solicitado a autenticar-se com a ID/senha do GitHub durante o processo de logon único.

### <a name="create-github-test-user"></a>Criar um usuário de teste do GitHub

O objetivo desta seção é criar um usuário chamado Britta Simon no GitHub. O GitHub dá suporte ao provisionamento automático de usuário, que está habilitado por padrão. Você pode encontrar mais detalhes [ aqui ](github-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuários.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do GitHub como administrador.

2. Clique em **Pessoas**.

    ![A captura de tela mostra o site do GitHub com a opção Pessoas selecionada.](./media/github-tutorial/people.png "Pessoas")

3. Clique em **Convidar membro**.

    ![Convidar Usuários](./media/github-tutorial/invite-member.png "Convidar Usuários")

4. Na caixa de diálogo **Convidar membro**, execute as seguintes etapas:

    a. Na caixa de texto **Email**, digite o endereço de email da conta de Brenda Fernandes.

    ![Convidar Pessoas](./media/github-tutorial/email-box.png "Convidar Pessoas")

    b. Clique em **Enviar Convite**.

    ![Captura de tela que mostra a página de diálogo "Convidar membro" com "Membro" selecionado e o botão "Enviar convite" selecionado.](./media/github-tutorial/send-invitation.png "Convidar Pessoas")

    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado para a URL de Entrada do GitHub, em que poderá iniciar o fluxo de logon. 

* Acesse a URL de Entrada do GitHub diretamente e inicie o fluxo de logon desse local.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do GitHub em Meus Aplicativos, isso redirecionará para a URL de logon do GitHub. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o GitHub, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)