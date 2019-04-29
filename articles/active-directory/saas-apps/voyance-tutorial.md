---
title: 'Tutorial: Integração do Azure Active Directory com o Voyance | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Voyance.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 539dc1f9-64c9-4dce-b259-2b0b49dcf857
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1771fe1d94eb64cf1e5227953bdc5defa488e85f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638950"
---
# <a name="tutorial-azure-active-directory-integration-with-voyance"></a>Tutorial: Integração do Azure Active Directory com o Voyance

Neste tutorial, você aprenderá a integrar o Voyance ao Azure AD (Azure Active Directory).

A integração do Voyance ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Voyance
- Você pode permitir que seus usuários façam logon automaticamente no Voyance (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Voyance, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do Voyance habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Voyance da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-voyance-from-the-gallery"></a>Adição do Voyance da galeria
Para configurar a integração do Voyance ao Azure AD, você precisará adicionar o Voyance da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Voyance por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Voyance**, selecione **Voyance** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Voyance na lista de resultados](./media/voyance-tutorial/tutorial_voyance_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Voyance, com base em uma usuária de teste chamada “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Voyance é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Voyance.

No Voyance, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Voyance, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Voyance](#create-a-voyance-test-user)** – para ter um equivalente de Brenda Fernandes no Voyance vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Testar o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Voyance.

**Para configurar o logon único do Azure AD com o Voyance, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Voyance**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/voyance-tutorial/tutorial_voyance_samlbase.png)

1. Na seção **Domínio e URLs do Voyance**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IdP**:

    ![Informações de logon único de Domínio e URLs do Voyance para IdP](./media/voyance-tutorial/tutorial_voyance_url1.png)

     a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<companyname>.nyansa.com`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<companyname>.nyansa.com/saml/create/`

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do Voyance para SP](./media/voyance-tutorial/tutorial_voyance_url2.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.nyansa.com/`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Contate a [equipe de suporte ao cliente do Voyance](mailto:support@nyansa.com) para obter esses valores. 

1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/voyance-tutorial/tutorial_voyance_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/voyance-tutorial/tutorial_general_400.png)
    
1. Na seção **Configuração do Voyance**, clique em **Configurar o Voyance** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configuração do Voyance](./media/voyance-tutorial/tutorial_voyance_configure.png) 

1. Em uma janela diferente do navegador da Web, faça logon em seu locatário do Voyance como um administrador.

1. Vá para o canto superior direito da barra de navegação e clique no menu suspenso que indica "**Acme University**".
    
    ![Configurar o logon único no lado do aplicativo – Acme University](./media/voyance-tutorial/tutorial_voyance_001.png) 

1. Clique em "**Configurações de Administração**".

    ![Configurar o logon único no lado do aplicativo – Configurações do Administrador](./media/voyance-tutorial/tutorial_voyance_002.png)

1. Clique na guia "**Acesso do Usuário**".

    ![Configurar o logon único no lado do aplicativo – Acesso do Usuário](./media/voyance-tutorial/tutorial_voyance_003.png)

1. Clique no botão "**SSO está desabilitado**" para configurar o Azure AD como um IdP que usa SAML 2.0.

    ![Configurar o logon único no lado do aplicativo – SSO está desabilitado](./media/voyance-tutorial/tutorial_voyance_004.png)

1. Acesse a seção **SAML v2** e execute as etapas a seguir:

    ![Configurar o logon único no lado do aplicativo – SAML v2](./media/voyance-tutorial/tutorial_voyance_005.png)
    
     a. Selecione **Habilitado**.
    
    b. Cole a **URL do Serviço de Logon Único SAML** copiada no Portal do Azure na a caixa de texto **URL de Logon do IdP**.

    c. Abra seu certificado codificado com Base64 baixado no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado IdP**.
    
    d. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/voyance-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/voyance-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![O botão Adicionar](./media/voyance-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![A caixa de diálogo Usuário](./media/voyance-tutorial/create_aaduser_04.png) 

     a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-voyance-test-user"></a>Criar um usuário de teste do Voyance

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Voyance. O Voyance dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Voyance, caso ele ainda não exista.

>[!NOTE]
>Se precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do Voyance](maiLto:support@nyansa.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Voyance.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao Voyance, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Voyance**.

    ![O link do Voyance na lista de Aplicativos](./media/voyance-tutorial/tutorial_voyance_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Voyance no Painel de Acesso, você deverá fazer logon automaticamente no aplicativo do Voyance.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/voyance-tutorial/tutorial_general_01.png
[2]: ./media/voyance-tutorial/tutorial_general_02.png
[3]: ./media/voyance-tutorial/tutorial_general_03.png
[4]: ./media/voyance-tutorial/tutorial_general_04.png

[100]: ./media/voyance-tutorial/tutorial_general_100.png

[200]: ./media/voyance-tutorial/tutorial_general_200.png
[201]: ./media/voyance-tutorial/tutorial_general_201.png
[202]: ./media/voyance-tutorial/tutorial_general_202.png
[203]: ./media/voyance-tutorial/tutorial_general_203.png

