---
title: 'Tutorial: Integração do Azure Active Directory ao Supermood | Microsoft Docs'
description: Aprenda como configurar o logon único entre o Active Directory do Azure e o Supermood.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: afc04efa-2eba-4e47-8ce4-b71eb293cd09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60efdd5b509045ce2398720d650894983c2cfc23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60639494"
---
# <a name="tutorial-azure-active-directory-integration-with-supermood"></a>Tutorial: Integração do Azure Active Directory ao Supermood

Neste tutorial, você aprenderá como integrar o Supermood ao Azure AD (Azure Active Directory).

A integração do Supermood ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Supermood.
- Você pode permitir que seus usuários entrem automaticamente no Supermood (Logon Único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Supermood, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do Supermood

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Supermood da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-supermood-from-the-gallery"></a>Adicionando Supermood da galeria
Para configurar a integração do Supermood no Azure AD, você precisa adicionar o Supermood da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar Supermood da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Supermood**, selecione **Supermood** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Supermood na lista de resultados](./media/supermood-tutorial/tutorial_supermood_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com Supermood, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber o que o usuário de contraparte do Supermood é para um usuário no Azure AD. Em outras palavras, um relacionamento de link entre um usuário do AD do Azure e o usuário relacionado em Supermood precisa ser estabelecido.

Para configurar e testar o logon único do Azure AD com Supermood, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[ Crie um usuário de teste Supermood ](#create-a-supermood-test-user)** - para ter um equivalente de Brenda Fernandes em Supermood que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Supermood.

**Para configurar o logon único do Azure AD com Supermood, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos **Supermood**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/supermood-tutorial/tutorial_supermood_samlbase.png)

1. Na seção **Domínio Supermood e URLs**, execute as seguintes etapas:

    ![URLs e supermood domínio único informações de logon](./media/supermood-tutorial/tutorial_supermood_url.png)

     a. Marque **Mostrar configurações de URL avançadas**.

    b. Se você deseja configurar o aplicativo no **modo iniciado pelo IDP**, na caixa de texto **Estado do retransmissão**, digite um URL: `https://supermood.co/auth/sso/saml20`

    c. Se você deseja configurar o aplicativo no modo **SP** iniciado, na caixa de texto **URL de login**, digite um URL: `https://supermood.co/app/#!/loginv2`

1. O aplicativo Supermood espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar o logon único](./media/supermood-tutorial/tutorial_supermood_attribute.png)

1. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:
    
    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |    
    | firstName | user.givenname |
    | lastName | user.surname |

     a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/supermood-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/supermood-tutorial/tutorial_attribute_05.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    d. Clique em **Ok**

1. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas.

    ![O link de download do Certificado](./media/supermood-tutorial/tutorial_supermood_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/supermood-tutorial/tutorial_general_400.png)

1. Vá para o painel de administração do Supermood.co como Administrador de Segurança.

1. Clique em **Minha conta** (canto inferior esquerdo) e **Logon único (SSO)**.

    ![O único certificado](./media/supermood-tutorial/tutorial_supermood_single.png)
1. Em **suas configurações de SAML 2.0**, clique em **Adicionar uma configuração de SAML 2.0 para um domínio de e-mail**.

    ![O certificado adiciona](./media/supermood-tutorial/tutorial_supermood_add.png)

1. Em **, adicione uma configuração do SAML 2.0 para um domínio de e-mail** . seção, execute as seguintes etapas:

    ![O certificado saml](./media/supermood-tutorial/tutorial_supermood_saml.png)

     a. Na caixa de texto **domínio de email para este provedor de identidade**, digite seu domínio.

    b. Na caixa de texto **Usar um URL de metadados**, cole o **URL de metadados da federação de aplicativos** que você copiou do portal do Azure.

    c. Clique em **Adicionar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/supermood-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/supermood-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/supermood-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/supermood-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-supermood-test-user"></a>Crie um usuário de teste Supermood

Nesta seção, você cria um usuário chamado Brenda Fernandes em Supermood. Supermood suporta o provisionamento just-in-time, que por padrão é habilitado para usuários cujos e-mails pertencem aos domínios que são adicionados durante a configuração no final da Supermood. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Supermood, se ele ainda não existir.

>[!Note]
>Caso precise criar um usuário manualmente, contate a  [equipe de suporte do Supermood](mailto:hello@supermood.fr).


### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Supermood.

![Atribuir a função de usuário][200] 

**Para atribuir Britta Simon a Supermood, execute os seguintes passos:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Supermood**.

    ![O link Supermood na lista de aplicativos](./media/supermood-tutorial/tutorial_supermood_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Supermood no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Supermood.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/supermood-tutorial/tutorial_general_01.png
[2]: ./media/supermood-tutorial/tutorial_general_02.png
[3]: ./media/supermood-tutorial/tutorial_general_03.png
[4]: ./media/supermood-tutorial/tutorial_general_04.png

[100]: ./media/supermood-tutorial/tutorial_general_100.png

[200]: ./media/supermood-tutorial/tutorial_general_200.png
[201]: ./media/supermood-tutorial/tutorial_general_201.png
[202]: ./media/supermood-tutorial/tutorial_general_202.png
[203]: ./media/supermood-tutorial/tutorial_general_203.png

