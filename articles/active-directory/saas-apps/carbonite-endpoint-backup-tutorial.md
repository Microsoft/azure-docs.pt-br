---
title: 'Tutorial: Integração do Azure Active Directory ao Carbonite Endpoint Backup | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Carbonite Endpoint Backup.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: ff19275270e5b6572fb7d637b88c4736a3aa6ea0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456476"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Tutorial: Integrar o Carbonite Endpoint Backup com o Azure Active Directory

Neste tutorial, você aprenderá como integrar o Carbonite Endpoint Backup ao Azure AD (Azure Active Directory). Ao integrar o Carbonite Endpoint Backup ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Carbonite Endpoint Backup.
* Permitir que os usuários sejam conectados automaticamente ao Carbonite Endpoint Backup com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Carbonite Endpoint Backup.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Carbonite Endpoint Backup dá suporte a SSO iniciado por pelo **SP e IDP**

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Como adicionar o Carbonite Endpoint Backup da galeria

Para configurar a integração do Carbonite Endpoint Backup ao Azure AD, você precisa adicionar o Carbonite Endpoint Backup da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Carbonite Endpoint Backup** na caixa de pesquisa.
1. Selecione **Carbonite Endpoint Backup** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Carbonite Endpoint Backup usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Carbonite Endpoint Backup.

Para configurar e testar o SSO do Azure AD com o Carbonite Endpoint Backup, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar SSO do Carbonite Endpoint Backup](#configure-carbonite-endpoint-backup-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Carbonite Endpoint Backup](#create-carbonite-endpoint-backup-test-user)** – para ter um equivalente de B.Fernandes no Carbonite Endpoint Backup vinculado à representação do usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Carbonite Endpoint Backup**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma das seguintes URLs:

    ```http
    https://red-us.mysecuredatavault.com
    https://red-apac.mysecuredatavault.com
    https://red-fr.mysecuredatavault.com
    https://red-emea.mysecuredatavault.com
    https://kamino.mysecuredatavault.com
    ```

    b. Na caixa de texto **URL de Resposta**, digite uma das seguintes URLs:

    ```http
    https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx
    ```

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite uma das seguintes URLs:

    ```http
    https://red-us.mysecuredatavault.com/
    https://red-apac.mysecuredatavault.com/
    https://red-fr.mysecuredatavault.com/
    https://red-emea.mysecuredatavault.com/
    ```

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar Carbonite Endpoint Backup**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Configurar o SSO do Carbonite Endpoint Backup

1. Para automatizar a configuração no Backup do Ponto de Extremidade do Carbonite, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clicar em **Configurar o Backup do Ponto de Extremidade do Carbonite** o direcionará para o aplicativo de Backup do Ponto de Extremidade do Carbonite. Daí em diante, forneça as credenciais de administrador para entrar no Backup do Ponto de Extremidade do Carbonite. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o Backup do Ponto de Extremidade do Carbonite manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do Backup do Ponto de Extremidade do Carbonite como administrador e execute as seguintes etapas:

4. Clique em **Empresa** no painel esquerdo.

    ![Captura de tela que mostra o Ponto de Extremidade do Carbonite com Empresa selecionado.](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Clique em **Logon único**.

    ![Captura de tela que mostra Empresa com Logon Único selecionado.](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Clique em **Habilitar** e depois em **Editar configurações** para configurar.

    ![Captura de tela que mostra a guia Logon único com as configurações Habilitar e Editar chamadas.](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Na página **Configurações de logon único**, realize as seguintes etapas:

    ![Captura de tela que mostra a guia Logon Único com as informações descritas nesta etapa.](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. Na caixa de texto **Nome do provedor de identidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    1. Na caixa de texto **URL do provedor de identidade**, cole o valor da **URL de Logon** copiado do portal do Azure.

    1. Clique em **Escolher arquivo** para carregar o **Certificado (Base64)** que você baixou do portal do Azure.

    1. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Carbonite Endpoint Backup.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Carbonite Endpoint Backup**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-carbonite-endpoint-backup-test-user"></a>Criar usuário de teste do Carbonite Endpoint Backup

1. Em uma janela diferente do navegador da Web, entre no site da sua empresa do Carbonite Endpoint Backup como administrador.

1. Clique em **Usuários** no painel esquerdo e, em seguida, clique em **Adicionar usuário**.

    ![Captura de tela que mostra a página Ponto de Extremidade do Carbonite com Usuários e Adicionar usuários selecionados.](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. Na página **Adicionar usuário**, realize as seguintes etapas:

    ![Captura de tela que mostra a página Adicionar usuário, na qual você pode executar as etapas descritas aqui.](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Insira o **Email**, o **Nome** e o **Sobrenome** do usuário e forneça as permissões necessárias para o usuário de acordo com os requisitos organizacionais.

    1. Clique em **Adicionar usuário**.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco Carbonite Endpoint Backup no Painel de Acesso, você deverá ser conectado automaticamente ao Carbonite Endpoint Backup para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)