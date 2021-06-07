---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Kemp LoadMaster – Azure AD integration | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Kemp LoadMaster – Azure AD integration.
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
ms.openlocfilehash: 91f6db79b7d18dc8b34ba1712d74a92000d63528
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953520"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-kemp-loadmaster-azure-ad-integration"></a>Tutorial: Integração do SSO do Azure Active Directory com o Kemp LoadMaster – Azure AD integration

Neste tutorial, você aprenderá a integrar o Kemp LoadMaster – Azure AD integration com o Azure AD (Azure Active Directory). Ao integrar o Kemp LoadMaster – Azure AD integration com o Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso ao Kemp LoadMaster – Azure AD integration.
* Permitir que os seus usuários sejam conectados automaticamente ao Kemp LoadMaster – Azure AD integration com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do Kemp LoadMaster – Azure AD integration.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Kemp LoadMaster – Azure AD integration é compatível com o SSO iniciado por **IDP**

## <a name="add-kemp-loadmaster-azure-ad-integration-from-the-gallery"></a>Tutorial: Adicionar o Kemp LoadMaster – Azure AD integration pela galeria

Para configurar a integração do Kemp LoadMaster – Azure AD integration com o Azure AD, você precisa adicionar o Kemp LoadMaster – Azure AD integration pela galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Kemp LoadMaster – Azure AD integration** na caixa de pesquisa.
1. Selecione **Kemp LoadMaster – Azure AD integration** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-kemp-loadmaster-azure-ad-integration"></a>Configurar e testar o SSO do Azure AD para o Kemp LoadMaster – Azure AD integration

Configure e teste o SSO do Azure AD com o Kemp LoadMaster – Azure AD integration usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Kemp LoadMaster – Azure AD integration.

Para configurar e testar o SSO do Azure AD com o Kemp LoadMaster – Azure AD integration, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Kemp LoadMaster – Azure AD integration](#configure-kemp-loadmaster-azure-ad-integration-sso)** : para definir as configurações de logon único no lado do aplicativo.

1. **[Como publicar o servidor Web](#publishing-web-server)**
    1. **[Criar um serviço virtual](#create-a-virtual-service)**
    1. **[Certificados e segurança](#certificates-and-security)**
    1. **[Perfil SAML do Kemp LoadMaster – Azure AD integration](#kemp-loadmaster-azure-ad-integration-saml-profile)**
    1. **[Verificar as alterações](#verify-the-changes)**
1. **[Como configurar a autenticação baseada em Kerberos](#configuring-kerberos-based-authentication)**
    1. **[Criar uma conta de delegação de Kerberos para o Kemp LoadMaster – Azure AD integration](#create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration)**
    1. **[KCD (contas de delegação de Kerberos) para o Kemp LoadMaster – Azure AD integration](#kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts)**
    1. **[ESP do Kemp LoadMaster – Azure AD integration](#kemp-loadmaster-azure-ad-integration-esp)**
    1. **[Criar um usuário de teste do Kemp LoadMaster – Azure AD integration](#create-kemp-loadmaster-azure-ad-integration-test-user)** para ter um equivalente a B.Fernandes no Kemp LoadMaster – Azure AD integration que seja vinculado à declaração do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Kemp LoadMaster – Azure AD integration**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL: `https://<KEMP-CUSTOMER-DOMAIN>.com/`

    b. Na caixa de texto **URL de Resposta**, insira uma URL: `https://<KEMP-CUSTOMER-DOMAIN>.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do Kemp LoadMaster – Azure AD integration](mailto:support@kemp.ax) para obter esses valores. Veja também os padrões mostrados na seção "Configuração Básica de SAML" no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e os arquivos XML de metadados de federação e salvá-lo no computador.

    ![O link de download do Certificado](./media/kemp-tutorial/certificate-base-64.png)

1. Na seção **Configurar o Kemp LoadMaster – Azure AD integration**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Kemp LoadMaster – Azure AD integration.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Kemp LoadMaster – Azure AD integration**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-kemp-loadmaster-azure-ad-integration-sso"></a>Configurar o SSO de integração do Kemp LoadMaster – Azure AD integration

## <a name="publishing-web-server"></a>Como publicar o servidor Web 

### <a name="create-a-virtual-service"></a>Criar um serviço virtual 

1. Acesse a integração do Kemp LoadMaster ao Azure AD em LoadMaster Web UI > Serviços Virtuais > Adicionar Novo.

1. Clique em Adicionar Novo.

1. Especifique os parâmetros do serviço virtual.

    ![Captura de tela que mostra a página "Especifique os Parâmetros para o Serviço Virtual" com os valores de exemplo nas caixas.](./media/kemp-tutorial/kemp-1.png)

    a. Endereço virtual
    
    b. Porta
    
    c. Nome do serviço (opcional)
    
    d. Protocolo 

1. Navegue até a seção de Servidores Reais.

1. Clique em Adicionar Novo.

1. Especifique os parâmetros do Servidor Real.
    
    ![Captura de tela que mostra a página "Especifique os Parâmetros para o Servidor Real" com os valores de exemplo nas caixas.](./media/kemp-tutorial/kemp-2.png)

    a. Selecione Permitir Endereços Remotos
    
    b. Digite o endereço do servidor real
    
    c. Porta
    
    d. Método de encaminhamento
    
    e. Peso
    
    f. Limite de conexão
    
    g. Clique em Adicionar este Servidor Real

## <a name="certificates-and-security"></a>Certificados e segurança
 
### <a name="import-certificate-on-kemp-loadmaster-azure-ad-integration"></a>Importar o certificado no Kemp LoadMaster – Azure AD integration 
 
1. Acesse o portal da Web do Kemp LoadMaster – Azure AD integration > Certificados e Segurança > Certificados SSL.

1. Em Gerenciar Certificados > Configuração de Certificado.

1. Clique em Importar Certificado.

1. Especifique o nome do arquivo que contém o certificado. O arquivo também pode reter a chave privada. Se o arquivo não contém a chave privada, o arquivo que contém a chave privada também precisa ser especificado. O certificado pode estar no formato .PEM ou .PFX (IIS).

1. No Arquivo de Certificado, clique em Escolher arquivo.

1. Clique no Arquivo de Chaves (opcional).

1. Clique em Salvar.

### <a name="ssl-acceleration"></a>Aceleração de SSL
 
1. Acesse Kemp LoadMaster Web UI > Serviços Virtuais > Exibir/Modificar Serviços.

1. Em Operação, clique em Modificar.

1. Clique em Propriedades SSL (que opera na camada 7).
    
    ![Captura de tela que mostra a seção "Propriedades do SAML" com a opção "Aceleração de SSL – Habilitada" selecionada e um certificado de exemplo selecionado.](./media/kemp-tutorial/kemp-3.png)
    
    a. Clique em Habilitado em Aceleração de SSL.
    
    b. Em Certificados Disponíveis, selecione o certificado importado e clique no símbolo `>`.
    
    c. Depois que o certificado SSL desejado aparecer em Certificados Atribuídos, clique em **Definir Certificados**.

    > [!NOTE]
    > Verifique se você clicou em **Definir Certificados**.

## <a name="kemp-loadmaster-azure-ad-integration-saml-profile"></a>Perfil SAML do Kemp LoadMaster – Azure AD integration
 
### <a name="import-idp-certificate"></a>Importar o certificado de IdP

Acesse o Console Web do Kemp LoadMaster – Azure AD integration 

1. Clique em Certificados Intermediários em Certificados e Autoridade.

    ![Captura de tela que mostra a seção "Certificados Intermediários Instalados Atualmente" com um certificado de exemplo selecionado.](./media/kemp-tutorial/kemp-6.png)

    a. Clique em escolher arquivo em Adicionar um novo Certificado Intermediário.
    
    b. Navegue até o arquivo de certificado baixado anteriormente no Aplicativo Empresarial do Azure AD.
    
    c. Clique em Abrir.
    
    d. Forneça um Nome em Nome do Certificado.
    
    e. Clique em Adicionar Certificado.

### <a name="create-authentication-policy"></a>Criar uma política de autenticação 
 
Acesse Gerenciar SSO em Serviços Virtuais.

   ![Captura de tela que mostra a página "Gerenciar SSO".](./media/kemp-tutorial/kemp-7.png)
   
   a. Em Adicionar Nova Configuração do Lado do Cliente, clique em Adicionar após dar um nome a ela.

   b. Selecione SAML em Protocolo de Autenticação.

   c. Selecione o Arquivo de Metadados em Provisionamento de IdP. 

   d. Clique em Escolher Arquivo.

   e. Navegue até o XML baixado anteriormente no portal do Azure.

   f. Clique em Abrir e clique no arquivo Importar Metadados de IdP.

   g. Selecione o certificado intermediário no Certificado de IdP.

   h. Defina a ID da Entidade do SP que deve corresponder à identidade criada no portal do Azure. 

   i. Clique em Definir a ID da Entidade do SP.

### <a name="set-authentication"></a>Definir autenticação  
 
No Console Web do Kemp LoadMaster – Azure AD integration

1. Clique em Serviços Virtuais.

1. Clique em Exibir/Modificar Serviços.

1. Clique em Modificar e navegue até as Opções de ESP.
    
    ![Captura de tela que mostra a página "Exibir/Modificar Serviços", com as seções "Opções ESP" e "Servidores Reais" expandidas.](./media/kemp-tutorial/kemp-8.png)

    a. Clique em Habilitar ESP.
    
    b. Selecione o SAML no Modo de Autenticação de Cliente.
    
    c. Selecione a Autenticação do Lado do Cliente criada anteriormente no Domínio de SSO.
    
    d. Digite o nome do host em Hosts Virtuais Permitidos e clique em Definir Hosts Virtuais Permitidos.
    
    e. Digite /* em Diretórios Virtuais Permitidos (com base nos requisitos de acesso) e clique em Definir Diretórios Permitidos.

### <a name="verify-the-changes"></a>Verificar as alterações 
 
Procure a URL do aplicativo 

Você deverá ver a página de logon do locatário em vez do acesso não autenticado anteriormente. 

![Captura de tela que mostra a página "Entrar" com locatários.](./media/kemp-tutorial/kemp-9.png)

## <a name="configuring-kerberos-based-authentication"></a>Como configurar a autenticação baseada em Kerberos 
 
### <a name="create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration"></a>Criar uma conta de delegação de Kerberos para o Kemp LoadMaster – Azure AD integration 

1. Crie uma Conta de usuário (neste exemplo, AppDelegation).
    
    ![Captura de tela que mostra a janela "Propriedades do Usuário da KCD" com a guia "Conta" selecionada.](./media/kemp-tutorial/kemp-10.png)


    a. Selecione a guia Editor de Atributo.

    b. Navegue até servicePrincipalName. 

    c. Selecione servicePrincipalName e clique em Editar.

    d. Digite http/kcduser no campo Valor para adicionar e clique em Adicionar. 

    e. Clique em Aplicar e em OK. A janela precisa ser fechada antes de você abri-la novamente (para ver a nova guia Delegação). 

1. Abra a janela Propriedades do usuário novamente e a guia Delegação estará disponível. 

1. Selecione a guia Delegação.

    ![Captura de tela que mostra a janela "Propriedades do Usuário da KCD" com a guia "Delegação" selecionada.](./media/kemp-tutorial/kemp-11.png)

    a. Selecione Confiar neste usuário apenas para delegação a serviços especificados.

    b. Selecione Usar qualquer protocolo de autenticação.

    c. Adicione os Servidores Reais e adicione http como o serviço. 
    
    d. Marque a caixa de seleção Expandido. 

    e. Você pode ver todos os servidores com o nome do host e o FQDN.
    
    f. Clique em OK.

> [!NOTE]
> Defina o SPN no Aplicativo/Site conforme aplicável. para acessar o aplicativo quando a identidade do pool de aplicativos tiver sido definida. Para acessar o aplicativo IIS usando o nome FQDN, acesse o prompt de comando do Real Server e digite SetSpn com os parâmetros necessários. Por exemplo, Setspn –S HTTP/sescoindc.sunehes.co.in suneshes\kdcuser 

### <a name="kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts"></a>KCD (contas de delegação de Kerberos) para o Kemp LoadMaster – Azure AD integration 

Acesse o Console Web do Kemp LoadMaster – Azure AD integration > Serviços Virtuais > Gerenciar SSO.

![Captura de tela que mostra a página "Gerenciar SSO – Gerenciar Domínio".](./media/kemp-tutorial/kemp-12.png)

a. Navegue até Configurações de Logon Único no Lado do Servidor.

b. Em Adicionar nova Configuração do Lado do Servidor digite o nome e clique em Adicionar.

c. Selecione a Delegação Restrita de Kerberos no Protocolo de Autenticação.

d. Digite o Nome de Domínio no Realm do Kerberos.

e. Clique em Definir realm do Kerberos.

f. Digite o endereço IP do controlador de domínio no Centro de Distribuição de Chaves do Kerberos.

g. Clique em Definir KDC do Kerberos.

h. Digite o nome de usuário do KCD em Nome de Usuário Confiável do Kerberos.

i. Clique em Definir nome de usuário confiável do KDC.

j. Digite a senha em Senha de Usuário Confiável do Kerberos.

k. Clique em Definir a senha de usuário confiável do KCD.

### <a name="kemp-loadmaster-azure-ad-integration-esp"></a>ESP do Kemp LoadMaster – Azure AD integration 

Acesse Serviços Virtuais > Exibir/Modificar Serviços.

![Servidor Web do Kemp LoadMaster – Azure AD integration](./media/kemp-tutorial/kemp-13.png)

a. Clique em Modificar no Apelido do Serviço Virtual.
    
b. Clique em Opções de ESP.
    
c. Em Modo de Autenticação do Servidor, selecione KCD.
        
d. Em Configuração do Lado do Servidor, selecione o perfil do lado do servidor criado anteriormente.

### <a name="create-kemp-loadmaster-azure-ad-integration-test-user"></a>Criar o usuário de teste do Kemp LoadMaster – Azure AD integration

Nesta seção, você criará uma usuária chamada B.Fernandes no Kemp LoadMaster – Azure AD integration. Trabalhe com a [Equipe de suporte do cliente do Kemp LoadMaster – Azure AD integration](mailto:support@kemp.ax) para adicionar os usuários à plataforma do Kemp LoadMaster – Azure AD integration. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você entrará automaticamente no Kemp LoadMaster – Azure AD integration, para o qual configurou o SSO.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Kemp LoadMaster – Azure AD integration em Meus Aplicativos, você deverá ser conectado automaticamente ao Kemp LoadMaster – Azure AD integration para o qual você definiu o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Kemp LoadMaster – Azure AD integration, você poderá impor o controle de sessão, que protege contra a exfiltração e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).