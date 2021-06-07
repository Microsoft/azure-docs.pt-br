---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Boomi | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Boomi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: c58566c628eedd1dbc3d86ae6a142156cbf31211
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585189"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Boomi

Neste tutorial, você aprenderá a integrar o Boomi ao Azure AD (Azure Active Directory). Ao integrar o Boomi ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Boomi.
* Permitir que os usuários sejam conectados automaticamente ao Boomi com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Boomi.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Boomi é compatível com o SSO iniciado por **IDP**.

## <a name="add-boomi-from-the-gallery"></a>Adicionar o Boomi por meio da galeria

Para configurar a integração do Boomi ao Azure AD, você precisa adicionar o Boomi por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Boomi** na caixa de pesquisa.
1. Selecione **Boomi** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-boomi"></a>Configurar e testar o SSO do Azure AD para o Boomi

Configure e teste o SSO do Azure AD com o Boomi usando um usuário de teste com o nome **B.Fernandes**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre o usuário do Azure AD e o usuário relacionado no Boomi.

Para configurar e testar o SSO do Azure AD no Boomi, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Boomi](#configure-boomi-sso)** – para configurar o logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Boomi](#create-boomi-test-user)** – para ter um equivalente de Brenda Fernandes no Boomi que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Boomi**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, se você tiver um **Arquivo de metadados do Provedor de Serviço** e quiser configurar o modo iniciado por **IDP**, execute as etapas a seguir:

    a. Clique em **Carregar arquivo de metadados**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão preenchidos automaticamente na seção "Configuração Básica do SAML".

    d. Insira a **URL de Logon**, por exemplo, `https://platform.boomi.com/AtomSphere.html#build;accountId={your-accountId}`.

    > [!Note]
    > Você obterá o **arquivo de metadados do Provedor de Serviços** na seção **Configurar SSO do Boomi**, que é explicada mais adiante neste tutorial. Se os valores de **Identificador** e **URL de Resposta** não forem populados automaticamente, preencha-os manualmente de acordo com suas necessidades.

1. O aplicativo Boomi espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração dos atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![Captura de tela que mostra Atributos e Declarações do Usuário com valores padrão, como Givenname user.givenname e Emailaddress User.mail.](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Boomi espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de Origem|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Boomi**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Boomi.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Boomi**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-boomi-sso"></a>Configurar o SSO do Boomi

1. Em uma janela diferente do navegador da Web, entre em seu site da empresa do Boomi como administrador.

1. Navegue até **Nome da Empresa** e vá para **Configurar**.

1. Clique na guia **Opções de SSO** e execute etapas a seguir.

    ![Configurar o logon único no lado do aplicativo](./media/boomi-tutorial/import.png)

    a. Marque a caixa de seleção **Habilitar Logon Único do SAML**.

    b. Clique em **Importar** para carregar o certificado baixado do Azure AD no **Certificado do Provedor de Identidade**.

    c. Na caixa de texto **URL de Logon do Provedor de Identidade**, insira o valor da **URL de Logon** da janela de configuração de aplicativo do Azure AD.

    d. Para **Local da ID de Federação**, selecione o botão de opção **A ID de Federação está no elemento de Atributo FEDERATION_ID**.

    e. Copie a **URL de metadados do AtomSphere**, acesse a **URL de metadados** por meio do navegador de sua escolha e salve a saída em um arquivo. Carregue a **URL de metadados** na seção **Configuração de SAML básica** no portal do Azure.

    f. Clique no botão **Salvar** .

### <a name="create-boomi-test-user&quot;></a>Criar um usuário de teste do Boomi

Para permitir que os usuários do Azure AD entrem no Boomi, eles deverão ser provisionados no Boomi. No caso do Boomi, o provisionamento é uma tarefa manual.

### <a name=&quot;to-provision-a-user-account-perform-the-following-steps&quot;></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Entre no site de empresa Boomi como administrador.

1. Depois de fazer logon, navegue até **Gerenciamento de Usuário** e vá até **Usuários**.

    ![Captura de tela que mostra a página Gerenciamento de Usuário com Usuários selecionado.](./media/boomi-tutorial/user.png &quot;Usuários")

1. Clique no ícone **+** e o diálogo **Adicionar/Manter Funções de Usuário** será aberto.

    ![Captura de tela que mostra o ícone + selecionado.](./media/boomi-tutorial/add.png "Usuários")

    ![Captura de tela que mostra Adicionar/Manter Funções de Usuário, em que configura um usuário.](./media/boomi-tutorial/roles.png "Usuários")

    a. Na caixa de texto **Endereço de email do usuário**, digite o email do usuário, como B.Simon@contoso.com.

    b. Na caixa de texto **Nome**, digite o nome do usuário, como B.

    c. Na caixa de texto **Sobrenome**, digite o Sobrenome do usuário como Fernandes.

    d. Insira a **ID de Federação** do usuário. Cada usuário deve ter uma ID de Federação que o identifique exclusivamente na conta.

    e. Atribua a função **Usuário Padrão** ao usuário. Não atribua a função Administrador, pois isso concederia a ele o acesso normal ao Atmosphere, além do acesso de logon único.

    f. Clique em **OK**.

    > [!NOTE]
    > O usuário não receberá um email de notificação de boas-vindas contendo uma senha que pode ser usada para fazer logon na conta do AtomSphere porque a senha é gerenciada por meio do provedor de identidade. É possível usar qualquer outra ferramenta de criação da conta de usuário do Boomi ou as APIs fornecidas pelo Boomi para provisionar as contas de usuário do AAD.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e entre automaticamente no Boomi, para o qual configurou o SSO.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Boomi em Meus Aplicativos, você deverá ser conectado automaticamente ao Boomi para o qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Boomi, você poderá impor controles de sessão, que fornecem proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).