---
title: 'Tutorial: Integração do Azure Active Directory ao Zendesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4d161295f236c711e8efbe23deb3c8ba5cad985
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086299"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Tutorial: Integração do Azure Active Directory ao Zendesk

Neste tutorial, você aprenderá a integrar o Zendesk ao Azure AD (Azure Active Directory).
A integração do Zendesk ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Zendesk.
* Você pode permitir que os usuários sejam conectados automaticamente ao Zendesk (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zendesk, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Zendesk

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Zendesk dá suporte ao SSO iniciado por **SP**

* O Zendesk dá suporte ao [provisionamento de usuário **Automatizado**](zendesk-provisioning-tutorial.md)

## <a name="adding-zendesk-from-the-gallery"></a>Adicionar o Zendesk da galeria

Para configurar a integração do Zendesk ao Azure AD, você precisa adicionar o Zendesk por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zendesk por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zendesk**, selecione **Zendesk** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Zendesk na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Zendesk, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zendesk.

Para configurar e testar o logon único do Azure AD com o Zendesk, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Zendesk](#configure-zendesk-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Zendesk](#create-zendesk-test-user)** – para ter um equivalente de Brenda Fernandes no Zendesk que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Zendesk, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativo **Zendesk**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Zendesk](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.zendesk.com`

   b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<subdomain>.zendesk.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de atendimento ao cliente do Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo Zendesk espera as declarações SAML em um formato específico. Não há nenhum atributo SAML obrigatório, mas, opcionalmente, você pode realizar o gerenciamento na seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

6. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, selecione o valor de atributo apropriado.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

    > [!NOTE]
    > Você pode usar atributos de extensão para adicionar atributos que não estão no Azure AD por padrão. Clique em [Atributos do usuário que podem ser definidos no SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) para obter a lista completa de atributos SAML que o **Zendesk** aceita.

7. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, copie a **Impressão Digital** e salve-a no computador.

    ![Copiar o valor da Impressão Digital](common/copy-certificatethumbprint.png)

8. Na seção **Configurar o Zendesk**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

9. Há duas maneiras pelas quais o Zendesk pode ser configurado: Automática e Manual.
  
10. Para automatizar a configuração no Zendesk, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![image](./media/zendesk-tutorial/install_extension.png)

11. Depois de adicionar a extensão ao navegador, clique em **Instalação do Zendesk**. Você será direcionado para o aplicativo Zendesk. Em seguida, forneça as credenciais de administrador para entrar no Zendesk. A extensão do navegador configurará automaticamente o aplicativo e automatizará a seção **Configurar o Logon Único do Zendesk**.

    ![image](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>Configurar o Logon Único do Zendesk

1. Se quiser configurar o Zendesk manualmente, abra uma nova janela do navegador da Web, entre no site da empresa do Zendesk como administrador e execute as seguintes etapas:

2. Clique em **Administrador**.

3. No painel de navegação à esquerda, clique em **Configurações** e em **Segurança**.

4. Na página **Segurança**, realize as seguintes etapas:

    ![Segurança](././media/zendesk-tutorial/ic773089.png "Segurança")

    ![Logon Único](././media/zendesk-tutorial/ic773090.png "Logon Único")

    a. Clique na guia **Administrador e Agentes**.

    b. Selecione **SSO (logon único) e SAML** e, em seguida, selecione **SAML**.

    c. Na caixa de texto **URL de SSO do SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Na caixa de texto **URL de Logoff Remoto**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    e. Na caixa de texto **Impressão Digital do Certificado**, cole o valor de **Impressão Digital** do certificado copiado do Portal do Azure.

    f. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Zendesk.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Zendesk**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Zendesk**.

    ![O link do Zendesk na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-zendesk-test-user"></a>Criar um usuário de teste do Zendesk

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Zendesk. O Zendesk suporta o provisionamento automático de usuários, que é ativado por padrão. Você pode encontrar mais detalhes [ aqui ](Zendesk-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuários.

**Se você precisar criar um usuário manualmente, siga estas etapas:**

> [!NOTE]
> As contas de **Usuário final** são provisionadas automaticamente ao entrar. Contas de **Agente** e **Admin** precisam ser provisionadas manualmente no **Zendesk** antes de entrar.

1. Faça logon em seu locatário do **Zendesk** .

2. Selecione a guia **Lista de Clientes** .

3. Selecione a guia **Usuário** e clique em **Adicionar**.

    ![Adicionar Usuário](././media/zendesk-tutorial/ic773632.png "Adicionar Usuário")
4. Digite o **Nome** e **Email** de uma conta existente do Azure AD que você deseja provisionar e clique em **Salvar**.

    ![Novo Usuário](././media/zendesk-tutorial/ic773633.png "Novo Usuário")

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Zendesk ou APIs fornecidas pelo Zendesk para provisionar as contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Zendesk no Painel de Acesso, você deverá ser conectado automaticamente ao Zendesk, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar Provisionamento de Usuário](zendesk-provisioning-tutorial.md)