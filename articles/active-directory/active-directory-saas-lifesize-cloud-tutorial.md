---
title: "Tutorial: integração do Azure Active Directory ao Lifesize Cloud | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Lifesize Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 7542360f9c75786bf400553090ba0a891d9c2fcc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Tutorial: Integração do Azure Active Directory com o Lifesize Cloud

Neste tutorial, você aprenderá a integrar o Lifesize Cloud ao Azure AD (Azure Active Directory).

A integração do Lifesize Cloud ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Lifesize Cloud
- Você pode habilitar os usuários a fazer logon automaticamente no Lifesize Cloud (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Lifesize Cloud, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Lifesize Cloud

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Lifesize Cloud da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Adição do Lifesize Cloud da galeria
Para configurar a integração do Lifesize Cloud ao Azure AD, você precisará adicionar o Lifesize Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Lifesize Cloud da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Lifesize Cloud**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_search.png)

5. No painel de resultados, selecione **Lifesize Cloud** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Lifesize Cloud, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Lifesize Cloud é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Lifesize Cloud.

No Lifesize Cloud, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Lifesize Cloud, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do Lifesize Cloud](#creating-a-lifesize-cloud-test-user)** – para ter um equivalente de Brenda Fernandes no Lifesize Cloud que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Lifesize Cloud.

**Para configurar o logon único do Azure AD com o Lifesize Cloud, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **Lifesize Cloud**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_samlbase.png)

3. Na seção **Domínio e URLs do Lifesize Cloud**, realize as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://login.lifesizecloud.com/ls/?acs`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://login.lifesizecloud.com/<companyname>`

     
4. Marque **Mostrar configurações avançadas de URL**, execute a seguinte etapa:    
   
    ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_url1.png)

    Na caixa de texto **Estado de Retransmissão**, digite uma URL usando os seguintes padrões: `https://webapp.lifesizecloud.com/?ent=<identifier>`
   
   > [!NOTE] 
   >Observe que esses não são os valores reais. é necessário atualizar esses valores com a URL de Logon, o Estado de retransmissão e o Identificador reais. Entre em contato com a [equipe de suporte do cliente Lifesize Cloud](https://www.lifesize.com/support) para obter a URL de Logon e os valores de identificador e você pode obter o valor do Estado de Retransmissão da configuração de SSO que é explicada posteriormente no tutorial.

4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Lifesize Cloud**, clique em **Configurar Lifesize Cloud** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único do SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_configure.png) 

7. Para configurar o SSO para seu aplicativo, faça logon no aplicativo Lifesize Cloud com privilégios de administrador.

8. No canto superior direito, clique em seu nome e, em seguida, clique em **Configurações Avançadas**.
   
    ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

9. Nas configurações de adiantamento agora, clique em de **configuração de SSO** link. Isso abrirá a página de configuração de SSO para sua instância.
   
    ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

10. Agora, configure os seguintes valores na interface do usuário de configuração de SSO.    
   
    ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
    
    a. Na caixa de texto **Emissor do Provedor de Identidade**, cole o valor da **ID de Entidade do SAML** que você copiou do Portal do Azure.

    b.  Na caixa de texto **URL de Logon**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do Portal do Azure.

    c. Abra seu certificado codificado em base 64 no bloco de notas baixado do portal do Azure, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509**.
  
    d. No mapeamento de atributo do SAML para a caixa de texto Nome, insira o valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    
    e. No mapeamento de atributo SAML para a caixa de texto **Sobrenome**, insira o valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    
    f. No mapeamento de atributo SAML para a caixa de texto **Email**, insira o valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

11. Para verificar a configuração, você pode clicar no botão **Testar**.
   
    >[!NOTE]
    >Para realizar um teste bem-sucedido, você precisa concluir o assistente de configuração no Azure AD e também fornecer acesso a usuários ou grupos que possam executar o teste.

12. Habilite o SSO ao marcar o botão **Habilitar SSO**.

13. Agora, clique no botão **Atualizar** para que todas as configurações sejam salvas. Isso irá gerar o valor de RelayState. Copie o valor de RelayState, que é gerado na caixa de texto, cole-o na caixa de texto **Estado de Retransmissão** na seção **URLs e Domínio do Lifesize Cloud**. 

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-lifesize-cloud-test-user"></a>Criação de um usuário de teste do Lifesize Cloud

Nesta seção, você criará um usuário chamado Brenda Fernandes no Lifesize Cloud. O Lifesize Cloud oferece suporte ao provisionamento automático de usuário. Após a autenticação bem-sucedida no Azure AD, o usuário será provisionado no aplicativo automaticamente. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Lifesize Cloud.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Lifesize Cloud, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Lifesize Cloud**.

    ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Lifesize Cloud no Painel de Acesso, você deve entrar na página de logon no aplicativo Lifesize Cloud.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png

