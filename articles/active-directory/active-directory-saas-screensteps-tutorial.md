---
title: "Tutorial: Integração do Azure Active Directory ao ScreenSteps | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o ScreenSteps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: b6ded8ba1adf03fdccbdb7573c09fae1857c8b16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Tutorial: Integração do Active Directory do Azure com o ScreenSteps

Neste tutorial, você aprende a integrar o ScreenSteps ao Azure AD (Azure Active Directory).

A integração do ScreenSteps ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao ScreenSteps.
- Você pode permitir que seus usuários façam logon automaticamente no ScreenSteps (logon único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ScreenSteps, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do ScreenSteps habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o ScreenSteps por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-screensteps-from-the-gallery"></a>Adicionar o ScreenSteps por meio da galeria
Para configurar a integração do ScreenSteps ao Azure AD, é necessário adicionar o ScreenSteps à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o ScreenSteps por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **ScreenSteps**, selecione **ScreenSteps** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![ScreenSteps na lista de resultados](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o ScreenSteps, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do ScreenSteps é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ScreenSteps.

No ScreenSteps, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o ScreenSteps, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do ScreenSteps](#create-a-screensteps-test-user)** – para ter um equivalente de Brenda Fernandes no ScreenSteps vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo ScreenSteps.

**Para configurar o logon único do Azure AD com o ScreenSteps, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **ScreenSteps**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_samlbase.png)

3. Na seção **Domínio e URLs do ScreenSteps**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do ScreenSteps](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de logon real, o que é explicado posteriormente neste tutorial. 

4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-screensteps-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do ScreenSteps**, clique em **Configurar o ScreenSteps** para abrir a janela **Configurar logon**. Copie a **URL do serviço de logon único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configuração do ScreenSteps](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_configure.png) 

7. Em outra janela do navegador da Web, faça logon em seu site de empresa ScreenSteps como um administrador.

8. Clique em **Configurações da Conta**.

    ![Gerenciamento de contas](./media/active-directory-saas-screensteps-tutorial/ic778523.png "Gerenciamento de contas")

9. Clique em **Logon Único**.

    ![Autenticação Remota](./media/active-directory-saas-screensteps-tutorial/ic778524.png "Autenticação Remota")

10. Clique em  **Criar Ponto de Extremidade de Logon Único**.

    ![Autenticação Remota](./media/active-directory-saas-screensteps-tutorial/ic778525.png "Autenticação Remota")

11. Na seção **Criar um Ponto de Extremidade de Logon Único**, realize as seguintes etapas:

    ![Criar um Ponto de Extremidade de Autenticação](./media/active-directory-saas-screensteps-tutorial/ic778526.png "Criar um Ponto de Extremidade de Autenticação")
    
    a. Na caixa de texto **Título** , digite um título.
    
    b. Na lista **Modo**, selecione **SAML**.
    
    c. Clique em **Criar**.

12. **Edite** o novo ponto de extremidade.

    ![Editar ponto de extremidade](./media/active-directory-saas-screensteps-tutorial/ic778528.png "Editar ponto de extremidade")

13. Na seção **Editar um Ponto de Extremidade de Logon Único**, realize as seguintes etapas:

    ![Ponto de Extremidade de Autenticação Remota](./media/active-directory-saas-screensteps-tutorial/ic778527.png "Ponto de Extremidade de Autenticação Remota")

    a. Clique em **Carregar novo arquivo de Certificado SAML** e carregue o certificado que você baixou do Portal do Azure.
    
    b. Cole o valor da **URL do Serviço de Logon Único SAML** copiado do Portal do Azure na caixa de texto **URL de Acesso Remoto**.
    
    c. Cole o valor da **URL de Saída** copiado do Portal do Azure na caixa de texto **URL de Logoff**.
    
    d. Selecione um **Grupo** para o qual atribuir usuários quando eles são provisionados.
    
    e. Clique em **Atualizar**.

    f. Copie a **URL do Consumidor SAML** para a área de transferência e cole-a na caixa de texto **URL de Logon** na seção **Domínio e URLs do ScreenSteps**.
    
    g. Retorne para **Editar Ponto de Extremidade de Logon Único**.
    
    h. Clique no botão **Tornar padrão para a conta** para usar esse ponto de extremidade para todos os usuários que fizerem logon no ScreenSteps. Como alternativa, você pode clicar no botão **Adicionar ao Site** usar esse ponto de extremidade para sites específicos em **ScreenSteps**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-screensteps-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-screensteps-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-screensteps-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-screensteps-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-screensteps-test-user"></a>Criar um usuário de teste do ScreenSteps

Nesta seção, você criará um usuário chamado Brenda Fernandes no ScreenSteps. Trabalhe com a [equipe de suporte ao cliente do ScreenSteps](https://www.screensteps.com/contact) para adicionar usuários na plataforma do ScreenSteps. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao ScreenSteps.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao ScreenSteps, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **ScreenSteps**.

    ![O link do ScreenSteps na lista de Aplicativos](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ScreenSteps no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo ScreenSteps.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_203.png

