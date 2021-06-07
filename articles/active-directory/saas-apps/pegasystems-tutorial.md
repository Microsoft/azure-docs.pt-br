---
title: 'Tutorial: Integração do Azure Active Directory aos Pega Systems | Microsoft Docs'
description: Neste tutorial você aprenderá a configurar o logon único entre o Azure Active Directory e o Pega Systems.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 802bd61d499f64a128a4d1c0585363cb1962f8a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649993"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Tutorial: Integração do Azure Active Directory ao Pega Systems

Neste tutorial, você aprenderá a integrar o Pega Systems ao Azure AD (Azure Active Directory). Ao integrar o Pega Systems ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Pega Systems.
* Permitir que os usuários sejam conectados automaticamente ao Pega Systems com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Pega Systems habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Pega Systems é compatível com SSO iniciado por SP e IdP.

## <a name="add-pega-systems-from-the-gallery"></a>Adicionar o Pega Systems por meio da galeria

Para configurar a integração do Pega Systems ao Azure AD, você precisa adicionar o Pega Systems da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Pega Systems** na caixa de pesquisa.
1. Selecione **Pega Systems** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-pega-systems"></a>Configurar e testar o SSO do Azure AD para o Pega Systems

Configure e teste o SSO do Azure AD com o Pega Systems usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Pega Systems.

Para configurar e testar o SSO do Azure AD com o Pega Systems, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Pega Systems](#configure-pega-systems-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Pega Systems](#create-pega-systems-test-user)** – para ter um equivalente de B.Fernandes no Pega Systems que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Pega Systems**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na caixa de diálogo **Configuração Básica de SAML**, caso deseje configurar o aplicativo no modo iniciado por IdP, realize as seguintes etapas.

    ![Caixa de diálogo Configuração Básica de SAML](common/idp-intiated.png)

    1. Na caixa **Identificador**, digite uma URL neste padrão:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Na caixa **URL de Resposta**, digite uma URL neste padrão:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Caso deseje configurar o aplicativo no modo iniciado por SP, selecione **Definir URLs adicionais** e realize as seguintes etapas.

    ![Informações de logon único de Domínio e URLs do Pega Systems](common/both-advanced-urls.png)

    1. Na caixa **URL de logon**, digite o valor da URL de logon.

    1. Na caixa **Estado de Retransmissão**, digite uma URL neste padrão: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Os valores fornecidos aqui são espaços reservados. Você precisa usar o identificador, a URL de resposta, a URL de logon único e a URL de estado de retransmissão reais. Você pode obter os valores do identificador e da URL de resposta de um aplicativo Pega, conforme explicado mais adiante neste tutorial. Para obter o valor do estado de retransmissão, entre em contato com a [equipe de suporte do Pega Systems](https://www.pega.com/contact-us). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. O aplicativo Pega Systems precisa que as declarações do SAML estejam em um formato específico. Para obtê-las no formato correto, você precisa adicionar mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra os atributos padrão. Selecione o ícone **Editar** para abrir a caixa de diálogo **Atributos do Usuário**:

    ![Atributos de usuário](common/edit-attribute.png)

7. Além dos atributos mostrados na captura de tela anterior, o aplicativo Pega Systems requer que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **Declarações do usuário** da caixa de diálogo **Atributos de usuário**, realize as seguintes etapas para adicionar esses atributos do token SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Esses valores são específicos para sua organização. Forneça os valores adequados.

    1. Selecione **Adicionar nova declaração** para abrir a caixa de diálogo **Gerenciar declarações do usuário**:

    ![Selecionar Adicionar nova declaração](common/new-save-attribute.png)

    ![Gerenciar a caixa de diálogo de declarações do usuário](common/new-attribute-details.png)

    1. Na caixa **Nome**, insira o nome do atributo mostrado para aquela linha.

    1. Deixe a caixa **Namespace** em branco.

    1. Em **Origem**, selecione **Atributo**.

    1. Na lista **Atributo de origem**, selecione o valor do atributo mostrado para essa linha.

    1. Selecione **OK**.

    1. Clique em **Salvar**.

8. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione o link **Baixar** ao lado do **XML de Metadados de Federação**, de acordo com suas necessidades e salve o certificado no computador:

    ![Link de download do certificado](common/metadataxml.png)

9. Na seção **Configurar Pega Systems**, copie as URLs adequadas, de acordo com suas necessidades.

    ![Copiar as URLs de configuração](common/copy-configuration-urls.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Pega Systems.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Pega Systems**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="configure-pega-systems-sso"></a>Configurar SSO do Pega Systems

1. Para configurar o logon único no lado do **Pega Systems**, entre no Portal do Pega com uma conta do administrador em outra janela do navegador.

2. Selecione **Criar** > **SysAdmin** > **Serviço de Autenticação**:

    ![Selecionar Serviço de Autenticação](./media/pegasystems-tutorial/admin.png)
    
3. Conclua as etapas a seguir na tela **Criar Serviço de Autenticação**.

    ![Tela Criar Serviço de Autenticação](./media/pegasystems-tutorial/admin1.png)

    1. Na lista **Tipo**, selecione **SAML 2.0**.

    1. Na caixa **Nome**, digite qualquer nome (por exemplo, **SSO do Azure AD**).

    1. Na caixa **Descrição resumida**, insira uma descrição.  

    1. Selecione **Criar e abrir**.
    
4. Na seção **Informações do IdP (Provedor de Identidade)** , selecione **Importar metadados do IdP** e procure o arquivo de metadados que você baixou do portal do Azure. Clique em **Enviar** para carregar os metadados:

    ![Seção de informações do IdP (Provedor de Identidade)](./media/pegasystems-tutorial/admin2.png)
    
    Essa importação preencherá os dados do IdP, conforme mostrado aqui:

    ![Dados do IdP importados](./media/pegasystems-tutorial/idp.png)
    
6. Execute as seguintes etapas na seção **Configurações do SP (Provedor de Serviço)** .

    ![Configurações do provedor de serviço](./media/pegasystems-tutorial/sp.png)

    1. Copie o valor de **Identificação da Entidade** e cole-o na caixa **Identificador** na seção **Configuração Básica de SAML** do portal do Azure.

    1. Copie o valor **Localização do ACS (Serviço do Consumidor de Declaração)** e cole-o na caixa **URL de Resposta** da seção **Configuração Básica de SAML** do portal do Azure.

    1. Selecione **Desabilitar assinatura de solicitação**.

7. Clique em **Salvar**.

### <a name="create-pega-systems-test-user"></a>Criar usuário de teste do Pega Systems

Em seguida, você precisará criar um usuário chamado Brenda Fernandes no Pega Systems. Trabalhar com a [equipe de suporte do Pega Systems](https://www.pega.com/contact-us) para criar usuários.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Pega Systems, em que você pode iniciar o fluxo de logon.  

* Acesse a URL de Logon do Pega Systems diretamente e inicie o fluxo de logon de lá.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Pega Systems para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Pega Systems em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao Pega Systems para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Pega Systems, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).