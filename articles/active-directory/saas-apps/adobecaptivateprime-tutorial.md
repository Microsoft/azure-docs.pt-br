---
title: 'Tutorial: integração do Azure Active Directory com a Adobe Captivate Prime | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a Adobe Captivate Prime.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 63bbec6de8f122178289cc313e5d938e3b926af2
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673212"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-captivate-prime"></a>Tutorial: integração do Azure Active Directory com a Adobe Captivate Prime

Neste tutorial, você aprenderá a integrar a Adobe Captivate Prime ao Azure AD (Azure Active Directory).
A integração da Adobe Captivate Prime ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD que tenha acesso ao Adobe Captivate Prime.
* É possível permitir que seus usuários entrem automaticamente no Adobe Captivate Prime (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD com Adobe Captivate Prime, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Adobe Captivate Prime habilitada por logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Adobe Captivate Prime dá suporte ao SSO iniciado por **IDP**

## <a name="adding-adobe-captivate-prime-from-the-gallery"></a>Adicionando Adobe Captivate Prime da Galeria

Para configurar a integração do Adobe Captivate Prime no Azure AD, você precisa adicionar Adobe Captivate Prime da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Adobe Captivate Prime da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Adobe Captivate Prime**, selecione **Adobe Captivate Prime** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Adobe Captivate Prime na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Adobe Captivate Prime com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Adobe Captivate Prime.

Para configurar e testar o logon único do Azure AD com Adobe Captivate Prime, você precisa concluir os blocos de construção a seguir:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Adobe Captivate Prime](#configure-adobe-captivate-prime-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Adobe Captivate Prime](#create-adobe-captivate-prime-test-user)** – para ter um equivalente de Brenda Fernandes no Adobe Captivate Prime vinculado à representação dela no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Adobe Captivate Prime, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Adobe Captivate Prime**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na página **Configurar Logon Único com SAML**, execute as seguintes etapas:

    ![Informações de logon único da URL e domínio do Adobe Captivate Prime](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite uma URL: `https://captivateprime.adobe.com`

    b. Na caixa de texto **URL de Resposta**, insira uma URL: `https://captivateprime.adobe.com/saml/SSO`

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Adobe Captivate Prime**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

7. Vá para guia **Propriedades**, copie a **URL de acesso de usuário** e cole-a no Bloco de Notas.

    ![O link de acesso do usuário](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_appprop.png)

### <a name="configure-adobe-captivate-prime-single-sign-on"></a>Configurar logon único do Adobe Captivate Prime

Para configurar o logon único no lado do **Adobe Captivate Prime**, é necessário enviar o **XML de metadados de federação** baixado, a **URL de acesso do usuário** copiada e as URLs copiadas adequadas do Portal do Azure para a [equipe de suporte do Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.

    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você deve habilitar Brenda Fernandes para usar logon único do Azure, concedendo acesso ao Adobe Captivate Prime.

1. No portal do Azure, escolha **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **Adobe Captivate Prime**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Adobe Captivate Prime**.

    ![O link do Adobe Captivate Prime na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-adobe-captivate-prime-test-user"></a>Criar usuário de teste do Adobe Captivate Prime

Nesta seção, você deve criar um usuário chamado Brenda Fernandes no Adobe Captivate Prime. Trabalhar com [equipe de suporte do Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com) para adicionar os usuários na plataforma Adobe Captivate Prime. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Adobe Captivate Prime no Painel de Acesso, você deverá ser conectado automaticamente ao Adobe Captivate Prime no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)