---
title: 'Tutorial: Integrar o SSO (logon único) do Azure Active Directory ao Maverics Identity Orchestrator SAML Connector | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Maverics Identity Orchestrator SAML Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: 31392c1fa3d14d6f1e01a8b302575e9b592e42cd
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183142"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Tutorial: Integrar o logon único do Azure AD ao Maverics Identity Orchestrator SAML Connector

O Strata fornece uma forma simples de integrar aplicativos locais ao Azure AD (Azure Active Directory) para autenticação e controle de acesso.

Este artigo explica como configurar o Maverics Identity Orchestrator para:
* Migrar de maneira incremental os usuários de um sistema de identidade local no Azure AD durante o logon para um aplicativo local herdado.
* Encaminhar solicitações de logon de um produto de gerenciamento de acesso à Web herdado, como o CA SiteMinder ou o Oracle Access Manager, para o Azure AD.
* Autenticar usuários em aplicativos locais que são protegidos usando cabeçalhos HTTP ou cookies de sessão proprietários depois de autenticar o usuário no Azure AD.

O Strata fornece um software que você pode implantar no local ou na nuvem. Ele ajuda você na descoberta, conexão e orquestração entre provedores de identidade para criar o gerenciamento de identidades distribuídas para empresas híbridas e de várias nuvens.

Este tutorial demonstra como migrar um aplicativo Web local atualmente protegido por um produto de gerenciamento de acesso à Web herdado (CA SiteMinder) para usar o Azure AD para autenticação e controle de acesso. Veja a seguir as etapas básicas:
1. Instalar o Maverics Identity Orchestrator.
2. Registrar seu aplicativo empresarial no Azure AD e configurá-lo para usar o Maverics Zero Code Connector do SAML do Azure AD para o SSO (logon único) baseado em SAML.
3. Integrar o Maverics ao SiteMinder e ao repositório de usuários do protocolo LDAP.
4. Configurar o Azure Key Vault e configurar o Maverics para usá-lo como o provedor de gerenciamento de segredos dele.
5. Demonstrar a migração de usuário e a abstração de sessão usando o Maverics para fornecer acesso a um aplicativo Web Java local.

Para obter mais instruções de instalação e configuração, acesse o [site do Strata](https://www.strata.io).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
- Uma assinatura do Maverics Identity Orchestrator SAML Connector habilitada para SSO. Para obter o software do Maverics, entre em contato com o [departamento de vendas do Strata](mailto:sales@strata.io).

## <a name="install-maverics-identity-orchestrator"></a>Instalar o Maverics Identity Orchestrator

Para começar a instalação do Maverics Identity Orchestrator, confira as [instruções de instalação](https://www.strata.io).

### <a name="system-requirements"></a>Requisitos do sistema
* Sistemas operacionais com suporte
  * RHEL 7+
  * CentOS 7+

* Dependências
  * systemd

### <a name="installation"></a>Instalação

1. Obtenha o pacote Maverics RPM (Redhat Package Manager) mais recente. Copie o pacote para o sistema no qual você deseja instalar o software do Maverics.

2. Instale o pacote do Maverics substituindo o nome do arquivo no lugar de `maverics.rpm`.

    `sudo rpm -Uvf maverics.rpm`

3. Após a instalação do Maverics, ele será executado como um serviço em `systemd`. Para confirmar se o serviço está em execução, execute o seguinte comando:

    `sudo systemctl status maverics`

Por padrão, o Maverics é instalado no diretório */usr/local/bin*.

Depois de instalar o Maverics, o arquivo *maverics.yaml* padrão é criado no diretório */etc/maverics*. Antes de você editar a configuração para incluir `workflows` e `connectors`, o arquivo de configuração terá esta aparência:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="configuration-options"></a>Opções de configuração
### <a name="version"></a>Versão
O campo `version` declara qual versão do arquivo de configuração está sendo usada. Se a versão não for especificada, a versão de configuração mais recente será usada.

```yaml
version: 0.1
```
### <a name="listenaddress"></a>listenAddress
`listenAddress` declara o endereço que o Orchestrator escutará. Se a seção do host do endereço estiver em branco, o Orchestrator escutará todos os endereços IP unicast e anycast disponíveis do sistema local. Se a seção da porta do endereço estiver em branco, um número da porta será escolhido automaticamente.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

O campo `tls` declara um mapa de objetos do protocolo TLS. Os objetos TLS podem ser usados por conectores, bem como pelo servidor do Orchestrator. Para obter todas as opções do TLS disponíveis, confira a documentação do pacote do `transport`.

O Microsoft Azure requer comunicação por TLS quando você está usando o SSO baseado em SAML. Para obter informações sobre como gerar certificados, acesse o [site do Let's Encrypt](https://letsencrypt.org/getting-started/).

A chave `maverics` é reservada para o servidor do Orchestrator. Todas as outras chaves estão disponíveis e podem ser usadas para injetar um objeto TLS em um conector especificado.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Incluir arquivos

Você pode definir `connectors` e `workflows` nos próprios arquivos de configuração separados e referenciá-los no arquivo *maverics.yaml* usando `includeFiles`, de acordo com o seguinte exemplo:

```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

Este tutorial usa um só arquivo de configuração *maverics.yaml*.

## <a name="use-azure-key-vault-as-your-secrets-provider"></a>Usar o Azure Key Vault como o provedor de segredos

### <a name="manage-secrets"></a>Gerenciar segredos

Para carregar segredos, o Maverics pode se integrar a várias soluções de gerenciamento de segredo. As integrações atuais incluem um arquivo, o Hashicorp Vault e o Azure Key Vault. Se nenhuma solução de gerenciamento de segredos for especificada, o Maverics usará como padrão o carregamento de segredos do arquivo *maverics.yaml* em texto sem formatação.

Para declarar um valor como um segredo em um arquivo de configuração *maverics.yaml*, empacote o segredo com colchetes angulares:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="load-secrets-from-a-file"></a>Carregar segredos de um arquivo

1. Para carregar segredos de um arquivo, adicione a variável de ambiente `MAVERICS_SECRET_PROVIDER` ao arquivo */etc/maverics/maverics.env* usando:

   `MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

2. Reinicie o serviço do Maverics executando:

   `sudo systemctl restart maverics`

O conteúdo do arquivo *secrets.yaml* pode ser preenchido com qualquer número de `secrets`.

```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="set-up-an-azure-key-vault"></a>Configurar um Azure Key Vault

Você pode configurar um Azure Key Vault usando o portal do Azure ou a CLI do Azure.

**Use o Portal do Azure**
1. Entre no [portal do Azure](https://portal.azure.com).
1. [Crie um cofre de chaves](../../key-vault/general/quick-create-portal.md).
1. [Adicione os segredos ao cofre de chaves](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).
1. [Registre um aplicativo no Azure AD](../develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
1. [Autorize um aplicativo a usar um segredo](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).

**Usar a CLI do Azure**

1. Abra a [CLI do Azure](/cli/azure/install-azure-cli) e digite o seguinte comando:

    ```azurecli
    az login
    ```

1. Crie um cofre de chaves executando o seguinte comando:
    ```azurecli
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

1. Adicione os segredos ao cofre de chaves executando o seguinte comando:
    ```azurecli
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

1. Registre um aplicativo no Azure AD executando o seguinte comando:
    ```azurecli
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

1. Autorize um aplicativo a usar um segredo executando o seguinte comando:
    ```azurecli
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

1. Para carregar segredos do Azure Key Vault, defina a variável de ambiente `MAVERICS_SECRET_PROVIDER` no arquivo */etc/maverics/maverics.env* usando as credenciais encontradas no arquivo *azure-credentials.json*, no seguinte formato:
 
   `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

1. Reinicie o serviço do Maverics: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Configurar seu aplicativo no Azure AD para o SSO baseado em SAML

1. Em seu locatário do Azure AD, acesse **Aplicativos empresariais**, procure o **Maverics Identity Orchestrator SAML Connector** e selecione-o.

1. No painel **Propriedades** do Maverics Identity Orchestrator SAML Connector, defina **Atribuição de usuário obrigatória?** como **Não** para permitir que o aplicativo funcione para usuários recém-migrados.

1. No painel **Visão geral** do Maverics Identity Orchestrator SAML Connector, selecione **Configurar logon único** e escolha **SAML**.

1. No painel **Logon baseado em SAML** do Maverics Identity Orchestrator SAML Connector, edite a **Configuração Básica do SAML** selecionando o botão **Editar** (ícone de lápis).

   ![Captura de tela do botão Editar de "Configuração Básica do SAML".](common/edit-urls.png)

1. Insira a **ID da Entidade** digitando uma URL no seguinte formato: `https://<SUBDOMAIN>.maverics.org`. A ID da Entidade precisa ser exclusiva entre os aplicativos no locatário. Salve o valor inserido aqui para que ele seja incluído na configuração do Maverics.

1. Insira a **URL de Resposta** no seguinte formato: `https://<AZURECOMPANY.COM>/<MY_APP>/`. 

1. Insira a **URL de Logon** no seguinte formato: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>`. 

1. Selecione **Salvar**.

1. Na seção **Certificado de Autenticação SAML**, selecione o botão **Copiar** para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no seu computador.

    ![Captura de tela do botão Copiar do "Certificado de Autenticação SAML".](common/copy-metadataurl.png)

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector"></a>Configurar o Maverics Identity Orchestrator Azure AD SAML Connector

O Maverics Identity Orchestrator Azure AD Connector dá suporte ao OpenID Connect e ao SAML Connect. Para configurar o conector, faça o seguinte: 

1. Para habilitar o SSO baseado em SAML, defina `authType: saml`.

1. Crie o valor para `samlMetadataURL` no seguinte formato: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`.

1. Defina a URL para a qual o Azure será redirecionado novamente para o seu aplicativo depois que os usuários fizerem logon com as respectivas credenciais do Azure. Use o seguinte formato: `samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`.

1. Copie o valor da EntityID previamente configurada: `samlEntityID: https://<SUBDOMAIN>.maverics.org`.

1. Copie o valor da URL de Resposta que o Azure AD usará para postar a resposta do SAML: `samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`.

1. Gere uma chave de assinatura JWT (Token Web JSON), usada para proteger as informações de sessão do Maverics Identity Orchestrator usando a [ferramenta OpenSSL](https://www.openssl.org/source/):

    ```console 
    openssl rand 64 | base64
    ```
1. Copie a resposta para a propriedade de configuração `jwtSigningKey`: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`.

## <a name="attributes-and-attribute-mapping"></a>Atributos e mapeamento de atributos
O mapeamento de atributos é usado para definir o mapeamento de atributos do usuário de um diretório local de usuários de origem para um locatário do Azure AD após a configuração do usuário.

Os atributos determinam quais dados do usuário podem ser retornados para um aplicativo em uma declaração, transmitidos em cookies de sessão ou transmitidos ao aplicativo em variáveis de cabeçalho HTTP.

## <a name="configure-the-maverics-identity-orchestrator-azure-ad-saml-connector-yaml-file"></a>Configurar o arquivo YAML do Maverics Identity Orchestrator Azure AD SAML Connector

A configuração do Maverics Identity Orchestrator Connector para Azure AD será parecida com esta:

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-an-azure-ad-tenant"></a>Migrar os usuários para um locatário do Azure AD

Siga esta configuração para migrar de maneira incremental os usuários de um produto de gerenciamento de acesso à Web, como o CA SiteMinder, o Oracle Access Manager ou o IBM Tivoli. Você também pode migrá-los de um diretório LDAP (Lightweight Directory Access Protocol) ou de um Banco de Dados SQL.

### <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Configurar as permissões de aplicativo no Azure AD para criar usuários

1. No seu locatário do Azure AD, acesse `App registrations` e selecione o aplicativo **Maverics Identity Orchestrator SAML Connector**.

1. No painel **Maverics Identity Orchestrator SAML Connector | Certificados e segredos**, selecione `New client secret` e escolha a opção de expiração. Selecione o botão **Copiar** para copiar o segredo e salvá-lo em seu computador.

1. No painel **Maverics Identity Orchestrator SAML Connector | Permissões de API**, selecione **Adicionar permissão** e, no painel **Solicitar permissões de API**, escolha **Microsoft Graph** e **Permissões de aplicativo**. 

1. Na próxima tela, selecione **User.ReadWrite.All** e escolha **Adicionar permissões**. 

1. De volta ao painel **Permissões de API**, selecione **Conceder consentimento do administrador**.

### <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-file-for-user-migration"></a>Configurar o arquivo YAML do Maverics Identity Orchestrator Azure AD SAML Connector para a migração de usuário

Para habilitar o fluxo de trabalho de migração de usuário, adicione estas propriedades adicionais ao arquivo de configuração:
1. Insira a **URL do Azure Graph** no seguinte formato: `graphURL: https://graph.microsoft.com`.
1. Insira a **URL do Token OAuth** no seguinte formato: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`.
1. Insira o segredo do cliente gerado anteriormente no seguinte formato: `oauthClientSecret: <CLIENT SECRET>`.


O arquivo de configuração final do Maverics Identity Orchestrator Azure AD Connector terá a seguinte aparência:

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

### <a name="configure-maverics-zero-code-connector-for-siteminder"></a>Configurar o Maverics Zero Code Connector para o SiteMinder

Você usa o conector do SiteMinder para migrar usuários para um locatário do Azure AD. Você faz logon dos usuários em aplicativos locais herdados protegidos pelo SiteMinder usando identidades e credenciais do Azure AD recém-criadas.

Para este tutorial, o SiteMinder foi configurado para proteger o aplicativo herdado usando a autenticação baseada em formulários e usando o cookie `SMSESSION`. Para integrar-se a um aplicativo que consome informações de autenticação e sessão por meio de cabeçalhos HTTP, será necessário adicionar a configuração de emulação de cabeçalho ao conector.

Este exemplo mapeia o atributo `username` para o cabeçalho HTTP `SM_USER`:

```yaml
  headers:
    SM_USER: username
```

Defina o `proxyPass` como a localização para a qual as solicitações são feitas por proxy. Normalmente, esse local é o host do aplicativo protegido.

`loginPage` deve corresponder à URL do formulário de logon atualmente usado pelo SiteMinder quando ele redireciona os usuários para autenticação.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

### <a name="configure-maverics-zero-code-connector-for-ldap"></a>Configurar o Maverics Zero Code Connector para LDAP

Quando os aplicativos são protegidos por um produto WAM (gerenciamento de acesso à Web) como o SiteMinder, as identidades e os atributos de usuário normalmente são armazenados em um diretório LDAP.

Essa configuração de conector demonstra como se conectar ao diretório LDAP. O conector é configurado como o repositório de usuários do SiteMinder, de modo que as informações de perfil do usuário corretas possam ser coletadas durante o fluxo de trabalho de migração e um usuário correspondente possa ser criado no Azure AD.

* `baseDN` especifica a localização do diretório na qual a pesquisa LDAP deve ser executada.

* `url` é o endereço e a porta do servidor LDAP com os quais deve ser feita a conexão.

* `serviceAccountUsername` é o nome de usuário usado para se conectar ao servidor LDAP, geralmente expresso como um DN de associação (por exemplo, `CN=Directory Manager`).

* `serviceAccountPassword` é a senha usada para se conectar ao servidor LDAP. Esse valor é armazenado na instância do Azure Key Vault configurada anteriormente.  

* `userAttributes` define a lista de atributos relacionados ao usuário para consulta. Esses atributos são mapeados posteriormente para os atributos correspondentes do Azure AD.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

### <a name="configure-the-migration-workflow"></a>Configurar o fluxo de trabalho de migração

A configuração do fluxo de trabalho de migração determina como o Maverics migra os usuários do SiteMinder/LDAP para o Azure AD.

Este fluxo de trabalho:
- Usa o conector do SiteMinder para fazer proxy do logon do SiteMinder. As credenciais do usuário são validadas por meio da autenticação do SiteMinder e transmitidas para as etapas a seguir do fluxo de trabalho.
- Recupera os atributos de perfil do usuário do repositório de usuários do SiteMinder.
- Faz uma solicitação à API do Microsoft Graph para criar o usuário no seu locatário do Azure AD.

Para configurar o fluxo de trabalho de migração, faça o seguinte:

1. Dê um nome ao fluxo de trabalho (por exemplo, **Migração do SiteMinder para o Azure AD**).
1. Especifique o `endpoint`, que é um caminho HTTP no qual o fluxo de trabalho é exposto, que dispara as `actions` desse fluxo de trabalho em resposta às solicitações. Geralmente, o `endpoint` corresponde ao aplicativo intermediado por proxy (por exemplo, `/my_app`). O valor precisa incluir as barras à esquerda e à direita.
1. Adicione as `actions` apropriadas ao fluxo de trabalho.

   a. Defina o método `login` para o conector do SiteMinder. O valor do conector precisa corresponder ao valor do nome na configuração do conector.

   b. Defina o método `getprofile` para o conector LDAP.

   c.  Defina o método `createuser` para o conector do AzureAD.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>Verificar o fluxo de trabalho de migração

1. Se o serviço do Maverics ainda não estiver em execução, inicie-o executando o seguinte comando:  

   `sudo systemctl start maverics`

1. Acesse a URL de logon com proxy, `http://host.company.com/my_app`.
1. Forneça as credenciais de usuário usadas para fazer logon no aplicativo enquanto ele é protegido pelo SiteMinder.
4. Acesse **Página Inicial** > **Usuários | Todos os Usuários** para verificar se o usuário foi criado no locatário do Azure AD.  

### <a name="configure-the-session-abstraction-workflow"></a>Configurar o fluxo de trabalho de abstração de sessão

O fluxo de trabalho de abstração de sessão move a autenticação e o controle de acesso para o aplicativo Web local herdado para o locatário do Azure AD.

O conector do Azure usa o método `login` para redirecionar o usuário para a URL de logon, supondo que não exista uma sessão.

Depois de autenticado, o token de sessão criado como resultado é passado para o Maverics. O método `emulate` do conector do SiteMinder é usado para emular a sessão baseada em cookie ou a sessão baseada em cabeçalho e decorar a solicitação com os atributos adicionais exigidos pelo aplicativo.

1. Dê um nome ao fluxo de trabalho (por exemplo, **Abstração de Sessão do SiteMinder**).
1. Especifique o `endpoint`, que corresponde ao aplicativo que está sendo intermediado por proxy. O valor precisa incluir barras à esquerda e à direita (por exemplo, `/my_app/`).
1. Adicione as `actions` apropriadas ao fluxo de trabalho.

   a. Defina o método `login` para o conector do Azure. O valor `connector` precisa corresponder ao valor de `name` na configuração do conector.

   b. Defina o método `emulate` para o conector do SiteMinder.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>Verificar o fluxo de trabalho de abstração de sessão

1. Acesse a URL do aplicativo com proxy, `https://<AZURECOMPANY.COM>/<MY_APP>`. 
    
    Você será redirecionado para a página de logon com proxy.

1. Insira as credenciais de usuário do Azure AD.

   Você deverá ser redirecionado para o aplicativo como se fosse autenticado diretamente pelo SiteMinder.
