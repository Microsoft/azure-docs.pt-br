---
title: 'Tutorial: Integração do logon único do Azure Active Directory ao Citrix ADC (autenticação baseada em cabeçalho) | Microsoft Docs'
description: Saiba como configurar SSO (logon único) entre Azure Active Directory e o Citrix ADC usando a autenticação baseada em cabeçalho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 9cab0597aeb3bc28f391de558240e5d894f5a49c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735224"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-adc-header-based-authentication"></a>Tutorial: Integração do logon único do Azure Active Directory ao Citrix ADC (autenticação baseada em cabeçalho)

Neste tutorial, você aprenderá a integrar o Citrix ADC ao Azure AD (Azure Active Directory). Ao integrar o Citrix ADC ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Citrix ADC.
* Permitir que os usuários sejam conectados automaticamente ao Citrix ADC com as contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Citrix ADC habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. Este tutorial inclui os seguintes cenários:

* SSO iniciado por **SP** para o Citrix ADC

* Provisionamento de usuário **Just-In-Time** para o Citrix ADC

* [Autenticação baseada em cabeçalho para Citrix ADC](#publish-the-web-server)

* [Autenticação baseada em Kerberos para Citrix ADC](citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-adc-from-the-gallery"></a>Adicionar o Citrix ADC por meio da galeria

Para integrar o Citrix ADC ao Azure AD, primeiro adicione o Citrix ADC à sua lista de aplicativos SaaS gerenciados por meio da galeria:

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.

1. No menu esquerdo, selecione **Azure Active Directory**.

1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.

1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.

1. Na seção **Adicionar por meio da galeria**, digite **Citrix ADC** na caixa de pesquisa.

1. Nos resultados, selecione **Citrix ADC** e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-citrix-adc"></a>Configurar e testar o SSO do Azure AD para o Citrix ADC

Configure e teste o SSO do Azure AD com o Citrix ADC usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Citrix ADC.

Para configurar e testar o SSO do Azure AD com o Citrix ADC, execute as seguintes etapas:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.

    1. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) – para testar o SSO do Azure AD com B.Fernandes.

    1. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) – para permitir que B.Fernandes use o SSO do Azure AD.

1. [Configurar o SSO do Citrix ADC](#configure-citrix-adc-sso) – para definir as configurações de SSO no lado do aplicativo.

    * [Criar um usuário de teste do Citrix ADC](#create-a-citrix-adc-test-user) – para ter um equivalente de B.Fernandes no Citrix ADC que esteja vinculado à representação de usuário do Azure AD.

1. [Testar o SSO](#test-sso) – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para habilitar o SSO do Azure AD usando o portal do Azure, siga estas etapas:

1. No portal do Azure, no painel do aplicativo do **Citrix ADC**, em **Gerenciar**, selecione **Logon único**.

1. No painel **Selecionar um método de logon único**, selecione **SAML**.

1. No painel **Configurar o logon único com o SAML**, selecione o ícone de caneta **Editar** da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, para configurar o aplicativo no modo **iniciado por IDP**:

    1. Na caixa de texto **Identificador**, digite uma URL com o seguinte padrão: `https://<Your FQDN>`

    1. Na caixa de texto **URL de Resposta**, digite uma URL com o seguinte padrão: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Para configurar o aplicativo no modo **Iniciado por SP**, selecione **Definir URLs adicionais** e realize a seguinte etapa:

    * Na caixa de texto **URL de Entrada**, digite uma URL com o seguinte padrão: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * As URLs que são usadas nesta seção não são valores reais. Atualize esses valores com os valores reais do Identificador, da URL de Resposta e da URL de Logon. Contate a [equipe de suporte ao cliente do Citrix ADC](https://www.citrix.com/contact/technical-support.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.
    > * Para configurar o SSO, as URLs devem ser acessíveis de sites públicos. É necessário habilitar o firewall ou outras configurações de segurança do Citrix ADC para permitir que o Azure AD publique o token na URL configurada.

1. No painel **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, encontre a **URL de Metadados de Federação do Aplicativo**, copie a URL e salve-a no Bloco de notas.

    ![O link de download do Certificado](common/certificatebase64.png)

1. O aplicativo Citrix ADC espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Selecione o ícone **Editar** e altere os mapeamentos de atributo.

    ![Editar os mapeamentos de atributos SAML](common/edit-attribute.png)

1. O aplicativo Citrix ADC também espera que mais alguns atributos sejam transmitidos novamente na resposta SAML. Na caixa de diálogo **Atributos do Usuário**, em **Declarações do usuário**, realize as seguintes etapas para adicionar os atributos de token SAML conforme mostrado na tabela:

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. Selecione **Adicionar nova declaração** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    1. Na caixa de texto **Nome**, insira o nome do atributo mostrado para essa linha.

    1. Deixe o **Namespace** em branco.

    1. Para **Atributo**, selecione **Origem**.

    1. Na lista **Atributo de origem**, insira o valor do atributo mostrado para essa linha.

    1. Selecione **OK**.

    1. Selecione **Salvar**.

1. Na seção **Configurar o Citrix ADC**, copie as URLs relevantes de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No menu esquerdo no portal do Azure, selecione **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.

1. Selecione **Novo usuário** na parte superior do painel.

1. Nas propriedades do **Usuário**, conclua estas etapas:

   1. Para **Nome**, insira `B.Simon`.  

   1. Para **Nome de usuário**, insira _username@companydomain.extension_ . Por exemplo, `B.Simon@contoso.com`.

   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote ou copie o valor exibido em **Senha**.

   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o SSO do Azure concedendo-lhe acesso ao Citrix ADC.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.

1. Na lista de aplicativos, selecione **Citrix ADC**.

1. Na visão geral do aplicativo, em **Gerenciar**, selecione **Usuários e grupos**.
1. Selecione **Adicionar usuário**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista **Usuários**. Escolha **Selecionar**.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-citrix-adc-sso"></a>Configurar o SSO do Citrix ADC

Selecione um link para as etapas referentes ao tipo de autenticação que você deseja configurar:

- [Configurar o SSO do Citrix ADC para autenticação baseada em cabeçalho](#publish-the-web-server)

- [Configurar o SSO do Citrix ADC para autenticação baseada em Kerberos](citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Publicar o servidor Web 

Para criar um servidor virtual:

1. Selecione **Gerenciamento de Tráfego** > **Balanceamento de Carga** > **Serviços**.
    
1. Selecione **Adicionar**.

    ![Configuração do Citrix ADC – painel de Serviços](./media/header-citrix-netscaler-tutorial/web01.png)

1. Defina os seguintes valores para o servidor Web que está executando os aplicativos:

   * **Nome do Serviço**
   * **IP do servidor/servidor existente**
   * **Protocolo**
   * **Porta**

     ![Painel de configuração do Citrix ADC](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configure-the-load-balancer"></a>Configure o balanceador de carga

Para configurar o balanceador de carga:

1. Acesse **Gerenciamento de Tráfego** > **Balanceamento de Carga** > **Servidores Virtuais**.

1. Selecione **Adicionar**.

1. Defina os valores a seguir, conforme descrito na seguinte captura de tela:

    * **Nome**
    * **Protocolo**
    * **Endereço IP**
    * **Porta**

1. Selecione **OK**.

    ![Configuração do Cequence Citrix ADC – painel de configurações básicas](./media/header-citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Associar o servidor virtual

Para associar o balanceador de carga ao servidor virtual:

1. No painel **Serviços e Grupos de Serviços**, selecione **Nenhuma Associação de Serviço do Servidor Virtual de Balanceamento de Carga**.

   ![Configuração do Citrix ADC – painel Associação de Serviço do Servidor Virtual de Balanceamento de Carga](./media/header-citrix-netscaler-tutorial/bind01.png)

1. Verifique as configurações conforme mostrado na captura de tela a seguir e, em seguida, selecione **Fechar**.

   ![Configuração do Citrix ADC – Verificar a associação de serviço do servidor virtual de balanceamento de carga](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Associar o certificado

Para publicar esse serviço como TLS, associe o certificado do servidor e, em seguida, teste o aplicativo:

1. Em **Certificado**, selecione **Nenhum Certificado do Servidor**.

   ![Configuração do Citrix ADC – painel do Certificado do Servidor](./media/header-citrix-netscaler-tutorial/bind03.png)

1. Verifique as configurações conforme mostrado na captura de tela a seguir e, em seguida, selecione **Fechar**.

   ![Configuração do Citrix ADC – Verificar o certificado](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Perfil SAML do Citrix ADC

Para configurar o perfil SAML do Citrix ADC, conclua as seções a seguir:

### <a name="create-an-authentication-policy"></a>Criar uma política de autenticação

Para criar uma política de autenticação:

1. Acesse **Segurança** > **AAA: Tráfego de Aplicativo** > **Políticas** > **Autenticação** > **Políticas de Autenticação**.

1. Selecione **Adicionar**.

1. No painel **Criar Política de Autenticação**, insira ou selecione os seguintes valores:

    * **Name**: insira um nome para a política de autenticação.
    * **Ação**: insira **SAML** e selecione **Adicionar**.
    * **Expressão**:  insira **verdadeiro**.     
    
    ![Configuração do Citrix ADC – painel Criar Política de Autenticação](./media/header-citrix-netscaler-tutorial/policy01.png)

1. Selecione **Criar**.

### <a name="create-an-authentication-saml-server"></a>Criar um servidor SAML de autenticação

Para criar um servidor SAML de autenticação, acesse o painel **Criar Servidor SAML de Autenticação** e, em seguida, conclua as seguintes etapas:

1. Para **Nome**, insira um nome para o servidor SAML de autenticação.

1. Em **Exportar Metadados SAML**:

   1. Marque a caixa de seleção **Importar Metadados**.

   1. Insira a URL de metadados de federação da interface do usuário SAML do Azure que você copiou anteriormente.
    
1. Para **Nome do Emissor**, insira a URL relevante.

1. Selecione **Criar**.

![Configuração do Citrix ADC – painel Criar Servidor SAML de Autenticação](./media/header-citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Criar um servidor virtual de autenticação

Para criar um servidor virtual de autenticação:

1.  Acesse **Segurança** > **AAA: Tráfego de Aplicativo** > **Políticas** > **Autenticação** > **Servidores Virtuais de Autenticação**.

1.  Selecione **Adicionar** e conclua as etapas a seguir:

    1. Para **Nome**, insira um nome para o servidor virtual de autenticação.

    1. Marque a caixa de seleção **Não endereçável**.

    1. Em **Protocolo**, selecione **SSL**.

    1. Selecione **OK**.

    ![Configuração do Citrix ADC – painel do Servidor Virtual de Autenticação](./media/header-citrix-netscaler-tutorial/server02.png)
    
### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Configurar o servidor virtual de autenticação para usar o Azure AD

Modifique duas seções para o servidor virtual de autenticação:

1.  No painel **Políticas de Autenticação Avançadas**, selecione **Nenhuma Política de Autenticação**.

    ![Configuração do Citrix ADC – painel Políticas de Autenticação Avançadas](./media/header-citrix-netscaler-tutorial/virtual01.png)

1. No painel **Associação de Política**, selecione a política de autenticação e, em seguida, selecione **Associar**.

    ![Configuração do Citrix ADC – painel Associação de Política](./media/header-citrix-netscaler-tutorial/virtual02.png)

1. No painel **Servidores Virtuais Baseados em Formulário**, selecione **Nenhum Servidor Virtual de Balanceamento de Carga**.

    ![Configuração do Citrix ADC – painel Servidores Virtuais Baseados em Formulário](./media/header-citrix-netscaler-tutorial/virtual03.png)

1. Para **FQDN de Autenticação**, insira um FQDN (nome de domínio totalmente qualificado) (obrigatório).

1. Selecione o servidor virtual de balanceamento de carga que deseja proteger com a Autenticação do Azure AD.

1. Selecione **Associar**.

    ![Configuração do Citrix ADC – painel Associação do Servidor Virtual de Balanceamento de Carga](./media/header-citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Selecione **Concluído** no painel **Configuração do Servidor Virtual de Autenticação**.

1. Para verificar as alterações, em um navegador, vá para a URL do aplicativo. Você deve ver sua página de entrada do locatário em vez do acesso não autenticado que você viu anteriormente.

    ![Configuração do Citrix ADC – uma página de entrada em um navegador da Web](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-adc-sso-for-header-based-authentication"></a>Configurar o SSO do Citrix ADC para autenticação baseada em cabeçalho

### <a name="configure-citrix-adc"></a>Configurar o Citrix ADC

Para configurar o Citrix ADC para autenticação baseada em cabeçalho, conclua as seções a seguir.

#### <a name="create-a-rewrite-action"></a>Criar uma ação de reescrita

1. Acesse **AppExpert** > **Reescrita** > **Ações de Reescrita**.
 
    ![Configuração do Citrix ADC – painel Ações de Reescrita](./media/header-citrix-netscaler-tutorial/header01.png)

1.  Selecione **Adicionar** e conclua as etapas a seguir:

    1. Para **Nome**, insira um nome para a ação de reescrita.

    1. Para **Tipo**, insira **INSERT_HTTP_HEADER**.

    1. Para **Nome do Cabeçalho**, insira um nome de cabeçalho (neste exemplo, usamos _SecretID_).

    1. Para **Expressão**, insira **aaa.USER.ATTRIBUTE("mySecretID")** , em que **mySecretID** é a declaração SAML do Azure AD enviada ao Citrix ADC.

    1. Selecione **Criar**.

    ![Configuração do Citrix ADC – painel Criar Ações de Reescrita](./media/header-citrix-netscaler-tutorial/header02.png)
 
#### <a name="create-a-rewrite-policy"></a>Crie uma política de reescrita

1.  Acesse **AppExpert** > **Reescrita** > **Políticas de Reescrita**.
 
    ![Configuração do Citrix ADC – painel Políticas de Reescrita](./media/header-citrix-netscaler-tutorial/header03.png)

1.  Selecione **Adicionar** e conclua as etapas a seguir:

    1. Para **Nome**, insira um nome para a política de reescrita.

    1. Para **Ação**, selecione a ação de reescrita criada na seção anterior.

    1. Para **Expressão**, digite **true**.

    1. Selecione **Criar**.

    ![Configuração do Citrix ADC – painel Criar Política de Reescrita](./media/header-citrix-netscaler-tutorial/header04.png)

### <a name="bind-a-rewrite-policy-to-a-virtual-server"></a>Associar a política de reescrita a um servidor virtual

Para associar uma política de reescrita a um servidor virtual usando a GUI:

1. Acesse **Gerenciamento de Tráfego** > **Balanceamento de Carga** > **Servidores Virtuais**.

1. Na lista de servidores virtuais, selecione o servidor virtual ao qual deseja associar a política de reescrita e, em seguida, seleciona **Abrir**.

1. No painel **Servidor Virtual de Balanceamento de Carga**, em **Configurações Avançadas**, selecione **Políticas**. Todas as políticas que são configuradas para sua instância do NetScaler aparecem na lista.
 
    ![Captura de tela que mostra a guia "Configuração" com os campos "Nome", "Ação" e "Expressão" realçados e o botão "Criar" selecionado.](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Configuração do Citrix ADC – painel Servidor Virtual de Balanceamento de Carga](./media/header-citrix-netscaler-tutorial/header06.png)

1.  Marque a caixa de seleção ao lado do nome da política que deseja associar a esse servidor virtual.
 
    ![Configuração do Citrix ADC – painel Associação da Política de Tráfego do Servidor Virtual de Balanceamento de Carga](./media/header-citrix-netscaler-tutorial/header08.png)

1. Na caixa de diálogo **Escolher Tipo**:

    1. Em **Escolher Política**, selecione **Tráfego**.

    1. Em **Escolher Tipo**, selecione **Solicitação**.

    ![Configuração do Citrix ADC – caixa de diálogo Políticas](./media/header-citrix-netscaler-tutorial/header07.png)

1.  Selecione **OK**. Uma mensagem na barra de status indica que a política foi configurada com êxito.

### <a name="modify-the-saml-server-to-extract-attributes-from-a-claim"></a>Modificar o servidor SAML para extrair atributos de uma declaração

1.  Acesse **Segurança** > **AAA: Tráfego de Aplicativo** > **Políticas** > **Autenticação** > **Políticas Avançadas** > **Ações** > **Servidores**.

1.  Selecione o Servidor SAML de Autenticação apropriado para o aplicativo.
 
    ![Configuração do Citrix ADC – painel Configurar Servidor SAML de Autenticação](./media/header-citrix-netscaler-tutorial/header09.png)

1. No painel **Atributos**, insira os atributos SAML que você deseja extrair, separados por vírgulas. Em nosso exemplo, inserimos o atributo `mySecretID`.
 
    ![Configuração do Citrix ADC – painel de Atributos](./media/header-citrix-netscaler-tutorial/header10.png)

1. Para verificar o acesso, na URL em um navegador, procure o atributo SAML em **Coleção de Cabeçalhos**.

    ![Configuração do Citrix ADC – Coleção de Cabeçalhos na URL](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-a-citrix-adc-test-user"></a>Criar um usuário de teste do Citrix ADC

Nesta seção, um usuário de nome B.Fernandes é criado no Citrix ADC. O Citrix ADC é compatível com o provisionamento de usuários Just-In-Time, que está habilitado por padrão. Não há nenhuma ação para você executar nesta seção. Se um usuário ainda não existir no Citrix ADC, um será criado após a autenticação.

> [!NOTE]
> Se for necessário criar um usuário manualmente, contate a [equipe de suporte ao Cliente do Citrix ADC](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado para a URL de Entrada do Citrix ADC, local em que poderá iniciar o fluxo de logon. 

* Acesse a URL de Entrada do Citrix ADC diretamente e inicie o fluxo de logon nesse local.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Citrix ADC em Meus Aplicativos, isso redirecionará você à URL de Logon do Citrix ADC. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Citrix ADC, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).