---
title: 'Tutorial: Integração do Azure Active Directory com o iPass SmartConnect | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 9417d7b957d69dc802ecb2f9f78723eb7aba08ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099846"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Tutorial: Integração do Azure Active Directory com o iPass SmartConnect

Neste tutorial, você aprenderá a integrar o iPass SmartConnect ao Azure AD (Azure Active Directory).
A integração do iPass SmartConnect com o Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao iPass SmartConnect.
* Você pode permitir que os usuários entrem automaticamente no iPass SmartConnect (logon único) com a conta do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o iPass SmartConnect, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do iPass SmartConnect habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O iPass SmartConnect é compatível com o SSO iniciado por **SP e IDP**
* O iPass SmartConnect é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Adicionando iPass SmartConnect da galeria

Para configurar a integração do iPass SmartConnect com o Azure AD, você precisa adicionar o iPass SmartConnect da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o iPass SmartConnect da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **iPass SmartConnect**, selecione **iPass SmartConnect** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![iPass SmartConnect na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o iPass SmartConnect usando um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do iPass SmartConnect.

Para configurar e testar o logon único do Azure AD com o iPass SmartConnect, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do iPass SmartConnect](#configure-ipass-smartconnect-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do iPass SmartConnect](#create-ipass-smartconnect-test-user)** – para que haja um equivalente de Brenda Fernandes no iPass SmartConnect que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o iPass SmartConnect, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **iPass SmartConnect**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

    ![Informações de logon único de URLs e Domínio do iPass SmartConnect](common/preintegrated.png)

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de URLs e Domínio do iPass SmartConnect](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

6. O aplicativo iPass SmartConnect espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

7. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas:

    | NOME |  Atributo de Origem|
    | ---------------| ----------|
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.userprincipalname |
    | Nome de Usuário | user.userprincipalname |
    | | |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

8. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

9. Na seção **Configurar o iPass SmartConnect**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-ipass-smartconnect-single-sign-on"></a>Configurar o logon único do iPass SmartConnect

Para configurar o logon único no **iPass SmartConnect**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do iPass SmartConnect](mailto:help@ipass.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao iPass SmartConnect.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **iPass SmartConnect**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **iPass SmartConnect**.

    ![O link do iPass SmartConnect na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-ipass-smartconnect-test-user"></a>Criar usuário de teste do iPass SmartConnect

Nesta seção, você deve criar um usuário chamado Brenda Fernandes no iPass SmartConnect. Trabalhe com a [equipe de suporte do iPass SmartConnect](mailto:help@ipass.com) para adicionar os usuários ou o domínio que precisa estar na lista de permissões na plataforma do iPass SmartConnect. Se o domínio for adicionado pela equipe, os usuários serão automaticamente provisionados à plataforma iPass SmartConnect. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

**Para testar o aplicativo no fluxo iniciado pelo SP, execute as seguintes etapas:**

a. Baixe o cliente windows iPass SmartConnect [aqui](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![O link do iPass SmartConnect na lista de Aplicativos](./media/ipasssmartconnect-tutorial/testing3.png)

b. Instale o cliente e execute a inicialização.

c. Clique em **Introdução**.

![O link do iPass SmartConnect na lista de Aplicativos](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Insira o nome de usuário do Azure com o domínio. Clique em **Continuar**. Isso será redirecionado à página de logon do Azure

![O link do iPass SmartConnect na lista de Aplicativos](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Após a autenticação bem-sucedida, a ativação do cliente será iniciada. O cliente será ativado.

**Para testar o aplicativo no fluxo iniciado pelo IdP, execute as seguintes etapas:**

a. Faça logon em [https://myapps.microsoft.com](https://myapps.microsoft.com).

b. Clique no aplicativo iPass SmartConnect.

c. Isso inicia a página SSA. Clique em **Baixar o Aplicativo para Windows** para instalar o cliente iPass SmartConnect.

![O link do iPass SmartConnect na lista de Aplicativos](./media/ipasssmartconnect-tutorial/testing4.png)

d. Após a instalação, o cliente na primeira inicialização iniciará automaticamente a ativação após aceitar os termos e condições.

e. Se a ativação não for iniciada, clique no botão Ativar na página SSA para iniciar a ativação.

f. O cliente será ativado.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)