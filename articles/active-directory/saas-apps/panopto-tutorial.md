---
title: 'Tutorial: Integração do Azure Active Directory com o Panopto | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Panopto.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2019
ms.author: jeedes
ms.openlocfilehash: 5eed5f0df5c6b8537beef2adcbcf81180bc997db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92512643"
---
# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>Tutorial: Integração do Active Directory do Azure com o Panopto

Neste tutorial, você aprenderá a integrar o Panopto ao Azure AD (Azure Active Directory).
A integração do Panopto ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Panopto.
* Você pode permitir que os usuários entrem automaticamente no Panopto (logon único) com a conta do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Panopto, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do Panopto habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Panopto é compatível com o SSO iniciado por **SP e IdP**

* O Panopto é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-panopto-from-the-gallery"></a>Adicionar o Panopto da galeria

Para configurar a integração do Panopto ao Azure AD, você precisa adicionar o Panopto por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Panopto da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Panopto**, selecione **Panopto** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Panopto na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Panopto usando um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do iQualify.

Para configurar e testar o logon único do Azure AD com o Panopto, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Panopto](#configure-panopto-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Panopto](#create-panopto-test-user)** – para que haja um equivalente de Brenda Fernandes no Panopto que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Panopto, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Panopto**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de domínio e de URLs do Panopto para logon único](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<tenant-name>.panopto.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte ao cliente do Panopto](mailto:support@panopto.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Panopto**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-panopto-single-sign-on"></a>Configurar o logon único do Panopto

1. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do Panopto como administrador.

2. Na barra de ferramentas à esquerda, clique em **Sistema** e em **Provedores de Identidade**.
   
    ![System](./media/panopto-tutorial/ic777670.png "Sistema")

3. Clique em **Adicionar Provedor**.
   
    ![Provedores de Identidade](./media/panopto-tutorial/ic777671.png "Provedores de Identidade")
   
4. Na seção de provedor SAML, execute as seguintes etapas:
   
    ![Configuração do SaaS](./media/panopto-tutorial/ic777672.png "Configuração do SaaS")
    
    a. Na lista **Provider Type** (Tipo de Provedor), selecione **SAML20**.    
    
    b. Na caixa de texto **Nome da Instância**, digite um nome para a instância.

    c. Na caixa de texto **Descrição Amigável**, digite uma descrição amigável.
    
    d. Na caixa de texto **Bounce Page Url** (URL da página de devolução), cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Na caixa de texto **Issuer** (Emissor), cole o valor de **Identificador do Azure AD**, copiado do portal do Azure.

    f. Abra seu certificado codificado em Base 64, que você baixou do Portal do Azure, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Public Key** (Chave Pública).

5. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Panopto.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **Panopto**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Panopto**.

    ![O link do Panopto na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-panopto-test-user"></a>Criar um usuário de teste do Panopto

Nesta seção, um usuário chamado Brenda Fernandes é criado no Panopto. O Panopto é compatível com o provisionamento de usuário Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se ainda não existir um usuário no Panopto, ele será criado após a autenticação.

>[!NOTE]
>Você pode usar quaisquer outras ferramentas de criação de contas de usuários do Panopto ou APIs fornecidas pela Panopto para provisionar contas de usuário do AD do Azure.
>

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Panopto no Painel de Acesso, você será conectado automaticamente ao aplicativo Panopto para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)