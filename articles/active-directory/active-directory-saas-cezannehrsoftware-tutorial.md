---
title: "Tutorial: integração do Azure Active Directory com o software Cezanne HR | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o software Cezanne HR."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 623c438edfce5f98c2d32d8bb25a97d86aa77909
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-integrate-azure-active-directory-with-cezanne-hr-software"></a>Tutorial: integração do Azure Active Directory com o software Cezanne HR

Neste tutorial, você aprende a integrar o software Cezanne HR ao Azure AD (Azure Active Directory).

A integração do software Cezanne HR ao Azure AD oferece os benefícios a seguir. Você pode:

- Controle, no Azure AD, quem tem acesso ao software Cezanne HR.
- Habilite seus usuários, com as respectivas contas do Azure AD, a fazer logon automaticamente no software Cezanne HR via SSO (logon único).
- Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) com o Azure AD, consulte [O que é acesso a aplicativos e SSO com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o software Cezanne HR, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do software Cezanne HR com SSO habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, recomendamos que você não use um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testa o SSO do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

* Adicionando o software Cezanne HR da galeria
* Configurar e testar o SSO do Azure AD

## <a name="add-cezanne-hr-software-from-the-gallery"></a>Adicionar o software Cezanne HR da galeria
Para configurar a integração do software Cezanne HR com o Azure AD, adicione o software Cezanne HR, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

Para adicionar o software Cezanne HR da galeria, execute o seguinte:

1. No **[portal do Azure](https://portal.azure.com)**, no painel esquerdo, selecione o botão **Azure Active Directory**. 

    ![Botão "Azure Active Directory"][1]

2. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![O link "Todos os aplicativos"][2]
    
3. Para adicionar um novo aplicativo, na parte superior da caixa de diálogo **Todos os aplicativos**, selecione **Novo aplicativo**.

    ![O botão “Novo aplicativo”][3]

4. Na caixa de pesquisa, digite **Cezanne HR Software**.

    ![A caixa de pesquisa](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_search.png)

5. Na lista de resultados, escolha **software Cezanne HR** e adicione o aplicativo selecionando o botão **Adicionar**.

    ![A lista de resultados](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configura e testa o SSO do Azure AD com o software Cezanne HR, com base em uma usuária de teste chamada “Brenda Fernandes”.

Para o SSO funcionar, o Azure AD precisa saber qual é o equivalente do usuário do Azure AD no software Cezanne HR. Em outras palavras, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do software Cezanne HR.

Para estabelecer a relação de vínculo, atribua o valor **nome de usuário** do software Cezanne HR como o valor **Username** do Azure AD.

Para configurar e testar o SSO do Azure AD usando o software Cezanne HR, conclua os blocos de construção a seguir.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você pode habilitar o SSO do Azure AD no portal do Azure e configura o SSO em seu aplicativo do software Cezanne HR fazendo seguinte:

1. No portal do Azure, na página de integração de aplicativo do **software Cezanne HR**, selecione **Logon único**.

    ![O comando "Logon único"][4]

2. Para habilitar o SSO, na caixa de diálogo **Logon único**, selecione o **Modo** **Logon baseado em SAML**.
 
    ![A caixa "Modo"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. Em **Domínio e URLs do Software Cezanne HR**, faça o seguinte:

    ![A seção "URLs e Domínio e URLs do Software Cezanne HR"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. Na caixa **URL de Entrada**, digite uma URL com a seguinte sintaxe: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`

    b. Na caixa **URL de Resposta**, digite uma URL com a seguinte sintaxe: `https://w3.cezanneondemand.com:443/<tenantid>`    
     
    > [!NOTE] 
    > Os valores anteriores não são reais. Atualize-os com a URL de resposta real e a URL de logon. Para obter os valores, entre em contato com a [equipe de suporte do cliente software Cezanne HR](mailto:info@cezannehr.com).

4. Na seção **Certificado de Autenticação do SAML**, selecione **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![A seção “Certificado de Autenticação SAML”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Selecione **Salvar**.

    ![Clique no botão “Salvar”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)
    
6. Em **Configuração do Software Cezanne HR**, selecione **Configurar o software Cezanne HR** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML** e a URL do **Serviço de Logon Único SAML** da seção **Referência Rápida**.

    ![A seção "Configuração do Software Cezanne HR"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. Em uma janela diferente do navegador da Web, faça logon em seu locatário do software Cezanne HR como um administrador.

8. No painel esquerdo, selecione **Configuração do Sistema**. Selecione **Configurações de Segurança** > **Configuração de Logon Único**.

    ![O link "Configuração de Logon Único"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. No painel **Permitir que os usuários façam logon usando o SSO (Serviço de Logon Único)**, marque a caixa de seleção **SAML 2.0** e selecione a opção **Configuração Avançada**.

    ![Opções de logon único em serviços](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. Selecione **Adicionar Novo**.

    ![O botão “Adicionar Novo”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. Em **Provedores de Identidade SAML 2.0**, faça o seguinte:

    ![A seção "Provedores de identidade SAML 2.0"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Na caixa **Nome de Exibição**, insira o nome do seu provedor de Identidade.

    b. Na caixa **Identificador da Entidade**, cole o **ID da Entidade SAML** que você copiou do portal do Azure. 

    c. Na caixa de listagem **Associação SAML**, selecione **POST**.

    d. Na caixa **Ponto de Extremidade de Serviço de Token de Segurança**, cole a URL **Serviço de Logon Único SAML** que você copiou do portal do Azure. 
    
    e. Na caixa **Nome do atributo de ID de usuário**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Para carregar o certificado baixado do Azure AD, selecione o botão **Carregar**.
    
    g. Selecione **OK**. 

12. Selecione **Salvar**.

    ![Clique no botão “Salvar”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Ao configurar o aplicativo, você pode ler as instruções anteriores em uma versão concisa no [portal do Azure](https://portal.azure.com). Depois de adicionar o aplicativo do **Active Directory**, seção  > **Aplicativos Empresariais**, selecione a guia **Logon único**. Em seguida, acesse a documentação inserida na seção **Configuração**. 

Para saber mais sobre o recurso de documentação inserida, consulte [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
Nesta seção, você cria a usuária de teste Brenda Fernandes no portal do Azure.

![A usuária de teste Brenda Fernandes][100]

Para criar um usuário de teste no Azure AD, faça o seguinte:

1. No **portal do Azure**, no painel esquerdo, selecione o botão **Azure Active Directory**.

    ![Botão "Azure Active Directory"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. Para exibir a lista de usuários, selecione **Usuários e grupos** > **Todos os usuários**.
    
    ![O link “Todos os usuários”](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 
    
    A caixa de diálogo **Todos os usuários** é aberta.

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar**.
 
    ![O botão “Adicionar”](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. Na caixa de diálogo **Usuário**, faça o seguinte:
 
    ![Caixa de diálogo "Usuário"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o **endereço de email** da usuária Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e anote o valor gerado na caixa **Senha**.

    d. Selecione **Criar**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Criar um usuário de teste do software Cezanne HR

Para habilitar usuários do Azure AD a entrar no software Cezanne HR, eles deverão ser provisionados no software Cezanne HR. No caso do software Cezanne HR, o provisionamento é uma tarefa manual.

Provisione uma conta de usuário fazendo o seguinte:

1.  Entre no site da empresa do software Cezanne HR como um administrador.

2.  No painel esquerdo, selecione **System Setup (Configuração do Sistema)** > **Manage Users (Gerenciar Usuários)** > **Add New User (Adicionar Novo Usuário)**.

    ![O link "Adicionar Novo Usuário"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Novo Usuário")

3.  Em **Person Details (Detalhes Pessoais)**, faça o seguinte:

    ![A seção "Detalhes Pessoais"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Novo Usuário")
    
    a. Defina **Internal User (Usuário Interno)** como **OFF (DESATIVADO)**.
    
    b. Na caixa **First Name (Nome)**, digite o nome do usuário, por exemplo, **Brenda**.  
 
    c. Em **Last Name (Sobrenome)**, digite o sobrenome do usuário, por exemplo, **Fernandes**.
    
    d. Na caixa **Email**, digite o endereço de email do usuário, por exemplo, Brittasimon@contoso.com.

4.  Em **Account Information (Informações de Conta)**, faça o seguinte:

    ![A seção "Informações de conta"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Novo Usuário")
    
    a. Na caixa **Username (Nome de Usuário)**, digite o endereço de email do usuário, por exemplo, Brittasimon@contoso.com.
    
    b. Na caixa **Password (Senha)**, digite a senha do usuário.
    
    c. Na caixa **Security Role (Função de Segurança)**, selecione **HR Professional (Profissional de RH)**.
    
    d. Selecione **OK**.

5. Na guia **Single sign-on (Logon único)**, na seção **SAML 2.0 Identifiers (Identificadores SAML 2.0)**, selecione **Add New (Adicionar Novo)**.

    ![O botão "Adicionar Novo"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Usuário")

6. Na caixa de listagem **Identity Provider (Provedor de Identidade)**, selecione seu provedor de identidade. Na caixa **User Identifier (Identificador de Usuário)**, digite o endereço de email para a conta da usuária de teste Brenda Fernandes.

    ![As caixas "Provedor de Identidade" e "Identificador de Usuário"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Usuário")
    
7. Selecione **Salvar**.

    ![O botão "Salvar"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Usuário")

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que a usuária de teste Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao software Cezanne HR.

![Testar o acesso do usuário][200] 

1. No Portal do Azure, abra a exibição de aplicativos, vá para a exibição de diretório. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![O link "Todos os aplicativos"][201] 

2. Na lista de aplicativos, selecione **Cezanne HR Software**.

    ![A lista "Aplicativos"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png) 

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![Atribuir usuário][202] 

4. Selecione **Adicionar**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![Link “Usuários e Grupos”][203]

5. Na caixa de diálogo **Usuários e grupos**, na lista **Usuários**, selecione **Brenda Fernandes**.

6. Na caixa de diálogo **Usuários e grupos**, selecione **Selecionar**.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.
    
### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Quando selecionar o bloco do software Cezanne HR no Painel de Acesso, você entrará automaticamente no seu aplicativo do software Cezanne HR.

## <a name="next-steps"></a>Próximas etapas

* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é acesso a aplicativos e SSO com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png

