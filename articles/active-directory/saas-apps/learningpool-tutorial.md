---
title: 'Tutorial: integração do Azure Active Directory com o Learningpool Act | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Learningpool Act.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: eb8d0c229ed5f41dcb5999243a57ee19c2b67f74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458720"
---
# <a name="tutorial-azure-active-directory-integration-with-learningpool-act"></a>Tutorial: integração do Azure Active Directory com o Learningpool Act

Neste tutorial, você aprenderá a integrar o Learningpool Act ao Azure AD (Azure Active Directory).
A integração do Learningpool Act com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Learningpool Act.
* É possível permitir que os usuários entrem automaticamente no Learningpool Act (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Learningpool Act, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura habilitada para logon único do Learningpool Act

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Learningpool Act é compatível com SSO iniciado por **SP**

## <a name="adding-learningpool-act-from-the-gallery"></a>Como adicionar o Learningpool Act por meio da galeria

Para configurar a integração do Learningpool Act ao Azure AD, você precisará adicionar o Learningpool Act por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Learningpool Act por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Learningpool Act**, selecione **Learningpool Act** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Learningpool Act na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Learningpool Act com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Learningpool Act.

Para configurar e testar o logon único do Azure AD com o Learningpool Act, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Learningpool Act](#configure-learningpool-act-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Learningpool Act](#create-learningpool-act-test-user)** – para ter um equivalente de Brenda Fernandes no Learningpool Act que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Learningpool Act, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Learningpool Act**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Learningpool Act](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite a URL: `https://parliament.preview.Learningpool.com/auth/shibboleth/index.php`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão:
    
    ```http
    https://<subdomain>.Learningpool.com/shibboleth
    https://<subdomain>.preview.Learningpool.com/shibboleth
    ```

    > [!NOTE]
    > O valor do Identificador não é real. Atualize esse valor com o Identificador real. Contate a [equipe de suporte ao Cliente do Learningpool Act](https://www.learningpool.com/support) para obter esse valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Seu aplicativo Learningpool Act espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo Atributos de usuário.

    ![Captura de tela que mostra Atributos de usuário com o ícone Editar selecionado.](common/edit-attribute.png)

6. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas: 

    | Nome | Atributo de Origem|
    | ------------------- | -------------------- |
    | urn:oid:1.2.840.113556.1.4.221 | user.userprincipalname |
    | urn: oid:2.5.4.42 | user.givenname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn: oid:2.5.4.4 | user.surname |
    | | |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![Captura de tela que mostra Declarações de usuário com a opção de Adicionar declaração.](common/new-save-attribute.png)

    ![Captura de tela que mostra a caixa de diálogo Gerenciar declarações do usuário, na qual você pode inserir os valores descritos.](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

7. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

8. Na seção **Configurar o Learningpool Act**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-learningpool-act-single-sign-on"></a>Configurar logon único do Learningpool Act

Para configurar o logon único no lado do **Learningpool Act**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Learningpool Act](https://www.learningpool.com/support). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure ao conceder acesso ao Learningpool Act.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Learningpool Act**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Learningpool Act**.

    ![O link do Learningpool Act na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-learningpool-act-test-user"></a>Criar usuário de teste do Learningpool Act

Para permitir que os usuários do Azure AD façam logon no Learningpool Act, eles devem ser provisionados no Learningpool Act.

Não há nenhum item de ação para a configuração de provisionamento de usuário para o Learningpool Act.  
Os usuários precisam ser criados por sua [equipe de suporte do Learningpool Act](https://www.Learningpool.com/support).

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação de conta de usuário do Learningpool Act ou APIs fornecidas pelo Learningpool Act para provisionar as contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Learningpool Act no Painel de Acesso, você deverá entrar automaticamente no Learningpool Act para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)