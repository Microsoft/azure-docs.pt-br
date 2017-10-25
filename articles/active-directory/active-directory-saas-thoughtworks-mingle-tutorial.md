---
title: "Tutorial: Integração do Azure Active Directory com o Thoughtworks Mingle | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Thoughtworks Mingle."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 268ae5affb88a718f68c08daa94fe7aba4a99c11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Tutorial: Integração do Active Directory do Azure com o Thoughtworks Mingle

Neste tutorial, você aprenderá a integrar o Thoughtworks Mingle ao Azure AD (Azure Active Directory).

A integração do Thoughtworks Mingle ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Thoughtworks Mingle
- Você pode permitir que os usuários façam logon automaticamente no Thoughtworks Mingle (Logon Único) com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Thoughtworks Mingle, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Thoughtworks Mingle habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Thoughtworks Mingle da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Adicionar o Thoughtworks Mingle da galeria
Para configurar a integração do Thoughtworks Mingle ao Azure AD, você precisa adicionar o Thoughtworks Mingle da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Thoughtworks Mingle da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Thoughtworks Mingle**, selecione **Thoughtworks Mingle** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![ThoughtWorks Mingle na lista de resultados](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o logon único do Azure AD com o Thoughtworks Mingle, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Thoughtworks Mingle é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado do Thoughtworks Mingle.

No Thoughtworks Mingle, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Thoughtworks Mingle, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Workrite](#create-a-thoughtworks-mingle-test-user)** – para ter um equivalente de Brenda Fernandes no Thoughtworks Mingle vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Testar o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Thoughtworks Mingle.

**Para configurar o logon único do Azure AD com o Thoughtworks Mingle, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Thoughtworks Mingle**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_samlbase.png)

3. Na seção **Domínio e URLs do Thoughtworks Mingle**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Thoughtworks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE] 
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do Thoughtworks Mingle](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) para obter o valor. 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_400.png)

6. Faça logon em seu site de empresa do **Thoughtworks Mingle** como administrador.

7. Clique na guia **Administrador** e em **Config. de SSO**.
   
    ![Guia Administrador](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785157.png "Configuração de SSO")

8. Na seção **Config. de SSO** , realize as seguintes etapas:
   
    ![Configuração de SSO](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785158.png "Configuração de SSO")
    
    a. Para carregar o arquivo de metadados, clique em **Escolher arquivo**. 

    b. Clique em **Salvar Alterações**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![O botão Adicionar](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![A caixa de diálogo Usuário](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-thoughtworks-mingle-test-user"></a>Criar um usuário de teste do Thoughtworks Mingle

Para usuários do Azure AD conseguirem entrar, eles devem ser provisionados para o aplicativo Thoughtworks Mingle usando seus nomes de usuário do Azure Active Directory. No caso do Thoughtworks Mingle, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon em seu site de empresa Thoughtworks Mingle como um administrador.

2. Clique em **Perfil**.
   
    ![Seu primeiro projeto](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785160.png "Seu primeiro projeto")

3. Clique na guia **Administrador** e em **Usuários**.
   
    ![Usuários](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785161.png "Usuários")

4. Clique em **Novo Usuário**.
   
    ![Novo Usuário](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785162.png "Novo Usuário")

5. Na página do diálogo **Novo Usuário** , realize as seguintes etapas:
   
    ![Caixa de diálogo Novo Usuário](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785163.png "Novo Usuário")  
 
    a. Digite o **Nome de entrada**, **Nome de exibição**, **Escolher senha** e **Confirmar senha** de uma conta válida do Azure AD que você deseja provisionar nas caixas de texto relacionadas. 

    b. Para **Tipo de usuário**, selecione **Usuário completo**.

    c. Clique em **Criar este Perfil**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Thoughtworks Mingle ou as APIs fornecidas pelo Thoughtworks Mingle para provisionar as contas de usuário do AAD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Thoughtworks Mingle.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Thoughtworks Mingle, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Thoughtworks Mingle**.

    ![O link do Thoughtworks Mingle na lista de Aplicativos](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Thoughtworks Mingle no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Thoughtworks Mingle.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_203.png

