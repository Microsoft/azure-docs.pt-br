---
title: 'Tutorial: Integração SSO (logon único) do Azure Active Directory com o Cisco Webex | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cisco Webex.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 49e92c485c1a6a66dfb12b3c7a91f29939851d82
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456114"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Tutorial: Integração SSO (logon único) do Azure Active Directory com o Cisco Webex

Neste tutorial, você aprenderá a integrar o Cisco Webex ao Azure AD (Azure Active Directory). Com a integração do Cisco Webex ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Cisco Webex.
* Permitir que os usuários entrem automaticamente ao Cisco Webex com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do Cisco Webex.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Cisco Webex é compatível com o SSO iniciado por **SP** .
* O Cisco Webex é compatível com o provisionamento de usuário **Automatizado** .
* Depois de configurar o Cisco Webex, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cisco-webex-from-the-gallery"></a>Adicionando o Cisco Webex da galeria

Para configurar a integração do Cisco Webex ao Azure Active Directory, você precisa adicionar o Cisco Webex da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory** .
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos** .
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo** .
1. Na seção **Adicionar por meio da galeria** , digite **Cisco Webex** na caixa de pesquisa.
1. Selecione **Cisco Webex** no painel de resultados e, depois, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Configurar e testar o logon único do Azure AD para Cisco WebEx

Configure e teste o SSO do Azure AD com o Cisco Webex usando um usuário de teste chamado **B.Fernandes** . Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cisco Webex.

Para configurar e testar o SSO do Azure AD com o Cisco Webex, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B. Fernandes use o logon único do Azure AD.
2. **[Configure o Cisco Webex](#configure-cisco-webex)** para definir as configurações de SSO no lado do aplicativo.
    1. **[Crie um usuário de teste do Cisco Webex](#create-cisco-webex-test-user)** para ter um equivalente de B.Fernandes no Cisco Webex que esteja vinculado à representação de usuário do Azure AD.
3. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Cisco Webex** , localize a seção **Gerenciar** e selecione **Logon único** .
1. Na página **Escolher um método de logon único** , escolha **SAML** .
1. Na página **Configurar o Logon Único com SAML** , clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML** , carregue o arquivo **Metadados do Provedor de Serviços** baixado e configure o aplicativo executando as seguintes etapas:

    >[!Note]
    >Você obterá o arquivo de Metadados do Provedor de Serviços na seção **Configurar Cisco Webex** , que é explicada mais adiante neste tutorial. 

    a. Clique em **Carregar arquivo de metadados** .

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar** .

    c. Após a conclusão bem-sucedida do upload do arquivo de metadados do Provedor de Serviços, os valores de **Identificador** e **URL de Resposta** são preenchidos automaticamente na seção **Configuração Básica do SAML** :

    Na caixa de texto **URL de logon** , cole o valor da **URL de Resposta** que é preenchida automaticamente com o upload do arquivo de metadados de SP.

1. O aplicativo Cisco Webex espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Cisco Webex espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
  
    | Nome |  Atributo de Origem|
    | ---------------|--------- |
    | uid | user.userprincipalname |

1. Na página **Configurar logon único com SAML** , na seção **Certificado de assinatura SAML** , localize **XML de Metadados de Federação** e escolha **Download** para fazer o download do certificado e salve-o em seu computador.

   ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Cisco Webex** , copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory** , **Usuários** e, em seguida, **Todos os usuários** .
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário** , siga estas etapas:
   1. No campo **Nome** , insira `B.Simon`.  
   1. No campo **Nome de usuário** , insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha** .
   1. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Cisco Webex.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos** .
1. Na lista de aplicativos, escolha **Cisco Webex** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos** .

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição** .

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** , selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **Atribuir** .

## <a name="configure-cisco-webex"></a>Configurar o Cisco Webex

1. Para automatizar a configuração no Cisco Webex, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos** , clicando em **Instalar a extensão** .

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, um clique em **Configurar o Cisco Webex** direcionará você ao aplicativo Cisco Webex. Nele, forneça as credenciais de administrador para entrar no Cisco Webex. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 8.

    ![Configuração da instalação](common/setup-sso.png)

3. Se quiser configurar o Cisco Webex manualmente, entre no [Gerenciamento de Colaboração de Nuvem da Cisco](https://admin.ciscospark.com/) com suas credenciais completas de administrador.

4. Escolha **Configurações** e, na seção **Autenticação** , clique em **Modificar** .

    ![Captura de tela que mostra as Configurações de Autenticação em que você pode selecionar Modificar.](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
5. Escolha **Integrar um provedor de identidade de terceiros. (Avançado)** e vá para a próxima tela.

6. Na página **Importar Metadados Idp** , arrastre e solte o arquivo de metadados do Azure AD na página ou use a opção de navegador de arquivos para localizar e carregar o arquivo de metadados do Azure AD. Em seguida, escolha **Exigir certificado assinado por uma autoridade de certificação em Metadados (mais seguro)** e clique em **Avançar** .

    ![Captura de tela que mostra a página Importar Metadados do IdP.](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

7. Escolha **Testar Conexão SSO** e, quando uma nova guia do navegador for aberta, autentique-se com o Azure AD conectando-se.

8. Volte para a guia do navegador **Gerenciamento de Colaboração de Nuvem da Cisco** . Se o teste tiver sido bem-sucedido, escolha a opção **Este teste foi executado com êxito. Habilitar Logon Único** e clique em **Avançar** .

### <a name="create-cisco-webex-test-user"></a>Criar um usuário de teste do Cisco Webex

Nesta seção, você criará um usuário chamado B. Fernandes no Cisco Webex. Nesta seção, você criará um usuário chamado B. Fernandes no Cisco Webex.

1. Acesse o [Gerenciamento de Colaboração de Nuvem da Cisco](https://admin.ciscospark.com/) com suas credenciais completas de administrador.

2. Clique em **Usuários** e em **Gerenciar Usuários** .
   
    ![Captura de tela que mostra a página Usuários, na qual você pode Gerenciar Usuários.](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. Na janela **Gerenciar Usuário** , escolha **Adicionar ou modificar usuários manualmente** e clique em **Avançar** .

4. Escolha **Nomes e Endereço de email** . Em seguida, preencha a caixa de texto como se segue:

    ![Captura de tela que mostra a caixa de diálogo Gerenciar usuários, na qual você pode adicionar ou modificar usuários manualmente.](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. Na caixa de texto **Nome** , digite o nome do usuário como **B** .

    b. Na caixa de texto **Sobrenome** , digite o sobrenome do usuário como **Fernandes** .

    c. Na caixa de texto **Endereço de email** , digite o endereço de email do usuário como b.simon@contoso.com.

5. Clique no sinal de mais para adicionar B. Fernandes. Em seguida, clique em **Avançar** .

6. Na janela **Adicionar Serviços para Usuários** , clique em **Salvar** e em **Concluir** .

## <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Cisco Webex no Painel de Acesso, você entrará automaticamente no Cisco Webex, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Cisco Webex com o Azure AD](https://aad.portal.azure.com)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Cisco Webex com visibilidade e controles avançados](/cloud-app-security/protect-webex)