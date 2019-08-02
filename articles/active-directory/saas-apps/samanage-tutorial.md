---
title: 'Tutorial: Integração do Azure Active Directory com o Samanage | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Samanage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 714e67bc7cf9e9c928eb4fd0764619041c9c1424
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092590"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Tutorial: Integração do Azure Active Directory com o Samanage

Neste tutorial, você aprende a integrar o Samanage ao Azure AD (Azure Active Directory).
A integração do Samanage ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Samanage.
* Você pode permitir que seus usuários entrem automaticamente no Samanage (Logon Único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Samanage, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do Samanage habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Samanage dá suporte ao SSO iniciado por **SP**

## <a name="adding-samanage-from-the-gallery"></a>Adição do Samanage da galeria

Para configurar a integração do Samanage ao Azure AD, você precisa adicionar o Samanage na galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Samanage da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Samanage**, selecione **Samanage** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Samanage na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Samanage com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Samanage.

Para configurar e testar o logon único do Azure AD com o Samanage, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Samanage](#configure-samanage-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Samanage](#create-samanage-test-user)** – para ter um equivalente de Brenda Fernandes no Samanage que esteja vinculado à representação do usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Samanage, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Samanage**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de Domínio e de URLs do Samanage para logon único](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Logon e o Identificador reais, que são explicados adiante no tutorial. Para obter mais detalhes, contate a [equipe de suporte ao Cliente do Samanage](https://www.samanage.com/support). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Samanage**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-samanage-single-sign-on"></a>Configurar o logon único do Samanage

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Samanage como administrador.

2. Clique em **Painel** e selecione **Configuração** no painel de navegação à esquerda.
   
    ![Painel](./media/samanage-tutorial/tutorial_samanage_001.png "Painel")

3. Clique em **Logon Único**.
   
    ![Logon Único](./media/samanage-tutorial/tutorial_samanage_002.png "Logon Único")

4. Navegue até a seção **Logon usando SAML** , execute as seguintes etapas:
   
    ![Logon usando SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Logon usando SAML")
 
    a. Clique em **Habilitar o Logon Único com SAML**.  
 
    b. Na caixa de texto **URL do Provedor de Identidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.    
 
    c. Confirme se a **URL de Logon** corresponde à **URL de Logon** da seção **Configuração Básica de SAML** no portal do Azure.
 
    d. Na caixa de texto **URL de Logoff**, insira o valor da **URL de Logoff** copiado do portal do Azure.
 
    e. Na caixa de texto **Emissor SAML**, digite o URI da ID do aplicativo definido no provedor de identidade.
 
    f. Abra o certificado codificado em Base64 baixado no portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Cole o Certificado x.509 do Provedor de Identidade abaixo**.
 
    g. Clique em **Criar usuários se eles não existirem no Samanage**.
 
    h. Clique em **Atualizar**.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Samanage.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Samanage**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Samanage**.

    ![O link do Samanage na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-samanage-test-user"></a>Criar um usuário de teste de Samanage

Para permitir que os usuários do Azure AD façam logon no Samanage, eles devem ser provisionados no Samanage.  
No caso do Samanage, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa Samanage como um administrador.

2. Clique em **Painel** e selecione **Configuração** no painel de navegação à esquerda.
   
    ![Configuração](./media/samanage-tutorial/tutorial_samanage_001.png "Configuração")

3. Clique na guia **Usuários**
   
    ![Usuários](./media/samanage-tutorial/tutorial_samanage_006.png "Usuários")

4. Clique em **Novo Usuário**.
   
    ![Novo Usuário](./media/samanage-tutorial/tutorial_samanage_007.png "Novo Usuário")

5. Digite o **Nome** e o **Endereço de Email** de uma conta do Azure Active Directory que você deseja provisionar e clique em **Criar usuário**.
   
    ![Criar usuário](./media/samanage-tutorial/tutorial_samanage_008.png "Criar usuário")
   
   >[!NOTE]
   >O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa. Você pode usar qualquer outra ferramenta de criação de conta de usuário do Samanage ou as APIs fornecidas pelo Samanage para provisionar contas de usuário do Azure Active Directory.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Samanage no Painel de Acesso, você deverá ser conectado automaticamente ao Samanage no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

