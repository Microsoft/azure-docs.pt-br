---
title: Desenvolver um aplicativo Web seguro | Microsoft Docs
description: Este aplicativo de exemplo simples implementa práticas recomendadas de segurança que melhoram seu aplicativo e a postura de segurança de sua organização quando você desenvolve no Azure.
keywords: na
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 66719d43db277e6c82d89ee17c602309d16140f8
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489972"
---
# <a name="develop-a-secure-web-app"></a>Desenvolver um aplicativo Web seguro

Este exemplo é um aplicativo Python simples que exibe uma página da Web que contém links para recursos de segurança para o desenvolvimento de aplicativos no Azure. O aplicativo implementa práticas recomendadas de segurança que podem ajudar a melhorar seu aplicativo e a postura de segurança da sua organização ao desenvolver aplicativos no Azure.

Você deve seguir as etapas descritas neste artigo sequencialmente para garantir que os componentes do aplicativo estejam configurados corretamente. O banco de dados, Azure App serviço, Azure Key Vault instância e Aplicativo Azure instância de gateway dependem uns dos outros.

Os scripts de implantação configuram a infraestrutura. Depois de executar os scripts de implantação, você precisará fazer alguma configuração manual no portal do Azure para vincular os componentes e serviços juntos.

O aplicativo de exemplo é direcionado para iniciantes que desenvolvem aplicativos no Azure que desejam implementar medidas de segurança em seus aplicativos.

Ao desenvolver e implantar este aplicativo, você aprenderá a:

- Crie uma instância de Azure Key Vault, armazene e recupere segredos dela.
- Implantar o banco de dados do Azure para PostgreSQL, configurar senhas seguras e autorizar o acesso a ela.
- Execute um contêiner do Alpine Linux em aplicativos Web do Azure para Linux e habilite identidades gerenciadas para recursos do Azure.
- Crie e configure uma instância de gateway Aplicativo Azure com um firewall que usa o [OWASP 10 principais RuleSet](https://coreruleset.org/).
- Habilite a criptografia de dados em trânsito e em repouso usando os serviços do Azure.

Depois de desenvolver e implantar esse aplicativo, você terá configurado o exemplo de aplicativo Web a seguir junto com as medidas de configuração e segurança descritas.

![Aplicativo Web de exemplo](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Arquitetura
O aplicativo é um aplicativo típico de n camadas com três camadas. O front-end, back-end e a camada de banco de dados com componentes de monitoramento e gerenciamento de segredo integrados são mostrados aqui:

![Arquitetura do aplicativo](./media/secure-web-app/architecture.png)

A arquitetura consiste nesses componentes:

- [Gateway de Aplicativo do Azure](https://docs.microsoft.com/azure/application-gateway/). Fornece o gateway e o firewall para nossa arquitetura de aplicativo.
- [Aplicativos Web do Azure no Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro). Fornece o tempo de execução do contêiner para executar o aplicativo Python em um ambiente Linux.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Armazena e criptografa os segredos de nosso aplicativo e gerencia a criação de políticas de acesso em relação a eles.
- [Banco de dados do Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql/). Armazena com segurança os dados do aplicativo.
- [Central de segurança do Azure](https://docs.microsoft.com/azure/security-center/) e [informações de aplicativo Azure](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Fornece monitoramento e alertas na operação do nosso aplicativo.

## <a name="threat-model"></a>Modelo de ameaça
A modelagem de ameaças é o processo de identificar possíveis ameaças à segurança para seus negócios e aplicativos e, em seguida, garantir que um plano de mitigação adequado esteja em vigor.

Este exemplo usou o [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) para implementar a modelagem de ameaças para o aplicativo de exemplo seguro. Ao diagramar os componentes e os fluxos de dados, você pode identificar problemas e ameaças no início do processo de desenvolvimento. Isso poupa tempo e dinheiro mais tarde.

Este é o modelo de ameaça para o aplicativo de exemplo:

![Modelo de ameaça](./media/secure-web-app/threat-model.png)

Algumas ameaças de exemplo e possíveis vulnerabilidades que a ferramenta de modelagem de ameaças gera são mostradas na captura de tela a seguir. O modelo de ameaça fornece uma visão geral da superfície de ataque exposta e solicita que os desenvolvedores pensem sobre como mitigar os problemas.

![Saída do modelo de ameaça](./media/secure-web-app/threat-model-output.png)

Por exemplo, a injeção de SQL na saída do modelo de ameaça anterior é atenuada com a limpeza de entradas do usuário e o uso de funções armazenadas no banco de dados do Azure para PostgreSQL. Essa mitigação impede a execução arbitrária de consultas durante leituras e gravações de dados.

Os desenvolvedores melhoram a segurança geral do sistema, reduzindo cada uma das ameaças na saída do modelo de ameaça.

## <a name="deployment"></a>Implantação
As opções a seguir permitem executar o Linux no serviço Azure App:

- Escolha um contêiner na lista de contêineres da Microsoft pré-criados no Azure que foram criados com tecnologias de suporte (Python, Ruby, PHP, Java, Node. js, .NET Core).
- Use um contêiner personalizado. Selecione seus próprios registros de contêiner como a origem da imagem e crie com base nas muitas tecnologias disponíveis que dão suporte a HTTP.

Neste exemplo, você executará o script de implantação que implantará o webapp no serviço de aplicativo e criará os recursos.

O aplicativo pode usar os diferentes modelos de implantação mostrados abaixo:

![Diagrama de fluxo de dados de implantação](./media/secure-web-app/deployment.png)

Há várias maneiras de implantar aplicativos no Azure, incluindo:

- Modelos do Azure Resource Manager
- PowerShell
- CLI do Azure
- Portal do Azure
- Azure DevOps

Este aplicativo usado:

- [Docker](https://docs.docker.com/) para criar e criar as imagens de contêiner.
- [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para implantação.
- [Hub](https://hub.docker.com/) do Docker como o registro de contêiner.

## <a name="security-considerations"></a>Considerações sobre segurança

### <a name="network"></a>Rede
O aplicativo de exemplo usa a criptografia SSL de ponta a ponta para dados em trânsito que fluem para dentro e para fora da rede. O gateway é configurado com um certificado autoassinado.
> [!IMPORTANT]
> Um certificado autoassinado é usado nesta demonstração. Em um ambiente de produção, você deve obter certificados de uma AC (autoridade de certificação) verificada.

O Firewall do aplicativo também inspeciona o tráfego de entrada e alerta os administradores quando o tráfego mal-intencionado é detectado no tráfego de rede.
O gateway de aplicativo atenua a possibilidade de ameaças de DDoS e de injeção de SQL descobertas no modelo de ameaça.

### <a name="identity"></a>Identidade
Para entrar no portal, o aplicativo de exemplo usa a autenticação multifator para administradores do Azure Active Directory (Azure AD) que recebem acesso aos recursos.
O aplicativo de exemplo usa identidades gerenciadas para obter permissões para ler e recuperar segredos de Azure Key Vault, garantindo que o aplicativo não precise codificar credenciais e tokens para ler os segredos. O Azure AD cria automaticamente as entidades de serviço que o aplicativo precisa para ler e modifica os segredos quando identidades gerenciadas são usadas.

Identidades gerenciadas para recursos do Azure e MFA tornam mais difícil para os adversários obter privilégios e escalonar seus privilégios no sistema. Essa ameaça foi apontada no modelo de ameaça.
O aplicativo usa o OAuth, que permite que os usuários registrados no aplicativo OAuth entrem no aplicativo.

### <a name="storage"></a>Armazenamento
O banco de dados PostgreSQL é criptografado em repouso automaticamente pelo Azure Database para PostgreSQL. O banco de dados autoriza os endereços IP do serviço de aplicativo para que somente o aplicativo Web do serviço de aplicativo implantado possa acessar os recursos do banco de dados com as credenciais de autenticação corretas.

### <a name="logging-and-auditing"></a>Registro em log e auditoria
O aplicativo implementa o registro em log usando Application Insights para rastrear métricas, logs e exceções que ocorrem. Esse registro em log fornece metadados de aplicativo suficientes para informar os desenvolvedores e os membros da equipe de operações sobre o status do aplicativo. Ele também fornece dados suficientes para refazer o recapitular em caso de incidentes de segurança.

## <a name="cost-considerations"></a>Considerações de custo
Se você ainda não tiver uma conta do Azure, poderá criar uma gratuita. Acesse a [página conta gratuita](https://azure.microsoft.com/free/) para começar, veja o que você pode fazer com uma conta gratuita do Azure e saiba quais produtos são gratuitos por 12 meses.

Para implantar os recursos no aplicativo de exemplo com os recursos de segurança, você precisa pagar por alguns recursos premium. Conforme o aplicativo é dimensionado e as camadas e avaliações gratuitas oferecidas pelo Azure precisam ser atualizadas para atender aos requisitos do aplicativo, seus custos podem aumentar. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) do Azure para estimar seus custos.

## <a name="deploy-the-solution"></a>Implantar a solução
### <a name="prerequisites"></a>Pré-requisitos
Para colocar o aplicativo em funcionamento, você precisa instalar essas ferramentas:

- Um editor de código para modificar e exibir o código do aplicativo. [Visual Studio Code](https://code.visualstudio.com/) é uma opção de código-fonte aberto.
- [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) em seu computador de desenvolvimento.
- [Git](https://git-scm.com/) em seu sistema. O Git é usado para clonar o código-fonte localmente.
- [JQ](https://stedolan.github.io/jq/), uma ferramenta UNIX para consultar o JSON de maneira amigável.

Você precisa de uma assinatura do Azure para implantar os recursos do aplicativo de exemplo. Se você não tiver uma assinatura do Azure, poderá [criar uma conta gratuita](https://azure.microsoft.com/en-us/free/) para testar o aplicativo de exemplo.

Depois de instalar essas ferramentas, você estará pronto para implantar o aplicativo no Azure.

### <a name="environment-setup"></a>Configuração do ambiente
Execute os scripts de implantação para configurar o ambiente e a assinatura:

1. Para clonar o repositório de código-fonte, use este comando git:

   ``` git
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```
2. Para ir para o diretório, use este comando:

   ```
   cd tutorial-project/scripts
   ```

3. Há arquivos na pasta scripts que são específicos para a plataforma que você está usando (Windows ou Linux). Como o CLI do Azure já foi instalado, entre na conta do Azure no prompt de comando executando este comando da CLI:

   ``` azurecli
   az login
   ```

O navegador será aberto, entre com suas credenciais. Depois de entrar, você pode começar a implantar os recursos do prompt de comando.

Os scripts `deploy-powershell.ps1` de implantação `deploy-bash.sh` e contêm código que implanta todo o aplicativo.
Para implantar a solução:

1. Se você estiver no PowerShell, execute `deploy-powershell.ps1` o arquivo digitando `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` substituindo a região e o nome do grupo de recursos por regiões do Azure adequadas e um nome para o grupo de recursos
2. Se você estiver no Linux, execute `deploy-bash.sh` o arquivo digitando `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`, pode ser necessário tornar o arquivo executável digitando`chmod +x deploy-bash.sh`

Os exemplos a seguir mostram trechos de código dos componentes principais. Você pode implantar os exemplos individualmente ou com o restante dos componentes executando os arquivos de implantação.

### <a name="implementation-guidance"></a>Diretrizes de implementação
O script de implantação é um script que pode ser dividido em quatro fases. Cada fase implanta e configura um recurso do Azure que está no [diagrama da arquitetura](#architecture).

As quatro fases são:

- Implantar Azure Key Vault.
- Implante o banco de dados do Azure para PostgreSQL.
- Implantar aplicativos Web do Azure no Linux.
- Implante o gateway de aplicativo com o Firewall do aplicativo Web.

Cada fase se baseia na anterior, usando a configuração dos recursos implantados anteriormente.

Para concluir as etapas de implementação, verifique se você instalou as ferramentas listadas em [pré-requisitos](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Implantar Azure Key Vault
Nesta seção, você cria e implanta uma instância de Azure Key Vault que é usada para armazenar segredos e certificados.

Depois de concluir a implantação, você terá uma instância Azure Key Vault implantada no Azure.

Para implantar Azure Key Vault usando CLI do Azure:

1. Declare as variáveis para Azure Key Vault.
2. Registre o provedor de Azure Key Vault.
3. Crie o grupo de recursos para a instância.
4. Crie a instância de Azure Key Vault no grupo de recursos criado na etapa 3.

   ``` azurecli

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```
É uma prática recomendada usar identidades gerenciadas para recursos do Azure em aplicativos que usam Key Vault para acessar recursos. Sua postura de segurança aumenta quando as chaves de acesso para Key Vault não são armazenadas no código ou na configuração.

#### <a name="deploy-azure-database-for-postgresql"></a>Implantar o banco de dados do Azure para PostgreSQL
O banco de dados do Azure para PostgreSQL funciona da seguinte maneira, primeiro crie o servidor de banco de dados e, em seguida, crie o banco de dados no qual armazenar o esquema e o dado.

Depois de concluir a implantação, você tem um servidor e banco de dados PostgreSQL em execução no Azure.

Para implantar o banco de dados do Azure para PostgreSQL usando CLI do Azure:

1. Abra um terminal com CLI do Azure e sua configuração de assinatura do Azure.
2. Gere uma combinação segura de nome de usuário e senha usada para acessar o banco de dados. (Eles devem ser armazenados em Azure Key Vault para aplicativos que os usam.)
3. Crie a instância do servidor PostgreSQL.
4. Crie um banco de dados na instância de servidor que você criou na etapa 3.
5. Execute scripts PostgreSQL na instância do PostgreSQL.

O código a seguir se baseia nos segredos PGUSERNAME e PGPASSWORD armazenados no Azure keyvault da etapa implantando keyvault acima.

   ``` azurecli
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

Depois de implantar o banco de dados, você precisa armazenar suas credenciais e a cadeia de conexão no Azure Key Vault.
Na pasta scripts, há um `functions.sql` arquivo que contém o código pl/pgsql que cria funções armazenadas quando você a executa. A execução desse arquivo parametriza as entradas para limitar a injeção de SQL.

O PostgreSQL é fornecido com uma ferramenta chamada `psql` que é usada para se conectar ao banco de dados. Para executar `functions.sql`o, você precisa se conectar à instância do banco de dados do Azure para PostgreSQL do computador local e executá-la a partir daí. A instalação da ferramenta psql está incluída na instalação padrão do PostgreSQL em cada sistema operacional.
Para obter mais informações, consulte a [documentação do psql](https://www.postgresql.org/docs/9.3/app-psql.html).

Azure cloud Shell também inclui a `psql` ferramenta. Você pode usar Cloud Shell diretamente da portal do Azure selecionando o ícone de Cloud Shell.

Para habilitar o acesso remoto à instância do PostgreSQL, você precisa autorizar o endereço IP no PostgreSQL.
Você habilita esse acesso acessando a guia **segurança da conexão** , selecionando **Adicionar IP do cliente**e salvando as novas configurações.

![Autorizar IP do cliente](./media/secure-web-app/add-client-ip-postgres.png)

Se você estiver usando Cloud Shell em vez da ferramenta psql local, selecione **permitir acesso aos serviços do Azure** e altere seu valor para **ativado** para permitir o acesso de Cloud Shell.

Em seguida, conecte-se à instância executando o comando psql abaixo com parâmetros de cadeia de conexão da guia cadeias de **conexão** da instância PostgreSQL no portal do Azure.
Substitua as chaves vazias por parâmetros da folha cadeia de conexão do banco de dados e a senha pela senha de Azure Key Vault.

```sql
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Execute o seguinte script PL/pgSQL depois de se certificar de que você está conectado ao banco de dados. O script cria as funções armazenadas usadas para inserir dados no banco de dado.

```sql
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;


CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```


Para obter mais informações sobre como configurar SSL e verificação de AC (autoridade de certificação) para PostgreSQL, consulte [Configurar a conectividade SSL no banco de dados do Azure para PostgreSQL](https://docs.microsoft.com/en-us/azure/postgresql/concepts-ssl-connection-security).

Um certificado raiz está incluído no contêiner. As etapas usadas para obter o certificado são:

1. Baixe o arquivo de certificado da [autoridade de certificação](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Baixe e instale o OpenSSL em seu computador](https://docs.microsoft.com/en-us/azure/postgresql/concepts-ssl-connection-security#download-and-install-openssl-on-your-machine).
3. Decodifique seu arquivo de certificado:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Leia mais sobre como configurar a segurança SSL para PostgreSQL aqui [Configurar a segurança de conexão SSL](https://docs.microsoft.com/en-gb/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Implantar aplicativos Web do Azure no Linux
Você pode facilmente criar serviços do Linux com base no serviço Azure App, pois o Azure fornece um conjunto de contêineres e imagens pré-criados para linguagens amplamente usadas, C#como Python, Ruby, e Java. O Azure também dá suporte a contêineres personalizados, que podem permitir que praticamente todas as linguagens de programação sejam executadas na plataforma de serviço de Azure App.

O aplicativo que está sendo implantado é um aplicativo Python simples que é executado na distribuição de Ubuntu Linux mais recente. Ele se conecta às instâncias Azure Key Vault e PostgreSQL que foram criadas nas seções anteriores para o gerenciamento de credenciais e o armazenamento de dados, respectivamente.

O seguinte arquivo do Docker é fornecido na pasta raiz do aplicativo:

``` docker
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

O Dockerfile acima é usado para criar o contêiner que está hospedado no registro de contêiner do Azure `mcr.microsoft.com/samples/basic-linux-app`em.

O código abaixo:

1. Declara as variáveis e os nomes da instância do serviço de aplicativo.
2. Cria o grupo de recursos para o plano do serviço de aplicativo.
3. Provisiona uma instância de aplicativos Web do Azure em contêineres do Linux.
4. Habilita o registro em log para o contêiner do aplicativo Web.
5. Define algumas configurações de aplicativo nas configurações do aplicativo do contêiner.

   ```
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }

   ```

Esse script cria uma identidade atribuída para a instância do serviço de aplicativo que pode ser usada com o MSI para interagir com Azure Key Vault sem segredos de codificação rígida no código ou na configuração.

Vá para a instância de Azure Key Vault no portal para autorizar a identidade atribuída na guia política de acesso. Selecione **Adicionar nova política de acesso**. Em **selecionar entidade de segurança**, procure o nome do aplicativo que é semelhante ao nome da instância do serviço de aplicativo criada.
Uma entidade de serviço anexada ao aplicativo deve estar visível. Selecione-a e salve a página política de acesso, conforme mostrado na captura de tela a seguir.

Como o aplicativo precisa apenas recuperar as chaves, selecione a permissão **obter** nas opções de segredos, permitindo o acesso enquanto reduz os privilégios concedidos.

![Política de acesso ao Key Vault](./media/secure-web-app/kv-access-policy.png)

*Criar uma política de acesso de Key Vault*

Salve a política de acesso e salve a nova alteração na guia **políticas de acesso** para atualizar as políticas.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Implantar o gateway de aplicativo com o Firewall do aplicativo Web habilitado
Em aplicativos Web, não é recomendável que você exponha serviços diretamente para o mundo exterior na Internet.
O balanceamento de carga e as regras de firewall fornecem mais segurança e controle sobre o tráfego de entrada e o ajudam a gerenciá-lo.

Para implantar uma instância do gateway de aplicativo:

1. Crie o grupo de recursos para alojar o gateway de aplicativo.
2. Provisione uma rede virtual para anexar ao gateway.
3. Crie uma sub-rede para o gateway na rede virtual.
4. Provisionar um endereço IP público.
5. Provisione o gateway de aplicativo.
6. Habilite o Firewall do aplicativo Web no gateway.

   ``` azurecli
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

O script anterior:

1. Cria um novo certificado autoassinado no Azure.
2. Baixa o certificado autoassinado como um arquivo codificado em base64.
3. Gera uma senha para o certificado autoassinado.
4. Exporta o certificado como um arquivo PFX assinado com a senha.
5. Armazena a senha do certificado no Azure Key Vault.

Esta seção implanta o gateway de aplicativo:

```powershell
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

Depois de concluir a implantação, você terá um gateway de aplicativo com o Firewall do aplicativo Web habilitado.

A instância de gateway expõe a porta 443 para HTTPS. Essa configuração garante que nosso aplicativo só esteja acessível na porta 443 por HTTPS.

Bloquear portas não utilizadas e limitar a exposição da superfície de ataque é uma prática recomendada de segurança.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Adicionar grupos de segurança de rede à instância do serviço de aplicativo

As instâncias do serviço de aplicativo podem ser integradas com redes virtuais. Essa integração permite que eles sejam configurados com políticas de grupo de segurança de rede que gerenciam o tráfego de entrada e saída do aplicativo.

1. Para habilitar esse recurso, na folha instância do serviço Azure App, em **configurações**, selecione **rede**. No painel direito, em **integração VNet**, selecione **clique aqui para configurar**.

   ![Nova integração de rede virtual](./media/secure-web-app/app-vnet-menu.png)

    *Nova integração de rede virtual para o serviço de aplicativo*
1. Na página seguinte, selecione **Adicionar VNET (versão prévia)** .

1. No menu avançar, selecione a rede virtual criada na implantação que começa com `hello-vnet`. Você pode criar uma nova sub-rede ou selecionar uma existente.
   Nesse caso, crie uma nova sub-rede. Defina o **intervalo de endereços** como **10.0.3.0/24** e nomeie a sub-rede **app-subnet**.

   ![Configuração de rede virtual do serviço de aplicativo](./media/secure-web-app/app-vnet-config.png)

    *Configuração de rede virtual para o serviço de aplicativo*

Agora que você habilitou a integração de rede virtual, você pode adicionar grupos de segurança de rede ao nosso aplicativo.

1. Use a caixa de pesquisa, procure **grupos de segurança de rede**. Selecione **grupos de segurança de rede** nos resultados.

    ![Pesquisar grupos de segurança de rede](./media/secure-web-app/nsg-search-menu.png)

    *Pesquisar grupos de segurança de rede*

2. No menu avançar, selecione **Adicionar**. Insira o **nome** do NSG e o **grupo de recursos** no qual ele deve ser localizado. Este NSG será aplicado à sub-rede do gateway de aplicativo.

    ![Criar um NSG](./media/secure-web-app/nsg-create-new.png)

    *Criar um NSG*

3. Depois que o NSG for criado, selecione-o. Em sua folha, em **configurações**, selecione **regras de segurança de entrada**. Defina essas configurações para permitir conexões que entram no gateway de aplicativo pela porta 443.

   ![Configurar o NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Configurar o NSG*

4. Nas regras de saída para o NSG do gateway, adicione uma regra que permita conexões de saída para a instância do serviço de aplicativo Criando uma regra que se `AppService`destina à marca de serviço:

   ![Adicionar regras de saída para o NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Adicionar regras de saída para o NSG*

    Adicione outra regra de saída para permitir que o gateway envie regras de saída para uma rede virtual.

   ![Adicionar outra regra de saída](./media/secure-web-app/nsg-outbound-vnet.png)

    *Adicionar outra regra de saída*

5. Na folha sub-redes do NSG, selecione **associar**, selecione a rede virtual criada na implantação e selecione a sub-rede de gateway denominada **GW-subnet**. O NSG é aplicado à sub-rede.

6. Crie outro NSG como na etapa anterior, desta vez para a instância do serviço de aplicativo. Dê um nome a ele. Adicione a regra de entrada para a porta 443, como fez para o NSG do gateway de aplicativo.

   Se você tiver uma instância do serviço de aplicativo implantada em uma instância do Ambiente do Serviço de Aplicativo, que não é o caso desse aplicativo, você poderá adicionar regras de entrada para permitir investigações de integridade do serviço do Azure abrindo as portas 454-455 nos grupos de segurança de entrada de seu serviço de aplicativo NSG. Aqui está a configuração:

   ![Adicionar regras para investigações de integridade do serviço do Azure](./media/secure-web-app/nsg-create-healthprobes.png)

    *Adicionar regras para investigações de integridade do serviço do Azure (somente Ambiente do Serviço de Aplicativo)*

7. Nas regras de segurança de saída, crie uma nova regra de segurança de saída que permita que a instância do serviço de aplicativo se comunique com o banco de dados PostgreSQL. Configure-o da seguinte maneira:

   ![Regra para permitir conexões do PostgreSQL de saída](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Adicionar uma regra para permitir conexões de saída do PostgreSQL*

Para limitar a superfície de ataque, modifique as configurações de rede do serviço de aplicativo para permitir que apenas o gateway de aplicativo acesse o aplicativo.
Para fazer isso, vá para a guia rede do serviço de aplicativo, selecione a guia **restrições de IP** e crie uma regra de permissão que permita que apenas o IP do gateway de aplicativo acesse diretamente o serviço.

Você pode recuperar o endereço IP do gateway de sua página de visão geral. Na guia **CIDR endereço IP** , insira o endereço IP neste formato: `<GATEWAY_IP_ADDRESS>/32`.

![Permitir somente o gateway](./media/secure-web-app/app-allow-gw-only.png)

*Permitir que somente o IP do gateway acesse o serviço de aplicativo*


#### <a name="implement-azure-active-directory-oauth"></a>Implementar Azure Active Directory OAuth

Os documentos do Azure distribuídos na página de aplicativo Web de exemplo são recursos em nosso aplicativo que podem precisar de proteção. Você pode usar o Azure Active Directory (Azure AD) para implementar a autenticação para aplicativos Web, desktop e móveis usando fluxos de autenticação diferentes.
O aplicativo usa **logon com a Microsoft**, que permite ao aplicativo ler perfis de usuários que foram adicionados à lista de usuários do Azure AD de locatário único.

No portal do Azure, configure o aplicativo para usar as credenciais necessárias:

1. Selecione **Azure Active Directory**ou pesquise por ele usando a caixa de pesquisa.

2. Selecione **novo registro**:

   ![Criar um registro](./media/secure-web-app/ad-auth-create.png)

   *Criar um registro de aplicativo do Azure AD*

3. Na página seguinte, insira o nome do aplicativo. Em **tipos de conta com suporte**, selecione **contas somente neste diretório organizacional**.
    Em **URI**de redirecionamento, insira o domínio base em que o aplicativo será executado mais um com o ponto de extremidade do token. Por exemplo:  *GATEWAY_HASH*. cloudapp.net/token.

   ![Configurar o registro de aplicativo do Azure AD](./media/secure-web-app/ad-auth-type.png)

   *Configurar o registro de aplicativo do Azure AD*

4. Você verá uma tela que mostra o aplicativo registrado e suas informações. Você precisa adicionar essas informações à instância de Azure Key Vault.
   1. Copie a ID do aplicativo (cliente) e salve-a em `CLIENTID`Key Vault como.
   2. Copie o URI de redirecionamento que você inseriu na etapa anterior e `REDIRECTURI`salve-o como.
   3. Copie o nome do diretório padrão do Azure AD, que tem o formato *Name*. microsoftonline.com, e salve-o `TENANT`em Key Vault como.
   4. Vá para a guia **certificados & segredos** do aplicativo do Azure AD que você criou anteriormente e selecione **novo segredo do cliente**, conforme mostrado na captura de tela a seguir. Defina uma data de expiração e, em seguida, copie o valor gerado e salve `CLIENTSECRET`-o em Key Vault como.

      ![Segredo de autorização do Azure AD](./media/secure-web-app/ad-auth-secrets.png)

      *Segredo de autorização do Azure AD*

   5. Gere uma chave secreta aleatória segura usando qualquer ferramenta de linha de comando/online. Salve-o em Key Vault `FLASKSECRETKEY`como. A estrutura do aplicativo usa essa chave para criar sessões.
        Para saber como gerar uma chave secreta, consulte [sessões do Flask](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. Depois de configurar a entrada, você precisa adicionar usuários ao link do Azure AD para permitir que eles entrem no recurso. Para adicioná-los, vá para a guia **usuários** no Azure AD, selecione **todos os usuários**e, em seguida, selecione **novo usuário** ou **novo usuário convidado**. Para teste, você pode adicionar um usuário convidado e convidar o usuário para o diretório. Ou você pode adicionar um novo usuário se o domínio no qual o aplicativo está sendo executado tiver sido validado. Neste exemplo, somente os usuários registrados no locatário do Azure AD podem ser registrados para acesso. Para obter informações sobre o acesso de entrada multilocatário, consulte a documentação.

   ![Adicionar usuários ao domínio padrão](./media/secure-web-app/ad-auth-add-user.png)

   *Adicionar usuários ao domínio de Azure Active Directory padrão*

Depois de adicionar a configuração e os segredos do Azure AD ao Key Vault, os usuários podem ser autenticados no aplicativo usando a autenticação OAuth do Azure.
No código do aplicativo, isso é tratado pela ADAL (biblioteca de autenticação Azure Active Directory).

Depois que os segredos estiverem em Key Vault e o aplicativo tiver acesso aos segredos e ao banco de dados, o serviço de aplicativo poderá ser acessado por meio https://GATEWAY_HASH.cloudapp.net) da URL do aplicativo do gateway (, que pode ser obtida em sua folha.

Se, ao entrar no Azure AD, você receber um erro dizendo "o usuário não está registrado no diretório no qual você está tentando fazer logon", você precisa adicionar o usuário. Para adicionar o usuário, vá para a guia **usuários** do Azure AD e adicione o usuário manualmente inserindo seus detalhes ou convide o usuário inserindo seu endereço de email como um usuário convidado no Azure ad na folha **convidar convidado** .

#### <a name="deploy-application-insights"></a>Implantar Application Insights
Agora que o aplicativo está implantado e funcionando, você precisa manipular os erros que ocorrem dentro do aplicativo, juntamente com a coleta de dados de log e rastreamento.
O log e a coleta de dados de rastreamento fornecem uma exibição dos eventos de auditoria que ocorrem no aplicativo.

Application Insights é um serviço que coleta logs que podem ser gerados por usuários ou pelo sistema.

Para criar uma instância de Application Insights:

1. Pesquise **Application insights** usando a caixa de pesquisa na portal do Azure.
2. Selecione **Application Insights**. Forneça os detalhes mostrados aqui para criar uma instância.

   ![Criar uma instância de Application Insights](./media/secure-web-app/app-insights-data.png)

Depois que a implantação for concluída, você terá uma instância de Application Insights.

Depois de criar a instância do Application insights, você precisa fazer com que o aplicativo reconheça a chave de instrumentação que permite enviar logs para a nuvem. Você faz isso recuperando a chave de Application Insights e usando-a nas bibliotecas de aplicativos que o Azure fornece para Application Insights. A prática recomendada é armazenar chaves e segredos em Azure Key Vault para mantê-los seguros.

Para o aplicativo de exemplo básico, depois de criar a instância do Application insights, você precisa fazer com que o aplicativo reconheça a chave de instrumentação que permite enviar logs para a nuvem.
Em Key Vault, defina um `APPINSIGHTSKEY` segredo e defina seu valor como a chave de instrumentação. Isso permite que o aplicativo envie logs e métricas para Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementar a autenticação multifator para Azure Active Directory
Os administradores precisam garantir que as contas de assinatura no portal sejam protegidas. A assinatura é vulnerável a ataques porque gerencia os recursos que você criou. Para proteger a assinatura, habilite a autenticação multifator na guia **Azure Active Directory** da assinatura.

O Azure AD opera com base em políticas que são aplicadas a um usuário ou grupos de usuários que se ajustam a determinados critérios.
O Azure cria uma política padrão especificando que os administradores precisam de autenticação de dois fatores para entrar no Portal.
Depois de habilitar essa política, você poderá ser solicitado a sair e entrar novamente no portal do Azure.

Para habilitar a MFA para entradas de administrador:

1. Vá para a guia **Azure Active Directory** na portal do Azure
2. Na categoria segurança, selecione acesso condicional. Você verá esta tela:

   ![Acesso condicional-políticas](./media/secure-web-app/ad-mfa-conditional-add.png)

Se você não puder criar uma nova política:

1. Vá para a guia **MFA** .
2. Selecione o link Azure AD Premium **avaliação gratuita** para assinar a avaliação gratuita.

   ![Azure AD Premium avaliação gratuita](./media/secure-web-app/ad-trial-premium.png)

Retorne à tela de acesso condicional.

1. Selecione a guia nova política.
2. Insira o nome da política.
3. Selecione os usuários ou grupos para os quais você deseja habilitar a MFA.
4. Em **controles de acesso**, selecione a guia **conceder** e, em seguida, selecione **exigir autenticação** multifator (e outras configurações, se desejar).

   ![Exigir MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

Você pode habilitar a política marcando a caixa de seleção na parte superior da tela ou fazer isso na guia **acesso condicional** . Quando a política está habilitada, os usuários precisam de MFA para entrar no Portal.

Há uma política de linha de base que requer MFA para todos os administradores do Azure. Você pode habilitá-lo imediatamente no Portal. A habilitação dessa política pode invalidar a sessão atual e forçá-lo a entrar novamente.

Se a política de linha de base não estiver habilitada:
1.  Selecione **exigir MFA para administradores**.
2.  Selecione **usar política imediatamente**.

   ![Selecione usar política imediatamente](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Usar o Azure Sentinel para monitorar aplicativos e recursos

À medida que um aplicativo cresce, torna-se difícil agregar todos os sinais de segurança e métricas recebidos dos recursos e torná-los úteis de forma orientada a ações.

O Azure Sentinel foi projetado para coletar dados, detectar os tipos de ameaças possíveis e fornecer visibilidade de incidentes de segurança.
Enquanto ele aguarda a intervenção manual, o Azure Sentinel pode contar com guias estratégicos pré-gravados para iniciar os alertas e os processos de gerenciamento de incidentes.

O aplicativo de exemplo é composto por vários recursos que o Azure Sentinel pode monitorar.
Para configurar o Azure Sentinel, primeiro você precisa criar um espaço de trabalho Log Analytics que armazena todos os dados coletados de vários recursos.

Para criar este espaço de trabalho:

1. Na caixa de pesquisa na portal do Azure, procure **log Analytics**. Escolha **workspaces do Log Analytics**.

   ![Pesquisar espaços de trabalho do Log Analytics](./media/secure-web-app/sentinel-log-analytics.png)

    *Pesquisar espaços de trabalho do Log Analytics*

2. Na página seguinte, selecione **Adicionar** e forneça um nome, um grupo de recursos e um local para o espaço de trabalho.
   ![Criar um espaço de trabalho do Log Analytics](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Criar um espaço de trabalho do Log Analytics*

3. Use a caixa de pesquisa para pesquisar o **Azure Sentinel**.

   ![Pesquisar pelo Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *Pesquisar pelo Azure Sentinel*

4. Selecione **Adicionar** e, em seguida, selecione o log Analytics espaço de trabalho que você criou anteriormente.

   ![Adicionar um espaço de trabalho Log Analytics](./media/secure-web-app/sentinel-workspace-add.png)

    *Adicionar um espaço de trabalho Log Analytics*

5. Na página **Azure Sentinel-conectores de dados** , em **configuração**, selecione conectores de **dados**. Você verá uma matriz de serviços do Azure que você pode vincular à instância de armazenamento Log Analytics para análise no Azure Sentinel.

   ![Conectores de dados Log Analytics](./media/secure-web-app/sentinel-connectors.png)

    *Adicionar um conector de dados ao Azure Sentinel*

   Por exemplo, para conectar o gateway de aplicativo, execute estas etapas:

   1. Abra a folha da instância do gateway Aplicativo Azure.
   2. Em **Monitoramento**, selecione **Configurações de diagnóstico**.
   3. Selecione **Adicionar configuração de diagnóstico**.

      ![Adicionar diagnóstico do gateway de aplicativo](./media/secure-web-app/sentinel-gateway-connector.png)

      *Adicionar diagnóstico do gateway de aplicativo*

   4. Na página **configurações de diagnóstico** , selecione o espaço de trabalho log Analytics que você criou e, em seguida, selecione todas as métricas que deseja coletar e enviar para o Azure Sentinel. Clique em **Salvar**.

        ![Configurações do conector do Azure Sentinel](./media/secure-web-app/sentinel-connector-settings.png)

        *Configurações do conector do Azure Sentinel*

  As métricas do recurso estão no Azure Sentinel, onde você pode consultá-las e investigue-as.

   Adicione as mesmas métricas nas configurações de diagnóstico para Azure Key Vault, o endereço IP público, o banco de dados do Azure para PostgreSQL e quaisquer serviços que ofereçam suporte a logs de diagnóstico em sua conta.

Depois de configurar as métricas, o Azure Sentinel tem dados para analisar.

## <a name="evaluate-and-verify"></a>Avaliar e verificar
Depois de desenvolver e implantar a arquitetura, você precisa garantir que o código e os serviços implantados atendam aos padrões de segurança. Estas são algumas etapas que você pode executar para verificar o software:

- Análise de código estático
- Verificação de vulnerabilidade
- Localizando e corrigindo vulnerabilidades em dependências de aplicativos

Esses são os blocos de construção básicos para as práticas recomendadas em desenvolvimento seguro.

### <a name="static-code-analysis"></a>Análise de código estático
Para o aplicativo de exemplo, a verificação com ferramentas de análise estática envolve a localização de vulnerabilidades no código do aplicativo usando técnicas como verificação de documentos e análise de fluxo de dados. As ferramentas de análise estática do Python dão a você mais confiança de que seu aplicativo é seguro.

**Lint**

PyFlakes, uma biblioteca de fiapos do Python, ajuda você a remover o código inativo e as funções não utilizadas de aplicativos, como mostrado aqui:

![PyFlakes](./media/secure-web-app/pyflakes.png)

O refiapoing fornece dicas e possíveis alterações que podem tornar seu código mais limpo e menos propenso a erros durante o tempo de execução.

**PyLint**

PyLint forneceu o maior valor para este projeto. Ele executa verificações padrão de código, verificação de erros e dicas de refatoração para garantir que o código em execução no servidor seja seguro. Usando o PyLint para atualizar seu código, você pode eliminar bugs e melhorar a classificação de PyLint, conforme mostrado nas imagens a seguir.

![Antes de PyLint](./media/secure-web-app/before-pylint.png)

*Antes de PyLint*

Depois de corrigir alguns dos erros de código encontrados pelas ferramentas de revestido, você tem mais confiança que o código não está propenso a erros. Corrigir os erros reduz significativamente os riscos de segurança que podem ocorrer quando o código é implantado em ambientes de produção.

![Após Pylint](./media/secure-web-app/after-pylint.png)

*Após PyLint*

### <a name="vulnerability-scanning"></a>Verificação de vulnerabilidade
A ferramenta [zap de OWASP](https://www.zaproxy.org/) é um verificador de vulnerabilidade de aplicativo Web de software livre que você pode usar para verificar se há vulnerabilidades no aplicativo de exemplo. A execução da ferramenta no aplicativo de exemplo revela alguns possíveis erros e vetores de ataque.

![Ferramenta ZAP](./media/secure-web-app/zap-tool.png)

*Ferramenta ZAP*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Localizar e corrigir vulnerabilidades em dependências de aplicativo
Para localizar e corrigir dependências de aplicativo, você pode usar a [verificação de dependência do OWASP](https://www.owasp.org/index.php/OWASP_Dependency_Check).

A segurança é um aplicativo semelhante que verifica as dependências. Você pode encontrá-lo no [GitHub](https://github.com/pyupio/safety). Verificações de segurança de vulnerabilidades encontradas em bancos de dados de vulnerabilidade bem conhecidas.

![Multi-usuário](./media/secure-web-app/pysafety.png)

*Multi-usuário*

## <a name="next-steps"></a>Próximas etapas
Os artigos a seguir podem ajudá-lo a projetar, desenvolver e implantar aplicativos seguros.

- [Design](secure-design.md)
- [Desenvolver](secure-develop.md)
- [Implantar](secure-deploy.md)
