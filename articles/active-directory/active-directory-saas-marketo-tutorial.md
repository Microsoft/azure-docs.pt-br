---
title: "Tutorial: Integração do Azure Active Directory ao Marketo | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Marketo."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: e146fd5a8075bc9c7ba049b25e5f301fc2645ed9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Tutorial: Integração do Azure Active Directory com o Marketo

Neste tutorial, você aprenderá a integrar o Marketo ao Azure AD (Azure Active Directory).

A integração do Marketo ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Marketo
- Você pode permitir que usuários façam logon automaticamente no Marketo (Logon Único) com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Marketo, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Marketo

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Marketo da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-marketo-from-the-gallery"></a>Adicionar o Marketo da galeria
Para configurar a integração do Marketo ao Azure AD, você precisará adicionar o Marketo da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Marketo da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Marketo**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_search.png)

5. No painel de resultados, selecione **Marketo** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Marketo, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Marketo é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Marketo.

No Marketo, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Marketo, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Marketo](#creating-a-marketo-test-user)** – para ter um equivalente de Brenda Fernandes no Marketo que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Marketo.

**Para configurar o logon único do Azure AD com o Marketo, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Marketo**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_samlbase.png)

3. Na seção **Domínio e URLs do Marketo**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://saml.marketo.com/sp`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte do Marketo](http://investors.marketo.com/contactus.cfm) para obter esses valores.
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Marketo**, clique em **Configurar Marketo** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_configure.png) 

7. Para obter a Id do Munchkin do aplicativo, faça logon no Marketo usando credenciais de administrador e execute as seguintes ações:
   
    a. Faça logon usando credenciais de administrador de aplicativo do Marketo.
   
    b. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue até o menu Integração e clique no link do **Munchkin**.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copie a identificação do Munchkin mostrada na tela e conclua sua URL de resposta no assistente de configuração do Azure AD.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png) 

8. Para configurar o SSO no aplicativo, siga estas etapas:
   
    a. Faça logon usando credenciais de administrador de aplicativo do Marketo.
   
    b. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue até o menu Integração e clique em **Logon Único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Para habilitar as configurações de SAML, clique no botão **Editar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 
   
    e. Configurações de logon único **habilitadas**.
   
    f. Colar a **ID da Entidade do SAML**, na caixa de texto **ID do Emissor**.
   
    g. Na caixa de texto **ID da Entidade**, insira a URL como `http://saml.marketo.com/sp`.
   
    h. Selecione o local da ID de usuário como **elemento do Identificador de Nome**.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Se o identificador de usuário não for um valor de UPN, altere o valor na guia Atributo.
   
    i. Carregue o certificado que você baixou do assistente de configuração do Azure AD. **Salve** as configurações.
   
    j. Edite as configurações de redirecionamento de páginas.
   
    k. Cole a **URL do Serviço de Logon Único do SAML** na caixa de texto **URL de Logon**.
   
    l. Cole a **URL de Logoff** na caixa de texto **URL de Logoff**.
   
    m. Na **URL de erro**, copie a **URL da instância do Marketo** e clique no botão **Salvar** para salvar as configurações.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

9. Para habilitar o SSO para usuários, conclua as seguintes ações:
   
    a. Faça logon usando credenciais de administrador de aplicativo do Marketo.
   
    b. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue até o menu **Segurança** e clique em **Configurações de Logon**.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)
   
    d. Marque a opção **Exigir SSO** e **salve** as configurações.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-marketo-test-user"></a>Criar um usuário de teste do Marketo

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Marketo. siga estas etapas para criar um usuário na plataforma do Marketo.

1. Faça logon usando credenciais de administrador de aplicativo do Marketo.

2. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 

3. Navegue até o menu **Segurança** e clique em **Usuários e Funções**
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  

4. Clique no link **Convidar Novo Usuário** na guia Usuários
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 

5. No assistente para Convidar Novo Usuário, preencha as informações a seguir
   
    a. Insira o endereço de **Email** do usuário na caixa de texto
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)
   
    b. Insira o **Nome** na caixa de texto
   
    c. Insira o **Sobrenome** na caixa de texto
   
    d. Clique em **Avançar**

6. Na guia **Permissões**, selecione **userRoles** e clique em **Avançar**
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)
7. Clique no botão **Enviar** para enviar o convite de usuário
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)

8. Os usuários receberão a notificação de email e precisarão clicar no link e alterar a senha para ativar a conta. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Marketo.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Marketo, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Marketo**.

    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Marketo no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo do Marketo.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png

