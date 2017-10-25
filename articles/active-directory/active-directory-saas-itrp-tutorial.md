---
title: "Tutorial: integração do Azure Active Directory com o ITRP | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o ITRP."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: fae1c7b6b0e04c1e23123d3aee7913cb3131e645
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Tutorial: integração do Azure Active Directory com o ITRP

Neste tutorial, você aprenderá a integrar o ITRP ao Azure AD (Azure Active Directory).

A integração do ITRP ao Azure AD oferece os seguintes benefícios:

- É possível controlar quem terá acesso ao ITRP no Azure AD
- Você pode permitir que seus usuários façam logon automaticamente no ITRP (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o ITRP, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do ITRP

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o ITRP por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-itrp-from-the-gallery"></a>Adicionar o ITRP por meio da galeria
Para configurar a integração do ITRP ao Azure AD, você precisará adicionar o ITRP por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ITRP da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **ITRP**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_search.png)

5. No painel de resultados, selecione **ITRP** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure

Nesta seção, você configurará e testará o logon único do Azure AD com o ITRP, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do ITRP é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ITRP.

No ITRP, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o ITRP, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do ITRP](#creating-an-itrp-test-user)**: para ter um equivalente da Brenda Fernandes no ITRP que está vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você vai habilitar o logon único do Azure AD no Portal do Azure e configurar o logon único em seu aplicativo ITRP.

**Para configurar o logon único do Azure AD com o ITRP, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **ITRP**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_samlbase.png)

3. Na seção **URLs e Domínio do ITRP**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.itrp.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.itrp.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente ITRP](https://www.itrp.com/support) para obter esses valores. 
 
4. Na seção **Certificado de Autenticação SAML**, copie o valor da **IMPRESSÃO DIGITAL** do certificado.

    ![Configurar Logon Único](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-itrp-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do ITRP**, clique em **Configurar ITRP** para abrir a janela **Configurar logon**. Copie a **URL do Serviço de Logon Único do SAML e a URL de Saída** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_configure.png) 

7. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do ITRP como administrador.

8. Na barra de ferramentas na parte superior, clique em **Configurações**.
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/ic775570.png "ITRP")

8. No painel de navegação à esquerda, selecione **Logon Único**.
   
    ![Logon Único](./media/active-directory-saas-itrp-tutorial/ic775571.png "Logon Único")

9. Na seção de configuração de logon único, execute as seguintes etapas:
   
    ![Logon Único](./media/active-directory-saas-itrp-tutorial/ic775572.png "Logon Único")
    
    ![Logon Único](./media/active-directory-saas-itrp-tutorial/ic775573.png "Logon Único")   

    a. Clique em **Habilitar**.

    b. Na caixa de texto **URL de Logoff Remoto**, cole a **URL de Saída** que você copiou do portal do Azure.

    c. Na caixa de texto **URL do SSO do SAML**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do Portal do Azure.

    d. Na caixa de texto **Impressão Digital do Certificado**, cole o valor de **Impressão Digital** do certificado, que você copiou do Portal do Azure. 
      
10. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-itrp-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-itrp-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-itrp-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-itrp-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-an-itrp-test-user"></a>Criando um usuário de teste do ITRP

Para permitir que os usuários do Azure AD façam logon no ITRP, eles devem ser provisionados no ITRP.  

No caso do ITRP, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **ITRP** .

2. Na barra de ferramentas na parte superior, clique em **Registros**.
   
    ![Admin](./media/active-directory-saas-itrp-tutorial/ic775575.png "Admin")

3. No menu pop-up, selecione **Pessoas**.
   
    ![Pessoas](./media/active-directory-saas-itrp-tutorial/ic775587.png "Pessoas")

4. Clique em **Adicionar Nova Pessoa** (“+”).
   
    ![Admin](./media/active-directory-saas-itrp-tutorial/ic775576.png "Admin")

5. Na caixa de diálogo Adicionar nova pessoa, execute as seguintes etapas:
   
    ![Usuário](./media/active-directory-saas-itrp-tutorial/ic775577.png "Usuário") 
      
    a. Digite o **Nome** e **Email** de uma conta válida do AAD que você deseja provisionar.

    b. Clique em **Salvar**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do ITRP ou APIs fornecidas pelo ITRP para provisionar as contas de usuário do AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure ao conceder acesso ao ITRP.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao ITRP, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **ITRP**.

    ![Configurar Logon Único](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco ITRP no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo ITRP.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_203.png

