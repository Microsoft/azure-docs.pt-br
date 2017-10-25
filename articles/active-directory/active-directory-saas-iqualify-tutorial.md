---
title: "Tutorial: Integração do Azure Active Directory ao iQualify LMS | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o iQualify LMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: cab1ce3694372c137667e0179caf8d3523147f7a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Tutorial: Integração do Azure Active Directory com o iQualify LMS

Neste tutorial, você aprenderá a integrar o iQualify LMS ao Azure AD (Azure Active Directory).

A integração do iQualify LMS ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao iQualify LMS.
- Você pode permitir que seus usuários entrem automaticamente no iQualify LMS (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao iQualify LMS, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do iQualify LMS com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o iQualify LMS da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-iqualify-lms-from-the-gallery"></a>Adicionar o iQualify LMS da galeria
Para configurar a integração do iQualify LMS com o Azure AD, você precisa adicionar o iQualify LMS da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o iQualify LMS da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **iQualify LMS**, selecione **iQualify LMS** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![iQualify LMS na lista de resultados](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o iQualify LMS, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do iQualify LMS é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do iQualify LMS.

No iQualify LMS, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o iQualify LMS, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do iQualify LMS](#create-an-iqualify-lms-test-user)** – para ter um equivalente de Brenda Fernandes no iQualify LMS que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você vai habilitar o logon único do Azure AD no Portal do Azure e configurar o logon único em seu aplicativo iQualify LMS.

**Para configurar o logon único do Azure AD com o iQualify LMS, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **iQualify LMS**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_samlbase.png)

3. Na seção **Domínio e URLs do iQualify LMS**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo IDP:

    ![Informações de logon único de Domínio e URLs do iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: 
    | |
    |--|--|
    | Ambiente de produção: `https://<yourorg>.iqualify.com/`|
    | Ambiente de teste: `https://<yourorg>.iqualify.io`|
    
    b. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão: 
    | |
    |--|--|
    | Ambiente de produção: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Ambiente de teste: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:
    | |
    |--|--|
    | Ambiente de produção: `https://<yourorg>.iqualify.com/login` |
    | Ambiente de teste: `https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Contate a [equipe de suporte do cliente iQualify LMS](https://www.iqualify.com) para obter esses valores. 

5. O aplicativo iQualify LMS espera que as declarações SAML (Security Assertion Markup Language) sejam exibidas em um formato específico. Configure as declarações e gerencie os valores dos atributos na seção **Atributos do Usuário** da página de integração de aplicativos do iQualify como mostrado na seguinte captura de tela:
    
    ![Configurar Logon Único](./media/active-directory-saas-iqualify-tutorial/atb.png)

6. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, para cada linha mostrada na tabela a seguir, execute as seguintes etapas:
    
    | Nome do atributo | Valor do atributo |
    | --- | --- |    
    | email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "your attribute" | 

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-iqualify-tutorial/atb2.png)

    ![Configurar Logon Único](./media/active-directory-saas-iqualify-tutorial/atb3.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **Ok**

    e. Repita as etapas de "a" até "d" para as próximas linhas da tabela seguintes. 

    > [!Note]
    > Repetir as etapas "a" até "d" para o atributo **person_id** é **Opcional**

7. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo de certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_certificate.png) 

8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-iqualify-tutorial/tutorial_general_400.png)
    
9. Na seção **Configuração do iQualify LMS**, clique em **Configurar iQualify LMS** para abrir a janela **Configurar logon**. Copie a **URL de Saída e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida.**

    ![Configuração do iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_configure.png) 

10.  Abra uma nova janela do navegador e conecte-se ao ambiente do iQualify como administrador.

11. Depois que estiver conectado, clique no avatar no canto superior direito e clique em **"Configurações da conta"**.

    ![Configurações da conta](./media/active-directory-saas-iqualify-tutorial/setting1.png) 
12. Na área de configurações de conta, clique no menu de faixa de opções à esquerda e clique em **"INTEGRAÇÕES"**.
    
    ![INTEGRAÇÕES](./media/active-directory-saas-iqualify-tutorial/setting2.png)

13. Em INTEGRAÇÕES, clique no ícone **SAML**.

    ![Ícone SAML](./media/active-directory-saas-iqualify-tutorial/setting3.png)

14. Na caixa de diálogo **Configurações de Autenticação de SAML**, realize as seguintes etapas:

    ![Configurações de Autenticação SAML](./media/active-directory-saas-iqualify-tutorial/setting4.png)

    a. Na caixa **URL DO SERVIÇO DE LOGON ÚNICO SAML**, cole o valor copiado da **URL do Serviço de Logon Único SAML** na janela de configuração de aplicativos do Azure AD.
    
    b. Na caixa **URL DE LOGOFF SAML**, cole o valor copiado da **URL DE SAÍDA** na janela de configuração de aplicativos do Azure AD.
    
    c. Abra o arquivo de certificados baixado no bloco de notas, copie o conteúdo e cole-o na caixa **CERTIFICADO PÚBLICO**.
    
    d. Em **RÓTULO DO BOTÃO DE LOGON**, insira o nome do botão a ser exibido na página de logon.
    
    e. Clique em **SALVAR**.

    f. Clique em **ATUALIZAR**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-iqualify-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-iqualify-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-iqualify-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-iqualify-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-iqualify-lms-test-user"></a>Criar um usuário de teste do iQualify LMS

Nesta seção, é criado um usuário chamado Brenda Fernandes no iQualify. O iQualify LMS dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Se um usuário ainda não existir no iQualify, um novo será criado quando você tentar acessar o iQualify LMS.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você concederá acesso ao iQualify LMS a Brenda Fernandes para habilitá-la a usar o logon único do Azure.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao iQualify LMS, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **iQualify LMS**.

    ![O link do iQualify LMS na lista Aplicativos](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco iQualify LMS no Painel de Acesso, você deve acessar a página de logon do aplicativo iQualify LMS. 

   ![página de logon](./media/active-directory-saas-iqualify-tutorial/login.png) 

Clique no botão **Entrar com o Azure AD** e você deverá ser automaticamente conectado no seu aplicativo do iQualify LMS.

Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_203.png

