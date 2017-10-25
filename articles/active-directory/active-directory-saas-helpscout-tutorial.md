---
title: "Tutorial: Integração do Azure Active Directory com o Help Scout | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Help Scout."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: fe8775bd39173b2adf1f82d32f5e851ef1c19931
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Tutorial: integração do Azure Active Directory com o Help Scout

Neste tutorial, você aprenderá a integrar o Help Scout ao Azure AD (Azure Active Directory).

A integração do Help Scout ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Help Scout.
- Você pode permitir que seus usuários façam logon automaticamente no Help Scout usando logon único com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Help Scout, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Help Scout

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Help Scout por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-help-scout-from-the-gallery"></a>Adicionar o Help Scout por meio da galeria
Para configurar a integração do Help Scout com o Azure AD, você precisará adicionar o Help Scout à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Help Scout por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Help Scout**, selecione **Help Scout** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Help Scout na lista de resultados](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Help Scout, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Help Scout é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Help Scout.

O Help Scout usa endereços de email para logons, portanto, para estabelecer a relação de link, use o mesmo **endereço de email** como **nome de usuário** no Azure AD.

Para configurar e testar o logon único do Azure AD com o Help Scout, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Help Scout](#create-a-help-scout-test-user)** – para ter um equivalente de Brenda Fernandes no Help Scout que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo Help Scout.

**Para configurar o logon único do Azure AD com o HPE SaaS, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **Help Scout**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. Na seção **Domínio e URLs do Help Scout**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de Domínio e URLs do Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

    a. **Identificador** é o **"URI do Público-Alvo (ID de Entidade do Provedor de Serviço)"** do Help Scout, começa com `urn:`

    b. **URL de Resposta** é a **"URL de Postback (URL do Serviço do Consumidor de Declaração)"** do Help Scout, começa com `https://` 

    > [!NOTE] 
    > Os valores nessas URLs são apenas para demonstração. Atualize esses valores com a URL de Resposta e o Identificador reais. Você obtém esses valores da guia **Logon único** na seção de Autenticação, que é explicada posteriormente no tutorial.

4. Se desejar configurar o aplicativo no modo iniciado pelo **SP**, marque a opção **Mostrar configurações de URL avançadas** e realize a seguinte etapa:

    ![Informações de logon único de Domínio e URLs do Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL como: `https://secure.helpscout.net/members/login/`
     
5. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)


7. Na seção **Configuração do Help Scout**, clique em **Configurar o Help Scout** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida**.

    ![Configurar Logon Único](./media/active-directory-saas-helpscout-tutorial/config.png) 

8. Em outra janela do navegador da Web, faça logon em seu site de empresa do Help Scout como um administrador.

9. Depois de estar conectado, clique em **"Gerenciar"** no menu superior e, em seguida, selecione **"Empresa"** no menu suspenso.

    ![Configurar Logon Único](./media/active-directory-saas-helpscout-tutorial/settings1.png) 
 
10. Selecione **"Autenticação"** no menu à esquerda. 

    ![Configurar Logon Único](./media/active-directory-saas-helpscout-tutorial/settings2.png) 

11. Isso direciona-o à seção de configurações do SAML e executa as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-helpscout-tutorial/settings3.png) 
 
    a. Copie o valor da **URL de Postback (URL do Serviço do Consumidor de Declaração)** e cole o valor na caixa **URL de Resposta** no portal do Azure, na seção **Domínio e URLs** do Help Scout.
    
    b. Copie o valor da **URI do Público-Alvo (ID de Entidade do Provedor de Serviço)** e cole o valor na caixa **Identificador** no portal do Azure, na seção **Domínio e URLs** do Help Scout.

12. Ative a opção **Habilitar SAML** e execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-helpscout-tutorial/settings4.png) 
 
    a. Na caixa de texto **URL de Logon Único**, cole o valor da **URL de Serviço de Logon Único** copiado do portal do Azure.
    
    b. Clique em **Carregar Certificado** para carregar o **Certificado(Base64)** baixado no portal do Azure.

    c. Insira os domínios de email de sua organização, por exemplo: `contoso.com` na caixa de texto **Domínios de Email**. Você pode separar múltiplos domínios com uma vírgula. A qualquer momento um Administrador ou Usuário do Help Scout que insere esse domínio específico na [Página de logon do Help Scout](https://secure.helpscout.net/members/login/) serão roteados para o Provedor de Identidade para autenticar com suas credenciais.

    d. Por fim, você pode alternar **Forçar Logon SAML** se desejar que os Usuários façam logon somente no Help Scout por meio desse método. Se você ainda desejar deixar a opção para que eles possam entrar com suas credenciais do Help Scout, pode a deixar desativada. Mesmo se estiver habilitada, um Proprietário de Conta sempre será capaz de fazer logon no Help Scout com a senha da conta.

    e. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-help-scout-test-user"></a>Criar um usuário de teste do Help Scout

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Help Scout. O Help Scout dá suporte ao provisionamento Just-In-Time, que é habilitado por padrão.

Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Help Scout, um novo será criado quando você tentar acessar o Help Scout.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Help Scout.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Help Scout, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Help Scout**.

    ![O link do Help Scout na lista de Aplicativos](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Help Scout no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Help Scout.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png

