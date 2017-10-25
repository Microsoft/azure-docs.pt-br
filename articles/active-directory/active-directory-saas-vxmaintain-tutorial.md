---
title: "Tutorial: Integração do Azure Active Directory ao vxMaintain | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o vxMaintain."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: ad87534af448356b8cc80d8ddd278bfb8a9165e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-integrate-azure-active-directory-with-vxmaintain"></a>Tutorial: Integração do Azure Active Directory ao vxMaintain

Neste tutorial, você aprenderá a integrar o vxMaintain ao Azure AD (Azure Active Directory).

Essa integração oferece vários benefícios importantes. Você pode:

- Controle no Azure AD quem tem acesso ao vxMaintain.
- Permita que seus usuários façam logon automaticamente no vxMaintain com logon único (SSO) usando suas contas do Azure AD.
- Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, consulte [O que é o acesso de aplicativos e o logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao vxMaintain, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada pelo SSO do vxMaintain

> [!NOTE]
> Ao testar as etapas deste tutorial, recomendamos que você não use um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

* Adicionando vxMaintain da Galeria
* Configurar e testar o logon único do AD do Azure

## <a name="add-vxmaintain-from-the-gallery"></a>Adicionar vxMaintain da galeria
Para configurar a integração do vxMaintain com o Azure AD, você precisará adicionar o vxMaintain a partir da galeria à sua lista de aplicativos SaaS gerenciados.

Para adicionar o vxMaintain a partir da galeria, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o botão **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![O painel “Aplicativos empresariais”][2]
    
3. Para adicionar um aplicativo, na caixa de diálogo **Todos os aplicativos**, selecione **Novo aplicativo**.

    ![O botão “Novo aplicativo”][3]

4. Na caixa de pesquisa, digite **vxMaintain**.

    ![A lista suspensa "Modo Logon Único"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_search.png)

5. Na lista de resultados, selecione **vxMaintain**e, em seguida, selecione **adicionar**.

    ![O link do vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o SSO do Azure AD usando o vxMaintain, com base em um usuário de teste chamado “Brenda Fernandes”.

Para o SSO funcionar, o Azure AD precisa saber qual é o equivalente do usuário do Azure AD no vxMaintain. Ou seja, você deve estabelecer uma relação de link entre o usuário do Azure AD e o usuário vxMaintain correspondente.

Para estabelecer a relação de vínculo, atribua o valor **nome de usuário** do vxMaintain como o valor de **Nome do usuário** do Azure AD.

Para configurar e testar o SSO do Azure AD usando o vxMaintain, você precisa concluir os seguintes blocos de construção.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você pode habilitar o SSO do Azure AD no portal do Azure e configurar o SSO em seu aplicativo vxMaintain fazendo seguinte:

1. No Portal do Azure, na página de integração de aplicativos do **vxMaintain**, selecione **Logon único**.

    ![O comando "Logon único"][4]

2. Para habilitar o SSO, na lista suspensa **Modo de Logon Único**, selecione **Logon baseado em SAML**.
 
    ![O comando "Logon único baseado em SAML"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

3. Em **Domínio e URLs do vxMaintain**, faça o seguinte:

    ![Seção Domínio e URLs do vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. Na caixa **Identificador**, digite uma URL com a seguinte sintaxe: `https://<company name>.verisae.com`

    b. Na caixa **URL de Resposta**, digite uma URL com a seguinte sintaxe: `https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true`

    > [!NOTE] 
    > Os valores anteriores não são reais. Atualize-os com o identificador e a URL de resposta reais. Para obter os valores, entre em contato com a [equipe de suporte do vxMaintain](http://www.verisae.com/contact-us).
 
4. Em **Certificado de Autenticação SAML**, selecione **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![A seção “Certificado de Autenticação SAML”](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

5. Selecione **Salvar**.

    ![O botão Salvar](./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_400.png)

6. Para configurar o SSO do **vxMaintain**, envie o arquivo de **Metadados XML** baixado para [a equipe de suporte do vxMaintain](http://www.verisae.com/contact-us).

> [!TIP]
> Ao configurar o aplicativo, você pode ler as instruções anteriores em uma versão concisa no [portal do Azure](https://portal.azure.com). Depois de adicionar o aplicativo na seção **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único** e, depois, acesse a documentação inserida na seção **Configuração**. 
>
>Para saber mais sobre o recurso incorporado de documentação, consulte [Gerenciar logon único para aplicativos empresariais](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
Nesta seção, você cria o usuário de teste Brenda Fernandes no portal do Azure fazendo o seguinte:

![Usuário de teste do Azure AD][100]

1. No **portal do Azure**, no painel esquerdo, selecione o botão **Azure Active Directory**.

    ![Botão "Azure Active Directory"](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_01.png) 

2. Para exibir uma lista de usuários, vá para **Usuários e grupos** > **Todos os usuários**.
    
    ![O link “Todos os usuários”](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_02.png)  
    A caixa de diálogo **Todos os usuários** é aberta. 

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar**.
 
    ![O botão Adicionar](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_03.png) 

4. Na caixa de diálogo **Usuário**, faça o seguinte:
 
    ![A caixa de diálogo Usuário](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_04.png) 

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário de teste Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e anote o valor gerado na caixa **Senha**.

    d. Selecione **Criar**.
 
### <a name="create-a-vxmaintain-test-user"></a>Criar um usuário de teste vxMaintain

Nesta seção, você cria a usuária de teste Brenda Fernandes no vxMaintain. Para adicionar os usuários na plataforma do vxMaintain, trabalhe com a [equipe de suporte do vxMaintain](http://www.verisae.com/contact-us). Antes de usar o SSO, crie e ative os usuários.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que o usuário de teste Brenda Fernandes use o SSO do Azure concedendo-lhe acesso ao vxMaintain. Para fazer isso, faça o seguinte:

![Usuário de teste na Lista de Nomes de Exibição][200] 

1. Na exibição de **Aplicativos** do portal do Azure, vá para a exibição de **Diretório** > **Aplicativos empresariais** > **Todos os aplicativos**.

    ![O link "Todos os aplicativos"][201] 

2. Na lista de **Aplicativos**, selecione **vxMaintain**.

    ![O link do vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

3. No painel esquerdo, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202] 

4. Selecione **Adicionar** e, em seguida, no painel **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][203]

5. Na caixa de diálogo **Usuários e grupos**, na lista **Usuários**, selecione **Brenda Fernandes** e, em seguida, selecione o botão **Selecionar**.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.
    
### <a name="test-your-azure-ad-single-sign-on"></a>Testar o logon único do Azure AD

Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Selecionar o bloco **vxMaintain** no Painel de Acesso deve fazer com que você entre automaticamente no seu aplicativo vxMaintain.

Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="next-steps"></a>Próximas etapas

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_203.png

