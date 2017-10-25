---
title: "Tutorial: Integração do Azure Active Directory ao Datahug | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Datahug."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5c0dc1ea-7ff4-4554-b60b-0f2fa9f5abaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: jeedes
ms.openlocfilehash: ec431dd5ccfa53e4b975e46da247704dd1e15c2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-datahug"></a>Tutorial: integração do Azure Active Directory ao Datahug

Neste tutorial, você aprenderá a integrar o Datahug ao Azure AD (Azure Active Directory).

A integração do Datahug ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Datahug
- Você pode permitir que usuários façam logon automaticamente no Datahug (Logon Único) com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte. [O que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Datahug, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Datahug com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Datahug da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-datahug-from-the-gallery"></a>Adicionar o Datahug da galeria
Para configurar a integração do Datahug ao Azure AD, é necessário adicionar o Datahug da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o Datahug da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Datahug**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_search.png)

5. No painel de resultados, selecione **Datahug** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Datahug, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Datahug é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Datahug.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como o valor de **Nome de usuário** no Datahug.

Para configurar e testar o logon único do Azure AD com o Datahug, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Datahug](#creating-a-datahug-test-user)** – para ter um equivalente de Brenda Fernandes no Datahug que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você vai habilitar o logon único do Azure AD no Portal do Azure e configurar o logon único em seu aplicativo Datahug.

**Para configurar o logon único do Azure AD com o Datahug, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Datahug**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_samlbase.png)

3. Na seção **Domínio e URLs do Datahug**, se você desejar configurar o aplicativo em modo iniciado pelo **IDP**:

    ![Configurar o logon único](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_ur1.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://apps.datahug.com/identity/<uniqueID>`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://apps.datahug.com/identity/<uniqueID>/acs`

4. Marque **Mostrar configurações de URL avançadas**. Se quiser configurar o aplicativo no modo iniciado em **SP**:

    ![Configurar o logon único](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_url2.png)

    Na caixa de texto **URL de Logon**, digite uma URL como: `https://apps.datahug.com/`
     
    > [!NOTE] 
    > Esses não são os valores reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Aqui, sugerimos que você use o valor exclusivo de cadeia de caracteres no Identificador e URL de Resposta. Contate a [equipe de suporte do cliente Datahug](http://datahug.com/about/contact-us/) para obter esses valores. 

5. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_certificate.png) 

6.  Marque **"Mostrar configurações avançadas de assinatura de certificado"** e execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_cert.png)

    a. Em **Opção de Assinatura**, selecione **Assinar declaração SAML**.
    
    b. Em **Algoritmo de assinatura**, selecione **SHA1**.
 
7. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-datahug-tutorial/tutorial_general_400.png)
    
8. Na seção **Configuração do Datahug**, clique em **Configurar Datahug** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML** e a **URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_configure.png) 

9. Para configurar logon único no lado do **Datahug**, é necessário enviar o **XML de metadados**, a **ID da Entidade SAML** e a **URL do Serviço de Logon Único SAML** baixados para o [suporte do Datahug](http://datahug.com/about/contact-us/). Eles configuram esse aplicativo para que tenham a conexão de SSO do SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-datahug-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-datahug-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-datahug-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-datahug-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-datahug-test-user"></a>Criar um usuário de teste Datahug

Para permitir que os usuários do Azure AD façam logon no Datahug, eles devem ser provisionados no Datahug.  
Quando se usa o Datahug, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Datahug como administrador.

2. Passe o mouse sobre a **engrenagem** no canto superior direito e clique em **Configurações**
   
   ![Adicionar Funcionário](./media/active-directory-saas-datahug-tutorial/1.png)

3. Escolha **Pessoas** e clique na guia **Adicionar Usuários**

    ![Adicionar Funcionário](./media/active-directory-saas-datahug-tutorial/2.png)

4. Digite o email da pessoa para a qual você gostaria de criar uma conta e clique em **Adicionar**.

    ![Adicionar Funcionário](./media/active-directory-saas-datahug-tutorial/3.png)

    > [!NOTE] 
    > Você pode enviar o email de registro de usuário selecionando a caixa de seleção **Enviar email de boas-vindas**.  
    > Se você estiver criando uma conta para o Salesforce, não envie o email de boas-vindas.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Datahug.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Datahug, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Datahug**.

    ![Configurar o logon único](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.
Quando você clicar no bloco Datahug no Painel de Acesso, você deverá fazer logon automaticamente no seu aplicativo Datahug. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_203.png

