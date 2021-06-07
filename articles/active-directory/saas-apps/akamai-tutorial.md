---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Akamai | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Akamai.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: a22a214104357b9ad99238a8db157839a1c9fd46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591938"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Akamai

Neste tutorial, você aprenderá a integrar o Akamai ao Azure AD (Azure Active Directory). Quando integrar o Akamai ao Azure AD, será possível:

* Controlar no Azure AD quem tem acesso ao Akamai.
* Permitir que seus usuários entrem automaticamente no Akamai com as contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

A integração de acesso a aplicativos empresariais do Azure Active Directory e do Akamai permite acesso contínuo a aplicativos herdados hospedados na nuvem ou localmente. A solução integrada aproveita as vantagens de todos os recursos modernos do Azure Active Directory, como [Acesso condicional do Azure AD](../conditional-access/overview.md), [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) e [Azure AD Identity Governance](../governance/identity-governance-overview.md) para acesso de aplicativos herdados sem modificações de aplicativo ou instalação de agentes.

A imagem abaixo descreve como o EAA do Akamai se encaixa no cenário de Acesso Seguro Híbrido mais amplo.

![O EAA do Akamai se encaixa no cenário de Acesso Seguro Híbrido mais amplo](./media/header-akamai-tutorial/introduction-1.png)

### <a name="key-authentication-scenarios"></a>Cenários de autenticação de chaves

Além do suporte à integração nativa do Azure Active Directory para protocolos de autenticação modernos, como Open ID Connect, SAML e WS-Fed, o EAA do Akamai estende o acesso seguro para aplicativos de autenticação herdados para acesso interno e externo com o Azure AD, habilitando cenários modernos (por exemplo, acesso sem senha) a esses aplicativos. Isso inclui:

* Aplicativos de autenticação baseada em cabeçalho
* Área de Trabalho Remota
* SSH (Secure Shell)
* Aplicativos de autenticação Kerberos
* VNC (Computação de Rede Virtual)
* Autenticação anônima ou nenhum aplicativo de autenticação interno
* Aplicativos de autenticação NTLM (proteção com prompts duplos para o usuário)
* Aplicativo baseado em formulários (proteção com prompts duplos para o usuário)

### <a name="integration-scenarios"></a>Cenários de integração

A parceria entre a Microsoft e o EAA do Akamai permite a flexibilidade de atender aos seus requisitos de negócios, dando suporte a vários cenários de integração com base em seu requisito de negócios. Isso pode ser usado para fornecer cobertura, desde o início, para todos os aplicativos, bem como classificar e configurar gradualmente as classificações de política apropriadas.

#### <a name="integration-scenario-1"></a>Cenário de integração 1

O EAA do Akamai é configurado como um único aplicativo no Azure AD. O administrador pode configurar a política de Acesso Condicional no aplicativo e, depois que as condições forem satisfeitas, os usuários poderão obter acesso ao portal do EAA do Akamai.

**Prós**:

* você só precisa configurar o IDP uma vez.

**Contras**:

* os usuários acabam tendo dois portais de aplicativos.

* Cobertura da política de Acesso Condicional única comum para todos os aplicativos.

![Cenário de integração 1](./media/header-akamai-tutorial/scenario-1.png)

#### <a name="integration-scenario-2"></a>Cenário de integração 2

O aplicativo EAA do Akamai é configurado individualmente no portal do Azure AD. O administrador pode configurar a política de Acesso Condicional individual nos aplicativos e, depois que as condições são atendidas, os usuários poderão ser redirecionados diretamente para o aplicativo específico.

**Prós**:

* você pode definir Políticas de AC individuais.

* Todos os aplicativos são representados no 0365 Waffle e no painel myApps.microsoft.com.


**Contras**:

* Você precisa configurar vários IDPs.

![Cenário de integração 2](./media/header-akamai-tutorial/scenario-2.png)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Akamai habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

- O Akamai dá suporte ao SSO iniciado por IDP.

#### <a name="important"></a>Importante

Todas as configurações listadas abaixo são as mesmas para o **Cenário de integração 1** e para o **Cenário 2**. Para o **Cenário de integração 2**, você precisa configurar o IDP individual no EAA do Akamai e a propriedade de URL precisará ser modificada para apontar para a URL do aplicativo.

![Captura de tela da guia Geral do AZURESSO-SP no Akamai Enterprise Application Access. O campo URL de configuração de autenticação é realçado.](./media/header-akamai-tutorial/important.png)

## <a name="add-akamai-from-the-gallery"></a>Adicionar o Akamai por meio da galeria

Para configurar a integração do Akamai ao Azure AD, você precisa adicioná-lo por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Akamai** na caixa de pesquisa.
1. Selecione **Akamai** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-akamai"></a>Configurar e testar o SSO do Azure AD para o Akamai

Configure e teste o SSO do Azure AD com o Akamai usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Akamai.

Para configurar e testar o SSO do Azure AD com o Akamai, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Akamai](#configure-akamai-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Como configurar o IDP](#setting-up-idp)**
    * **[Autenticação baseada em cabeçalho](#header-based-authentication)**
    * **[Área de trabalho remota](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Autenticação Kerberos](#kerberos-authentication)**
    * **[Criar um usuário de teste do Akamai](#create-akamai-test-user)** – para ter um equivalente de B. Fernandes no Akamai que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Akamai**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao Cliente do Akamai](https://www.akamai.com/us/en/contact-us/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Akamai**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao Akamai.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Akamai**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-akamai-sso"></a>Configurar o SSO do Akamai

### <a name="setting-up-idp"></a>Configurando IDP

**Configuração de IDP do EAA do AKAMAI**

1. Entre no console de **Acesso ao aplicativo empresarial do Akamai**.
1. No **Console EAA do Akamai**, selecione **Identidade** > **Provedores de Identidade** e clique em **Adicionar Provedor de Identidade**.

    ![Captura de tela da janela Provedores de Identidade do console do Akamai EAA. Selecione provedores de identidade no menu Identidade e selecione Adicionar Provedor de Identidade.](./media/header-akamai-tutorial/configure-1.png)

1. Em **Criar Provedor de Identidade**, execute as seguintes etapas:

    ![Captura de tela da caixa de diálogo Criar Provedores de Identidade no console do Akamai EAA.](./media/header-akamai-tutorial/configure-2.png)

    a. Especifique o **Nome Exclusivo**.

    b. Escolha **SAML de Terceiros** e clique em **Criar Provedor de Identidade e Configurar**.

### <a name="general-settings"></a>Configurações gerais

1. **Interceptação de identidade** – especifique o nome do (URL base do SP – será usado para a configuração do Azure AD).

    > [!NOTE]
    > Você pode optar por ter seu próprio domínio personalizado (exigirá uma entrada DNS e um Certificado). Neste exemplo, vamos usar o domínio do Akamai.

1. **Zona de nuvem do Akamai** – selecione a zona de nuvem apropriada.
1. **Validação de certificado** – verifique a documentação do Akamai (opcional).

    ![Captura de tela da guia Geral do console do Akamai EAA mostrando as configurações para Interceptação de Identidade, Zona da Nuvem do Akamai e Validação de Certificado.](./media/header-akamai-tutorial/configure-3.png)

### <a name="authentication-configuration"></a>Configuração de autenticação

1. URL – especifique a URL igual à sua interceptação de identidade (é aqui que os usuários são redirecionados após a autenticação).
2. URL de Logoff: Atualize a URL de logoff.
3. Assinar solicitação SAML: padrão desmarcado.
4. Para o arquivo de metadados de IDP, adicione o aplicativo no console do Azure AD.

    ![Captura de tela da configuração de Autenticação do console do Akamai EAA mostrando as configurações de URL, URL de Logout, Solicitação SAML de Assinatura e Arquivo de Metadados do IDP.](./media/header-akamai-tutorial/configure-4.png)

### <a name="session-settings"></a>Configurações da sessão

Deixe as configurações como padrão.

![Captura de tela da caixa de diálogo de Configurações da sessão do console do Akamai EAA.](./media/header-akamai-tutorial/session-settings.png)

### <a name="directories"></a>Diretórios

Ignore a configuração de diretório.

![Captura de tela da guia Diretórios do console do Akamai EAA.](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>UI de personalização

Você pode adicionar a personalização ao IDP.

![Captura de tela da guia Personalização do console do Akamai EAA mostrando as configurações para Personalizar a Interface do Usuário, Configurações de Idioma e Temas.](./media/header-akamai-tutorial/customization.png)

### <a name="advanced-settings"></a>Configurações avançadas

Ignore as configurações avançadas/consulte a documentação do Akamai para obter mais detalhes.

![Captura de tela da guia Configurações Avançadas do console do Akamai EAA mostrando as configurações do EAA Client, Advanced e OIDC para ponte SAML.](./media/header-akamai-tutorial/advance-settings.png)

### <a name="deployment"></a>Implantação

1. Clique em Implantar Provedor de Identidade.

    ![Captura de tela da guia Implantação do console do Akamai EAA mostrando o botão Implantar provedor de identidade.](./media/header-akamai-tutorial/deployment.png)

2. Verifique se a implantação foi bem-sucedida.

### <a name="header-based-authentication"></a>Autenticação baseada em cabeçalho

Autenticação baseada em cabeçalho do Akamai

1. Escolha **HTTP personalizado** para o Assistente para adicionar aplicativos.

    ![Captura de tela do assistente para Adicionar aplicativos do console do Akamai EAA mostrando CustomHTTP listados na seção Acessar Aplicativos.](./media/header-akamai-tutorial/configure-5.png)

2. Insira o **Nome do Aplicativo** e a **Descrição**.

    ![Captura de tela de uma caixa de diálogo de Aplicativo HTTP Personalizado mostrando as configurações de Nome e Descrição do Aplicativo.](./media/header-akamai-tutorial/configure-6.png)

    ![Captura de tela da guia Geral do console do Akamai EAA mostrando configurações gerais para MYHEADERAPP.](./media/header-akamai-tutorial/configure-7.png)

    ![Captura de tela do console do Akamai EAA mostrando as configurações para Certificado e Localização.](./media/header-akamai-tutorial/configure-8.png)

#### <a name="authentication"></a>Autenticação

1. Selecione a guia **Autenticação**.

    ![Captura de tela do console do Akamai EAA com a guia Autenticação selecionada.](./media/header-akamai-tutorial/configure-9.png)

2. Atribua o **Provedor de identidade**.

    ![Captura de tela da guia Autenticação do console do Akamai EAA para MYHEADERAPP mostrando o provedor de identidade definido como SSO do Azure AD.](./media/header-akamai-tutorial/configure-10.png)

#### <a name="services"></a>Serviços

Clique em Salvar e Ir para Autenticação.

![Captura de tela da guia Serviços do console Akamai EAA para MYHEADERAPP mostrando o botão Salvar e ir para AdvancedSettings no canto inferior direito.](./media/header-akamai-tutorial/configure-11.png)

#### <a name="advanced-settings"></a>Configurações avançadas

1. Em **Cabeçalhos HTTP do Cliente**, especifique **CustomerHeader** e **Atributo SAML**.

    ![Captura de tela da guia Configurações Avançadas do console do Akamai EAA mostrando o campo URL de logon do SSO realçado em Autenticação.](./media/header-akamai-tutorial/configure-12.png)

1. Clique no botão **Salvar e ir para Implantação**.

    ![Captura de tela da guia Configurações Avançadas do console do Akamai EAA mostrando o botão Salvar e ir para Implantação no canto inferior direito.](./media/header-akamai-tutorial/configure-13.png)

#### <a name="deploy-the-application"></a>Implantar o aplicativo

1. Clique no botão **Implantar Aplicativo**.

    ![Captura de tela da guia Implantação do console do Akamai EAA mostrando o botão Implantar aplicativo.](./media/header-akamai-tutorial/configure-14.png)

1. Verifique se o aplicativo foi implantado com êxito.

    ![Captura de tela da guia Implantação do console Akamai EAA mostrando a mensagem de status do aplicativo: "Aplicativo implantado com êxito".](./media/header-akamai-tutorial/configure-15.png)

1. Experiência do usuário final.

    ![Captura da tela com a tela de abertura para myapps.microsoft.com com uma imagem em segundo plano e uma caixa de diálogo de Entrar.](./media/header-akamai-tutorial/end-user-1.png)

    ![Captura de tela mostrando parte de uma janela Aplicativos com ícones para Suplemento, HRWEB, Akamai-CorpApps, Despesas, Grupos e Análises de acesso. ](./media/header-akamai-tutorial/end-user-2.png)

1. Acesso Condicional.

    ![Captura de tela da mensagem: Aprovar solicitação de entrada. Enviamos uma notificação para o seu dispositivo móvel. Responda para continuar.](./media/header-akamai-tutorial/conditional-access-1.png)

    ![Instantâneo de uma tela de aplicativos mostrando um ícone para o MyHeaderApp.](./media/header-akamai-tutorial/conditional-access-2.png)

#### <a name="remote-desktop"></a>Área de Trabalho Remota

1. Escolha **RDP** no Assistente para adicionar aplicativos.

    ![Captura de tela do assistente para Adicionar aplicativos do console do Akamai EAA mostrando RDP listado entre os aplicativos na seção Acessar Aplicativos.](./media/header-akamai-tutorial/configure-16.png)

1. Insira o **Nome do Aplicativo** e a **Descrição**.

    ![Captura de tela de uma caixa de diálogo de Aplicativo RDP Personalizado mostrando as configurações de Nome e Descrição do Aplicativo.](./media/header-akamai-tutorial/configure-17.png)

    ![Captura de tela da guia Geral do console do Akamai EAA mostrando as configurações de Identidade do aplicativo para SECRETRDPAPP.](./media/header-akamai-tutorial/configure-18.png)

1. Especifique o Conector que atenderá a ele.

    ![Captura de tela do console do Akamai EAA mostrando as configurações para Certificado e Localização. Os conectores associados são definidos como USWST-CON1.](./media/header-akamai-tutorial/configure-19.png)

#### <a name="authentication"></a>Autenticação

Clique em **Salvar e ir para Serviços**.

![Captura de tela da guia de autenticação do console do Akamai EAA para SECRETRDPAPP mostrando o botão Salvar e ir para Serviços no canto inferior direito.](./media/header-akamai-tutorial/configure-20.png)

#### <a name="services"></a>Serviços

Clique em **Salvar e ir para Configurações Avançadas**.

![Captura de tela da guia Serviços do console Akamai EAA para SECRETRDPAPP mostrando o botão Salvar e ir para AdvancedSettings no canto inferior direito.](./media/header-akamai-tutorial/configure-21.png)

#### <a name="advanced-settings"></a>Configurações avançadas

1. Clique em **Salvar e ir para Implantação**.

    ![Captura de tela da guia Configurações Avançadas do console do Akamai EAA para SECRETRDPAPP mostrando as definições para a configuração da Área de Trabalho Remota.](./media/header-akamai-tutorial/configure-22.png)

    ![Captura de tela da guia Configurações Avançadas do console do Akamai EAA para SECRETRDPAPP mostrando as definições para a configuração de Autenticação e Verificação de integridade.](./media/header-akamai-tutorial/configure-23.png)

    ![Captura de tela de configurações de cabeçalhos HTTP Personalizados do console do Akamai EAA para SECRETRDPAPP com o botão Salvar e ir para Implantação no canto inferior direito.](./media/header-akamai-tutorial/configure-24.png)

1. Experiência do usuário final

    ![Captura de tela de uma janela myapps.microsoft.com com uma imagem de tela de fundo e uma caixa de diálogo de Entrar.](./media/header-akamai-tutorial/end-user-3.png)

    ![Captura de tela da janela Aplicativos do myapps.microsoft.com com ícones para Suplemento, HRWEB, Akamai-CorpApps, Despesas, Grupos e Análises de acesso.](./media/header-akamai-tutorial/end-user-2.png)

1. Acesso condicional

    ![Captura de tela da mensagem de acesso condicional: Aprovar solicitação de entrada. Enviamos uma notificação para o seu dispositivo móvel. Responda para continuar.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Instantâneo de uma tela Aplicativos mostrando ícones para MyHeaderApp e SecretRDPApp.](./media/header-akamai-tutorial/conditional-access-5.png)

    ![Captura de tela do Windows Server 2012 RS mostrando ícones de usuário genéricos. Os ícones para administrador, user0 e user1 mostram que eles estão Conectados.](./media/header-akamai-tutorial/conditional-access-6.png)

1. Como alternativa, você também pode digitar diretamente a URL do aplicativo RDP.

#### <a name="ssh"></a>SSH

1. Vá para Adicionar Aplicativos e escolha **SSH**.

    ![Captura de tela do assistente para Adicionar aplicativos do console do Akamai EAA mostrando SSH listado entre os aplicativos na seção Acessar Aplicativos.](./media/header-akamai-tutorial/configure-25.png)

1. Insira o **Nome do Aplicativo** e a **Descrição**.

    ![Captura de tela de uma caixa de diálogo de Aplicativo SSH Personalizado mostrando as configurações de Nome e Descrição do Aplicativo.](./media/header-akamai-tutorial/configure-26.png)

1. Configurar a identidade do aplicativo.

    ![Captura de tela da guia Geral do console do Akamai EAA mostrando as configurações de Identidade do aplicativo para SSH-SECURE.](./media/header-akamai-tutorial/configure-27.png)

    a. Especifique nome/descrição.

    b. Especifique o IP/FQDN do servidor de aplicativos e a porta para SSH.

    c. Especifique o nome de usuário/senha de SSH *Verifique a EAA do Akamai.

    d. Especifique o nome do host externo.

    e. Especifique o local para o conector e escolha o conector.

#### <a name="authentication"></a>Autenticação

Clique em **Salvar e ir para Serviços**.

![Captura de tela da guia de autenticação do console do Akamai EAA para SSH-SECURE mostrando o botão Salvar e ir para Serviços no canto inferior direito.](./media/header-akamai-tutorial/configure-28.png)

#### <a name="services"></a>Serviços

Clique em **Salvar e ir para Configurações Avançadas**.

![Captura de tela da guia Serviços do console Akamai EAA para SSH-SECURE mostrando o botão Salvar e ir para AdvancedSettings no canto inferior direito.](./media/header-akamai-tutorial/configure-29.png)

#### <a name="advanced-settings"></a>Configurações avançadas

Clique em Salvar e vá para a Implantação.

![Captura de tela da guia Configurações Avançadas do console do Akamai EAA para SSH-SECURE mostrando as definições para a configuração de Autenticação e Verificação de integridade.](./media/header-akamai-tutorial/configure-30.png)

![Captura de tela de configurações de cabeçalhos HTTP Personalizados do console do Akamai EAA para SSH-SECURE com o botão Salvar e ir para Implantação no canto inferior direito.](./media/header-akamai-tutorial/configure-31.png)

#### <a name="deployment"></a>Implantação

1. Clique em **Implantar aplicativo**.

    ![Captura de tela da guia Implantação do console do Akamai EAA para SSH-SECURE mostrando o botão Implantar aplicativo.](./media/header-akamai-tutorial/configure-32.png)

1. Experiência do usuário final

    ![Captura de tela de uma caixa de diálogo de Entrar da janela do myapps.microsoft.com.](./media/header-akamai-tutorial/end-user-3.png)

    ![Captura de tela da janela Aplicativos para myapps.microsoft.com mostrando ícones para Suplemento, HRWEB, Akamai-CorpApps, Despesas, Grupos e Análises de acesso.](./media/header-akamai-tutorial/end-user-4.png)

1. Acesso condicional

    ![Captura de tela mostrando a mensagem: Aprovar solicitação de entrada. Enviamos uma notificação para o seu dispositivo móvel. Responda para continuar.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Captura de tela com a tela Aplicativos mostrando ícones para MyHeaderApp, SSH Secure e SecretRDPApp.](./media/header-akamai-tutorial/conditional-access-7.png)

    ![Captura de tela de uma janela Comando para ssh-secure-go.akamai-access.com mostrando um prompt de Senha.](./media/header-akamai-tutorial/conditional-access-8.png)

    ![Captura de tela de uma janela Comando para ssh-secure-go.akamai-access.com mostrando informações sobre o aplicativo e exibindo um prompt para comandos.](./media/header-akamai-tutorial/conditional-access-9.png)

### <a name="kerberos-authentication"></a>Autenticação Kerberos

No exemplo abaixo, publicaremos um servidor Web interno <code>http://frp-app1.superdemo.live</code> e habilitaremos o SSO usando o KCD.

#### <a name="general-tab"></a>Guia Geral

![Captura de tela da guia Geral do console do Akamai EAA para MYKERBOROSAPP.](./media/header-akamai-tutorial/general-tab.png)

#### <a name="authentication-tab"></a>Guia Autenticação

Atribua o Provedor de Identidade.

![Captura de tela da guia Autenticação do console do Akamai EAA para MYKERBOROSAPP mostrando o provedor de identidade definido como SSO do Azure AD.](./media/header-akamai-tutorial/authentication-tab.png)

#### <a name="services-tab"></a>Guia Serviços

![Captura de tela da guia Serviços do console do Akamai EAA para MYKERBOROSAPP.](./media/header-akamai-tutorial/services-tab.png)

#### <a name="advanced-settings"></a>Configurações avançadas

![Captura de tela da guia Configurações Avançadas do console do Akamai EAA para MYKERBOROSAPP mostrando as configurações para Aplicativos Relacionados e Autenticação.](./media/header-akamai-tutorial/advance-settings-2.png)

> [!NOTE]
> O SPN para o servidor Web deve estar no formato SPN@Domain, por exemplo, `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` para esta demonstração. Deixe o restante das configurações como padrão.

#### <a name="deployment-tab"></a>Guia Implantação

![Captura de tela da guia Implantação do console do Akamai EAA para MYKERBOROSAPP mostrando o botão Implantar aplicativo.](./media/header-akamai-tutorial/deployment-tab.png)

#### <a name="adding-directory"></a>Adicionando diretório

1. Selecione **AD** no menu suspenso.

    ![Captura de tela da janela Diretórios do console do Akamai EAA mostrando uma caixa de diálogo Criar Diretório com o AD selecionado na lista suspensa para Tipo de Diretório.](./media/header-akamai-tutorial/configure-33.png)

1. Forneça os dados necessários.

    ![Captura de tela da janela SUPERDEMOLIVE do console do Akamai EAA com configurações para DirectoryName, Serviço de Diretório, Conector e Mapeamento de Atributo.](./media/header-akamai-tutorial/configure-34.png)

1. Verifique a criação do diretório.

    ![Captura de tela da janela Diretórios do console do Akamai EAA mostrando que o diretório superdemo.live foi adicionado.](./media/header-akamai-tutorial/directory-domain.png)

1. Adicione os grupos/UOs que precisariam de acesso.

    ![Captura de tela das configurações do diretório superdemo.live. O ícone selecionado para adicionar Grupos ou UOs é realçado.](./media/header-akamai-tutorial/add-group.png)

1. Abaixo, o grupo é chamado de EAAGroup e tem um membro.

    ![Captura de tela da janela GROUPS ON SUPERDEMOLIVE DIRECTORY do console do Akamai EAA. O EAAGroup com um usuário é listado em Grupos.](./media/header-akamai-tutorial/eaagroup.png)

1. Adicione o diretório ao provedor de identidade clicando em **Identidade** > **Provedores de Identidade** e clique na guia **Diretórios** e em **Atribuir diretório**.

    ![Captura de tela da guia Diretórios do console do Akamai EAA para o SSO do Azure AD, mostrando superdemo.live na lista de Diretórios atribuídos no momento.](./media/header-akamai-tutorial/assign-directory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>Passo a passo da configuração da delegação de KCD para EAA

#### <a name="step-1-create-an-account"></a>Etapa 1: Criar uma conta 

1. No exemplo, usaremos uma conta chamada **EAADelegation**. Execute isso usando o snap-in **Usuários e computadores do Active Directory**.

    ![Captura de tela da guia Diretórios do console do Akamai EAA para o SSO do Azure AD. O diretório superdemo.live é listado em Diretórios atualmente atribuídos.](./media/header-akamai-tutorial/assign-directory.png)

    > [!NOTE]
    > O nome de usuário deve estar em um formato específico com base no **Nome da Interceptação de Identidade**. Na Figura 1, vemos que é **corpapps.login.go.akamai-access.com**

1. O nome de logon do usuário será:`HTTP/corpapps.login.go.akamai-access.com`

    ![Captura de tela mostrando Propriedades EAADelegation com o Nome definido como "EAADelegation" e Nome de logon do usuário definido como HTTP/corpapps.login.go.akamai-access.com.](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>Etapa 2: configurar o SPN para essa conta

1. Com base neste exemplo, o SPN será o seguinte.

2. setspn -s **Http/corpapps.login.go.akamai-access.com eaadelegation**

    ![Captura de tela de um prompt de comando de administrador mostrando os resultados do comando setspn -s Http/corpapps.login.go.akamai-access.com eaadelegation.](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>Etapa 3: configurar a delegação

1. Para a conta do EAADelegation, clique na guia Delegação.

    ![Captura de tela de um prompt de comando de administrador mostrando o comando para configurar o SPN.](./media/header-akamai-tutorial/spn.png)

    * Especifique o uso de qualquer protocolo de autenticação.
    * Clique em Adicionar e Adicionar Conta do Pool de Aplicativos ao site do Kerberos. Isso deve ser resolvido automaticamente para o SPN correto, se configurado corretamente.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>Etapa 4: criar um arquivo Keytab para o EAA do AKAMAI

1. Aqui está a sintaxe genérica.

1. ktpass /out ActiveDirectorydomain.keytab  /princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /pass +rdnPass  /crypto All /ptype KRB5_NT_PRINCIPAL

1. Exemplo explicado

    | Snippet | Explicação |
    | - | - |
    | Ktpass /out EAADemo.keytab | // O nome do arquivo Keytab de saída |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | // Conta de delegação do EAA |
    | /pass RANDOMPASS | // Conta de delegação do EAA Senha |
    | /crypto All ptype KRB5_NT_PRINCIPAL | // Documentação do EAA do Akamai de consultoria |
    | | |

1. Ktpass /out EAADemo.keytab  /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS /crypto All ptype KRB5_NT_PRINCIPAL

    ![Captura de tela de um prompt de comando de administrador mostrando os resultados do comando para criar um arquivo keytab para o AKAMAI EAA.](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>Etapa 5: importar Keytab no console do EAA do AKAMAI

1. Clique em **Sistema** > **Keytabs**.

    ![Captura de tela do console do Akamai EAA mostrando Keytabs que estão sendo selecionadas no menu de Sistema.](./media/header-akamai-tutorial/keytabs.png)

1. Em Tipo de Keytab, escolha **Delegação Kerberos**.

    ![Captura de tela com a tela EAAKEYTAB do console do Akamai EAA mostrando as configurações de keytab. O Tipo de Keytab é definido como Delegação do Kerberos.](./media/header-akamai-tutorial/keytab-delegation.png)

1. Verifique se Keytab aparece como Implantado e Verificado.

    ![Captura de tela com a tela KEYTABS do console do Akamai EAA listando a Keytab do EAA como "Keytab implantada e verificada".](./media/header-akamai-tutorial/keytabs-2.png)

1. Experiência de usuário

    ![Captura de tela da caixa de diálogo de Entrada em myapps.microsoft.com. ](./media/header-akamai-tutorial/end-user-3.png)

    ![Captura de tela da janela Aplicativos para myapps.microsoft.com mostrando ícones de aplicativo.](./media/header-akamai-tutorial/end-user-4.png)

1. Acesso condicional

    ![Captura de tela mostrando uma mensagem Aprovar solicitação de entrada. a mensagem.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Captura de tela com a tela Aplicativos mostrando ícones para MyHeaderApp, SSH Secure, SecretRDPApp e myKerberosApp.](./media/header-akamai-tutorial/conditional-access-10.png)

    ![Captura da tela inicial do myKerberosApp. A mensagem "Bem-vindo, superdemo\user1" é exibida em uma imagem em segundo plano.](./media/header-akamai-tutorial/conditional-access-11.png)

### <a name="create-akamai-test-user"></a>Criar usuário de teste do Akamai

Nesta seção, você criará um usuário chamado B.Fernandes no Akamai. Trabalhe com a [equipe de suporte ao Cliente do Akamai](https://www.akamai.com/us/en/contact-us/) para adicionar os usuários à plataforma Akamai. Os usuários devem ser criados e ativados antes de usar o logon único. 

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você será conectado automaticamente ao Akamai para o qual configurou o SSO.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Akamai em Meus Aplicativos, você deverá ser conectado automaticamente ao Akamai no qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Akamai, você poderá impor um controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).