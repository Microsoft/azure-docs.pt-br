---
title: 'Tutorial: Integração do Azure Active Directory com o SuccessFactors | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9526cde92fe2f504207de188fa8f0567ffa580d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089820"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Tutorial: Integração do Azure Active Directory com o SuccessFactors

Neste tutorial, você aprenderá a integrar o SuccessFactors ao Azure AD (Azure Active Directory).
A integração do SuccessFactors ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao SuccessFactors.
* Você pode permitir que os usuários sejam conectados automaticamente ao SuccessFactors (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SuccessFactors, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do SuccessFactors habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SuccessFactors dá suporte a SSO iniciado por **SP**

## <a name="adding-successfactors-from-the-gallery"></a>Adição do SuccessFactors da galeria

Para configurar a integração do SuccessFactors ao Azure AD, você precisará adicionar o SuccessFactors da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SuccessFactors da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SuccessFactors**, selecione **SucessFactors** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![SuccessFactors na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SuccessFactors com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SuccessFactors.

Para configurar e testar o logon único do Azure AD com o SuccessFactors, é preciso concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do SuccessFactors](#configure-successfactors-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do SuccessFactors](#create-successfactors-test-user)** – para ter um equivalente de Brenda Fernandes no SuccessFactors vinculado à representação dela no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SuccessFactors, execute as seguintes etapas:

1. No [Portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SuccessFactors**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do SuccessFactors](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte do cliente do SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html) para obter esses valores.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o SuccessFactors**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-successfactors-single-sign-on"></a>Configurar o logon único do SuccessFactors

1. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do **portal de administração do SuccessFactors** como administrador.

2. Visite **Segurança de Aplicativo** e nativo do **Recurso Logon Único**.

3. Coloque qualquer valor em **Redefinir Token** e clique em **Salvar Token** para habilitar SSO do SAML.

    ![Configurar o logon único no lado do aplicativo][11]

    > [!NOTE]
    > Esse valor é usado como chave liga/desliga. Se nenhum valor for salvo, o SSO do SAML será ATIVADO. Se um valor em branco for salvo, o SSO do SAML será DESATIVADO.

4. Nativo da captura de tela abaixo e execute as ações a seguir:

    ![Configurar o logon único no lado do aplicativo][12]
  
    a. Selecione o botão de opção **SSO do SAML v2**
  
    b. Defina o **Nome da Parte de Declaração SAML** (por exemplo, emissor SAML + nome da empresa).

    c. Na caixa de texto **Issuer URL**, cole o valor **Identificador do Microsoft Azure Active Directory** que você copiou do portal do Azure.

    d. Selecione **Asserção** como **Exigir Assinatura Obrigatória**.

    e. Selecione **Habilitado** como **Habilitar Sinalizador SAML**.

    f. Selecione **Não** como **Assinatura da Solicitação de Logon (Gerado por SF/SP/RP)** .

    g. Selecione **Perfil de Navegador/Postagem** como **Perfil SAML**.

    h. Selecione **Não** como **Impor Período de Certificado Válido**.

    i. Copie o conteúdo do arquivo do certificado baixado do portal do Azure e, então, cole-o na caixa de texto **Certificado de Verificação SAML**.

    > [!NOTE] 
    > O conteúdo do certificado deve ter começar marcas de certificado do certificado e de fim.

5. Navegue até SAML V2 e então execute as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo][13]

    a. Selecione **Sim** como **Logoff Global iniciado por SP de suporte**.

    b. Na caixa de texto **URL do Global Logout Service (destino LogoutRequest)** , cole o valor **URL de logout** que você copiou do portal do Azure.

    c. Selecione **Não** como **Exigir que sp criptografe todos os elementos NameID**.

    d. Selecione **não especificado** como **Formato de NameID**.

    e. Selecione **Sim** como **Habilitar logon iniciado por sp (AuthnRequest)** .

    f. Na caixa de texto **Enviar solicitação como emissor de toda a empresa**, cole o valor da **URL de Logon** copiada do portal do Azure.

6. Execute estas etapas se quiser fazer com os nomes de usuário de logon diferenciem maiúsculas de minúsculas.

    ![Configurar o logon único][29]

    a. Visite **Configurações da Empresa**(próximo à parte inferior).

    b. Marque a caixa de seleção ao lado de **Habilitar Nome de Usuário que Não Diferencia Maiúsculas de Minúsculas**.

    c. Clique em **Save** (Salvar).

    > [!NOTE]
    > Se você tentar habilitar essa opção, o sistema verifica se ele cria um nome de logon SAML duplicado. Por exemplo, se o cliente tiver os nomes de usuário Usuário1 e usuário1. Parar de diferenciar maiúsculas e minúsculas cria essas duplicatas. O sistema fornece a você uma mensagem de erro e não habilita o recurso. O cliente precisa alterar um dos nomes de usuário para que ele seja digitado diferente.

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

Nesta seção, você permite que Britta Simon use o logon único do Azure concedendo acesso ao SuccessFactors.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **SuccessFactors**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SuccessFactors**.

    ![O link do SuccessFactors na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-successfactors-test-user"></a>Crie um usuário de teste do SuccessFactors

Para permitir que os usuários do Azure AD façam logon no SuccessFactors, eles deverão ser provisionados no SuccessFactors. No caso do SuccessFactors, o provisionamento será uma tarefa manual.

Para obter os usuários criados no SuccessFactors, você precisará entrar em contato com a [equipe de suporte do SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html).

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SuccessFactors no Painel de Acesso, você deverá ser conectado automaticamente ao SuccessFactors, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
