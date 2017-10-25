---
title: "Tutorial: Integração do Azure Active Directory ao Hightail | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Hightail."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: ba55f9b62d274aa3eb91723c62b53f54de0891b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Tutorial: Integração do Azure Active Directory ao Hightail

Neste tutorial, você aprenderá a integrar o Hightail ao Azure AD (Azure Active Directory).

A integração do Hightail ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Hightail
- Você pode habilitar os usuários a entrar automaticamente no Hightail (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Hightail, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Hightail

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Hightail a partir da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-hightail-from-the-gallery"></a>Adição do Hightail a partir da galeria
Para configurar a integração do Hightail ao Azure AD, você precisará adicionar o Hightail da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Hightail da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Hightail**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_search.png)

5. No painel de resultados, selecione **Hightail** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com Hightail com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Hightail é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Hightail.

No Hightail, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Hightail, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Como criar de um usuário de teste do Hightail](#creating-a-hightail-test-user)** – para ter um equivalente de Brenda Fernandes no Hightail que esteja vinculado de usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Hightail.

**Para configurar o logon único do Azure AD com o Hightail, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Hightail**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_samlbase.png)

3. Na seção **Domínio e URLs do Hightail**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_url.png)

     Na caixa de texto **URL de Resposta**, digite a URL como: `https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`

    > [!NOTE] 
    > O valor anterior não é um valor real. Você atualizará o valor com a URL de Resposta real, que é explicada no tutorial posteriormente.
 
4. Na seção **Domínio e URLs do Hightail**, se você quiser configurar o aplicativo no **Modo iniciado por SP**, execute as seguintes etapas:
    
    ![Configurar Logon Único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_url1.png)

    a. Clique na opção **Mostrar configurações avançadas de URL**.

    b. Na caixa de texto **URL de Logon**, digite a URL como: `https://www.hightail.com/loginSSO`

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_certificate.png) 

5. O aplicativo Hightail espera que as declarações SAML estejam em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar o valor dos atributos na guia **"Atributo"** do aplicativo. A captura de tela a seguir mostra um exemplo disso. 

    ![Configurar Logon Único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_attribute.png) 

6. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |
    | Nome | user.givenname |
    | Sobrenome | user.surname |
    | Email | user.mail |    
    | UserIdentity | user.mail |
    
    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_officespace_04.png)

    ![Configurar Logon Único](./media/active-directory-saas-hightail-tutorial/tutorial_officespace_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    e. Clique em **OK**.

7. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-hightail-tutorial/tutorial_general_400.png)

8. Na seção **Configuração do Hightail**, clique em **Configurar Hightail** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_configure.png) 

    >[!NOTE] 
    >Antes de configurar o logon único no aplicativo Hightail, coloque seu domínio de email na lista branca com a equipe do Hightail para que todos os usuários que usam esse domínio possam utilizar a funcionalidade de Logon Único.


9. Para configurar o SSO para o aplicativo, você precisa entrar no locatário Hightail como administrador.
   
    a. No menu na parte superior, clique na guia **Conta** e selecione **Configurar SAML**.
 
    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_001.png) 

    b. Marque a caixa de seleção **Habilitar Autenticação SAML**.

    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_002.png) 

    c. Abra seu certificado codificado em base-64 no bloco de notas baixado do portal do Azure, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado de Autenticação de Tokens do SAML**.

    ![Configurar Logon Único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_003.png) 

    d. Na caixa de texto **Autoridade SAML (Provedor de Identidade)**, cole o valor de **URL de Serviço de Logon Único do SAML** copiada do portal do Azure.

    ![Configurar Logon Único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_004.png)

    e. Para configurar o aplicativo no **modo iniciado pelo IDP**, selecione **"Logon iniciado pelo IdP (Provedor de Identidade)"**. Se você usar o **modo iniciado pelo SP**, selecione **"Logon iniciado pelo SP (Provedor de Serviços)"**.

    ![Configurar Logon Único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_006.png)

    f. Copie a URL do consumidor do SAML para a sua instância e cole-a na caixa de texto **URL de Resposta** na seção **Domínio e URLs do Hightail** no portal do Azure.
    
    g. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hightail-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hightail-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hightail-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hightail-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-hightail-test-user"></a>Criação de um usuário de teste do Hightail

O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no Hightail. 

Não há itens de ação para você nesta seção. O Hightail dá suporte ao provisionamento de usuários just-in-time com base nas declarações personalizadas. Se você tiver configurado as declarações personalizadas, como mostrado na seção **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** acima, um usuário será criado automaticamente no aplicativo, caso ainda não exista. 

>[!NOTE]
>Se precisar criar um usuário manualmente, você precisará entrar em contato com a [equipe de suporte do Hightail](mailto:support@hightail.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Hightail.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Hightail, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Hightail**.

    ![Configurar Logon Único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Hightail no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Hightail.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_203.png

