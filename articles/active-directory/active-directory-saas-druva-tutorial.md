---
title: "Tutorial: integração do Azure Active Directory com Druva | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Druva."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: b23e73c47b9a00893e036b67826e4b7ead819a1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Tutorial: integração do Azure Active Directory com o Druva

Neste tutorial, você aprenderá a integrar o Druva ao Azure AD (Azure Active Directory).

A integração do Druva ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Druva.
- Você pode permitir que os usuários entrem automaticamente no Druva (Logon Único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Druva, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Druva

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Druva da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-druva-from-the-gallery"></a>Adicionar o Druva da galeria
Para configurar a integração do Druva com o Azure AD, você precisará adicionar o Druva da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Druva da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Druva**, selecione **Druva** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Druva na lista de resultados](./media/active-directory-saas-druva-tutorial/tutorial_druva_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Druva, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Druva é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado do Druva.

No Druva, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Druva, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Druva](#create-a-druva-test-user)** – para ter um equivalente de Brenda Fernandes no Druva vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Druva.

**Para configurar o logon único do Azure AD com o Druva, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Druva**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-druva-tutorial/tutorial_druva_samlbase.png)

3. Na seção **Domínio e URLs do Druva**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-druva-tutorial/tutorial_druva_url.png)

    Na caixa de texto **URL de Logon**, digite a URL: `https://cloud.druva.com/home`

4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-druva-tutorial/tutorial_druva_certificate.png) 

5. Seu aplicativo Druva espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de **Atributos do Token SAML**. 

    ![Configurar Logon Único](./media/active-directory-saas-druva-tutorial/tutorial_druva_attribute.png)

6. Na seção **Atributos de Usuário** da caixa de diálogo **Logon único**, configure o atributo do token SAML, conforme mostrado na imagem anterior e realize as seguintes etapas:

    | Nome do atributo      | Valor do atributo      |
    | ------------------- | -------------------- |
    | insync\_auth\_token |Insira o valor gerado pelo token |
    
    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.
    
    ![Configurar o logon único](./media/active-directory-saas-druva-tutorial/tutorial_attribute_04.png)
    
    ![Configurar Logon Único](./media/active-directory-saas-druva-tutorial/tutorial_attribute_05.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha. O valor gerado pelo token é explicado posteriormente no tutorial.
    
    d. Clique em **OK**.    

7. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-druva-tutorial/tutorial_general_400.png)

8. Na seção **Configuração do Druva**, clique em **Configurar o Druva** para abrir a janela **Configurar logon**. Copie a **URL do serviço de logon único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-druva-tutorial/tutorial_druva_configure.png) 

9. Em outra janela do navegador da Web, faça logon em seu site de empresa do Druva como administrador.

10. Vá para **Gerenciar \> Configurações**.

    ![Configurações](./media/active-directory-saas-druva-tutorial/ic795091.png "Configurações")

11. Na caixa de diálogo Configurações de Logon Único, execute as seguintes etapas:

    ![Configurações de Logon Único](./media/active-directory-saas-druva-tutorial/ic795092.png "Configurações de Logon Único")
    
    a. Cole o valor da **URL do Serviço de Logon Único SAML** copiado do Portal do Azure na caixa de texto **URL de Logon do Provedor de ID**.
    
    b. Cole o valor da **URL de Saída** copiado do Portal do Azure na caixa de texto **URL de Logon do Provedor de ID**.
    
     c. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do Provedor de ID**
     
     d. Para abrir a página **Configurações**, clique em **Salvar**.

12. Na página **Configurações**, clique em **Gerar Token de SSO**.

    ![Configurações](./media/active-directory-saas-druva-tutorial/ic795093.png "Configurações")

13. No diálogo **Token de Autenticação de Logon Único** , realize as seguintes etapas:

    ![Token SSO](./media/active-directory-saas-druva-tutorial/ic795094.png "Token SSO")
    
    a. Clique em **Copiar**, cole o valor copiado na caixa de texto **Valor** na seção **Adicionar Atributo**.
    
    b. Clique em **fechar**

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-druva-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-druva-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-druva-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-druva-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-druva-test-user"></a>Criar um usuário de teste do Druva

Para permitir que os usuários do Azure AD façam logon no Druva, eles devem estar provisionados no Druva. No caso do Druva, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **Druva** como administrador.

2. Vá para **Gerenciar \> Usuários**.
   
   ![Gerenciar Usuários](./media/active-directory-saas-druva-tutorial/ic795097.png "Gerenciar Usuários")

3. Clique em **Criar Novo**.
   
   ![Gerenciar Usuários](./media/active-directory-saas-druva-tutorial/ic795098.png "Gerenciar Usuários")

4. Na caixa de diálogo Criar Novo Usuário, execute as seguintes etapas:
   
   ![Criar Novo Usuário](./media/active-directory-saas-druva-tutorial/ic795099.png "Criar Novo Usuário")
   
   a. Na caixa de texto **Endereço de email**, insira o email do usuário como **brittasimon@contoso.com**.
   
   b. Na caixa de texto **Nome**, insira o nome de usuário, como **BrendaFernandes**.
   
   c. Clique em **Criar Usuário**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Druva ou as APIs fornecidas pelo Druva para provisionar as contas de usuário do Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Druva.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Druva, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Druva**.

    ![O link do Druva na lista de Aplicativos](./media/active-directory-saas-druva-tutorial/tutorial_druva_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Druva no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Druva.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-druva-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-druva-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-druva-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-druva-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-druva-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-druva-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-druva-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-druva-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-druva-tutorial/tutorial_general_203.png

