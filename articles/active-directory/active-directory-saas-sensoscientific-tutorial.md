---
title: "Tutorial: Integração do Azure Active Directory ao Sistema de Monitoramento de Temperatura Sem Fio SensoScientific | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Sistema de Monitoramento de Temperatura Sem Fio SensoScientific."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeedes
ms.openlocfilehash: fa6242cf7f9559ca394ffde2e5e734cb935b03dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Tutorial: Integração do Azure Active Directory ao Sistema de Monitoramento de Temperatura Sem Fio SensoScientific

Neste tutorial, você aprende a integrar o Sistema de Monitoramento de Temperatura Sem Fio SensoScientific ao Azure AD (Azure Active Directory).

A integração do Sistema de Monitoramento de Temperatura Sem Fio SensoScientific ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Sistema de Monitoramento de Temperatura Sem Fio SensoScientific
- Você pode permitir que os usuários sejam conectados automaticamente ao Sistema de Monitoramento de Temperatura Sem Fio SensoScientific (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Sistema de Monitoramento de Temperatura Sem Fio SensoScientific, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Sistema de Monitoramento de Temperatura Sem Fio SensoScientific habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Sistema de Monitoramento de Temperatura Sem Fio SensoScientific por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Adicionar o Sistema de Monitoramento de Temperatura Sem Fio SensoScientific por meio da galeria
Para configurar a integração do Sistema de Monitoramento de Temperatura Sem Fio SensoScientific no Azure AD, você precisa adicionar o Sistema de Monitoramento de Temperatura Sem Fio SensoScientific à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Sistema de Monitoramento de Temperatura Sem Fio SensoScientific por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Sistema de Monitoramento de Temperatura Sem Fio SensoScientific**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_search.png)

5. No painel de resultados, selecione **Sistema de Monitoramento de Temperatura Sem Fio SensoScientific** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o Sistema de Monitoramento de Temperatura Sem Fio SensoScientific com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Sistema de Monitoramento de Temperatura Sem Fio SensoScientific é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Sistema de Monitoramento de Temperatura Sem Fio SensoScientific.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD ao valor de **Nome de Usuário** no Sistema de Monitoramento de Temperatura Sem Fio SensoScientific.

Para configurar e testar o logon único do Azure AD com o Sistema de Monitoramento de Temperatura Sem Fio SensoScientific, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do Sistema de Monitoramento de Temperatura Sem Fio SensoScientific](#creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user)** – para ter um equivalente de Brenda Fernandes no Sistema de Monitoramento de Temperatura Sem Fio SensoScientific que está vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Sistema de Monitoramento de Temperatura Sem Fio SensoScientific.

**Para configurar o logon único do Azure AD com o Sistema de Monitoramento de Temperatura Sem Fio SensoScientific, realize as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Sistema de Monitoramento de Temperatura Sem Fio SensoScientific**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_samlbase.png)

3. Na seção **Domínio e URLs do Sistema de Monitoramento de Temperatura Sem Fio SensoScientific**, não é necessário executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure:

    ![Configurar Logon Único](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_url.png)

4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_400.png)

6. Na seção **Sistema de Monitoramento de Temperatura Sem Fio SensoScientific**, clique em **Configurar o Sistema de Monitoramento de Temperatura Sem Fio SensoScientific** para abrir a janela **Configurar logon**. Copie a **URL de Logoff, a ID de Entidade do SAML** e a **URL do Serviço de Logon Único do SAML** da **seção Referência rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_configure.png) 

7. Faça logon no aplicativo Sistema de Monitoramento de Temperatura Sem Fio SensoScientific como administrador.

8. No menu de navegação na parte superior, clique em **Configuração** e acesse **Configurar** em **Logon Único** para abrir as Configurações de Logon Único.

    ![Configurar Logon Único](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png) 

9. No formulário **Configurações de Logon Único**, realize as seguintes etapas:
 
    a. Selecione **Nome do Emissor** como Azure AD.
    
    b. Cole a **ID de Entidade do SAML** que você copiou do portal do Azure na caixa de texto URL do Emissor.
    
    c. Cole a **URL de Serviço de Logon Único do SAML** que você copiou do portal do Azure na caixa de texto URL de Serviço de Logon Único.

    d. Cole a **URL de Logoff** que você copiou do portal do Azure na caixa de texto URL de Serviço de Logon Único.

    e. Procure o certificado que você baixou do portal do Azure e carregue-o aqui.
    
    f. Clique em **Salvar**.
  
> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD](https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Criando um usuário de teste do Sistema de Monitoramento de Temperatura Sem Fio SensoScientific

Para permitir que os usuários do Azure AD façam logon no Sistema de Monitoramento de Temperatura Sem Fio SensoScientific, eles devem ser provisionados no Sistema de Monitoramento de Temperatura Sem Fio SensoScientific. Trabalhe com a [equipe de suporte do Sistema de Monitoramento de Temperatura Sem Fio SensoScientific](https://www.sensoscientific.com/contact-us/) para adicionar os usuários à plataforma Sistema de Monitoramento de Temperatura Sem Fio SensoScientific. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Sistema de Monitoramento de Temperatura Sem Fio SensoScientific.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Sistema de Monitoramento de Temperatura Sem Fio SensoScientific, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Sistema de Monitoramento de Temperatura Sem Fio SensoScientific**.

    ![Configurar o logon único](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso. Clique no bloco Sistema de Monitoramento de Temperatura Sem Fio SensoScientific no Painel de Acesso e você será automaticamente conectado ao aplicativo Sistema de Monitoramento de Temperatura Sem Fio SensoScientific. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_203.png

