---
title: 'Tutorial: Integração do Azure Active Directory ao Adobe Sign | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Adobe Sign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb27e24e9b53b734a24304a63c8fd91d5e94f5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107340"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Tutorial: Integração do Azure Active Directory ao Adobe Sign

Neste tutorial, você aprende a integrar o Adobe Sign ao Azure AD (Azure Active Directory).
A integração do Adobe Sign ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Adobe Sign.
* Você pode permitir que os usuários sejam conectados automaticamente ao Adobe Sign (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Adobe Sign, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Adobe Sign

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Adobe Sign é compatível com o SSO iniciado por **SP**

## <a name="adding-adobe-sign-from-the-gallery"></a>Adicionando o Adobe Sign por meio da galeria

Para configurar a integração do Adobe Sign ao Azure AD, você precisa adicionar o Adobe Sign à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Adobe Sign por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Adobe Sign**, selecione **Adobe Sign** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Adobe Sign na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Adobe Sign, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Adobe Sign.

Para configurar e testar o logon único do Azure AD com o Adobe Sign, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Adobe Sign](#configure-adobe-sign-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Adobe Sign](#create-adobe-sign-test-user)** – para ter um equivalente de Brenda Fernandes no Adobe Sign que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Adobe Sign, realize as etapas a seguir:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Adobe Sign**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de URLs e domínio do Adobe Sign](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.echosign.com/`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do Adobe Sign](https://helpx.adobe.com/in/contact/support.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Adobe Sign**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-adobe-sign-single-sign-on"></a>Configurar o logon único do Adobe Sign

1. Antes da configuração, entre em contato com a [equipe de suporte do Adobe Sign Client](https://helpx.adobe.com/in/contact/support.html) para adicionar seu domínio na lista de permissões do Adobe Sign. Veja como adicionar o domínio:

    a. A equipe de suporte do [ Adobe Sign Client ](https://helpx.adobe.com/in/contact/support.html) envia um token gerado aleatoriamente. Para o seu domínio, o token será como o seguinte: **adobe-sign-verification = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publique o token de verificação em um registro de texto DNS e notifique a [ equipe de suporte do Adobe Sign Client ](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Isso pode levar alguns dias ou mais. Observe que os atrasos de propagação de DNS significam que um valor publicado no DNS pode não estar visível por uma hora ou mais. Seu administrador de TI deve ter conhecimento sobre como publicar esse token em um registro de texto DNS.
    
    c. Quando você notifica a [ equipe de suporte do Adobe Sign Client ](https://helpx.adobe.com/in/contact/support.html) por meio do ticket de suporte, depois que o token é publicado, ele valida o domínio e o adiciona à sua conta.
    
    d. Geralmente, aqui está como publicar o token em um registro DNS:

    * Entre na sua conta de domínio
    * Encontrar a página para atualizar o registro DNS. Esta página pode ser chamada de Gerenciamento de DNS, Gerenciamento do Servidor de Nomes ou Configurações Avançadas.
    * Encontrar os registros TXT para o domínio.
    * Adicione um registro TXT com o valor total do token fornecido pela Adobe.
    * Salve suas alterações.

1. Em uma janela diferente do navegador da Web, entre no site da empresa Adobe Sign como administrador.

1. No menu SAML, selecione **Configurações da conta**  >  **Configurações SAML**.
   
    ![ Captura de tela da página Configurações de SAML do Adobe Sign ](./media/adobe-echosign-tutorial/ic789520.png " Conta ")

1. Na seção **Configuração do SAML**, execute as seguintes etapas:
  
   ![ Captura de tela das configurações do SAML ](./media/adobe-echosign-tutorial/ic789521.png " Configurações do SAML ")
   
   ![ Captura de tela das configurações do SAML ](./media/adobe-echosign-tutorial/ic789522.png " Configurações do SAML ")

   a. Em **Modo SAML**, selecione **SAML Obrigatório**.
   
   b. Selecione **Permitir Administradores da Conta Echosign para efetuar login usando suas Credenciais Echosign**.
   
   c. Em **Criação de usuário**, selecione **Adicionar automaticamente usuários autenticados por SAML**.

   d. Cole o **Identificador do Azure AD** que você copiou do portal do Azure na caixa de texto **Identidade da entidade do IDP**.
    
   e. Cole a **URL de logon**, copiada do portal do Azure, na caixa de texto **URL de logon do IDP**.
   
   f. Cole a **URL de logoff**, copiada do portal do Azure, na caixa de texto **URL de logoff do IDP**.

   g. Abra o arquivo **Certificate (Base64)** baixado no Bloco de Notas. Copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado IdP**.

   h. Selecione **Salvar alterações**.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Adobe Sign.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Adobe Sign**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e escolha **Adobe Sign**.

    ![O link do Adobe Sign na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-adobe-sign-test-user"></a>Criar usuário de teste do Adobe Sign

Para permitir que os usuários do Azure AD entrem no Adobe Sign, eles devem ser provisionados no Adobe Sign. Esta é uma tarefa manual.

>[!NOTE]
>Você pode usar qualquer outra ferramenta ou API de criação de conta de usuário do Adobe Sign fornecida pelo Adobe Sign para aprovisionar contas de usuários do Azure AD. 

1. Faça login no **site da empresa do Adobe Sign** como administrador.

2. No menu no topo, selecione **Conta**. Em seguida, no painel esquerdo, selecione **Usuários e grupos**  >  **Crie um novo usuário**.
   
    ![ Captura de tela do site da empresa Adobe Sign, com Conta, Usuários e Grupos e Criar um novo usuário realçado ](./media/adobe-echosign-tutorial/ic789524.png " Conta ")
   
3. Na seção **Criar Novo Usuário** , realize as seguintes etapas:
   
    ![ Captura de tela da seção Criar novo usuário ](./media/adobe-echosign-tutorial/ic789525.png " Criar usuário ")
   
    a. Digite **Endereço de e-mail**, **Nome** e **Sobrenome** de uma conta válida do AD do Azure que você deseja provisionar nas caixas de texto relacionadas.
   
    b. Selecione **Criar usuário**.

>[!NOTE]
>O titular da conta do Active Directory do Azure recebe um e-mail que inclui um link para confirmar a conta antes que ela se torne ativa. 

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Adobe Sign no Painel de Acesso, você deverá ser conectado automaticamente ao Adobe Sign para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

