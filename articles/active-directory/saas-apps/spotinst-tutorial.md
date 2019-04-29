---
title: 'Tutorial: Integração do Azure Active Directory com o Spotinst | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0bffdf439a192fb10fe695fbfa18e8c7abf8077
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541685"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>Tutorial: Integração do Azure Active Directory com o Spotinst

Neste tutorial, você aprenderá como integrar o Spotinst ao Azure AD (Azure Active Directory).

A integração do Spotinst ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no AD do Azure quem tem acesso ao Spotinst.
- Você pode permitir que seus usuários entrem automaticamente no Spotinst (Logon único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Spotinst, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do Spotinst

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Spotinst da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-spotinst-from-the-gallery"></a>Adicionando Spotinst da Galeria
Para configurar a integração do Spotinst no Azure AD, você precisa adicionar o Spotinst da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar Spotinst da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Spotinst**, selecione **Spotinst** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Spotinst na lista de resultados](./media/spotinst-tutorial/tutorial_spotinst_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Spotinst, com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o Azure AD precisa saber o que o usuário de contraparte do Spotinst é para um usuário no Azure AD. Em outras palavras, um relacionamento de link entre um usuário do AD do Azure e o usuário relacionado no Spotinst precisa ser estabelecido.

Para configurar e testar o AD do Azure logon único com Spotinst, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do Front](#create-a-spotinst-test-user)** : para ter um equivalente de Brenda Fernandes no Front que esteja vinculado à representação de usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo do Spotinst.

**Para configurar o logon único do Azure AD com o Spotinst, execute as seguintes etapas:**

1. No portal do Azure, sobre o **Spotinst** página de integração do aplicativo, clique em **logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/spotinst-tutorial/tutorial_spotinst_samlbase.png)

3. Na seção **Domínio Spotinst e URLs**, execute as seguintes etapas se você deseja configurar o aplicativo no modo iniciado pelo IDP:

    ![Informações de logon único Spotinst domínio e URLs](./media/spotinst-tutorial/tutorial_spotinst_url1.png)

     a. Marque **Mostrar configurações de URL avançadas**.

    b. No **estado de retransmissão** caixa de texto, digite um valor: `<ID>`

    c. Se você deseja configurar o aplicativo no modo **SP** iniciado, na caixa de texto **Fazer login no URL**, digite o URL: `https://console.spotinst.com`

    > [!NOTE]
    > O valor de estado de retransmissão não é real. Você atualizará o valor do estado do relé com o valor atual do estado do relé, que será explicado posteriormente no tutorial.

4. Aplicativo Spotinst espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. A captura de tela a seguir mostra um exemplo disso.

    ![Configurar o logon único](./media/spotinst-tutorial/tutorial_Spotinst_attribute.png)

5. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:

    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |
    | Email | user.mail |
    | Nome | user.givenname |
    | Sobrenome | user.surname |
    
     a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/spotinst-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/spotinst-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.

    e. Clique em **Ok**

6. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/spotinst-tutorial/tutorial_spotinst_certificate.png) 

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/spotinst-tutorial/tutorial_general_400.png)

8. Em uma janela diferente do navegador da Web, efetue login no Spotinst como um administrador de segurança.

9. Clique no **ícone do usuário** no canto superior direito da tela e clique **configurações**.

    ![Configurações de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

10. Clique no **segurança** na parte superior e, em seguida, selecione **provedores de identidade** e execute as seguintes etapas:

    ![Segurança Spotinst](./media/spotinst-tutorial/tutorial_spotinst_security.png)

     a. Copie o valor **Estado do retransmissor** da sua instância e cole-o na caixa de texto **Estado do retransmissão** na seção **Domínio Spotinst e URLs** no portal do Azure.

    b. Clique em **PROCURAR** para carregar o arquivo xml de metadados que você baixou do portal do Azure

    c. Clique em **SALVAR**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/spotinst-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/spotinst-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/spotinst-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/spotinst-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-spotinst-test-user"></a>Criar um usuário de teste Spotinst

O objetivo desta seção é criar um usuário chamado Britta Simon no Spotinst.

1. Se desejar configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:

    a. Em uma janela diferente do navegador da Web, efetue login no Spotinst como um administrador de segurança.

   b. Clique no **ícone do usuário** no canto superior direito da tela e clique **configurações**.

    ![Configurações de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Clique em **os usuários** e selecione **adicionar usuário**.

    ![Configurações de Spotinst](./media/spotinst-tutorial/adduser1.png)

    d. Na seção add user, execute as seguintes etapas:

    ![Configurações de Spotinst](./media/spotinst-tutorial/adduser2.png)

    * Na caixa de texto **Nome Completo**, digite o nome completo do usuário como **BrittaSimon**.

    * No **E-mail** caixa de texto, insira o endereço de email do usuário, como **brendafernandes\@contoso.com**.

    * Selecione seus detalhes específicos da organização para a **Função da organização, Função da conta e Contas**.

2. Se você configurou o aplicativo no modo **IDP** iniciado, não há nenhum item de ação para você nesta seção. Spotinst dá suporte ao provisionamento just-in-time, que é habilitado por padrão. Um novo usuário é criado durante uma tentativa de acessar o Spotinst, se ele ainda não existir.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Spotinst.

![Atribuir a função de usuário][200] 

**Para atribuir Britta Simon a Spotinst, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Spotinst**.

    ![O link Spotinst na lista de aplicativos](./media/spotinst-tutorial/tutorial_spotinst_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Spotinst no Painel de Acesso, você deve entrar automaticamente no aplicativo Spotinst.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spotinst-tutorial/tutorial_general_01.png
[2]: ./media/spotinst-tutorial/tutorial_general_02.png
[3]: ./media/spotinst-tutorial/tutorial_general_03.png
[4]: ./media/spotinst-tutorial/tutorial_general_04.png

[100]: ./media/spotinst-tutorial/tutorial_general_100.png

[200]: ./media/spotinst-tutorial/tutorial_general_200.png
[201]: ./media/spotinst-tutorial/tutorial_general_201.png
[202]: ./media/spotinst-tutorial/tutorial_general_202.png
[203]: ./media/spotinst-tutorial/tutorial_general_203.png

