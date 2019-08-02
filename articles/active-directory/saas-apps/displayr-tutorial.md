---
title: 'Tutorial: Integração do Azure Active Directory ao Displayr | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Displayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cd1785595cf2f6b2401837780106f52fdc97e36
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103981"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Tutorial: Integrar o Displayr com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Displayr ao Azure AD (Azure Active Directory). Ao integrar o Displayr no Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Displayr.
* Permitir que os usuários sejam conectados automaticamente ao Displayr com suas contas do Azure AD.
* Gerencie suas contas em uma localização central – o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Displayr.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Displayr é compatível com SSO iniciado por **SP**.

## <a name="adding-displayr-from-the-gallery"></a>Como adicionar o Displayr por meio da galeria

Para configurar a integração do Displayr ao Azure AD, você precisará adicionar o Displayr por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Displayr** na caixa de pesquisa.
1. Selecione **Displayr** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Displayr usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Displayr.

Para configurar e testar o SSO do Azure AD com o Displayr, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configurar o Displayr](#configure-displayr)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Displayr](#create-displayr-test-user)** para ter um equivalente de Brenda Fernandes no Displayr que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Displayr**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, selecione **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone Editar/de caneta de **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, execute a seguinte etapa:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<YOURDOMAIN>.displayr.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte ao cliente do Displayr](mailto:support@displayr.com) para obter esses valores. Veja também os padrões mostrados na seção "Configuração Básica de SAML" no portal do Azure.

1. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/certificatebase64.png)

1. O aplicativo Displayr espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo Atributos de usuário.

    ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo Displayr espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção**Atributos e Declarações de Usuário** na caixa de diálogo **Declarações de Grupo (Versão Prévia)** , execute as seguintes etapas:

    a. Clique na **caneta** ao lado de **Grupos retornados na declaração**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Selecione **Todos os Grupos** na lista de opções.

    c. Selecione **Atributo de Origem** da **ID do Grupo**.

    d. Marque **Personalizar o nome da declaração de grupo**.

    e. Marque **Emitir grupos como declarações de função**.

    f. Clique em **Save** (Salvar).

1. Na seção **Configurar o Displayr**, copie as URLs apropriadas de acordo com seus requisitos.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Configurar o Displayr

1. Para automatizar a configuração no Displayr, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Instalação do Displayr**. Você será direcionado para o aplicativo Displayr. Em seguida, forneça as credenciais de administrador para entrar no Displayr. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se quiser configurar o Displayr manualmente, abra uma nova janela do navegador da Web, entre no site corporativo do Displayr como administrador e execute as seguintes etapas:

4. Clique em **Configurações** e, em seguida, navegue para **Conta**.

    ![Configuração](./media/displayr-tutorial/config01.png)

5. Alterne para **Configurações** no menu superior e role a página para baixo para clicar em **Configurar Logon Único (SAML)** .

    ![Configuração](./media/displayr-tutorial/config02.png)

6. Na página **Logon Único (SAML)** , execute as seguintes etapas:

    ![Configuração](./media/displayr-tutorial/config03.png)

    a. Marque a caixa **Habilitar Logon Único (SAML)** .

    b. Copie o valor real do **Identificador** da seção **Configuração Básica de SAML** do Azure AD e cole-o na caixa de texto **Emissor**.

    c. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    e. Abra o Certificado (Base64) no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado**.

    f. Os **mapeamentos de grupo** são opcionais.

    g. Clique em **Save** (Salvar).  

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

Nesta seção, você habilitará Brenda Fernandes para usar o logon único do Azure permitindo a ela acesso ao Displayr.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Displayr**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-displayr-test-user"></a>Criar um usuário de teste do Displayr

Para permitir que os usuários do Azure AD entrem no Displayr, eles precisam ser provisionados no Displayr. No Displayr, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Displayr como administrador.

2. Clique em **Configurações** e, em seguida, navegue para **Conta**.

    ![Configuração do Displayr](./media/displayr-tutorial/config01.png)

3. Alterne para **Configurações** no menu superior e role a página para baixo até a seção **Usuários** e, em seguida, clique em **Novo Usuário**.

    ![Configuração do Displayr](./media/displayr-tutorial/config07.png)

4. Na página **Novo Usuário**, execute as seguintes etapas:

    ![Configuração do Displayr](./media/displayr-tutorial/config06.png)

    a. Na caixa de texto **Nome**, insira o nome do usuário como **Brendafernandes**.

    b. Na caixa de texto **Email**, insira o email do usuário como `Brittasimon@contoso.com`.

    c. Selecione a **Associação de grupo** apropriada.

    d. Clique em **Save** (Salvar).

### <a name="test-sso"></a>Testar o SSO

Ao clicar no bloco do Displayr no Painel de Acesso, você deverá ser conectado automaticamente ao Displayr, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
