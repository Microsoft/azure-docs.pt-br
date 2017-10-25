---
title: "Tutorial: integração do Azure Active Directory com o Onit | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Onit."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: 47c0055b89dbcf6a30a7f9ac5a33913e7bf463fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-onit"></a>Tutorial: Integração do Active Directory do Azure com o Onit

Neste tutorial, você aprenderá a integrar o Onit ao Azure AD (Azure Active Directory).

A integração do Onit ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Onit.
- Você pode permitir que os usuários façam logon automaticamente no Onit (logon único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Onit, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Onit

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Onit da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-onit-from-the-gallery"></a>Adicionar o Onit da galeria
Para configurar a integração do Onit ao Azure AD, você precisará adicionar o Onit à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Onit da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Onit**, selecione **Onit** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Onit na lista de resultados](./media/active-directory-saas-onit-tutorial/tutorial_onit_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Onit, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Onit é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Onit.

No Onit, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Onit, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Onit](#create-an-onit-test-user)** – para ter um equivalente de Brenda Fernandes no Onit vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Onit.

**Para configurar o logon único do Azure AD com o Onit, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Onit**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-onit-tutorial/tutorial_onit_samlbase.png)

3. Na seção **Domínio e URLs do Onit**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Onit](./media/active-directory-saas-onit-tutorial/tutorial_onit_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<sub-domain>.onit.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<sub-domain>.onit.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do Onit](https://www.onit.com/support) para obter esses valores. 
 
4. Na seção **Certificado de Autenticação SAML**, copie o valor da **IMPRESSÃO DIGITAL** do certificado.

    ![O link de download do Certificado](./media/active-directory-saas-onit-tutorial/tutorial_onit_certificate.png) 

5. O aplicativo Onit espera que as declarações SAML estejam em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar o valor dos atributos na guia **"Atributo"** do aplicativo. A captura de tela a seguir mostra um exemplo disso. 

    ![Configurar Logon Único](./media/active-directory-saas-onit-tutorial/tutorial_onit_attribute.png) 

6. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |
    | email | user.mail |
    
    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-onit-tutorial/tutorial_attribute_04.png)

    ![Configurar Logon Único](./media/active-directory-saas-onit-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    e. Clique em **OK**.

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-onit-tutorial/tutorial_general_400.png)

8. Na seção **Configuração do Onit**, clique em **Configurar o Onit** para abrir a janela **Configurar logon**. Copie a **URL do Serviço de Logon Único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configuração do Onit](./media/active-directory-saas-onit-tutorial/tutorial_onit_configure.png)

9. Em outra janela do navegador da Web, faça logon em seu site de empresa Onit como um administrador.

10. No menu na parte superior, clique em **Administração**.
   
   ![Administração](./media/active-directory-saas-onit-tutorial/IC791174.png "Administração")
11. Clique em **Editar Empresa**.
   
   ![Editar Corporação](./media/active-directory-saas-onit-tutorial/IC791175.png "Editar Corporação")
   
12. Clique na guia **Segurança** .
    
    ![Editar Informações da Empresa](./media/active-directory-saas-onit-tutorial/IC791176.png "Editar Informações da Empresa")

13. Na guia **Segurança** , realize as seguintes etapas:

    ![Logon Único](./media/active-directory-saas-onit-tutorial/IC791177.png "Logon Único")

    a. Como **Estratégia de Autenticação**, selecione **Logn Único e Senha**.
    
    b. Na caixa de texto **URL de Destino do IdP**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do Portal do Azure.

    c. Na caixa de texto **URL de Logoff do IDP**, cole o valor da **URL de Saída** copiado do Portal do Azure.

    d. Na caixa de texto **Impressão Digital do Certificado IdP (SHA1)**, cole o valor da **Impressão Digital** do certificado copiado do Portal do Azure.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-onit-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-onit-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-onit-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-onit-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-onit-test-user"></a>Criar um usuário de teste do Onit

Para permitir que os usuários do AD do Azure façam logon no Onit, eles devem ser provisionados no Onit.  

No caso do Onit, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **Onit** como administrador.
2. Clique em **Adicionar Usuário**.
   
   ![Administração](./media/active-directory-saas-onit-tutorial/IC791180.png "Administração")
3. Na página do diálogo **Adicionar usuário** , realize as seguintes etapas:
   
   ![Adicionar Usuário](./media/active-directory-saas-onit-tutorial/IC791181.png "Adicionar Usuário")
   
  1. Digite o **Nome** e **Endereço de Email** de uma conta válida do Azure AD que você deseja provisionar nas caixas de texto relacionadas.
  2. Clique em **Criar**.    
   
 > [!NOTE]
 > O titular da conta do Azure Active Directory recebe um email e segue um link para confirmar sua conta antes que ela se torne ativa.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Onit.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Onit, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Onit**.

    ![O link do Onit na lista de Aplicativos](./media/active-directory-saas-onit-tutorial/tutorial_onit_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Onit no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Onit.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-onit-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-onit-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-onit-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-onit-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-onit-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-onit-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-onit-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-onit-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-onit-tutorial/tutorial_general_203.png

