---
title: 'Tutorial: Integração do Azure Active Directory com o SolarWinds Service Desk (anteriormente conhecido como Samanage) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SolarWinds Service Desk (anteriormente conhecido como Samanage).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: ab720430af0341f3a42d9f4d4dc19b9469872211
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675601"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Tutorial: Integração do Azure Active Directory ao SolarWinds Service Desk (anteriormente conhecido como Samanage)

Neste tutorial, você aprenderá a integrar o SolarWinds ao Azure AD (Azure Active Directory).
A integração do SolarWinds ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao SolarWinds.
* É possível permitir que os usuários entrem automaticamente no SolarWinds (logon único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao SolarWinds Service Desk (anteriormente conhecido como Samanage), você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do Samanage habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SolarWinds dá suporte a SSO iniciado por **SP**

## <a name="adding-solarwinds-from-the-gallery"></a>Adicionar o SolarWinds da galeria

Para configurar a integração do SolarWinds ao Azure AD, você precisará adicionar o SolarWinds da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o SolarWinds da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione o ícone **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos** .

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SolarWinds** , selecione **SolarWinds** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![SolarWinds na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SolarWinds, com base em um usuário de teste chamado **Brenda Fernandes** .
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SolarWinds.

Para configurar e testar o logon único do Azure AD com o SolarWinds, é preciso concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do SolarWinds Service Desk](#configure-solarwinds-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do SolarWinds Service Desk](#create-solarwinds-test-user)** – para ter um equivalente de Brenda Fernandes no SolarWinds Service Desk que esteja vinculado à representação da usuária no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SolarWinds, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **SolarWinds** , selecione **Logon único** .

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único** , selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML** .

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML** , realize as seguintes etapas:

    ![Informações de Domínio e de URLs do Samanage para logon único](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon** , digite uma URL usando o seguinte padrão: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Logon e o Identificador reais, que são explicados adiante no tutorial. Para obter mais detalhes, contate a [equipe de suporte ao Cliente do Samanage](https://www.samanage.com/support). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML** , na seção **Certificado de Autenticação SAML** , clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o SolarWinds** , copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

<a name="configure-solarwinds-single-sign-on"></a>

### <a name="configure-solarwinds-service-desk-single-sign-on"></a>Configurar o Logon Único do SolarWinds Service Desk

1. Em outra janela do navegador da Web, faça logon no site corporativo do SolarWinds como administrador.

2. Clique em **Painel** e selecione **Configuração** no painel de navegação à esquerda.
   
    ![Painel](./media/samanage-tutorial/tutorial_samanage_001.png "Painel")

3. Clique em **Logon Único** .
   
    ![Logon único](./media/samanage-tutorial/tutorial_samanage_002.png "Logon Único")

4. Navegue até a seção **Logon usando SAML** , execute as seguintes etapas:
   
    ![Logon usando SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Logon usando SAML")
 
    a. Clique em **Habilitar o Logon Único com SAML** .  
 
    b. Na caixa de texto **URL do Provedor de Identidade** , cole o valor do **Identificador do Azure AD** copiado do portal do Azure.    
 
    c. Confirme se a **URL de Logon** corresponde à **URL de Logon** da seção **Configuração Básica de SAML** no portal do Azure.
 
    d. Na caixa de texto **URL de Logoff** , insira o valor da **URL de Logoff** copiado do portal do Azure.
 
    e. Na caixa de texto **Emissor SAML** , digite o URI da ID do aplicativo definido no provedor de identidade.
 
    f. Abra o certificado codificado em Base64 baixado no portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Cole o Certificado x.509 do Provedor de Identidade abaixo** .
 
    g. Clique em **Criar usuários se eles não existirem no SolarWinds** .
 
    h. Clique em **Atualizar** .

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory** , selecione **Usuários** e, em seguida, **Todos os usuários** .

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome** , insira **BrendaFernandes** .
  
    b. No campo **Nome de usuário** , digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao SolarWinds.

1. No portal do Azure, escolha **Aplicativos Empresariais** , selecione **Todos os aplicativos** e **SolarWinds** .

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SolarWinds** .

3. No menu à esquerda, selecione **Usuários e grupos** .

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição** .

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos** , escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função** , escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **Atribuir** .

### <a name="create-solarwinds-test-user"></a>Criar usuário de teste do SolarWinds

Para que os usuários do Azure AD possam fazer logon no SolarWinds, eles devem ser provisionados no SolarWinds.  
No caso do SolarWinds, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no site corporativo do SolarWinds como administrador.

2. Clique em **Painel** e selecione **Configuração** no painel de navegação à esquerda.
   
    ![Instalação](./media/samanage-tutorial/tutorial_samanage_001.png "Instalação")

3. Clique na guia **Usuários**
   
    ![Usuários](./media/samanage-tutorial/tutorial_samanage_006.png "Usuários")

4. Clique em **Novo usuário** .
   
    ![Novo usuário](./media/samanage-tutorial/tutorial_samanage_007.png "Novo Usuário")

5. Digite o **Nome** e o **Endereço de Email** de uma conta do Azure Active Directory que você deseja provisionar e clique em **Criar usuário** .
   
    ![Criar Usuário](./media/samanage-tutorial/tutorial_samanage_008.png "Criar Usuário")
   
   >[!NOTE]
   >O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa. Você pode usar qualquer outra ferramenta de criação de conta de usuário do SolarWinds ou as APIs fornecidas pelo SolarWinds para provisionar contas de usuário do Azure Active Directory.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SolarWinds no Painel de Acesso, você deverá ser conectado automaticamente ao SolarWinds para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o Acesso Condicional no Azure Active Directory?](../conditional-access/overview.md)