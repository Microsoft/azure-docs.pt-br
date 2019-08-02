---
title: 'Tutorial: Integração do Azure Active Directory com o ClickTime | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ClickTime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19b14b060cc37f94e51ceb902f8b4158120d4d02
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105289"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Tutorial: Integração do Azure Active Directory com o ClickTime

Neste tutorial, você aprenderá a integrar o ClickTime ao Azure AD (Azure Active Directory).
A integração do ClickTime ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao ClickTime.
* Você pode permitir que seus usuários entrem automaticamente no ClickTime (logon único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o ClickTime, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do ClickTime habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ClickTime é compatível com SSO iniciado por **IDP**

## <a name="adding-clicktime-from-the-gallery"></a>Adicionando ClickTime da galeria

Para configurar a integração do ClickTime com o Azure AD, você precisará adicionar o ClickTime à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o ClickTime por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ClickTime**, selecione **ClickTime** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![ClickTime na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o ClickTime com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ClickTime.

Para configurar e testar o logon único do Azure AD com o ClickTime, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do ClickTime](#configure-clicktime-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do ClickTime](#create-clicktime-test-user)** – para ter um equivalente de Brenda Fernandes no ClickTime vinculado à representação da usuária no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ClickTime, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativo do **ClickTime**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na página **Configurar Logon Único com SAML**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do ClickTime](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite uma URL: `https://app.clicktime.com/sp/`

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar ClickTime**, copie a URL apropriada de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-clicktime-single-sign-on"></a>Configurar o logon único do ClickTime

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do ClickTime como administrador.

1. Na barra de ferramentas na parte superior, clique em **Preferências** e em **Configurações de Segurança**.

1. Na seção de configuração de **Preferências de Logon Único** , realize as seguintes etapas:
   
    ![Configurações de segurança](./media/clicktime-tutorial/tic777280.png "as configurações de segurança")
   
    a.  Selecione **Permitir** a entrada usando o SSO (Logon Único) com **Azure AD**.
   
    b. Na caixa de texto **Ponto de Extremidade do Provedor de Identidade**, cole a **URL de Logon** que você copiou do portal do Azure.
   
    c.  Abra o **certificado codificado em Base 64** baixado no Portal do Azure no **Bloco de notas**, copie o conteúdo e cole-o na caixa de texto **Certificado X.509**.
   
    d.  Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao ClickTime.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, selecione **ClickTime**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ClickTime**.

    ![O link do ClickTime na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-clicktime-test-user"></a>Criar usuário de teste do ClickTime

Para permitir que os usuários do Azure AD façam logon no ClickTime, eles devem ser provisionados no ClickTime.  
No caso do ClickTime, o provisionamento é uma tarefa manual.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do ClickTime ou as APIs fornecidas pelo ClickTime para provisionar as contas de usuário do Azure AD.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **ClickTime** .

1. Na barra de ferramentas na parte superior, clique na **Empresa** e em **Pessoas**.
   
    ![Pessoas](./media/clicktime-tutorial/tic777282.png "Pessoas")

1. Clique em **Adicionar Pessoa**.
   
    ![Adicionar pessoa](./media/clicktime-tutorial/tic777283.png "Adicionar pessoa")

1. Na seção Nova Pessoa, execute as etapas a seguir:
   
    ![Pessoas](./media/clicktime-tutorial/tic777284.png "Pessoas")
   
    a.  Na caixa de texto **nome completo**, digite o nome completo do usuário, por exemplo, **Brenda Fernandes**. 
  
    b.  Na caixa de texto **Endereço de email**, insira o email do usuário como **brendafernandes\@contoso.com**.
       
    > [!NOTE]
    > Se quiser, defina propriedades adicionais do novo objeto pessoa.
   
    c.  Clique em **Save** (Salvar).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ClickTime no Painel de Acesso, você deverá ser conectado automaticamente ao ClickTime, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

