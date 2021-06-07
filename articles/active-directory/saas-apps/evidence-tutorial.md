---
title: 'Tutorial: Integração do Azure Active Directory ao Evidence.com | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Evidence.com.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2020
ms.author: jeedes
ms.openlocfilehash: ff77ace78dd3cdd4ee942308432795421f6da997
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453783"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evidencecom"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Evidence.com

Neste tutorial, você aprenderá a integrar o Evidence.com ao Azure AD (Azure Active Directory). Ao integrar o Evidence.com ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Evidence.com.
* Permitir que os usuários sejam conectados automaticamente ao Evidence.com com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Evidence.com.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Evidence.com dá suporte ao SSO iniciado por **SP**
* Após configurar o Evidence.com, você poderá impor controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-evidencecom-from-the-gallery"></a>Adicionar o Evidence.com da galeria

Para configurar a integração do Evidence.com ao Azure AD, é necessário adicionar o Evidence.com da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Evidence.com** na caixa de pesquisa.
1. Selecione **Evidence.com** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evidencecom"></a>Configurar e testar o logon único do Azure AD para o Evidence.com

Configure e teste o SSO do Azure AD com o Evidence.com usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Evidence.com.

Para configurar e testar o SSO do Azure AD com o Evidence.com, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Evidence.com](#configure-evidencecom-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Evidence.com](#create-evidencecom-test-user)** – para ter um equivalente de B.Fernandes no Evidence.com que esteja vinculado à declaração de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Evidence.com**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar Logon Único com SAML**, execute as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<yourtenant>.evidence.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<yourtenant>.evidence.com`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<your tenant>.evidence.com/?class=UIX&proc=Login`
    
    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon reais. Contate a [equipe de suporte ao cliente do Evidence.com](https://communities.taser.com/support/SupportContactUs?typ=LE) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Evidence.com**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Evidence.com.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Evidence.com**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-evidencecom-sso"></a>Configurar o SSO do Evidence.com

1. Em uma janela separada do navegador da Web, entre como administrador em seu locatário do Evidence.com e navegue até a guia **Administrador**

2. Clique em **Logon Único de Agência**

3. Escolha **Logon Único Baseado em SAML**

4. Copie os valores de **Identificador do Azure AD**, **URL de Logon** e **URL de Logoff** mostrados no portal do Azure nos campos correspondentes do Evidence.com.

5. Abra o arquivo de Certificado (Base64) baixado no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa **Certificado de Segurança**. 

6. Salve a configuração no Evidence.com.

### <a name="create-evidencecom-test-user"></a>Criar um usuário de teste do Evidence.com

Para que os usuários do AD do Azure possam fazer logon, eles deverão receber a permissão de acesso dentro do aplicativo Evidence.com. Esta seção descreve como criar contas de usuário do AD do Azure no Evidence.com.

**Para provisionar uma conta de usuário no Evidence.com:**

1. Em uma janela do navegador da Web, entre no site de sua empresa do Evidence.com como administrador.

2. Navegue até a guia **Administrador** .

3. Clique em **Adicionar Usuário**.

4. Clique no botão **Adicionar** .

5. O **Endereço de Email** do usuário adicionado deverá coincidir com o nome de usuário dos usuários no AD do Azure aos quais você deseja conceder acesso. Se o nome de usuário e o endereço de email não tiverem o mesmo valor em sua organização, use a seção **Evidence.com > Atributos > Logon Único** do Portal do Azure para alterar o identificador de nome enviado ao Evidence.com para ser o endereço de email.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Evidence.com no Painel de Acesso, você deverá ser conectado automaticamente ao Evidence.com, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Avaliar o Evidence.com com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Evidence.com com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)