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
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: ad6d5308638b112afe2b51c4e149f876651e429d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592516"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Tutorial: Integração SSO (logon único) do Azure Active Directory com o Cisco Webex

Neste tutorial, você aprenderá a integrar o Cisco Webex ao Azure AD (Azure Active Directory). Com a integração do Cisco Webex ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Cisco Webex.
* Permitir que os usuários entrem automaticamente ao Cisco Webex com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do Cisco Webex.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Cisco Webex é compatível com o SSO iniciado por **SP**.
* O Cisco Webex dá suporte ao [**provisionamento de usuário automatizado**](./cisco-webex-provisioning-tutorial.md).

## <a name="adding-cisco-webex-from-the-gallery"></a>Adicionando o Cisco Webex da galeria

Para configurar a integração do Cisco Webex ao Azure Active Directory, você precisa adicionar o Cisco Webex da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Cisco Webex** na caixa de pesquisa.
1. Selecione **Cisco Webex** no painel de resultados e, depois, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex"></a>Configurar e testar o SSO do Azure AD para o Cisco Webex

Configure e teste o SSO do Azure AD com o Cisco Webex usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cisco Webex.

Para configurar e testar o SSO do Azure AD com o Cisco Webex, execute as seguintes etapas:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B. Fernandes use o logon único do Azure AD.
2. **[Configure o Cisco Webex](#configure-cisco-webex)** para definir as configurações de SSO no lado do aplicativo.
    1. **[Crie um usuário de teste do Cisco Webex](#create-cisco-webex-test-user)** para ter um equivalente de B.Fernandes no Cisco Webex que esteja vinculado à representação de usuário do Azure AD.
3. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Cisco Webex**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, carregue o arquivo **Metadados do Provedor de Serviços** baixado e configure o aplicativo executando as seguintes etapas:

    >[!Note]
    >Você obterá o arquivo de Metadados do Provedor de Serviços na seção **Configurar Cisco Webex**, que é explicada mais adiante neste tutorial. 

    a. Clique em **Carregar arquivo de metadados**.

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    c. Após a conclusão bem-sucedida do upload do arquivo de metadados do Provedor de Serviços, os valores de **Identificador** e **URL de Resposta** são preenchidos automaticamente na seção **Configuração Básica do SAML**:

    Na caixa de texto **URL de logon**, cole o valor da **URL de Resposta** que é preenchida automaticamente com o upload do arquivo de metadados de SP.

1. O aplicativo Cisco Webex espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Cisco Webex espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
  
    | Nome |  Atributo de Origem|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    > [!NOTE]
    >  O valor do atributo de origem é, por padrão, mapeado para userpricipalname. Isso pode ser alterado para usar.mail ou user.onpremiseuserprincipalname ou qualquer outro valor de acordo com a configuração no WebEx.


1. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, localize **XML de Metadados de Federação** e escolha **Download** para fazer o download do certificado e salve-o em seu computador.

   ![O link de download do Certificado](common/metadataxml.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Cisco Webex.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Cisco Webex**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-cisco-webex"></a>Configurar o Cisco Webex

1. Entre no Cisco Webex com suas credenciais de administrador.

1. Selecione **Configurações da Organização** e, na seção **Autenticação**, clique em **Modificar**.

    ![Captura de tela que mostra as Configurações de Autenticação em que você pode selecionar Modificar.](./media/cisco-spark-tutorial/organization-settings.png)
  
1. Escolha **Integrar um provedor de identidade de terceiros. (Avançado)** e clique em **Avançar**.

    ![Captura de tela mostrando Integrar um provedor de identidade de terceiros.](./media/cisco-spark-tutorial/enterprise-settings.png)

1. Clique em **Baixar Arquivo de Metadados** para baixar o **arquivo de metadados do provedor de serviços**, salve-o em seu computador e clique em **Avançar**.

    ![Captura de tela mostrando o arquivo de metadados do provedor de serviços.](./media/cisco-spark-tutorial/sp-metadata.png)

1. Clique na opção **navegador de arquivos** para localizar e carregar o arquivo de metadados do Azure AD. Em seguida, escolha **Exigir certificado assinado por uma autoridade de certificação em Metadados (mais seguro)** e clique em **Avançar**.

    ![Captura de tela que mostra a página Importar Metadados do IdP.](./media/cisco-spark-tutorial/idp-metadata.png)

1. Escolha **Testar Conexão SSO** e, quando uma nova guia do navegador for aberta, autentique-se com o Azure AD conectando-se.

1. Volte para a guia do navegador **Gerenciamento de Colaboração de Nuvem da Cisco**. Se o teste tiver sido bem-sucedido, escolha a opção **Este teste foi executado com êxito. Habilitar Logon Único** e clique em **Avançar**.

1. Clique em **Save** (Salvar).

> [!NOTE]
> Para saber mais sobre como configurar o Cisco WebEx, confira [esta](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) página.

### <a name="create-cisco-webex-test-user"></a>Criar um usuário de teste do Cisco Webex

Nesta seção, um usuário chamado B.Fernandes é criado no Cisco WebEx. Esse aplicativo dá suporte ao provisionamento automático de usuário, o que permite o provisionamento e o desprovisionamento automático com base em suas regras de negócio.  A Microsoft recomenda usar o provisionamento automático sempre que possível. Confira como habilitar o provisionamento automático para o [Cisco Webex](./cisco-webex-provisioning-tutorial.md).

Se você precisar criar um usuário manualmente, execute as seguintes etapas:

1. Entre no Cisco Webex com suas credenciais de administrador.

2. Clique em **Usuários** e em **Gerenciar Usuários**.
   
    ![Captura de tela que mostra a página Usuários, na qual você pode Gerenciar Usuários.](./media/cisco-spark-tutorial/user-1.png) 

3. Na janela **Gerenciar Usuários**, selecione **Adicionar ou Modificar Usuários Manualmente**.

    ![Captura de tela mostrando a página Usuários, em que você pode Gerenciar Usuários e selecionar Adicionar ou Modificar Usuários Manualmente.](./media/cisco-spark-tutorial/user-2.png)

4. Escolha **Nomes e Endereço de email**. Em seguida, preencha a caixa de texto como se segue:

    ![Captura de tela que mostra a caixa de diálogo Gerenciar usuários, na qual você pode adicionar ou modificar usuários manualmente.](./media/cisco-spark-tutorial/user-3.png) 

    a. Na caixa de texto **Nome**, digite o nome do usuário como **B**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como **Fernandes**.

    c. Na caixa de texto **Endereço de email**, digite o endereço de email do usuário como b.simon@contoso.com.

5. Clique no sinal de mais para adicionar B. Fernandes. Em seguida, clique em **Avançar**.

6. Na janela **Adicionar Serviços para Usuários**, clique em **Adicionar Usuários** e em **Concluir**.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado à URL de Logon do Cisco Webex, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Cisco Webex e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando clicar no bloco do Citrix Webex em Meus Aplicativos, você será redirecionado à URL de Logon do Citrix Webex. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Cisco Webex, você poderá aplicar o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O Controle de Sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)