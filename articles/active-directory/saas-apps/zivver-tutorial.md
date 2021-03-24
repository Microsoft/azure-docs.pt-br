---
title: 'Tutorial: Integração do Azure Active Directory com ZIVVER | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ZIVVER.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: d500270d579558a88cc4e5127ee7946b58bddda1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92894654"
---
# <a name="tutorial-azure-active-directory-integration-with-zivver"></a>Tutorial: Integração do Azure Active Directory com ZIVVER

Neste tutorial, você aprende a integrar o ZIVVER com o Azure AD (Azure Active Directory).
A integração do ZIVVER com o Azure AD fornece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao ZIVVER.
* Você pode permitir que os usuários sejam conectados automaticamente ao ZIVVER (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ZIVVER, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do ZIVVER

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ZIVVER é compatível com o SSO iniciado por **IDP**

## <a name="adding-zivver-from-the-gallery"></a>Adicionando o ZIVVER da galeria

Para configurar a integração do ZIVVER com o Azure AD, você precisará adicionar o ZIVVER da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ZIVVER da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ZIVVER**, selecione **ZIVVER** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![ZIVVER na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o ZIVVER baseado em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ZIVVER.

Para configurar e testar o logon único do Azure AD com o ZIVVER, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar Logon Único do ZIVVER](#configure-zivver-single-sign-on)** – para configurar o Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do ZIVVER](#create-zivver-test-user)** – para ter um equivalente de Brenda Fernandes no ZIVVER que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ZIVVER, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ZIVVER**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do ZIVVER](common/idp-identifier.png)

    Na caixa de texto **Identificador**, digite uma URL: `https://app.zivver.com/SAML/Zivver`

5. O aplicativo ZIVVER espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo ZIVVER espera que **nameidentifier** seja mapeado com **user.mail**. Portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![Captura de tela que mostra atributos de usuário com o ícone Editar selecionado.](common/edit-attribute.png)

6. Além do indicado acima, o aplicativo ZIVVER espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome | Namespace | Atributo de Origem|
    | ---------------| --------------- |
    | ZivverAccountKey | https:\//zivver.com/SAML/Attributes | user.objectid |

    >[!NOTE]
    >Se você estiver usando uma configuração híbrida com o Active Directory local e a Ferramenta Azure AD Connect, VALUE deverá ser definido como `user.objectGUID`

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![Captura de tela que mostra Declarações de usuário com a opção de Adicionar declaração.](common/new-save-attribute.png)

    ![Captura de tela que mostra a caixa de diálogo Gerenciar declarações do usuário, na qual você pode inserir os valores descritos.](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Save** (Salvar).

7. Na página **Configurar Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** e no ícone **Copiar** para copiar a **URL de Metadados da Federação do Aplicativo** das opções fornecidas de acordo com suas necessidades e salve-a em seu computador.

    ![O link de download da URL do Certificado](./media/zivver-tutorial/metadataxmlurl.png)

8. Na seção **Configurar o ZIVVER**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-zivver-single-sign-on"></a>Configurar Logon Único do ZIVVER

1. Em uma janela diferente do navegador da Web, entre no [site](https://app.zivver.com/login) da empresa ZIVVER como administrador.

2. Clique no ícone **Configurações da organização** na parte inferior esquerda da janela do navegador.

3. Acesse **Logon único**.

4. Clique no arquivo XML de Metadados de Federação que você baixou do portal do Azure.

5. Na caixa de texto **URL de Metadados do Provedor de Identidade**, cole a **URL de Metadados de Federação do Aplicativo** que você salvou anteriormente do portal do Azure.

6. Marque a caixa de seleção **Ativar SSO**.

7. Clique em **SALVAR**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao ZIVVER.

1. No portal do Azure, selecione **Aplicativos empresariais**, **Todos os aplicativos** e, em seguida, **ZIVVER**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ZIVVER**.

    ![O link do ZIVVER na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-zivver-test-user"></a>Criar usuário de teste do ZIVVER

Nesta seção, você criará uma usuária chamada Brenda Fernandes no ZIVVER. Trabalhe com a [equipe de suporte do ZIVVER](https://support.zivver.com/) para adicionar os usuários na plataforma do ZIVVER. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ZIVVER no Painel de Acesso, você deverá ser conectado automaticamente ao ZIVVER, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)