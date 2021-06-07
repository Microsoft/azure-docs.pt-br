---
title: 'Tutorial: integração do Azure Active Directory com o Cezanne HR Software | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cezanne HR Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: 0aa0dab7b512c85fbbdf374c962e6ee8e1c7d616
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456268"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Tutorial: Integração do Azure Active Directory ao Cezanne HR Software

Neste tutorial, você aprende a integrar o Cezanne HR Software ao Azure AD (Azure Active Directory).
A integração do Cezanne HR Software ao Azure AD oferece os seguintes benefícios:

* Você pode controlar, no Azure Active Directory, quem tem acesso ao Cezanne HR Software.
* Você pode permitir que os usuários sejam conectados automaticamente ao Cezanne HR Software (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD com o Cezanne HR Software, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Cezanne HR Software

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Cezanne HR Software dá suporte ao SSO iniciado por **SP**

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Adição do Cezanne HR Software da galeria

Para configurar a integração do Cezanne HR Software com o Azure AD, você precisa adicionar o Cezanne HR Software, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Cezanne HR Software por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Cezanne HR Software**, selecione **Cezanne HR Software** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Software de RH Cezanne na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Cezanne HR Software, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cezanne HR Software.

Para configurar e testar o logon único do Azure AD com o Cezanne HR Software, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Cezanne HR Software](#configure-cezanne-hr-software-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Cezanne HR Software](#create-cezanne-hr-software-test-user)** – para ter um equivalente de Brenda Fernandes no Cezanne HR Software que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Cezanne HR Software, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativo do **Cezanne HR Software**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Cezanne HR Software](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite a URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Para obter esses valores, entre em contato com a [equipe de suporte ao cliente do Cezanne HR Software](https://cezannehr.com/services/support/).

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Cezanne HR Software**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Configurar o logon único do Cezanne HR Software

1. Em uma janela diferente do navegador da Web, faça logon em seu locatário do Cezanne HR Software como um administrador.

2. No painel de navegação esquerdo, clique em **Configuração do Sistema**. Vá para **Configurações de Segurança**. Em seguida, navegue até **Configuração de Logon Único**.

    ![A captura de tela mostra o locatário de software Cezanne H R com Configurações de Segurança e a Configuração de Logon Único selecionada.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. No painel **Permitir que os usuários façam logon usando o SSO (Serviço de Logon Único)** a seguir, marque a caixa **SAML 2.0** e selecione a opção **Configuração Avançada**.

    ![A captura de tela mostra o painel Permitir usuários com SAML 2.0 e Configuração Avançada selecionados.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Clique no botão **Adicionar Novo** .

    ![A captura de tela mostra o botão Adicionar.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Execute as etapas a seguir na seção **PROVEDORES DE IDENTIDADE DO SAML 2.0** .

    ![A captura de tela mostra um painel em que você pode inserir os valores descritos nesta etapa.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Insira o nome do seu Provedor de Identidade como o **Nome de Exibição**.

    b. Na caixa de texto **Identificador da Entidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Altere a **Associação SAML** para 'POST'.

    d. Na caixa de texto **Ponto de Extremidade de Serviço de Token de Segurança**, cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Na caixa de texto "Nome do atributo de ID de usuário", digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    f. Clique no ícone **Carregar** para carregar o certificado baixado do Portal do Azure.

    g. Clique no botão **Ok** .

6. Clique no botão **Salvar** .

    ![A captura de tela mostra o botão Salvar para Configuração de Logon Único.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Cezanne HR Software.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Cezanne HR Software**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Cezanne HR Software**.

    ![O link Cezanne HR Software na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-cezanne-hr-software-test-user"></a>Criar um usuário de teste do Cezanne HR Software

Para habilitar usuários do AD do Azure a fazer logon no Cezanne HR Software, eles devem ser provisionados no Cezanne HR Software. No caso do Cezanne HR Software, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no site da empresa Cezanne HR Software como um administrador.

2. No painel de navegação esquerdo, clique em **Configuração do Sistema**. Vá para **Gerenciar Usuários**. Em seguida, navegue até **Add New User**.

    ![A captura de tela mostra o locatário de software Cezanne H R com Gerenciar Usuários e Adicionar Usuário selecionados.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Novo Usuário")

3. Na seção **DETALHES PESSOAIS**, execute as etapas abaixo:

    ![A captura de tela mostra a seção DETALHES DA PESSOA, em que você pode inserir os valores descritos nesta etapa.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Novo Usuário")

    a. Defina **Internal User** como OFF.

    b. Na caixa de texto **Nome**, digite o Nome do usuário, como **Brenda**.  

    c. Na caixa de texto **Sobrenome**, digite o Sobrenome do usuário, como **Fernandes**.

    d. Na caixa de texto **Email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.

4. Na seção **Informações da Conta** , execute as etapas abaixo:

    ![A captura de tela mostra INFORMAÇÕES DA CONTA em que você pode inserir os valores descritos nesta etapa.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Novo Usuário")

    a. Na caixa de texto **Nome de usuário**, digite o email do usuário, como Brittasimon@contoso.com.

    b. Na caixa de texto **Senha**, digite a senha do usuário.

    c. Selecione **Profissional de RH** como **Função de Segurança**.

    d. Clique em **OK**.

5. Navegue até a guia **Logon Único** e selecione **Adicionar Novo** na área **Identificadores SAML 2.0**.

    ![A captura de tela mostra a guia Logon Único, na qual você pode selecionar Adicionar.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Usuário")

6. Escolha o **Provedor de Identidade** para o Provedor de Identidade e, na caixa de texto de **Identificador do Usuário**, insira o endereço de email da conta de Brenda Fernandes.

    ![A captura de tela mostra os identificadores SAML 2.0 em que você pode selecionar seu Provedor de Identidade e o Identificador de Usuário.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Usuário")

7. Clique no botão **Salvar** .

    ![A captura de tela mostra o botão Salvar para as Configurações do Usuário.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Usuário")

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Cezanne HR Software no Painel de Acesso, você deverá ser conectado automaticamente ao Cezanne HR Software, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)