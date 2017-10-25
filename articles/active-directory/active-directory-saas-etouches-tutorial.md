---
title: "Tutorial: integração do Azure Active Directory com o etouches | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o etouches."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 3cd9e9d6aae924369065ca492b1f6380c0ddc5fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Tutorial: integração do Azure Active Directory com o etouches

Neste tutorial, você aprenderá a integrar o etouches ao Azure AD (Azure Active Directory).

A integração do etouches ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao etouches
- Você pode permitir que os usuários façam logon automaticamente no etouches (Logon Único) com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o etouches, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do etouches habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o etouches da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-etouches-from-the-gallery"></a>Adicionar o etouches da galeria
Para configurar a integração do etouches ao Azure AD, você precisará adicionar o etouches da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o etouches da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **etouches**, selecione **etouches** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![etouches na lista de resultados](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o logon único do Azure AD com o etouches, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do etouches é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no etouches.

No etouches, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o etouches, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do etouches](#create-an-etouches-test-user)** – para ter um equivalente de Brenda Fernandes no etouches vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Testar o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo etouches.

**Para configurar o logon único do Azure AD com o etouches, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **etouches**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_samlbase.png)

3. Na seção **Domínio e URLs do etouches**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do etouches](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Esses valores não são reais. Você atualiza o valor com a URL de logon real e o identificador, o que é explicado no tutorial posteriormente.
    > 

4. O aplicativo etouches espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. É possível gerenciar os valores desses atributos em **Atributos de Usuário** do aplicativo. A captura de tela a seguir mostra um exemplo disso. 

    ![Atributo de Usuário](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_attribute.png) 

5. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |
    | Email | user.mail |    
    
    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Adicionar Atributo](./media/active-directory-saas-etouches-tutorial/tutorial_attribute_04.png)

    ![Caixa de diálogo Adicionar Atributo](./media/active-directory-saas-etouches-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**. 

6. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_certificate.png) 

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-etouches-tutorial/tutorial_general_400.png)

8. Para que o SSO seja configurado para o aplicativo, execute as seguintes etapas no aplicativo etouches: 

    ![configuração do etouches](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_06.png) 

    a. Faça logon no aplicativo **etouches** usando os direitos de Administrador.
   
    b. Vá para a configuração de **SAML**.

    c. Na seção **Configurações Gerais**, abra seu certificado baixado do Portal do Azure no bloco de notas, copie o conteúdo e, em seguida, cole-o na caixa de texto de metadados IdP. 

    d. Clique no botão **Save & Stay**.
  
    e. Clique no botão **Update Metadata** na seção SAML Metadata. 

    f. Isso abre a página e executa o SSO. Depois que o SSO estiver funcionando, você poderá configurar o nome de usuário.

    g. No campo Nome de usuário, selecione o **endereço de email** conforme mostrado na imagem abaixo. 

    h. Copie o valor da **ID da entidade SP** e cole-o na caixa de texto **Identificador**, que está na seção **Domínio e URLs do etouches** no Portal do Azure.

    i. Copie o valor da **ACS/URL de SSO** e cole-o na caixa de texto **URL de Entrada**, que está na seção **Domínio e URLs do etouches** no Portal do Azure.
   
> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-etouches-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-etouches-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![O botão Adicionar](./media/active-directory-saas-etouches-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![A caixa de diálogo Usuário](./media/active-directory-saas-etouches-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-etouches-test-user"></a>Criar um usuário de teste no etouches

Nesta seção, você criará um usuário chamado Brenda Fernandes no etouches. Trabalhe com a [equipe de suporte ao cliente do etouches](https://www.etouches.com/event-software/support/customer-support/) para adicionar usuários na plataforma do etouches.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao etouches.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao etouches, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **etouches**.

    ![O link do etouches na lista de Aplicativos](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único


O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco etouches no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo etouches.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_203.png

