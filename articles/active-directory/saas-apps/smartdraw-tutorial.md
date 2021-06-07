---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SmartDraw | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SmartDraw.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: b4b9aa16dfc474575bd522238f06fe487508e7e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515841"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smartdraw"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SmartDraw

Neste tutorial, você aprenderá a integrar o SmartDraw ao Azure AD (Azure Active Directory). Ao integrar o SmartDraw ao Azure AD, você pode:

* Controle quem terá acesso ao SmartDraw no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao SmartDraw com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do SmartDraw habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SmartDraw dá suporte ao SSO iniciado por **SP e IDP**
* O SmartDraw dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-smartdraw-from-the-gallery"></a>Adicionando o SmartDraw da galeria

Para configurar a integração do SmartDraw ao Azure AD, é necessário adicionar o SmartDraw da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da por meio da galeria**, digite **SmartDraw** na caixa de pesquisa.
1. Selecione **SmartDraw** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-smartdraw"></a>Configurar e testar o logon único do Azure AD para o SmartDraw

Configure e teste o SSO do Azure AD com o SmartDraw com um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SmartDraw.

Para configurar e testar o SSO do Azure AD com o SmartDraw, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SmartDraw](#configure-smartdraw-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do SmartDraw](#create-smartdraw-test-user)** : para ter um equivalente de B.Fernandes no SmartDraw que esteja vinculado à representação de usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SmartDraw**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://cloud.smartdraw.com/sso/saml/login/<domain>`

    > [!NOTE]
    > O valor da URL de logon não é real. Você atualizará o valor da URL de Logon com a URL de Logon real, que será explicada posteriormente no tutorial. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Clique em **Save** (Salvar).

1. Seu aplicativo SmartDraw espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo SmartDraw espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem|
    | ---------------| --------------- |
    | Nome | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |
    | Grupos | user.groups |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o SmartDraw**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo a ela acesso ao SmartDraw.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SmartDraw**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-smartdraw-sso"></a>Configurar o SSO do SmartDraw

1. Para automatizar a configuração no SmartDraw, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, se você clicar em **Configurar o SmartDraw**, será direcionado para o aplicativo SmartDraw. Nele, forneça as credenciais de administrador para entrar no SmartDraw. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 5.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o SmartDraw manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do SmartDraw como administrador e execute as seguintes etapas:

1. Clique em **Logon Único** em Gerenciar sua licença do SmartDraw.

    ![Captura de tela que mostra a caixa de diálogo Gerenciar sua licença do SmartDraw, na qual você pode selecionar Logon Único.](./media/smartdraw-tutorial/configure01.png)

1. Na página Configuração, siga estas etapas:

    ![Captura de tela que mostra a página Configuração, na qual você pode inserir os valores descritos.](./media/smartdraw-tutorial/configure02.png)

    a. Na caixa de texto **Seu domínio (como acme.com)** , digite seu domínio.

    b. Copie **Sua URL de logon iniciada por SP será** da sua instância e cole-a na caixa de texto URL de logon na **Configuração Básica de SAML** no portal do Azure.

    c. Na caixa de texto **Grupo de segurança para permitir o acesso do SmartDraw**, digite **Todos**.

    d. Na caixa de texto **Sua URL de emissor do SAML**, cole o valor de **Identificador do Azure AD** que você copiou do portal do Azure.

    e. No Bloco de notas, abra o arquivo XML de metadados que você baixou do portal do Azure, copie seu conteúdo e, em seguida, cole-o na caixa **Seus metadados do SAML**.

    f. Clique em **Salvar Configuração** 

### <a name="create-smartdraw-test-user"></a>Criar usuário de teste do SmartDraw

Nesta seção, um usuário chamado B.Fernandes será criado no SmartDraw. O SmartDraw dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se o usuário ainda não existir no SmartDraw, será criado um novo usuário após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SmartDraw no Painel de Acesso, você deverá entrar automaticamente no SmartDraw para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Avaliar o SmartDraw com o Azure AD](https://aad.portal.azure.com/)