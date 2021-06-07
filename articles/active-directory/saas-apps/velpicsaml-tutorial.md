---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Velpic SAML | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Velpic SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.openlocfilehash: 81523a1c11b6a3da4e7d1db5d8249921ad5047ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92635857"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Velpic SAML

Neste tutorial, você aprenderá a integrar o Velpic SAML ao Azure AD (Azure Active Directory). Ao integrar o Velpic SAML ao Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao Velpic SAML.
* Habilitar seus usuários para que entrem automaticamente no Velpic SAML com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Velpic SAML habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Velpic SAML dá suporte ao SSO iniciado por **SP**

## <a name="adding-velpic-saml-from-the-gallery"></a>Adicionando Velpic SAML usando a galeria

Para configurar a integração do Velpic SAML ao Azure AD, você precisa adicionar o Velpic SAML da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Velpic SAML** na caixa de pesquisa.
1. Selecione **Velpic SAML** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Configurar e testar o logon único do Azure AD para o Velpic SAML

Configure e teste o SSO do Azure AD com o Velpic SAML usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Velpic SAML.

Para configurar e testar o SSO do Azure AD no Velpic SAML, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Velpic SAML](#configure-velpic-saml-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Velpic SAML](#create-velpic-saml-test-user)** – para ter um equivalente de B. Fernandes no Velpic SAML que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Velpic SAML**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<sub-domain>.velpicsaml.net`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Observe que a URL de Logon será fornecida pela equipe do Velpic SAML e o valor do Identificador estará disponível quando você configurar o Plug-in do SSO no lado do Velpic SAML. É preciso copiar esse valor na página do aplicativo Velpic SAML e colá-lo aqui.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Velpic SAML**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo a ela acesso ao Velpic SAML.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Velpic SAML**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-velpic-saml-sso"></a>Configurar o SSO do Velpic SAML

1. Para automatizar a configuração no Velpic SAML, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Instalar Velpic SAML**. Você será direcionado ao aplicativo Velpic SAML. Nele, forneça as credenciais de administrador para entrar no Velpic SAML. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 8.

    ![Configuração da instalação](common/setup-sso.png)

3. Caso deseje configurar o Velpic SAML manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do Velpic SAML como administrador e execute as seguintes etapas:

4. Clique na guia **Gerenciar** e vá para a seção **Integração**, onde você pode clicar no botão **Plug-ins** para criar novo plug-in para Conectar.

    ![A captura de tela mostra a página Integração, na qual você pode selecionar Plug-ins.](./media/velpicsaml-tutorial/velpic_1.png)

5. Clique no botão **"Adicionar plug-in"** .
    
    ![A captura de tela mostra o botão Adicionar Plug-in selecionado.](./media/velpicsaml-tutorial/velpic_2.png)

6. Clique no bloco **SAML** na página Adicionar Plug-in.
    
    ![A captura de tela mostra o SAML selecionado na página Adicionar Plug-in.](./media/velpicsaml-tutorial/velpic_3.png)

7. Insira o nome do novo plug-in SAML e clique no botão **"Adicionar"** .

    ![A captura de tela mostra a caixa de diálogo Adicionar novo plug-in SAML com o Azure AD inserido.](./media/velpicsaml-tutorial/velpic_4.png)

8. Insira os detalhes da seguinte maneira:

    ![A captura de tela mostra a página do Azure AD em que você pode inserir os valores descritos.](./media/velpicsaml-tutorial/velpic_5.png)

    a. Na caixa de texto **Nome**, digite o nome do plug-in SAML.

    b. Na caixa de texto **URL do Emissor**, cole o **Identificador do Azure AD** que copiou da janela **Configurar logon** no portal do Azure.

    c. Em **Configuração dos Metadados do Provedor**, faça upload do arquivo XML de Metadados que você baixou no portal do Azure.

    d. Você também pode optar por habilitar o SAML apenas durante o provisionamento, habilitando a caixa de seleção **"Criar automaticamente novos usuários"** . Se um usuário não existir no Velpic e esse sinalizador não estiver habilitado, o logon no Azure falhará. Se o sinalizador estiver habilitado, o usuário será provisionado automaticamente no Velpic no momento do logon. 

    e. Copie a **URL de logon único** da caixa de texto e cole-a no portal do Azure.
    
    f. Clique em **Save** (Salvar).

### <a name="create-velpic-saml-test-user"></a>Crie um usuário de teste no Velpic SAML

Normalmente, esta etapa não é necessária, pois o aplicativo dá suporte ao provisionamento de usuário na hora certa. Se o provisionamento automático de usuário não estiver habilitado, a criação manual do usuário poderá ser feita como descrito abaixo.

Entre no site da empresa do seu Velpic SAML como um administrador e execute as seguintes etapas:
    
1. Clique na guia Gerenciar, vá para a seção Usuários e clique no botão Novo para adicionar usuários.

    ![adicionar usuário](./media/velpicsaml-tutorial/velpic_7.png)

2. Na página da caixa de diálogo **"Criar Novo Usuário"** , execute as etapas que se seguem.

    ![usuário](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Na caixa de texto **Nome**, digite o nome B.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome Fernandes.

    c. Na caixa de texto **Nome de Usuário**, digite o nome de usuário de B. Fernandes.

    d. Na caixa de texto **Email**, digite o endereço de email da conta B.Simon@contoso.com.

    e. O restante das informações é opcional, você pode preenchê-las se necessário.
    
    f. Clique em **SALVAR**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

1. Ao clicar no bloco Velpic SAML no Painel de Acesso, você deve acessar a página de logon do aplicativo Velpic SAML. Você deve ver o botão **"Fazer logon com Azure AD"** na página de entrada.

    ![A captura de tela mostra o Portal de Aprendizado com o botão Fazer Logon com o Azure AD selecionado.](./media/velpicsaml-tutorial/velpic_6.png)

1. Clique no botão **"Fazer logon com Azure AD"** para entrar no Velpic usando sua conta do Azure AD.

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Velpic SAML com o Azure AD](https://aad.portal.azure.com/)