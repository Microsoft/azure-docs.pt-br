---
title: "Tutorial: Integração do Azure Active Directory com o NetSuite | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Netsuite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: jeedes
ms.openlocfilehash: f2184f426c0e60ea2b6ce029b80d3d63821794d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Tutorial: Integração do Azure Active Directory com o Netsuite

Neste tutorial, você aprende a integrar o Netsuite ao Azure AD (Azure Active Directory).

A integração do Netsuite ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Netsuite
- É possível permitir que os usuários se conectem automaticamente ao Netsuite (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Netsuite, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Netsuite

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Netsuite por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-netsuite-from-the-gallery"></a>Adicionando o Netsuite por meio da galeria
Para configurar a integração do Netsuite ao Azure AD, é necessário adicionar o Netsuite à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Netsuite por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Netsuite**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_search.png)

5. No painel de resultados, selecione **Netsuite** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o Netsuite, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Netsuite é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Netsuite.

Essa relação de vínculo é estabelecida com a atribuição do valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no Netsuite.

Para configurar e testar o logon único do Azure AD com o Netsuite, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do Netsuite](#creating-a-netsuite-test-user)** – para ter um equivalente de Brenda Fernandes no Netsuite que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Netsuite.

**Para configurar o logon único do Azure AD com o Netsuite, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Netsuite**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_samlbase.png)

3. Na seção **Domínio e URLs do Netsuite**, realize as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_url.png)

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.netsuite.com/saml2/acs` `https://<tenant-name>.na1.netsuite.com/saml2/acs` `https://<tenant-name>.na2.netsuite.com/saml2/acs` `https://<tenant-name>.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    > [!NOTE] 
    > Esses não são valores reais. Atualize esses valores com a URL de Resposta real. Contate a [equipe de suporte do Netsuite](http://www.netsuite.com/portal/services/support.shtml) para obter esses valores.
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo XML em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-netsuite-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Netsuite**, clique em **Configurar o Netsuite** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_configure.png) 

7. Abra uma nova guia no navegador e entre no site Netsuite de sua empresa como administrador.

8. Na barra de ferramentas na parte superior da página, clique em **Instalação** e depois em **Gerenciador de Instalação**.

    ![Configurar Logon Único](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

9. Na lista **Tarefas de Instalação**, selecione **Integração**.

    ![Configurar Logon Único](./media/active-directory-saas-Netsuite-tutorial/ns-integration.png)

10. Na seção **Gerenciar Autenticação**, clique em **Logon Único do SAML**.

    ![Configurar Logon Único](./media/active-directory-saas-Netsuite-tutorial/ns-saml.png)

11. Na página **Instalação do SAML** , realize as seguintes etapas:
   
    a. Copie o valor da **URL do Serviço de Logon Único SAML** da seção **Referência Rápida** de **Configurar logon** e cole-o no campo **Página de Logon do Provedor de Identidade** do Netsuite.

    ![Configurar Logon Único](./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png)
  
    b. No NetSuite, selecione **Método de Autenticação Primária**.

    c. Para o campo **Metadados do Provedor de Identidade SAMLV2**, selecione **Carregar Arquivo de Metadados IDP**. Em seguida, clique em **Procurar** para carregar o arquivo de metadados baixado no portal do Azure.

    ![Configurar Logon Único](./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png)

    d. Clique em **Enviar**.

12. No Azure AD, clique na caixa de seleção **Exibir e editar todos os outros atributos de usuário** e adicione um atributo.

    ![Configurar Logon Único](./media/active-directory-saas-Netsuite-tutorial/ns-attributes.png)

13. Para o campo **Nome do Atributo**, digite `account`. Para o campo **Valor do Atributo** , digite a ID de conta do NetSuite. Esse valor é constante e muda com a conta. Instruções sobre como localizar a ID de conta estão abaixo:

      ![Configurar Logon Único](./media/active-directory-saas-Netsuite-tutorial/ns-add-attribute.png)

    a. No Netsuite, clique em **Instalação** no menu de navegação superior.

    b. Em seguida, clique na seção **Tarefas de Instalação** no menu de navegação à esquerda, selecione a seção **Integração** e clique em **Preferências de Serviços Web**.

    c. Copie a ID de conta do NetSuite e cole-a no campo **Valor do Atributo** no Azure AD.

    ![Configurar Logon Único](./media/active-directory-saas-Netsuite-tutorial/ns-account-id.png)

14. Antes que os usuários possam realizar logon único no NetSuite, eles devem primeiro ter as permissões apropriadas no NetSuite. Siga as instruções abaixo para atribuir essas permissões.

    a. No menu de navegação superior, clique em **Instalação** e depois em **Gerenciador de Instalação**.
      
      ![Configurar Logon Único](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    b. No menu de navegação à esquerda, selecione **Usuários/Funções** e, em seguida, clique em **Gerenciar Funções**.
      
      ![Configurar Logon Único](./media/active-directory-saas-Netsuite-tutorial/ns-manage-roles.png)

    c. Clique em **Nova Função**.

    d. Digite um **Nome** para a nova função e marque a caixa de seleção **Somente Logon Único**.
      
      ![Configurar Logon Único](./media/active-directory-saas-Netsuite-tutorial/ns-new-role.png)

    e. Clique em **Salvar**.

    f. No menu na parte superior, clique em **Permissões**. Em seguida, clique em **Instalação**.
      
       ![Configurar Logon Único](./media/active-directory-saas-Netsuite-tutorial/ns-sso.png)

    g. Selecione **Instalar o Logon Único do SAM** e, em seguida, clique em **Adicionar**.

    h. Clique em **Salvar**.

    i. No menu de navegação superior, clique em **Instalação** e depois em **Gerenciador de Instalação**.
      
       ![Configurar Logon Único](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    j. No menu de navegação à esquerda, selecione **Usuários/Funções** e, em seguida, clique em **Gerenciar Usuários**.
      
       ![Configurar Logon Único](./media/active-directory-saas-Netsuite-tutorial/ns-manage-users.png)

    k. Selecione um usuário de teste. Em seguida, clique em **Editar**.
      
       ![Configurar Logon Único](./media/active-directory-saas-Netsuite-tutorial/ns-edit-user.png)

    l. Na caixa de diálogo Funções, selecione a função que você criou e clique em **Adicionar**.
      
       ![Configurar Logon Único](./media/active-directory-saas-Netsuite-tutorial/ns-add-role.png)

    m. Clique em **Salvar**.
    
> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-netsuite-tutorial/create_aaduser_01.png) 

2.  Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-netsuite-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **Adicionar** para abrir a caixa de diálogo **Usuário**.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-netsuite-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-netsuite-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 

### <a name="creating-a-netsuite-test-user"></a>Criando um usuário de teste do Netsuite

Nesta seção, um usuário chamado Brenda Fernandes é criado no Netsuite. O Netsuite dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão.
Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Netsuite, um novo será criado quando você tentar acessar o Netsuite.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Netsuite.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Netsuite, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Netsuite**.

    ![Configurar Logon Único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Para testar as configurações de logon único, abra o Painel de Acesso em [https://myapps.microsoft.com](https://myapps.microsoft.com/), entre na conta de teste e clique em **Netsuite**.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Provisionamento de Usuário](active-directory-saas-netsuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_203.png

