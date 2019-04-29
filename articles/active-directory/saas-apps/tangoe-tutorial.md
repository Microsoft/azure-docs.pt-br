---
title: 'Tutorial: Integração do Azure Active Directory ao Tangoe Command Premium Mobile| Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Tangoe Command Premium Mobile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c9f410fa890df7aac3c3bf4d89468b92e69ba38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60869049"
---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Tutorial: Integração do Azure Active Directory ao Tangoe Command Premium Mobile

Neste tutorial, você aprenderá como integrar o Tangoe Command Premium Mobile ao Azure AD (Azure Active Directory).

A integração do Tangoe Command Premium Mobile ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Tangoe Command Premium Mobile
- Você pode habilitar os usuários a entrar automaticamente no Tangoe Command Premium Mobile (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Tangoe Command Premium Mobile, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do Tangoe Command Premium Mobile habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Tangoe Command Premium Mobile da galeria
1. Configurar e testar logon único do Azure AD

## <a name="add-tangoe-command-premium-mobile-from-the-gallery"></a>Adicionar o Tangoe Command Premium Mobile da galeria
Para configurar a integração do Tangoe Command Premium Mobile ao Azure AD, você precisará adicionar o Tangoe Command Premium Mobile da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Tangoe Command Premium Mobile por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

1. Na caixa de pesquisa, digite **Tangoe Command Premium Mobile**, selecione **Tangoe Command Premium Mobile** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Adicionar o Tangoe Command Premium Mobile da galeria](./media/tangoe-tutorial/tutorial_tangoe_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o logon único do Azure AD com o Tangoe Command Premium Mobile, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Tangoe Command Premium Mobile é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Tangoe Command Premium Mobile.

No Tangoe Command Premium Mobile, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Tangoe Command Premium Mobile, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Tangoe Command Premium Mobile](#create-a-tangoe-command-premium-mobile-test-user)** – para ter um equivalente de Brenda Fernandes no Tangoe Command Premium Mobile vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Testar o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Tangoe Command Premium Mobile.

**Para configurar o logon único do Azure AD com o Tangoe Command Premium Mobile, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Tangoe Command Premium Mobile**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Logon único baseado em SAML](./media/tangoe-tutorial/tutorial_tangoe_samlbase.png)

1. Na seção **Domínio e URLs do Tangoe Command Premium Mobile**, execute as seguintes etapas:

    ![Domínio e URLs do Tangoe Command Premium Mobile](./media/tangoe-tutorial/tutorial_tangoe_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=<tenant issuer>&TARGET=<target page url>`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://sso.tangoe.com/sp/ACS.saml2`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao cliente do Tangoe Command Premium Mobile](https://www.tangoe.com/contact-us/) para obter esses valores. 

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Seção Certificado de Autenticação SAML](./media/tangoe-tutorial/tutorial_tangoe_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar](./media/tangoe-tutorial/tutorial_general_400.png)
    
1. Na seção **Configuração do Tangoe Command Premium Mobile**, clique em **Configurar o Tangoe Command Premium Mobile** para abrir a janela **Configurar o logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Seção Configuração do Tangoe Command Premium Mobile](./media/tangoe-tutorial/tutorial_tangoe_configure.png) 

1. Para que o SSO seja configurado para seu aplicativo, contate sua [equipe de suporte ao cliente do Tangoe Command Premium Mobile](https://www.tangoe.com/contact-us/) e forneça o seguinte:

   - O arquivo de metadados baixado
   - A **ID da entidade SAML**
   - A **URL do serviço de logon único SAML**
   - A **URL de saída**

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/tangoe-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Usuários e grupos -> Todos os usuários](./media/tangoe-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Adicionar usuário](./media/tangoe-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Página da caixa de diálogo do usuário](./media/tangoe-tutorial/create_aaduser_04.png) 

     a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-tangoe-command-premium-mobile-test-user"></a>Criar um usuário de teste do Tangoe Command Premium Mobile

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Tangoe Command Premium Mobile. 

O aplicativo Tangoe Command Premium Mobile precisa que todos os usuários sejam provisionados no aplicativo antes de fazer o Logon Único. Portanto, trabalhe com [equipe de suporte ao cliente do Tangoe Command Premium Mobile](https://www.tangoe.com/contact-us/) para provisionar todos esses usuários para o aplicativo. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Tangoe Command Premium Mobile.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Tangoe Command Premium Mobile, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Tangoe Command Premium Mobile**.

    ![Tangoe Command Premium Mobile na lista de aplicativos](./media/tangoe-tutorial/tutorial_tangoe_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Tangoe Command Premium Mobile no Painel de Acesso, você deve ser conectado automaticamente ao aplicativo Tangoe Command Premium Mobile. Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tangoe-tutorial/tutorial_general_01.png
[2]: ./media/tangoe-tutorial/tutorial_general_02.png
[3]: ./media/tangoe-tutorial/tutorial_general_03.png
[4]: ./media/tangoe-tutorial/tutorial_general_04.png

[100]: ./media/tangoe-tutorial/tutorial_general_100.png

[200]: ./media/tangoe-tutorial/tutorial_general_200.png
[201]: ./media/tangoe-tutorial/tutorial_general_201.png
[202]: ./media/tangoe-tutorial/tutorial_general_202.png
[203]: ./media/tangoe-tutorial/tutorial_general_203.png

