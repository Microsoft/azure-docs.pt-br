---
title: 'Tutorial: Integração do Azure Active Directory com o Velpic SAML | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 883620d6aa25f71a454c7add6d60e38b4974facf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67087660"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Tutorial: Integração do Azure Active Directory com o Velpic SAML

Neste tutorial, você aprenderá a integrar o Velpic SAML ao Azure AD (Active Directory).
A integração do Velpic SAML ao Azure AD proporciona os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao Velpic SAML.
* Você pode permitir que os usuários sejam conectados automaticamente ao SAML (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Velpic SAML, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma assinatura do Velpic SAML habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Velpic SAML dá suporte ao SSO iniciado por **SP**

## <a name="adding-velpic-saml-from-the-gallery"></a>Adicionando Velpic SAML usando a galeria

Para configurar a integração do Velpic SAML ao Azure AD, você precisa adicionar o Velpic SAML da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Velpic SAML da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Velpic SAML**, selecione **Velpic SAML** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Velpic SAML na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Velpic SAML, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Velpic SAML.

Para configurar e testar o logon único do Azure AD com o Velpic SAML, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Velpic SAML](#configure-velpic-saml-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criação de um usuário de teste do Velpic SAML](#create-velpic-saml-test-user)** - para ter um equivalente de Brenda Fernandes no Velpic SAML que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Velpic SAML, execute as seguintes etapas:

1. No [Portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Velpic SAML**, Selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Velpic SAML](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<sub-domain>.velpicsaml.net`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Observe que a URL de Logon será fornecida pela equipe do Velpic SAML e o valor do Identificador estará disponível quando você configurar o Plug-in do SSO no lado do Velpic SAML. É preciso copiar esse valor na página do aplicativo Velpic SAML e colá-lo aqui.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Velpic SAML**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-velpic-saml-single-sign-on"></a>Configurar o logon único do Velpic SAML

1. Em uma janela diferente do navegador da Web, faça logon no site do Velpic SAML na sua empresa como administrador.

2. Clique na guia **Gerenciar** e vá para a seção **Integração**, onde você pode clicar no botão **Plug-ins** para criar novo plug-in para Conectar.

    ![Plug-in](./media/velpicsaml-tutorial/velpic_1.png)

3. Clique no botão **"Adicionar plug-in"** .
    
    ![Plug-in](./media/velpicsaml-tutorial/velpic_2.png)

4. Clique no bloco **SAML** na página Adicionar Plug-in.
    
    ![Plug-in](./media/velpicsaml-tutorial/velpic_3.png)

5. Insira o nome do novo plug-in SAML e clique no botão **"Adicionar"** .

    ![Plug-in](./media/velpicsaml-tutorial/velpic_4.png)

6. Insira os detalhes da seguinte maneira:

    ![Plug-in](./media/velpicsaml-tutorial/velpic_5.png)

    a. Na caixa de texto **Nome**, digite o nome do plug-in SAML.

    b. Na caixa de texto **URL do Emissor**, cole o **Identificador do Azure AD** que copiou da janela **Configurar logon** no portal do Azure.

    c. Em **Configuração dos Metadados do Provedor**, faça upload do arquivo XML de Metadados que você baixou no portal do Azure.

    d. Você também pode optar por habilitar o SAML apenas durante o provisionamento, habilitando a caixa de seleção **"Criar automaticamente novos usuários"** . Se um usuário não existir no Velpic e esse sinalizador não estiver habilitado, o logon no Azure falhará. Se o sinalizador estiver habilitado, o usuário será provisionado automaticamente no Velpic no momento do logon. 

    e. Copie a **URL de logon único** da caixa de texto e cole-a no portal do Azure.
    
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
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Velpic SAML.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, selecione **Velpic SAML**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Velpic SAML**.

    ![O link Velpic SAML na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-velpic-saml-test-user"></a>Crie um usuário de teste no Velpic SAML

Normalmente, esta etapa não é necessária, pois o aplicativo dá suporte ao provisionamento de usuário na hora certa. Se o provisionamento automático de usuário não estiver habilitado, a criação manual do usuário poderá ser feita como descrito abaixo.

Entre no site da empresa do seu Velpic SAML como um administrador e execute as seguintes etapas:
    
1. Clique na guia Gerenciar, vá para a seção Usuários e clique no botão Novo para adicionar usuários.

    ![adicionar usuário](./media/velpicsaml-tutorial/velpic_7.png)

2. Na página da caixa de diálogo **"Criar Novo Usuário"** , execute as etapas que se seguem.

    ![usuário](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Na caixa de texto **Nome**, digite o nome Brenda.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome Fernandes.

    c. Na caixa de texto **Nome de Usuário**, digite o nome de usuário de Brenda Fernandes.

    d. Na caixa de texto **Email**, digite o endereço de email da conta Brittasimon@contoso.com.

    e. O restante das informações é opcional, você pode preenchê-las se necessário.
    
    f. Clique em **SALVAR**.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

1. Ao clicar no bloco Velpic SAML no Painel de Acesso, você deve acessar a página de logon do aplicativo Velpic SAML. Você deve ver o botão **"Fazer logon com Azure AD"** na página de entrada.

    ![Plug-in](./media/velpicsaml-tutorial/velpic_6.png)

1. Clique no botão **"Fazer logon com Azure AD"** para entrar no Velpic usando sua conta do Azure AD.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

