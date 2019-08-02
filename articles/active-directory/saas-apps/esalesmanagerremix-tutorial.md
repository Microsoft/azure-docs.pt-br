---
title: 'Tutorial: Integração do Azure Active Directory ao E Sales Manager Remix | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o E Sales Manager Remix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895fb0d83e383618818325263ac80c5919a0ee7b
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "65406967"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Tutorial: Integrar o Azure Active Directory ao E Sales Manager Remix

Neste tutorial, você aprenderá a integrar o Azure Active Directory (Azure AD) ao E Sales Manager Remix.

Ao integrar o E Sales Manager Remix ao Microsoft Azure Active Directory, você obtém os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao E Sales Manager Remix.
- Você pode permitir que seus usuários façam logon automaticamente no E Sales Manager Remix (Logon Único ou SSO) com as respectivas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local, o portal clássico do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, consulte [O que é o acesso de aplicativos e o logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao E Sales Manager Remix, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do E Sales Manager Remix

> [!NOTE]
> Ao testar as etapas deste tutorial, recomendamos que você *não* use um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

* Adicionando o E Sales Manager Remix da galeria
* configurar e testar o logon único do AD do Azure

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Adicionar o E Sales Manager Remix da galeria
Para configurar a integração do Microsoft Azure Active Directory ao E Sales Manager Remix, adicione o E Sales Manager Remix da galeria à sua lista de aplicativos SaaS gerenciados da seguinte maneira:

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![A janela “Aplicativos empresariais”][2]
    
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da janela.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **E Sales Manager Remix**, selecione **E Sales Manager RemixE Sales Manager Remix** na lista de resultados e, em seguida, selecione **Adicionar**.

    ![E Sales Manager Remix na lista de resultados](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure Active Directory com o E Sales Manager Remix com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Microsoft Azure Active Directory precisa identificar o usuário do E Sales Manager Remix e sua contraparte do Microsoft Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o mesmo usuário no E Sales Manager Remix.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o E Sales Manager Remix, complete os blocos de construção nas próximas cinco seções:

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Habilite o logon único do Microsoft Azure Active Directory no portal do Azure e configura o logon único no aplicativo E Sales Manager Remix da seguinte maneira:

1. No portal do Azure, na página de integração do aplicativo **E Sales Manager Remix**, selecione **Logon único**.

    ![O link “Logon único”][4]

1. Na janela **Logon único**, na caixa **Modo Logon único**, selecione **Logon baseado em SAML**.
 
    ![A janela “Logon único”](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. Em **Domínio e URLs do E Sales Manager Remix**, faça o seguinte:

    ![Informações de logon único em Domínio e URLs do E Sales Manager Remix](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Na caixa **URL de logon** caixa, digite uma URL no seguinte formato: *https://\<Server-Based-URL>/\<sub-domain>/esales-pc*.

    b. Na caixa **Identificador**, digite uma URL no seguinte formato: *https://\<Server-Based-URL>/\<sub-domain>/* .

    c. Observe o valor do **Identificador** para usar posteriormente neste tutorial.
    
    > [!NOTE] 
    > Os valores anteriores não são reais. Atualize-os com a URL de Logon e o Identificador reais. Para obter os valores, entre em contato com a [equipe de atendimento ao cliente do E Sales Manager Remix](mailto:esupport@softbrain.co.jp).

1. Na seção **Certificado de Autenticação do SAML**, selecione **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![O link de download do Certificado (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Marque a caixa de seleção **Exibir e editar todos os outros atributos de usuário** e selecione o atributo **emailaddress**.
    
    ![A janela Atributos de Usuário](./media/esalesmanagerremix-tutorial/configure1.png)

    A janela **Editar atributo** é aberta.

1. Copie os valores **Namespace** e **Nome**. Gere o valor no padrão *\<Namespace>/\<Nome>* e salve-o para uso posterior neste tutorial.

    ![A janela Editar atributo](./media/esalesmanagerremix-tutorial/configure2.png)

1. Em **Configuração do E Sales Manager Remix**, selecione **Configurar E Sales Manager Remix**.

    ![Configuração do E Sales Manager Remix](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    A janela **Configurar logon** é aberta.

1. Na seção **Referência rápida**, copie a URL de saída e a URL do serviço de logon único do SAML.

1. Clique em **Salvar**.

    ![O botão Salvar](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Entre em seu aplicativo E Sales Manager Remix como um administrador.

1. Na parte superior direita, selecione **Menu Para o Administrador**.

    ![O comando "Menu Para o Administrador"](./media/esalesmanagerremix-tutorial/configure4.png)

1. No painel esquerdo, selecione **Configurações do sistema** > **Cooperação com o sistema externo**.

    ![Os links “Configurações do sistema” e “Cooperação com o sistema externo”](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. Na janela **Cooperação com o sistema externo**, selecione **SAML**.

    ![A janela "Cooperação com o sistema externo"](./media/esalesmanagerremix-tutorial/configure6.png)

1. Em **Configuração de autenticação SAML**, faça o seguinte:

    ![A seção "Configuração de autenticação SAML"](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Marque a caixa de seleção **Versão do PC**.
    
    b. Na seção **item Colaboração**, na lista suspensa, selecione **email**.

    c. Na caixa do item Colaboração, cole o **valor de declaração** copiado anteriormente do Portal do Azure (ou seja, **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** ).

    d. Na caixa **Emissor (ID da entidade)** , cole o valor do identificador que você copiou do portal do Azure na seção **Domínio e URLs do E Sales Manager Remix**.

    e. Para carregar seu certificado baixado do portal do Azure, selecione **Seleção de arquivo**.

    f. Na caixa **URL de login do provedor de Identidade**, cole a URL de serviço de logon único SAML que você copiou anteriormente do portal do Azure.

    g. Na caixa **URL de login do provedor de identidade**, cole o valor da URL de saída que você copiou anteriormente do portal do Azure.

    h. Selecione **Configuração concluída**.

> [!TIP]
> Ao configurar o aplicativo, você pode ler as instruções anteriores em uma versão concisa no [portal do Azure](https://portal.azure.com). Depois de adicionar esse aplicativo na seção **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único** e, em seguida, acesse a documentação inserida na seção **Configuração**, na parte inferior. Para saber mais informações sobre o recurso de documentação inserida, consulte [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você cria o usuário de teste Brenda Fernandes no portal do Azure fazendo o seguinte:

![Criar um usuário de teste do Azure AD][100]

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**.

    ![O link do Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Para exibir uma lista de usuários atuais, selecione **Usuários e grupos** > **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Na parte superior da janela **Todos os usuários**, selecione **Adicionar**.

    ![O botão Adicionar](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    A janela **Usuário** abre.

1. Na janela **Usuário**, faça o seguinte:

    ![A janela de usuário](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Criar um usuário de teste do E Sales Manager Remix

1. Entre em seu aplicativo E Sales Manager Remix como um administrador.

1. Selecione **Menu Para o Administrador** no menu na parte superior direita.

    ![Configuração do E Sales Manager Remix](./media/esalesmanagerremix-tutorial/configure4.png)

1. Selecione **Configurações da empresa** > **Manutenção de departamentos e funcionários** e selecione **Funcionários registrados**.

    ![A guia "Funcionários registrados"](./media/esalesmanagerremix-tutorial/user1.png)

1. Na seção **Novo registro de funcionário**, execute as etapas a seguir:
    
    ![A seção “Novo registro de funcionário”](./media/esalesmanagerremix-tutorial/user2.png)

    a. Na caixa **Nome do funcionário**, digite o nome do usuário (por exemplo, **Brenda**).

    b. Complete os campos necessários restantes.
    
    c. Se você habilitar SAML, o administrador não poderá entrar na página de entrada. Conceda privilégios de entrada de administrador para o usuário marcando a caixa de seleção **Logon de administrador**.

    d. Selecione **Registro**.

1. No futuro, para entrar como um administrador, entre como usuário com permissões de administrador e, em seguida, no canto superior direito, selecione **Menu Para o Administrador**.

    ![O comando "Menu Para o Administrador"](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que o usuário Brenda Fernandes use o logon único do Azure concedendo acesso ao E Sales Manager Remix. Para fazer isso, faça o seguinte: 

![Atribuir a função de usuário][200] 

1. No portal do Azure, abra a exibição de **Aplicativos**, acesse a exibição de **Diretório** e, em seguida, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.

    ![Os links “Aplicativos empresariais” e “Todos os aplicativos”][201] 

1. Na lista de **Aplicativos**, selecione **E Sales Manager Remix**.

    ![O link do E Sales Manager Remix](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. No painel esquerdo, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Selecione **Adicionar** e, em seguida, no painel **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

1. Na janela **Usuários e grupos**, na lista **Usuários**, selecione **Britta Simon**.

1. Escolha o botão **Selecionar**.

1. Na janela **Adicionar Atribuição**, selecione **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar no bloco do E Sales Manager Remix no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo E Sales Manager Remix.

Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png

