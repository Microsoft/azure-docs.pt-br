---
title: "Tutorial: Integração do Azure Active Directory com o Zendesk | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Zendesk."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 51c06d838c5ed6286dfb99ea25faaaf33bad5f3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Tutorial: Integração do Active Directory do Azure com o Zendesk

Neste tutorial, você aprenderá a integrar o Zendesk ao Azure AD (Azure Active Directory).

A integração do Zendesk ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Zendesk
- Você pode permitir que seus usuários façam logon automaticamente no Zendesk (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zendesk, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Zendesk


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Zendesk da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-zendesk-from-the-gallery"></a>Adicionar o Zendesk da galeria
Para configurar a integração do Zendesk ao Azure AD, você precisa adicionar o Zendesk por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zendesk por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação esquerdo, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Zendesk**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_search.png)

5. No painel de resultados, selecione **Zendesk** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Zendesk, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Zendesk é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no Zendesk.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como sendo o valor de **nome de usuário** no Zendesk.

Para configurar e testar o logon único do Azure AD com o Zendesk, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Zendesk](#creating-a-zendesk-test-user)** – para ter um equivalente de Brenda Fernandes no Zendesk vinculado à representação de um usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único em seu aplicativo Zendesk.

**Para configurar o logon único do Azure AD com o Zendesk, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Zendesk**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_samlbase.png)

3. Na seção **URLs e Domínio do Zendesk**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_url.png)

    a. Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão: `https://<subdomain>.zendesk.com`

    b. Na caixa de texto **Identificador**, digite o valor usando o seguinte padrão: `https://<subdomain>.zendesk.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e a URL do Identificador reais. Entre em contato com a [equipe de suporte do Zendesk](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) para obter esses valores. 

4. O Zendesk espera que as declarações SAML estejam em um formato específico. Não há nenhum atributo SAML obrigatório, mas opcionalmente você pode adicionar um atributo da seção **Atributos de Usuário** seguindo as etapas abaixo: 

     ![Configurar Logon Único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes1.png)

    a. Clique na caixa de seleção **Exibir e editar todos os outros atributos**.
     
    ![Configurar Logon Único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes2.png)
   
    b. Clique em **Adicionar Atributo** para abrir a caixa de diálogo **Adicionar atributo**.
    
    ![Configurar Logon Único](./media/active-directory-saas-zendesk-tutorial/tutorial_attribute_05.png)

    c. Na caixa de texto **Nome**, digite o nome do atributo (por exemplo, **emailaddress**).
    
    d. Na lista **Valor**, selecione o valor do atributo (como **user.mail**).
    
    e. Clique em **Ok**
 
    > [!NOTE] 
    > Você pode usar atributos de extensão para adicionar atributos que não estão no Azure AD por padrão. Clique em [Atributos do usuário que podem ser definidos no SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) para obter a lista completa de atributos SAML que o **Zendesk** aceita. 

5. Na seção **Certificado de Autenticação SAML**, copie o valor da **IMPRESSÃO DIGITAL** do certificado.

    ![Configurar Logon Único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_certificate.png) 

6. Na seção **Configuração do Zendesk**, clique em **Configurar o Zendesk** para abrir a janela **Configurar logon**. Copie a **URL do serviço de logon único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_configure.png) 

7. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Zendesk como administrador.

8. Clique em **Administrador**.

9. No painel de navegação à esquerda, clique em **Configurações** e em **Segurança**.

10. Na página **Segurança**, realize as seguintes etapas: 
   
     ![Segurança](./media/active-directory-saas-zendesk-tutorial/ic773089.png "Segurança")

    ![Logon Único](./media/active-directory-saas-zendesk-tutorial/ic773090.png "Logon Único")

     a. Clique na guia **Administrador e Agentes**.

     b. Selecione **SSO (logon único) e SAML** e, em seguida, selecione **SAML**.

     c. Na caixa de texto **URL do SSO do SAML**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do Portal do Azure. 

     d. Na caixa de texto **URL de Logoff Remoto**, cole o valor da **URL de Saída** que você copiou do Portal do Azure.
        
     e. Na caixa de texto **Impressão Digital do Certificado**, cole o valor de **Impressão Digital** do certificado copiado do Portal do Azure.
     
     f. Clique em **Salvar**.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zendesk-tutorial/create_aaduser_01.png) 

2. Para exibir a lista de usuários, vá para **Usuários e grupos** e clique em **Todos os usuários**.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zendesk-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **Adicionar** para abrir a caixa de diálogo **Usuário**.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zendesk-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zendesk-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 

### <a name="creating-a-zendesk-test-user"></a>Criação de um usuário de teste do Zendesk

Para permitir que os usuários do Azure AD façam logon no **Zendesk**, eles devem ser provisionados no **Zendesk**.  
Dependendo da função atribuída nos aplicativos, esse é o comportamento esperado:

 1. As contas de **Usuário final** são provisionadas automaticamente ao entrar.
 2. Contas de **Agente** e **Admin** precisam ser provisionadas manualmente no **Zendesk** antes de entrar.
 
**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Zendesk** .

2. Selecione a guia **Lista de Clientes** .

3. Selecione a guia **Usuário** e clique em **Adicionar**.
   
    ![Adicionar Usuário](./media/active-directory-saas-zendesk-tutorial/ic773632.png "Adicionar Usuário")
4. Digite o endereço de uma conta existente do Azure AD que você deseja provisionar e clique em **Salvar**.
   
    ![Novo Usuário](./media/active-directory-saas-zendesk-tutorial/ic773633.png "Novo Usuário")

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Zendesk ou APIs fornecidas pelo Zendesk para provisionar as contas de usuário do AAD.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Zendesk.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Zendesk, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Zendesk**.

    ![Configurar Logon Único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Zendesk no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Zendesk.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_203.png
