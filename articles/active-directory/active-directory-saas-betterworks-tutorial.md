---
title: "Tutorial: Integração do Azure Active Directory ao BetterWorks | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o BetterWorks."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5bb9505a-be02-46ae-9979-5308715d2b47
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: d6a5b167c0befbd0fe2c65bdd16abc35ed0a659c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-betterworks"></a>Tutorial: integração do Azure Active Directory ao BetterWorks

Neste tutorial, você aprende a integrar o BetterWorks ao Azure AD (Azure Active Directory).

A integração do BetterWorks ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você poderá controlar quem tem acesso ao BetterWorks
- Você pode permitir que seus usuários façam logon automaticamente no BetterWorks (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao BetterWorks, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do BetterWorks

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o BetterWorks da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-betterworks-from-the-gallery"></a>Adicionando o BetterWorks da galeria
Para configurar a integração do BetterWorks ao Azure AD, você precisará adicionar o BetterWorks da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o BetterWorks da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **BetterWorks**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_search.png)

5. No painel de resultados, selecione **BetterWorks** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o BetterWorks, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do BetterWorks é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do BetterWorks.

No BetterWorks, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o BetterWorks, você precisará executar as seguintes tarefas básicas:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do BetterWorks](#creating-a-betterworks-test-user)** – para ter um equivalente de Brenda Fernandes no BetterWorks que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo BetterWorks.

**Para configurar o logon único do Azure AD com o BetterWorks, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **BetterWorks**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_samlbase.png)

3. Na seção **Domínio e URLs do BetterWorks**, se desejar configurar o aplicativo no **modo iniciado pelo IDP**:

    ![Configurar Logon Único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://app.betterworks.com/saml2/metadata/`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://app.betterworks.com/saml2/acs/`

4. Na seção **Domínio e URLs do BetterWorks**, se desejar configurar o aplicativo no **modo iniciado pelo SP**, realize as seguintes etapas:
    
    ![Configurar Logon Único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_url1.png)

    a. Clique em **Mostrar configurações de URL avançadas**.

    b. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://app.betterworks.com`

    > [!NOTE] 
    > Esses não são valores reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon real. Contate a [equipe de suporte do BetterWorks](mailto:support@betterworks.com) para obter esses valores.
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_certificate.png)  

5. O aplicativo BetterWorks espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Gerencie os valores desses atributos na guia “**Atributo**” do aplicativo. A captura de tela a seguir mostra um exemplo disso. 

    ![Configurar Logon Único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_attribute.png)

6. Na caixa de diálogo **Atributos de token SAML** , para cada linha mostrada na tabela a seguir, execute as seguintes etapas:
 
   | Nome do atributo | Valor do atributo |
   | -------------- |  ------------ |
   | saml_token     | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

   a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-betterworks-tutorial/tutorial_officespace_04.png)

    ![Configurar Logon Único](./media/active-directory-saas-betterworks-tutorial/tutorial_officespace_05.png)

   b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha. 

   c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
   d. Clique em **OK**.

7. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-betterworks-tutorial/tutorial_general_400.png)

8. Para configurar o logon único no lado do **BetterWorks**, é necessário enviar o **XML de Metadados** baixado para a [equipe de suporte do BetterWorks](mailto:support@betterworks.com).


> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-betterworks-test-user"></a>Criando um usuário de teste do BetterWorks

Nesta seção, você criará um usuário chamado Brenda Fernandes no BetterWorks. Trabalhe com a [equipe de suporte do BetterWorks](mailto:support@betterworks.com) para adicionar os usuários à plataforma BetterWorks.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao BetterWorks.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao BetterWorks, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **BetterWorks**.

    ![Configurar Logon Único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco BetterWorks no Painel de Acesso, você fará logon automaticamente no seu aplicativo BetterWorks.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_203.png

