---
title: "Tutorial: Integração do Azure Active Directory ao Asana | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Asana."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeedes
ms.openlocfilehash: a2f0cecb93cc382bcfd710c44eb70f80ae67f9b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Tutorial: Integração do Azure Active Directory ao Asana

Neste tutorial, você aprenderá a integrar o Asana ao Azure AD (Azure Active Directory).

A integração do Asana ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Asana
- Você pode permitir que seus usuários faça logon automaticamente no Asana (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Asana, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Asana

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Asana da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-asana-from-the-gallery"></a>Adicionando Asana da galeria
Para configurar a integração do Asana ao Azure AD, você precisará adicionar o Asana da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Asana da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Asana**, selecione **Asana** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Asana, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Asana é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado do Asana.

No Asana, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Asana, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Asana](#create-an-asana-test-user)** – para ter um equivalente de Brenda Fernandes no Asana que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Asana.

**Para configurar o logon único do Azure AD com o Asana, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **Asana**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-asana-tutorial/tutorial_asana_samlbase.png)

3. Na seção **Domínio e URLs do Asana**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_url.png)

    a. Na caixa de texto **URL de Logon**, digite a URL: `https://app.asana.com/`

    b. Na caixa de texto **Identificador**, digite o valor: `https://app.asana.com/`
 
4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-asana-tutorial/tutorial_asana_certificate.png)
    
5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-asana-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Asana**, clique em **Configurar o Asana** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configuração do Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_configure.png) 

7. Em uma janela de navegador diferente, entre em seu aplicativo Asana. Para configurar o SSO no Asana, acesse as configurações de espaço de trabalho clicando no nome do espaço de trabalho no canto superior direito da tela. Em seguida, clique em **\<nome do espaço de trabalho\>Configurações**. 
   
    ![configurações de sso do Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)

8. Na janela **Configurações da organização**, clique em **Administração**. Em seguida, clique em **Os membros devem fazer logon por meio de SAML** para habilitar a configuração de SSO. Execute as seguintes etapas:
   
    ![Definições de Configurar Organização de Logon Único](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)  

     a. Na caixa de texto **URL da página de entrada**, cole a **URL do Serviço de Logon Único SAML**.

     b. Clique com o botão direito do mouse no certificado baixado no portal do Azure e, em seguida, abra o arquivo de certificado usando o Bloco de notas ou seu editor de texto preferido. Copie o conteúdo entre o início e o final do título do certificado e cole-o na caixa de texto **Certificado X.509**.

9. Clique em **Salvar**. Vá para [Guia do Asana para configurar SSO](https://asana.com/guide/help/premium/authentication#gl-saml) se precisar de assistência adicional.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-asana-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-asana-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![O botão Adicionar](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-asana-test-user"></a>Criar um usuário de teste do Asana

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Asana.

1. No **Asana**, vá para a seção **Equipes** no painel esquerdo. Clique no botão de sinal de adição. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 

2. Digite o email britta.simon@contoso.com na caixa de texto e selecione **Convidar**.

3. Clique em **Enviar Convite**. A nova usuária receberá um email em sua conta de email. Ela precisará criar e validar a conta.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Asana.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao Asana, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Asana**.

    ![O link do Asana na lista de Aplicativos](./media/active-directory-saas-asana-tutorial/tutorial_asana_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

O objetivo desta seção é testar o logon único do Azure AD.

Vá para a página de logon do Asana. Na caixa de texto Endereço de email, insira o endereço de email britta.simon@contoso.com. Deixe a caixa de texto de senha em branco e clique em **Fazer Logon**. Você será redirecionado à página de logon do Azure AD. Preencha suas credenciais do Azure AD. Agora, você está conectado ao Asana.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-asana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
