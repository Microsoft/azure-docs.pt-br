---
title: 'Tutorial: Integração do Azure Active Directory ao RFPIO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o RFPIO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: c4e838afa867a7fb1e7fa8f582bc8879c24056a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92506013"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Tutorial: Integração do Azure Active Directory ao RFPIO

Neste tutorial, você aprenderá a integrar o RFPIO ao Azure AD (Azure Active Directory).
A integração do RFPIO ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao RFPIO.
* Você pode permitir que os usuários sejam conectados automaticamente ao RFPIO (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao RFPIO, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do RFPIO

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O RFPIO dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-rfpio-from-the-gallery"></a>Adicionar o RFPIO da galeria

Para configurar a integração do RFPIO ao Azure AD, você precisará adicionar o RFPIO da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o RFPIO da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **RFPIO**, selecione **RFPIO** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![RFPIO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o RFPIO, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do RFPIO.

Para configurar e testar o logon único do Azure AD com o RFPIO, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do RFPIO](#configure-rfpio-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do RFPIO](#create-rfpio-test-user)** – para ter um equivalente de Brenda Fernandes no RFPIO que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o RFPIO, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **RFPIO**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute a seguinte etapa:

    ![Captura de tela que mostra a opção Configuração Básica do SAML, na qual você pode inserir um Identificador.](common/idp-identifier.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.rfpio.com`

    b. Clique em **Definir URLs adicionais**.

    c. Na caixa de texto **Estado de Retransmissão** insira um valor de cadeia de caracteres. Entre em contato com a [equipe de suporte do RFPIO](https://www.rfpio.com/contact/) para obter esse valor.

    ![Captura de tela que mostra a opção Definir URLs adicionais.](common/idp-preintegrated-relay.png)

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![image](common/both-preintegrated-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://www.app.rfpio.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Logon reais. Contate a [equipe de suporte ao Cliente do RFPIO](https://www.rfpio.com/contact/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o RFPIO**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-rfpio-single-sign-on"></a>Configurar o logon único do RFPIO

1. Em outra janela do navegador da Web, faça logon no site do **RFPIO** como administrador.

1. Clique no menu suspenso de canto inferior esquerdo.

    ![Captura de tela que mostra a seta para baixo na parte inferior do painel.](./media/rfpio-tutorial/app1.png)

1. Clique em **Configurações da Organização**. 

    ![Captura de tela que mostra a opção Configurações da Organização selecionada.](./media/rfpio-tutorial/app2.png)

1. Clique no **RECURSOS E INTEGRAÇÃO**.

    ![Captura de tela que mostra a opção Recursos e Integração selecionada em Configurações.](./media/rfpio-tutorial/app4.png)

1. Em **Configuração de SSO de SAML**, clique **Editar**.

    ![Captura de tela que mostra a opção Configuração de SSO do SAML com o botão Editar em destaque.](./media/rfpio-tutorial/app3.png)

1. Nesta seção, execute as seguintes ações:

    ![Captura de tela que mostra a opção Configuração de SSO do SAML com o SAML habilitado.](./media/rfpio-tutorial/app5.png)
    
    a. Copie o conteúdo do **XML de Metadados baixado** e cole-o no campo de **configuração de identidade**.

    > [!NOTE]
    >Para copiar o conteúdo baixado do **XML de Metadados de Federação**, use o **Notepad++** ou o **Editor XML** adequado.

    b. Clique em **Validar**.

    c. Depois de clicar em **Validar**, inverta **SAML (Habilitado)** para ativado.

    d. Clique em **Enviar**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao RFPIO.

1. No portal do Azure, escolha **Aplicativos empresariais**, escolha **Todos os aplicativos** e, em seguida, escolha **RFPIO**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **RFPIO**.

    ![O link do RFPIO na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-rfpio-test-user"></a>Criar usuário de teste do RFPIO

1. Faça logon no site da empresa do RFPIO como administrador.

1. Clique no menu suspenso de canto inferior esquerdo.

    ![Captura de tela que mostra a seta para baixo na parte inferior do painel.](./media/rfpio-tutorial/app1.png)

1. Clique em **Configurações da Organização**. 

    ![Captura de tela que mostra a opção Configurações da Organização selecionada.](./media/rfpio-tutorial/app2.png)

1. Clique em **MEMBROS DA EQUIPE**.

    ![Captura de tela que mostra a opção Membros da Equipe selecionada em Configurações.](./media/rfpio-tutorial/app6.png)

1. Clique em **ADICIONAR MEMBROS**.

    ![Captura de tela que mostra o botão Adicionar Membros.](./media/rfpio-tutorial/app7.png)

1. Na seção **Adicionar Novos Membros**. Execute as seguintes ações:

    ![Captura de tela que mostra a opção Adicionar Novos Membros, na qual você pode inserir os valores descritos.](./media/rfpio-tutorial/app8.png)

    a. Insira **Endereço de email** no campo **Inserir um email por linha**.

    b. Selecione **Função** de acordo com seus requisitos.

    c. Clique em **ADICIONAR MEMBROS**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe um email e segue um link para confirmar sua conta antes que ela se torne ativa.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do RFPIO no Painel de Acesso, você deverá ser conectado automaticamente ao RFPIO, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)