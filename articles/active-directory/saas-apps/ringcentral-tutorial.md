---
title: 'Tutorial: Integração do Azure Active Directory com o RingCentral | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: jeedes
ms.openlocfilehash: c374c9e8fd91d50b7e6589f22f9bed09fbe0de39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092869"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Tutorial: Integrar o RingCentral ao Azure Active Directory

Neste tutorial, você aprenderá como integrar o RingCentral ao Azure AD (Azure Active Directory). Ao integrar o RingCentral ao Azure AD, é possível:

* Controlar quem tem acesso ao RingCentral no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao RingCentral com suas contas do Azure AD.
* Gerencie suas contas em uma localização central – o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para SSO (logon único) do RingCentral.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O RingCentral é compatível com SSO iniciado por **IDP**

## <a name="adding-ringcentral-from-the-gallery"></a>Adicionando o RingCentral da galeria

Para configurar a integração do RingCentral ao Azure AD, você precisará adicionar o RingCentral da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da por meio da galeria**, digite **RingCentral** na caixa de pesquisa.
1. Selecione **RingCentral** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o RingCentral usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do RingCentral.

Para configurar e testar o SSO do Azure AD com o RingCentral, conclua os blocos de construção a seguir:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do RingCentral](#configure-ringcentral-sso)** – para configurar o Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do RingCentral](#create-ringcentral-test-user)** – para que haja um equivalente de Brenda Fernandes no RingCentral vinculado à representação do usuário no Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **RingCentral**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, selecione **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    1. Clique em **Carregar arquivo de metadados**.
    1. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.
    1. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão preenchidos automaticamente na seção **Configuração Básica do SAML**.

    > [!Note]
    > O **arquivo de metadados do provedor de serviços** será inserido na página de configuração de SSO do RingCentral, que é explicada posteriormente no tutorial.

1. Se você não tiver o **arquivo de metadados do Provedor de Serviços**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma URL:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Na caixa de texto **URL de Resposta**, digite uma URL:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-ringcentral-sso"></a>Configurar o SSO do RingCentral

1. Em uma janela de navegador da Web diferente, entre no RingCentral como administrador de segurança.

1. Na parte superior, clique em **Ferramentas**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Navegue até **Logon único**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Na página **Configuração de Logon Único** em **Configurações de SSO**, da **Etapa 1**, clique em **Editar** e execute as etapas a seguir:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Na página **Configurar Logon Único**, execute as seguintes etapas:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Clique em **Procurar** para fazer upload do arquivo de metadados que você baixou do portal do Azure.

    b. Depois que você carregar os metadados, os valores serão populados automaticamente na seção **Informações Gerais de SSO**.

    c. Na seção **Mapeamento de Atributos**, selecione **Mapear atributo de email para** como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Clique em **Save** (Salvar).

    e. Na **Etapa 2** clique em **Baixar** para baixar o **arquivo de metadados do provedor de serviço** e carregue-o na seção **Configuração Básica do SAML** para preencher automaticamente os valores de **Identificador** e **URL de resposta** no portal do Azure.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Na mesma página, navegue até a seção **Habilitar SSO** e execute as seguintes etapas:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Selecione **Habilitar Serviço de SSO**.

    * Selecione **Permitir que os usuários entrem com credenciais de SSO ou do RingCentral**.

    * Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará a usuária de teste Brenda Fernandes no portal do Azure.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `Britta Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao RingCentral.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **RingCentral**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-ringcentral-test-user"></a>Criar usuário de teste do RingCentral

Nesta seção, você criará um usuário chamado Brenda Fernandes no RingCentral. Trabalhe com a  [equipe de suporte ao Cliente do RingCentral](https://success.ringcentral.com/RCContactSupp)  para adicionar os usuários à plataforma do RingCentral. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do RingCentral no Painel de Acesso, você deverá ser conectado automaticamente ao RingCentral, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
