---
title: "Tutorial: Integração do Azure Active Directory ao HR2day by Merces | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o HR2day by Merces."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.openlocfilehash: 77e2fcf9c306259286b15767f3a992510d6d79d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Tutorial: Integração do Active Directory do Azure ao HR2day by Merces

Neste tutorial, você aprenderá a integrar o HR2day by Merces ao Azure AD (Azure Active Directory).

A integração do HR2day by Merces ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao HR2day by Merces.
- Você pode permitir que os usuários façam logon automaticamente no HR2day by Merces com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local: o novo Portal do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS ao Azure AD, consulte [O que é o acesso de aplicativos e o logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure ao HR2day by Merces, você precisará dos seguintes itens:

- Uma assinatura do Azure AD.
- Uma assinatura do HR2day by Merces habilitada para logon único.

> [!NOTE]
> Para testar as etapas deste tutorial, não recomendamos o uso de um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção a menos que seja necessário.
- Obtenha uma [avaliação gratuita de um mês do Azure AD](https://azure.microsoft.com/pricing/free-trial/) se você ainda não o fez.  

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito aqui consiste em dois blocos de construção principais:

1. Adicionar o HR2day by Merces da galeria.
2. Configurar e testar o logon único do Azure AD.

## <a name="add-hr2day-by-merces-from-the-gallery"></a>Adicionar o HR2day by Merces da galeria
Para configurar a integração do HR2day by Merces ao Azure AD, adicione o HR2day by Merces da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o HR2day by Merces da galeria, execute as seguintes etapas:**

1. No [Portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione o ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Executar **Aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **HR2day by Merces**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_search.png)

5. No painel de resultados, selecione **HR2day by Merces** e selecione o botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o logon único do Azure AD com o HR2day by Merces, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do HR2day by Merces é equivalente a um usuário do Azure AD. Em outras palavras, você precisa estabelecer um vínculo entre um usuário do Azure AD e o usuário relacionado no HR2day by Merces.

No HR2day by Merces, atribua o **nome de usuário** no Azure AD ao **Nome de usuário** para estabelecer a relação.

Para configurar e testar o logon único do AD do Azure com o HR2day by Merces, você precisará concluir os seguintes blocos de construção:

1. [Configurar o logon único do Azure AD](#configuring-azure-ad-single-sign-on): habilite seus usuários a usar esse recurso.
2. [Criar um usuário de teste do Azure AD](#creating-an-azure-ad-test-user): para testar o logon único do Azure AD com Brenda Fernandes.
3. [Criar um usuário de teste do HR2day by Merces](#creating-an-hr2day-by-merces-test-user): crie um equivalente de Brenda Fernandes no HR2day by Merces que esteja vinculado à representação do usuário no Azure AD.
4. [Atribuir o usuário de teste do Azure AD](#assigning-the-azure-ad-test-user): permita que Brenda Fernandes use o logon único do Azure AD.
5. [Testar o logon único](#testing-single-sign-on): verifique se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo HR2day by Merces.

**Para configurar o logon único do Azure AD com o HR2day by Merces, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **HR2day by Merces**, selecione **Logon único**.

    ![Configurar o Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

3. Na seção **Domínio e URLs no HR2day by Merces**, execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. Na caixa de texto **URL de Entrada**, digite uma URL usando o seguinte padrão: `https://<tenantname>.force.com/<instancename>`.

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://hr2day.force.com/<companyname>`.

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do HR2day by Merces](mailto:servicedesk@merces.nl) para obter esses valores. 
 


4. Na seção **Certificado de Autenticação do SAML**, selecione **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

5. Esta seção descreve como permitir que os usuários se autentiquem no HR2day com as respectivas contas do Azure AD. Eles fazem isso usando a federação, que é baseada no protocolo SAML.

    Seu aplicativo HR2day by Merces espera as declarações de SAML em um formato específico, o que exige adicionar mapeamentos de atributo personalizados ao token SAML. As capturas de tela a seguir mostram um exemplo disso. 

    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    Antes de configurar a instrução de declaração SAML, você precisará entrar em contato com a [equipe de suporte do Cliente HR2day by Merces](mailto:servicedesk@merces.nl) e solicitar o valor do atributo de identificador exclusivo para seu locatário. Você precisa desse valor para concluir as etapas na próxima seção. 

6. Na caixa de diálogo **Logon único**, na seção **Atributos do Usuário**, configure o atributo do token SAML como mostrado na imagem a seguir. Em seguida, execute as etapas a seguir.
    
      | Nome do atributo    |   Valor do atributo |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | join([mail],"102938475Z","@" |
    
      a. Para abrir a caixa de diálogo **Adicionar Atributo**, selecione **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_04.png)

    ![Configurar o Logon Único](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_05.png)

    b. Na caixa **Nome**, digite **ATTR_LOGINCLAIM**.

    c. Na lista **Valor**, selecione **Join()**.

    d. Na lista **String1**, selecione **user.mail**.

    e. Para **String2**, digite o identificador exclusivo fornecido pela equipe do HR2day.

    f. Na caixa **Separador**, digite **@**.
    
    g. Selecione **Ok**.

7. Selecione o botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_general_400.png)

8. Na seção **Configuração do HR2day by Merces**, selecione **Configurar o HR2day by Merces** para abrir a janela **Configurar o logon**. Copie a **URL de Saída**, a **ID da Entidade SAML** e a **URL do Serviço de Logon Único SAML** da seção **Referência Rápida**.

    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

9. Para configurar o SSO para seu aplicativo, entre em contato com o [HR2day pela equipe de suporte de cliente Merces](mailTo:servicedesk@merces.nl). Anexe o arquivo **Certificate(Base64)** baixado ao seu email. Forneça também a **URL de Saída**, a **ID de Entidade SAML** e a **URL do Serviço de Logon Único SAML** para que possam ser configuradas para a integração com o SSO.

    > [!NOTE]
    >Mencione à equipe do Merces que essa integração precisa que a ID da Entidade seja definida com o padrão **https://hr2day.force.com/INSTANCENAME**.

    > [!TIP]
    >É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar este aplicativo por meio da seção **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único**. Em seguida, acesse a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida na [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, selecione o ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_01.png) 

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, em seguida, selecione **Todos os usuários**.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o **endereço de email** de Brenda Fernandes.

    c. Selecione **Mostrar Senha** e anote o valor da senha.

    d. Selecione **Criar**.
 
### <a name="create-an-hr2day-by-merces-test-user"></a>Criar um usuário de teste do HR2day by Merces

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no HR2day by Merces. Para adicionar usuários à conta do HR2day, trabalhe com a [equipe de suporte ao cliente HR2day by Merces](mailto:servicedesk@merces.nl). 

> [!NOTE]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao cliente do HR2day by Merces](mailto:servicedesk@merces.nl).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao HR2day by Merces.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao HR2day by Merces, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos, vá para a exibição de diretório e, em seguida, vá para **Aplicativos empresariais**. Em seguida, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **HR2day by Merces**.

    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![Atribuir usuário][202] 

4. Selecione o botão **Adicionar**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, na lista **Usuários**, selecione **Brenda Fernandes**.

6. Clique no botão **Selecionar**.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  

Quando você seleciona o bloco HR2day by Merces no Painel de Acesso, você entra automaticamente em seu aplicativo HR2day by Merces.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png

