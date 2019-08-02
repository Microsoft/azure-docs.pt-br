---
title: 'Tutorial: integração do Azure Active Directory com o Projectplace | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 179721075484c35c5ebbb3d936b83bc407b75a8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093534"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Tutorial: Integrar o Projectplace ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Projectplace ao Azure AD (Azure Active Directory). Quando integrar o Projectplace ao Azure AD, será possível:

* Controlar no Azure AD quem tem acesso ao Projectplace.
* Permitir que seus usuários entrem automaticamente no Projectplace com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.
* Os usuários podem ser provisionados no Projectplace automaticamente.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do Projectplace.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Projectplace é compatível com o SSO iniciado por **SP e IDP** e com o provisionamento de usuário **Just-In-Time**.

## <a name="adding-projectplace-from-the-gallery"></a>Adicionar o Projectplace da galeria

Para configurar a integração do Projectplace ao Azure AD, você precisa adicionar o Projectplace à sua lista de aplicativos SaaS gerenciados na galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Projectplace** na caixa de pesquisa.
1. Selecione **Projectplace** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Projectplace usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Projectplace.

Para configurar e testar o SSO do Azure AD com o Projectplace, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configure o Projectplace](#configure-projectplace)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Projectplace](#create-projectplace-test-user)** – para ter um equivalente de B. Fernandes no Projectplace que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Projectplace**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, se desejar configurar o aplicativo no modo iniciado por **IDP**, o aplicativo será pré-configurado e as URLs necessárias já serão preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite uma URL: `https://service.projectplace.com`

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no **ícone** para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

   ![O link de download do Certificado](common/copy-metadataurl.png)

1. Na seção **Configurar o Projectplace**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Configurar o Projectplace

Para configurar o logon único no lado do **Projectplace**, é necessário enviar a **URL de Metadados de Federação do Aplicativo** copiada do portal do Azure para a [equipe de suporte do Projectplace](https://success.planview.com/Projectplace/Support). Essa equipe garante que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

>[!NOTE]
>A configuração de logon único deve ser executada pela [equipe de suporte do Projectplace](https://success.planview.com/Projectplace/Support). Assim que a configuração for concluída, você receberá uma notificação. 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B. Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo a ela acesso ao Projectplace.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Projectplace**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B. Fernandes** na lista Usuários e, em seguida, clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-projectplace-test-user"></a>Criar um usuário de teste do Projectplace

>[!NOTE]
>Será possível ignorar esta etapa se você tiver habilitado o provisionamento no Projectplace. Você pode pedir para a [equipe de suporte do Projectplace](https://success.planview.com/Projectplace/Support) habilitar o provisionamento, depois que os usuários concluídos serão criados no Projectplace durante o primeiro logon.

Para permitir que os usuários do Azure AD entrem no Projectplace, você precisará adicioná-los ao Projectplace. Você precisará adicioná-los manualmente.

**Para criar uma conta de usuário, siga estas etapas:**

1. Entre no site do **Projectplace** da sua empresa como administrador.

2. Acesse **Pessoas** e selecione **Membros**:
   
    ![Acesse Pessoas e, em seguida, selecione Membros](./media/projectplace-tutorial/ic790228.png "Pessoas")

3. Selecione **Adicionar membro**:
   
    ![Selecionar Adicionar membro](./media/projectplace-tutorial/ic790232.png "Adicionar membros")

4. Na seção **Adicionar Membro**, realize as etapas a seguir.
   
    ![Seção Adicionar Membro](./media/projectplace-tutorial/ic790233.png "Novos Membros")
   
    1. Na caixa **Novos Membros**, digite o endereço de email de uma conta válida do Azure AD que você deseja adicionar.
   
    1. Selecione **Enviar**.

   Um email contendo um link para confirmar a conta antes que ela se torne ativa é enviado ao titular da conta do Azure AD.

>[!NOTE]
>Você também pode usar qualquer outra API ou ferramenta de criação de conta de usuário fornecida pelo Projectplace para adicionar contas de usuário do Azure AD.


### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Projectplace no Painel de Acesso, você deverá entrar automaticamente no Projectplace no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)