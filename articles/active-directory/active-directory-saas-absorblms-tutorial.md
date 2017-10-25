---
title: "Tutorial: Integração do Azure Active Directory ao Absorb LMS | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Absorb LMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: 549e353bb41f1f31bc0441294d0a8066779402e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Tutorial: Integração do Azure Active Directory ao Absorb LMS

Neste tutorial, você aprenderá a integrar o Absorb LMS ao Azure AD (Azure Active Directory).

A integração do Absorb LMS ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Absorb LMS.
- É possível habilitar os usuários para que entrem automaticamente no Absorb LMS (por meio de logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local, o portal clássico do Azure.

Se deseja saber mais sobre a integração de aplicativos SaaS (software como serviço) ao Azure AD, consulte [O que é o acesso do aplicativo e o logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Absorb LMS, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Absorb LMS com logon único habilitado

> [!NOTE]
> É recomendável não utilizar um ambiente de produção para este tutorial.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

* Adicionar o Absorb LMS da Galeria
* Configurar e testar o logon único do AD do Azure

## <a name="add-absorb-lms-from-the-gallery"></a>Adicionar o Absorb LMS da galeria
Para configurar a integração do Absorb LMS com o Azure AD, adicione o Absorb LMS da galeria à sua lista de aplicativos SaaS gerenciados.

Para adicionar o Absorb LMS da galeria, siga as seguintes etapas:

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone do **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Acesse **Aplicativos empresariais** > **Todos os aplicativos**.

    ![O painel Aplicativos Empresariais][2]
    
3. Para adicionar um aplicativo, selecione o botão **Novo aplicativo**.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Absorb LMS**, selecione **Absorb LMS** no painel de resultados e, em seguida, selecione o botão **Adicionar**.

    ![Absorb LMS na lista de resultados](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Absorb LMS, com base em um usuário de teste chamado Brenda Fernandes.

Para que o login único funcione, o Azure AD precisa saber qual usuário do Absorver LMS é equivalente no Azure AD. Em outras palavras, você deve estabelecer uma relação de vinculação entre um usuário no Azure AD e o usuário correspondente no Absorb LMS.

Você estabelece essa relação de vinculação atribuindo o valor de *nome de usuário*no Azure AD como o valor do *Nome de usuário* no Absorb LMS.

Para configurar e testar o logon único do Azure AD com o Absorb LMS, complete os blocos de construção nas próximas cinco seções.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você vai habilitar o logon único do Azure AD no Portal do Azure e configurar o logon único em seu aplicativo Absorb LMS.

Para configurar o logon único do Azure AD com o Absorb LMS, siga as seguintes etapas:

1. No Portal do Azure, na página de integração de aplicativos do **Absorb LMS**, selecione **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, na caixa **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. Na seção **Domínio e URLs do Absorb LMS**, siga as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. Na caixa **Identificador**, digite uma URL que utilize a seguinte sintaxe: `https://<subdomain>.myabsorb.com/Account/SAML`.

    b. Na caixa **URL de Resposta**, digite uma URL que utilize a seguinte sintaxe: `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > Essas URLs não são os valores reais. Atualize-as com Identificador e URLs de Resposta reais. Para obter esses valores, contate a [Equipe de suporte do cliente Absorb LMS](https://www.absorblms.com/support). 

4. Na seção **Certificado de Autenticação SAML** na coluna **Download**, selecione **Metadados XML** e, em seguida, salve o arquivo de metadados no seu computador.

    ![Link de download do certificado de autenticação](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Selecione **Salvar**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
6. Na seção **Configuração do Absorb LMS**, selecione **Configurar Absorb LMS** para abrir a janela **Configurar logon** e, em seguida, copie a **URL de Saída** na seção **Referência Rápida.**

    ![Painel de Configuração do Absorver LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

7. Em uma nova janela do navegador da Web, entre no site da empresa do Absorb LMS como administrador.

8. Selecione o botão **Conta** na parte superior direita. 

    ![Botão Conta](./media/active-directory-saas-absorblms-tutorial/1.png)

9. No painel Conta, selecione **Configurações do Portal**.

    ![Link de Configurações do Portal](./media/active-directory-saas-absorblms-tutorial/2.png)
    
10. Clique a guia **Usuários** .

    ![A guia Usuários](./media/active-directory-saas-absorblms-tutorial/3.png)

11. Na página de configuração de logon único, faça o seguinte:

    ![Página de configuração de logon único](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. Na caixa **Modo**, selecione **Provedor de Identidade Iniciado**.

    b. No Bloco de Notas, abra o certificado que você baixou pelo Portal do Azure. Remoa as marcas **---BEGIN CERTIFICATE---** e **---END CERTIFICATE---**. Em seguida, na caixa **Chave**, cole o conteúdo restante.
    
    c. Na caixa **Propriedade de ID**, selecione o atributo que você configurou como o identificador de usuário no Azure AD. Por exemplo, se *userPrincipalName* estiver selecionado no Azure AD, selecione **Nome de Usuário**.

    d. Na caixa **URL de Logon**, cole a **URL de Acesso do Usuário** a partir da página **Propriedade** do aplicativo do Portal do Azure.

    e. Na **URL de Logoff**, cole o valor da **URL de Saída** copiado da janela **Configurar logon** do Portal do Azure.

12. Alterne **Permitir apenas Logon SSO** para **Ativar**.

    ![Alternância para Permitir apenas Logon SSO](./media/active-directory-saas-absorblms-tutorial/5.png)

13. Selecione **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo. Depois de adicionar esse aplicativo a partir da seção **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único** e acesse a documentação inserida na seção **Configuração**, na parte inferior. Para saber mais, confira a [documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você cria a usuária de teste Brenda Fernandes no portal do Azure.

![Criar um usuário de teste do Azure AD][100]

Para criar um usuário de teste no Azure AD, faça o seguinte:

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Para exibir a lista de usuários, selecione **Usuários e grupos** > **Todos os usuários**.
    
    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, selecione **Adicionar**.
 
    ![O botão Adicionar](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. Na caixa de diálogo **Usuário**, faça o seguinte:
 
    ![A caixa de diálogo Usuário](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o endereço de email da conta de Brenda Fernandes.

    c. Selecione a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor na caixa **Senha**.

    d. Selecione **Criar**.

### <a name="create-an-absorb-lms-test-user"></a>Criar um usuário de teste do Absorb LMS

Para que os usuários do Azure AD entrem no Absorb LMS, os usuários deverão ser configurados no Absorb LMS.  

Para Absorb LMS, a configuração é uma tarefa manual.

Para configurar uma conta de usuário, faça o seguinte:

1. Entre no site da empresa do Absorb LMS como administrador.

2. No painel esquerdo, selecione **Usuários**.

    ![Link Usuários do Absorb LMS](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. No painel **Usuários**, selecione **Usuários**.

    ![Link Usuários](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4. Na lista suspensa **Adicionar Novo**, selecione **Usuário**.

    ![Lista suspensa Adicionar Novo](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. Na página **Adicionar Usuário**, faça o seguinte:

    ![Página Adicionar Usuário](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. Na caixa **Nome**, digite o primeiro nome, como **Brenda**.

    b. Na caixa **Sobrenome**, digite o último nome, como **Fernandes**.
    
    c. Na caixa **Nome de Usuário**, digite o nome completo, como **Brenda Fernandes**.

    d. Na caixa **Senha**, digite a senha de Brenda Fernandes.

    e. Na caixa **Confirmar Senha**, digite a senha novamente.
    
    f. Defina a alternância **Está Ativo** para **Ativo**.  

6. Selecione **Salvar**.
 
### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilita o usuário Brenda Fernandes para utilizar o logon único do Azure, concedendo acesso ao Absorb LMS.

![Atribuir a função de usuário][200]

Para atribuir o usuário Brenda Fernandes ao Absorb LMS, faça o seguinte:

1. No Portal do Azure, abra a exibição de aplicativos, acesse a exibição de diretório e, em seguida, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.

    ![O link "Todos os aplicativos"][201] 

2. Na lista **Aplicativos**, selecione **Absorb LMS**.

    ![O link do Absorb LMS na lista Aplicativos](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. No painel esquerdo, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202] 

4. Selecione **Adicionar** e, em seguida, no painel **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, na lista **Usuários**, selecione **Brenda Fernandes**.

6. Na caixa de diálogo **Usuários e grupos**, selecione o botão **Selecionar**.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

No Painel de Acesso, selecionar o bloco **Absorb LMS**, conectará automaticamente no aplicativo do Absorver LMS. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png

