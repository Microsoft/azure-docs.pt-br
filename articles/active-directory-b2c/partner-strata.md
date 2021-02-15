---
title: Tutorial para configurar o Azure Active Directory B2C com o Strata
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação de Azure AD B2C com o whoIam para verificação de usuário
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c7f7f162355b919c395dd0ee6d03b2bc5526e3da
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96936685"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>Tutorial para estender Azure AD B2C para proteger aplicativos locais usando o Strata

Neste tutorial de exemplo, saiba como integrar o Azure Active Directory (AD) B2C com o [orquestrador de identidade Maverics](https://www.strata.io/maverics-identity-orchestrator/)do Strata.
O orquestrador de identidade Maverics estende Azure AD B2C para proteger os aplicativos locais. Ele se conecta a qualquer sistema de identidade, migra os usuários e as credenciais de forma transparente, sincroniza políticas e configurações e abstrai o gerenciamento de autenticação e de sessão. O uso de empresas Stratas pode fazer a transição rapidamente do herdado para o Azure AD B2C sem reescrever aplicativos. A solução oferece as seguintes vantagens:

- **SSO (Sign-On único) do cliente para aplicativos híbridos locais**: Azure ad B2C dá suporte ao SSO do cliente com o Maverics Identity Orchestrator. Os usuários entram com suas contas hospedadas no Azure AD B2C ou no IdP (provedor de identidade social). O Maverics estende o SSO para aplicativos que foram historicamente protegidos por sistemas de identidade herdados, como o Symantec SiteMinder.

- **Estenda o SSO baseado em padrões para aplicativos sem regravá-los**: Use Azure ad B2C para gerenciar o acesso do usuário e habilitar o SSO com conectores SAML ou OIDC do Orchestrator de identidade Maverics.

- **Configuração fácil**: Azure ad B2C fornece uma interface do usuário simples e passo a passo para conectar conectores SAML ou OIDC do Orchestrator Identity ao Azure ad B2C.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- Um [locatário Azure ad B2C](./tutorial-create-tenant.md) que está vinculado à sua assinatura do Azure.

- Uma instância de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para armazenar segredos que são usados pelo orquestrador de identidade Maverics. Ele é usado para se conectar a Azure AD B2C ou a outros provedores de atributos, como um diretório ou banco de dados LDAP (Lightweight Directory Access Protocol).

- Uma instância do [orquestrador de identidade Maverics](https://www.strata.io/maverics-identity-orchestrator/) que está instalada e em execução em uma máquina virtual do Azure ou no servidor local de sua escolha. Para obter informações sobre como obter o software e o acesso à documentação de instalação e configuração, entre em contato com [Strata](https://www.strata.io/contact/)

- Um aplicativo local que você fará a transição de um sistema de identidade herdado para Azure AD B2C.

## <a name="scenario-description"></a>Descrição do cenário

A integração Maverics do Strata inclui os seguintes componentes:

- **Azure ad B2C**: o servidor de autorização responsável por verificar as credenciais do usuário. Os usuários autenticados podem acessar aplicativos locais usando uma conta local armazenada no diretório Azure AD B2C.

- **Um IDP externo ou empresarial social**: pode ser qualquer provedor do OpenID Connect, Facebook, Google ou github. Confira informações sobre como usar o [IDPs externo](./technical-overview.md#external-identity-providers) com Azure ad B2C.  

- **Orquestrador de identidade Maverics do Strata**: o serviço que orquestra o logon do usuário e passa de forma transparente a identidade para aplicativos por meio de cabeçalhos HTTP.

O diagrama de arquitetura a seguir mostra a implementação.

![Imagem mostre a arquitetura de uma integração de Azure AD B2C com o Strata Maverics para habilitar o acesso a aplicativos híbridos](./media/partner-strata/strata-architecture-diagram.png)

| Etapas | Descrição |
|:-------|:---------------|
| 1. | O usuário faz uma solicitação para acessar o aplicativo hospedado local. O Maverics Identity Orchestrator proxies a solicitação feita pelo usuário para o aplicativo.|
| 2. | O orquestrador verifica o estado de autenticação do usuário. Se ele não receber um token de sessão ou o token de sessão fornecido for inválido, ele enviará o usuário para Azure AD B2C para autenticação.|
| 3. | Azure AD B2C envia a solicitação de autenticação para o IdP social configurado.|
| 4. | O IdP desafia o usuário para as credenciais. Dependendo do IdP, o usuário pode exigir a autenticação multifator (MFA).|
| 5. | O IdP envia a resposta de autenticação de volta para Azure AD B2C. Opcionalmente, o usuário pode criar uma conta local no diretório Azure AD B2C durante essa etapa.|
| 6. | Azure AD B2C envia a solicitação do usuário para o ponto de extremidade especificado durante o registro do aplicativo Orchestrator no locatário Azure AD B2C.|
| 7. | O orquestrador avalia as políticas de acesso e calcula os valores de atributo a serem incluídos nos cabeçalhos HTTP encaminhados para o aplicativo. Durante essa etapa, o orquestrador pode chamar provedores de atributos adicionais para recuperar as informações necessárias para definir corretamente os valores de cabeçalho. O orquestrador define os valores de cabeçalho e envia a solicitação para o aplicativo.|
| 8. | O usuário agora é autenticado e tem acesso ao aplicativo.|

## <a name="get-maverics-identity-orchestrator"></a>Obter orquestrador de identidade Maverics
Para obter o software que você usará para integrar seu aplicativo local herdado com o Azure AD B2C, entre em contato com [Strata](https://www.strata.io/contact/). Depois de obter o software, siga as etapas abaixo para determinar os pré-requisitos específicos do orquestrador e executar as etapas necessárias de instalação e configuração.

## <a name="configure-your-azure-ad-b2c-tenant"></a>Configurar seu locatário de Azure AD B2C

1. **Registre seu aplicativo**

   a. [Registre o orquestrador como um aplicativo](./tutorial-register-applications.md?tabs=app-reg-ga) em Azure ad B2C locatário.
   >[!Note]
   >Você precisará do nome do locatário e do identificador, da ID do cliente, do segredo do cliente, das declarações configuradas e do URI de redirecionamento mais tarde ao configurar sua instância do Orchestrator.

   b. Conceda permissões do Microsoft MS API do Graph aos seus aplicativos. Seu aplicativo precisará das seguintes permissões: `offline_access` , `openid` .

   c. Adicione um URI de redirecionamento para seu aplicativo. Esse URI corresponderá ao `oauthRedirectURL` parâmetro da configuração do conector de Azure ad B2C do seu orquestrador, por exemplo, `https://example.com/oidc-endpoint` .

2. **Criar um fluxo de usuário**: criar um [fluxo de usuário de inscrição e entrada](./tutorial-create-user-flows.md).

3. **Adicionar um IDP**: opte por conectar seu usuário com uma conta local ou um [IDP](./tutorial-add-identity-providers.md)empresarial ou social.

4. **Definir atributos de usuário**: defina os atributos a serem coletados durante a inscrição.

5. **Especificar declarações do aplicativo**: Especifique os atributos a serem retornados ao aplicativo por meio de sua instância do Orchestrator. O orquestrador consome atributos de declarações retornadas pelo Azure AD B2C e pode recuperar atributos adicionais de outros sistemas de identidade conectados, como diretórios e bancos de dados LDAP. Esses atributos são definidos em cabeçalhos HTTP e enviados para o aplicativo de upstream local.

## <a name="configure-maverics-identity-orchestrator"></a>Configurar o orquestrador de identidade Maverics

Nas seções a seguir, vamos orientá-lo pelas etapas necessárias para configurar sua instância do Orchestrator. Para obter suporte e documentação adicionais, entre em contato com [Strata](https://www.strata.io/contact/).

### <a name="maverics-identity-orchestrator-server-requirements"></a>Requisitos do servidor Maverics Identity Orchestrator

Você pode executar a instância do Orchestrator em qualquer servidor, seja local ou em uma infraestrutura de nuvem pública por provedor, como Azure, AWS ou GCP.

- Sistema operacional: REHL 7,7 ou superior, CentOS 7 +

- Disco: 10 GB (pequeno)

- Memória: 16 GB

- Portas: 22 (SSH/SCP), 443, 80

- Acesso à raiz para tarefas administrativas/de instalação

- O orquestrador de identidade Maverics é executado como usuário `maverics` em `systemd`

- Saída de rede do servidor que hospeda o orquestrador de identidade Maverics com a capacidade de acessar seu locatário do Azure AD.

### <a name="install-maverics-identity-orchestrator"></a>Instalar o Maverics Identity Orchestrator

1. Obtenha o pacote RPM mais recente do Maverics. Coloque o pacote no sistema no qual você deseja instalar o Maverics. Se você estiver copiando o arquivo para um host remoto, o [SCP](https://www.ssh.com/ssh/scp/) será uma ferramenta útil.

2. Para instalar o pacote Maverics, execute o comando a seguir substituindo seu nome de arquivo no lugar de `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   Por padrão, o Maverics é instalado no diretório `/usr/local/bin`.

3. Após a instalação do Maverics, ele será executado como um serviço em `systemd`.  Para verificar se o serviço Maverics está em execução, execute o seguinte comando:

   `sudo service maverics status`

  Se a instalação do Orchestrator tiver sido bem-sucedida, você verá uma mensagem semelhante a esta:

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. Se o serviço Maverics falhar ao iniciar, execute o seguinte comando para investigar o problema:

   `journalctl --unit=maverics.service --reverse`

   A entrada de log mais recente será exibida no início da saída.

Após a instalação do Maverics, o arquivo `maverics.yaml` padrão é criado no diretório `/etc/maverics`.

Configure seu Orchestrator para proteger o aplicativo. Integre com Azure AD B2C, armazene e recupere segredos de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9). Defina o local do qual o orquestrador deve ler sua configuração.

### <a name="supply-configuration-using-environment-variables"></a>Fornecer configuração usando variáveis de ambiente

Forneça configuração para suas instâncias do Orchestrator por meio de variáveis de ambiente.

`MAVERICS_CONFIG`

Essa variável de ambiente informa à instância de orquestrador que YAML os arquivos de configuração a serem usados e onde encontrá-los durante a inicialização ou reinicializações. Defina a variável de ambiente em `/etc/maverics/maverics.env` .

### <a name="create-the-orchestrators-tls-configuration"></a>Criar a configuração TLS do Orchestrator

O `tls` campo em seu `maverics.yaml` declara as configurações de segurança da camada de transporte que sua instância do Orchestrator usará. Os conectores podem usar objetos TLS e o servidor Orchestrator.

A chave `maverics` é reservada para o servidor do Orchestrator. Todas as outras chaves estão disponíveis e podem ser usadas para injetar um objeto TLS em um conector especificado.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>Configurar o conector de Azure AD B2C

Os orquestradores usam conectores para integrar com provedores de autenticação e de atributos. Nesse caso, esse gateway de aplicativo Orchestrator usa o conector de Azure AD B2C como um provedor de autenticação e de atributo. Azure AD B2C usa o IdP social para autenticação e, em seguida, atua como um provedor de atributos para o orquestrador, passando atributos em declarações definidas em cabeçalhos HTTP.  

A configuração desse conector corresponde ao aplicativo registrado no locatário Azure AD B2C.

1. Copie a ID do cliente, o segredo e o URI de redirecionamento da configuração do aplicativo em seu locatário.

2. Dê um nome ao seu conector, mostrado aqui como `azureADB2C` , e defina o conector `type` como `azure` . Anote o nome do conector, pois esse valor é usado em outros parâmetros de configuração abaixo.

3. Para essa integração, o `authType` deve ser definido como `oidc` .

4. Defina a ID do cliente que você copiou na etapa 1 como o valor para o `oauthClientID` parâmetro.

5. Defina o segredo do cliente que você copiou na etapa 1 como o valor para o `oauthClientSecret` parâmetro.

6. Defina o URI de redirecionamento que você copiou na etapa 1 como o valor para o `oauthRedirectURL` parâmetro.

7. O conector do Azure AD B2C OIDC usa o ponto de extremidade OIDC conhecido para descobrir metadados, incluindo URLs e chaves de assinatura. Defina o valor de `oidcWellKnownURL` para o ponto de extremidade do locatário.

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>Definir Azure AD B2C como seu provedor de autenticação

Um provedor de autenticação determina como fazer a autenticação para um usuário que não tenha apresentado uma sessão válida como parte da solicitação de recurso de aplicativo. A configuração em seu locatário de Azure AD B2C determina como desafiar um usuário para as credenciais e aplicar políticas de autenticação adicionais. Por exemplo, para exigir um segundo fator para concluir o processo de autenticação e decidir quais declarações devem ser retornadas ao gateway de aplicativo Orchestrator após a autenticação ser realizada com sucesso.

O valor de `authProvider` deve corresponder ao valor do conector `name` .

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>Proteger seu aplicativo local com um gateway de aplicativo do Orchestrator

A configuração do gateway de aplicativo do orquestrador declara como Azure AD B2C deve proteger seu aplicativo e como os usuários devem acessar o aplicativo.

1. Crie um nome para o seu gateway de aplicativo. Você pode usar um nome amigável ou o nome de host totalmente qualificado como um identificador para seu aplicativo.

2. Defina o `location`. O exemplo aqui usa a raiz do aplicativo `/` , no entanto, pode ser qualquer caminho de URL do seu aplicativo.

3. Defina o aplicativo protegido no `upstream` usando a Convenção host: porta: `https://example.com:8080` .

4. Defina os valores para páginas de erro e não autorizadas.

5. Defina os nomes de cabeçalho HTTP e os valores de atributo que devem ser fornecidos ao aplicativo para estabelecer a autenticação e controlar o acesso ao aplicativo. Os nomes de cabeçalho são arbitrários e geralmente correspondem à configuração do aplicativo. Os valores de atributo são namespacedos pelo conector que os fornece. No exemplo a seguir, os valores retornados de Azure AD B2C são prefixados com o nome do conector `azureADB2C` em que o sufixo é o nome do atributo que contém o valor necessário, por exemplo `given_name` .

6. Defina as políticas a serem avaliadas e impostas. Três ações são definidas: `allowUnauthenticated` , `allowAnyAuthenticated` e `allowIfAny` . Cada ação é associada a um `resource` e a política é avaliada para isso `resource` .

>[!NOTE]
>Ambos `headers` e `policies` usam o JavaScript ou o GoLang Service Extensions para implementar uma lógica arbitrária que aprimora significativamente os recursos padrão.

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>Usar o Azure Key Vault como o provedor de segredos

É importante proteger os segredos que o Orchestrator usa para se conectar ao Azure AD B2C e a qualquer outro sistema de identidade. O Maverics usará como padrão o carregamento de segredos em texto sem formatação, no `maverics.yaml` entanto, neste tutorial, você utilizará Azure Key Vault como o provedor de segredos.

Siga as instruções para [criar um novo Key Vault](../key-vault/secrets/quick-create-portal.md) que sua instância do Orchestrator usará como um provedor de segredos. Adicione seus segredos ao seu cofre e anote o `SECRET NAME` determinado para cada segredo. Por exemplo, `AzureADB2CClientSecret`.

Para declarar um valor como um segredo em um arquivo de configuração `maverics.yaml`, empacote o segredo com colchetes angulares:

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

O valor especificado nos colchetes angulares deve corresponder ao `SECRET NAME` determinado para o segredo em seu Azure Key Vault.

Para carregar segredos de Azure Key Vault, defina a variável de ambiente `MAVERICS_SECRET_PROVIDER` no arquivo `/etc/maverics/maverics.env` , com as credenciais encontradas no azure-credentials.jsno arquivo, usando o seguinte padrão:

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>Colocar tudo em conjunto

Veja como a configuração do orquestrador será exibida quando você concluir as configurações descritas acima.

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>Testar o fluxo

1. Navegue até a URL do aplicativo local, `https://example.com/sonar/dashboard` .

2. O orquestrador deve redirecionar para a página que você configurou em seu fluxo de usuário.

3. Selecione o IdP na lista na página.

4. Quando você for Redirecionado para o IdP, forneça suas credenciais conforme solicitado, incluindo um token de MFA, se exigido por esse IdP.

5. Depois de autenticar com êxito, você deve ser redirecionado para Azure AD B2C, que encaminha a solicitação do aplicativo para o URI de redirecionamento do Orchestrator.

6. O orquestrador avalia as políticas, calcula os cabeçalhos e envia o usuário para o aplicativo upstream.  

7. Você deve ver o aplicativo solicitado.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
