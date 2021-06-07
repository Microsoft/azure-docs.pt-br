---
title: Instalar e desenvolver localmente com o emulador Azure Cosmos DB
description: Saiba como instalar e usar o emulador de Azure Cosmos DB em ambientes Windows, Linux, macOS e Windows Docker. Usando o emulador, você pode desenvolver e testar seu aplicativo localmente gratuitamente, sem criar uma assinatura do Azure.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/22/2020
ms.custom: devx-track-csharp, contperf-fy21q1
ms.openlocfilehash: 89e82e210dbf7c404ebd5978d87bf391d8565ee2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97033928"
---
# <a name="install-and-use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Instalar e usar o emulador de Azure Cosmos DB para desenvolvimento e teste locais
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Emulador do Azure Cosmos DB fornece um ambiente local que emula o serviço Azure Cosmos DB para fins de desenvolvimento. Com o Emulador do Azure Cosmos DB, você pode desenvolver e testar seu aplicativo localmente sem criar uma assinatura Azure ou incorrer em custos. Quando estiver satisfeito com o funcionamento do aplicativo no emulador de Azure Cosmos DB, você poderá alternar para o uso de uma conta do Azure Cosmos na nuvem. Este artigo descreve como instalar e usar o emulador em ambientes Windows, Linux, macOS e Windows Docker.

## <a name="download-the-emulator"></a>Baixar o emulador

Para começar, baixe e instale a versão mais recente do emulador de Azure Cosmos DB no computador local. O artigo [notas de versão do emulador](local-emulator-release-notes.md) lista todas as versões disponíveis e as atualizações de recursos que foram feitas em cada versão.

:::image type="icon" source="media/local-emulator/download-icon.png" border="false":::**[Baixar o emulador de Azure Cosmos DB](https://aka.ms/cosmosdb-emulator)**

Você pode desenvolver aplicativos usando o emulador Azure Cosmos DB com as contas [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api)e API de [tabela](local-emulator.md#table-api) . Atualmente, o data Explorer no emulador dá suporte total apenas à exibição de dados SQL; os dados criados usando MongoDB, Gremlin/Graph e aplicativos cliente Cassandra não são visíveis neste momento. Para saber mais, confira [como se conectar ao ponto de extremidade do emulador](#connect-with-emulator-apis) de APIs diferentes.

## <a name="how-does-the-emulator-work"></a>Como funciona o emulador?

O Emulador do Azure Cosmos DB fornece uma emulação de alta fidelidade do serviço Azure Cosmos DB. Ele dá suporte à funcionalidade equivalente à Azure Cosmos DB, que inclui a criação de dados, consulta de dados, provisionamento e dimensionamento de contêineres e execução de procedimentos armazenados e gatilhos. Você pode desenvolver e testar aplicativos usando o emulador de Azure Cosmos DB e implantá-los no Azure em escala global atualizando o ponto de extremidade de conexão Azure Cosmos DB.

Embora a emulação do serviço do Azure Cosmos DB seja fiel, a implementação do emulador é diferente do serviço. Por exemplo, o emulador usa componentes padrão do SO, como sistema de arquivos local para persistência e pilha do protocolo HTTPS para conectividade. A funcionalidade que depende da infraestrutura do Azure, como replicação global, latência de milissegundos de dígito único para leituras/gravações e níveis de consistência ajustáveis, não é aplicável quando você usa o emulador.

Você pode migrar dados entre o emulador de Azure Cosmos DB e o serviço de Azure Cosmos DB usando a [ferramenta de migração de dados do Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

## <a name="differences-between-the-emulator-and-the-cloud-service"></a>Diferenças entre o emulador e o serviço de nuvem

Como o emulador de Azure Cosmos DB fornece um ambiente emulado que é executado na estação de trabalho do desenvolvedor local, há algumas diferenças na funcionalidade entre o emulador e uma conta do Azure Cosmos na nuvem:

* Atualmente, o painel de **Data Explorer** no emulador dá suporte total apenas a clientes da API do SQL. A exibição de **Data Explorer** e as operações para apis de Azure Cosmos DB como MongoDB, tabela, grafo e APIs Cassandra não são totalmente suportadas.

* O emulador dá suporte a apenas uma única conta fixa e uma chave primária conhecida. Você não pode regenerar a chave ao usar o emulador de Azure Cosmos DB, no entanto, você pode alterar a chave padrão usando a opção de [linha de comando](emulator-command-line-parameters.md) .

* Com o emulador, você pode criar uma conta do Azure Cosmos somente no modo de [taxa de transferência provisionado](set-throughput.md) ; Atualmente, ele não dá suporte ao modo sem [servidor](serverless.md) .

* O emulador não é um serviço escalonável e não dá suporte a um grande número de contêineres. Ao usar o emulador de Azure Cosmos DB, por padrão, você pode criar até 25 contêineres de tamanho fixo em 400 RU/s (somente com suporte usando SDKs de Azure Cosmos DB) ou 5 contêineres ilimitados. Para obter mais informações sobre como alterar esse valor, consulte [set the PartitionCount Value](emulator-command-line-parameters.md#set-partitioncount) article.

* O emulador não oferece diferentes [níveis de consistência de Azure Cosmos DB](consistency-levels.md) , como o serviço de nuvem.

* O emulador não oferece [replicação de várias regiões](distribute-data-globally.md).

* Como a cópia do seu emulador de Azure Cosmos DB talvez nem sempre esteja atualizada com as alterações mais recentes no serviço de Azure Cosmos DB, você deve sempre consultar o [Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md) para estimar com precisão as necessidades de taxa de transferência (RUs) do seu aplicativo.

* O emulador dá suporte a um tamanho de propriedade de ID máximo de 254 caracteres.

## <a name="install-the-emulator"></a>Instalar o emulador

Antes de instalar o emulador, verifique se você tem os seguintes requisitos de hardware e software:

* Requisitos de software:
  * No momento, há suporte para o SO Windows Server 2016, 2019 ou Windows 10 host. No momento, não há suporte para o sistema operacional host com Active Directory habilitado.
  * Sistema operacional de 64 bits

* Requisitos mínimos de hardware:
  * 2 GB de RAM
  * Espaço disponível no disco rígido de 10 GB

* Para instalar, configurar e executar o Emulador do Azure Cosmos DB, você deve ter privilégios administrativos no computador. O emulador adicionará um certificado e também definirá as regras de firewall para executar seus serviços. Portanto, os direitos de administrador são necessários para que o emulador possa executar essas operações.

Para começar, baixe e instale a versão mais recente do [emulador de Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) no computador local. Se você encontrar algum problema ao instalar o emulador, consulte o artigo [solução de problemas do emulador](troubleshoot-local-emulator.md) para depurar.

Dependendo dos requisitos do sistema, você pode executar o emulador no [Windows](#run-on-windows), [Docker for Windows](#run-on-windows-docker), [Linux ou MacOS](#run-on-linux-macos) , conforme descrito nas próximas seções deste artigo.

## <a name="check-for-emulator-updates"></a>Verificar atualizações do emulador

Cada versão do emulador vem com um conjunto de atualizações de recursos ou correções de bugs. Para ver as versões disponíveis, leia o artigo [notas de versão do emulador](local-emulator-release-notes.md) .

Após a instalação, se você tiver usado as configurações padrão, os dados correspondentes ao emulador serão salvos no local%LOCALAPPDATA%\CosmosDBEmulator. Você pode configurar um local diferente usando as configurações de caminho de dados opcionais; Esse é o `/DataPath=PREFERRED_LOCATION` como o [parâmetro de linha de comando](emulator-command-line-parameters.md). Não há garantia de que os dados criados em uma versão do emulador de Azure Cosmos DB estejam acessíveis ao usar uma versão diferente. Se você precisar persistir seus dados por um longo prazo, é recomendável que você armazene esses dados em uma conta do Azure Cosmos, em vez do emulador de Azure Cosmos DB.

## <a name="use-the-emulator-on-windows"></a><a id="run-on-windows"></a>Usar o emulador no Windows

O emulador de Azure Cosmos DB é instalado no `C:\Program Files\Azure Cosmos DB Emulator` local por padrão. Para iniciar o emulador de Azure Cosmos DB no Windows, selecione o botão **Iniciar** ou pressione a tecla Windows. Comece a digitar **Emulador do Azure Cosmos DB** e selecione o emulador na lista de aplicativos.

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="Selecione o botão iniciar ou pressione a tecla Windows, comece digitando Azure Cosmos DB emulador e selecione o emulador na lista de aplicativos":::

Quando o emulador for iniciado, você verá um ícone na área de notificação da barra de tarefas do Windows. Ele abre automaticamente o Azure Cosmos data Explorer no seu navegador nesta URL de URL `https://localhost:8081/_explorer/index.html` .

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="Notificação na barra de tarefas do emulador local do Azure Cosmos DB":::

Você também pode iniciar e parar o emulador dos comandos da linha de comando ou do PowerShell. Para obter mais informações, consulte o artigo de [referência da ferramenta de linha de comando](emulator-command-line-parameters.md) .

O Emulador do Azure Cosmos DB, por padrão, é executado no computador local ("localhost") escutando na porta 8081. O endereço é exibido como `https://localhost:8081/_explorer/index.html`. Se você fechar o Explorer e quiser reabri-lo mais tarde, é possível abrir a URL no navegador ou iniciá-lo no Emulador do Azure Cosmos DB no Ícone de Bandeja do Windows, como mostrado abaixo.

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Iniciador do Data Explorer do emulador local do Azure Cosmos":::

## <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>Usar o emulador no Docker for Windows

Você pode executar o emulador de Azure Cosmos DB no contêiner do Docker do Windows. Consulte o [Hub do Docker](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) para o comando Docker pull e o [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) para obter as `Dockerfile` informações e mais. Atualmente, o emulador não funciona no Docker para Oracle Linux. Use as instruções a seguir para executar o emulador no Docker for Windows:

1. Depois de instalar o [Docker for Windows](https://www.docker.com/docker-windows) , alterne para contêineres do Windows clicando com o botão direito do mouse no ícone do Docker na barra de ferramentas e selecionando **alternar para contêineres do Windows**.

1. Em seguida, efetue pull da imagem do emulador do Hub do Docker, executando o comando a seguir do seu shell favorito.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. Para iniciar a imagem, execute os seguintes comandos dependendo da linha de comando ou do ambiente do PowerShell:

   # <a name="command-line"></a>[Linha de comando](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   As imagens do Docker baseadas no Windows podem não ser geralmente compatíveis com todos OS sistemas operacionais do host Windows. Por exemplo, a imagem de emulador de Azure Cosmos DB padrão é compatível apenas com o Windows 10 e o Windows Server 2016. Se você precisar de uma imagem compatível com o Windows Server 2019, execute o seguinte comando em vez disso:

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   A resposta tem aparência semelhante à seguinte:

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > Ao executar o `docker run` comando, se você vir um erro de conflito de porta (ou seja, se a porta especificada já estiver em uso), passe uma porta personalizada alterando os números de porta. Por exemplo, você pode alterar o parâmetro "-p 8081:8081" para "-p 443:8081"

1. Agora, use o ponto de extremidade do emulador e a chave primária da resposta e importe o certificado TLS/SSL para o host. Para importar o certificado TLS/SSL, execute as seguintes etapas em um prompt de comando de administrador:

   # <a name="command-line"></a>[Linha de comando](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. Se você fechar o shell interativo depois que o emulador for iniciado, ele desligará o contêiner do emulador. Para reabrir o data Explorer, navegue até a URL a seguir no navegador. O ponto de extremidade do emulador é fornecido na mensagem de resposta mostrada acima.

   `https://<emulator endpoint provided in response>/_explorer/index.html`

Se você tiver um aplicativo cliente .NET em execução em um contêiner do Docker do Linux e estiver executando o emulador Azure Cosmos DB em um computador host, use as instruções na próxima seção para importar o certificado para o contêiner do Docker do Linux.

### <a name="regenerate-the-emulator-certificates-when-running-on-a-docker-container"></a>Regenerar os certificados do emulador ao executar em um contêiner do Docker

Ao executar o emulador em um contêiner do Docker, os certificados associados ao emulador são regenerados toda vez que você parar e reiniciar o respectivo contêiner. Por causa disso, você precisa importar novamente os certificados após o início de cada contêiner. Para contornar essa limitação, você pode usar um arquivo Docker Compose para associar o contêiner do Docker a um endereço IP específico e uma imagem de contêiner.

Por exemplo, você pode usar a configuração a seguir no arquivo do Docker Compose, certifique-se de formatá-lo de acordo com seu requisito: 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="use-the-emulator-on-linux-or-macos"></a><a id="run-on-linux-macos"></a>Usar o emulador no Linux ou macOS

Atualmente, o emulador Azure Cosmos DB só pode ser executado no Windows. Se você estiver usando o Linux ou o macOS, poderá executar o emulador em uma máquina virtual do Windows hospedada em um hipervisor como, por exemplo, paralelos ou VirtualBox.

> [!NOTE]
> Sempre que você reiniciar a máquina virtual do Windows que está hospedada em um hipervisor, precisará reimportar o certificado porque o endereço IP da máquina virtual é alterado. A importação do certificado não é necessária caso você tenha configurado a máquina virtual para preservar o endereço IP.

Use as seguintes etapas para usar o emulador em ambientes Linux ou macOS:

1. Execute o seguinte comando na máquina virtual do Windows e anote o endereço IPv4:

   ```bash
   ipconfig.exe
   ```

1. Em seu aplicativo, altere a URL do ponto de extremidade para usar o endereço IPv4 retornado `ipconfig.exe` em vez de `localhost` .

1. Na VM do Windows, inicie o emulador Azure Cosmos DB na linha de comando usando as opções a seguir. Para obter detalhes sobre os parâmetros com suporte na linha de comando, consulte a [referência da ferramenta de linha de comando do emulador](emulator-command-line-parameters.md):

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   ```

1. Por fim, você precisa resolver o processo de confiança de certificado entre o aplicativo em execução no ambiente Linux ou Mac e o emulador. Você pode usar uma das duas opções a seguir para resolver o certificado:

   1. [Importar o certificado TLS/SSL do emulador para o ambiente Linux ou Mac](#import-certificate) ou
   2. [Desabilitar a validação de TLS/SSL no aplicativo](#disable-ssl-validation)

### <a name="option-1-import-the-emulator-tlsssl-certificate"></a><a id="import-certificate"></a>Opção 1: importar o certificado TLS/SSL do emulador

As seções a seguir mostram como importar o certificado TLS/SSL do emulador para ambientes Linux e macOS.

#### <a name="linux-environment"></a>Ambiente do Linux

Se você estiver trabalhando no Linux, o .NET será retransmitido no OpenSSL para realizar a validação:

1. [Exporte o certificado no formato PFX](local-emulator-export-ssl-certificates.md#export-emulator-certificate). A opção PFX está disponível ao optar por exportar a chave privada.

1. Copie esse arquivo PFX para seu ambiente Linux.

1. Converta o arquivo PFX em um arquivo CRT

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Copie o arquivo CRT para a pasta que contém certificados personalizados em sua distribuição do Linux. Normalmente, em distribuições do Debian, ela está localizada em `/usr/local/share/ca-certificates/`.

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Atualize os certificados TLS/SSL, que irão atualizar a `/etc/ssl/certs/` pasta.

   ```bash
   update-ca-certificates
   ```

#### <a name="macos-environment"></a>ambiente macOS

Use as seguintes etapas se estiver trabalhando no Mac:

1. [Exporte o certificado no formato PFX](local-emulator-export-ssl-certificates.md#export-emulator-certificate). A opção PFX está disponível ao optar por exportar a chave privada.

1. Copie esse arquivo PFX para seu ambiente Mac.

1. Abra o aplicativo de *Acesso do Conjunto de Chaves* e importe o arquivo PFX.

1. Abra a lista de certificados e identifique aquele com o nome `localhost`.

1. Abra o menu de contexto desse item específico, selecione *Obter Item* e, na opção *Confiar* > *Ao usar este certificado*, selecione *Sempre Confiar*. 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="Abra o menu de contexto desse item específico, selecione Obter Item e, na opção Confiar – Ao usar este certificado, selecione Sempre Confiar":::
  
### <a name="option-2-disable-the-ssl-validation-in-the-application"></a><a id="disable-ssl-validation"></a>Opção 2: desabilitar a validação de SSL no aplicativo

A desabilitação da validação SSL só é recomendada para fins de desenvolvimento e não deve ser feita durante a execução em um ambiente de produção. Os exemplos a seguir mostram como desabilitar a validação SSL para aplicativos .NET e Node.js.

# <a name="net-standard-21"></a>[.NET Standard 2.1+](#tab/ssl-netstd21)

Para qualquer aplicativo em execução em uma estrutura compatível com o .NET Standard 2,1 ou posterior, podemos aproveitar o `CosmosClientOptions.HttpClientFactory` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2.0](#tab/ssl-netstd20)

Para qualquer aplicativo em execução em uma estrutura compatível com o .NET Standard 2,0, podemos aproveitar o `CosmosClientOptions.HttpClientFactory` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.js](#tab/ssl-nodejs)

Para Node.js aplicativos, você pode modificar o `package.json` arquivo para definir o `NODE_TLS_REJECT_UNAUTHORIZED` ao iniciar o aplicativo:

```json
"start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
```
--- 

## <a name="enable-access-to-emulator-on-a-local-network"></a>Habilitar o acesso ao emulador em uma rede local

Se você tiver vários computadores usando uma única rede, e se configurar o emulador em um computador e quiser acessá-lo de outro computador. Nesse caso, você precisa habilitar o acesso ao emulador em uma rede local.

Você pode executar o emulador em uma rede local. Para habilitar o acesso à rede, especifique a opção `/AllowNetworkAccess` na [linha de comando](emulator-command-line-parameters.md), o que também requer que você especifique `/Key=key_string` ou `/KeyFile=file_name`. Você pode usar `/GenKeyFile=file_name` para gerar um arquivo com uma chave aleatória antecipadamente. Em seguida, você pode passá-lo para `/KeyFile=file_name` ou `/Key=contents_of_file`.

Para habilitar o acesso à rede pela primeira vez, o usuário deve desligar o emulador e excluir o diretório de dados do emulador *%LocalAppData%\CosmosDBEmulator*.

## <a name="authenticate-connections-when-using-emulator"></a><a id="authenticate-requests"></a>Autenticar conexões ao usar o emulador

Assim como no Azure Cosmos DB na nuvem, cada solicitação feita no Emulador do Azure Cosmos DB deve ser autenticada. O emulador de Azure Cosmos DB dá suporte apenas à comunicação segura via TLS. O emulador de Azure Cosmos DB dá suporte a uma única conta fixa e uma chave de autenticação conhecida para autenticação de chave primária. Essa conta e a chave são as únicas credenciais permitidas para uso com o Emulador do Azure Cosmos DB. São eles:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> A chave primária com suporte do emulador de Azure Cosmos DB é destinada para uso somente com o emulador. Você não pode usar sua conta do Azure Cosmos DB de produção e a chave com o Emulador do Azure Cosmos DB.

> [!NOTE]
> Se você tiver iniciado o emulador com a opção/Key, use a chave gerada em vez da chave padrão `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` . Para saber mais sobre a opção /Keym veja a [referência da ferramenta da linha de comando](emulator-command-line-parameters.md).

## <a name="connect-to-different-apis-with-the-emulator"></a><a id="connect-with-emulator-apis"></a>Conectar-se a APIs diferentes com o emulador

### <a name="sql-api"></a>API do SQL

Depois que o emulador de Azure Cosmos DB estiver em execução na área de trabalho, você poderá usar qualquer [SDK do Azure Cosmos DB](sql-api-sdk-dotnet-standard.md) com suporte ou a [API REST do Azure Cosmos DB](/rest/api/cosmos-db/) para interagir com o emulador. O emulador de Azure Cosmos DB também inclui um data Explorer interno que permite criar contêineres para a API do SQL ou Azure Cosmos DB para a API do Mongo DB. Usando o data Explorer, você pode exibir e editar itens sem escrever nenhum código.

```csharp
// Connect to the Azure Cosmos DB Emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

Depois que o emulador de Azure Cosmos DB estiver em execução na área de trabalho, você poderá usar a [API do Azure Cosmos DB para o MongoDB](mongodb-introduction.md) para interagir com o emulador. Inicie o emulador do [prompt de comando](emulator-command-line-parameters.md) como administrador com "/EnableMongoDbEndpoint". Em seguida, use a seguinte cadeia de conexão para se conectar à conta da API do MongoDB:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>API de Tabela

Depois que o emulador de Azure Cosmos DB estiver em execução na área de trabalho, você poderá usar o [SDK do API de tabela de Azure Cosmos DB](./tutorial-develop-table-dotnet.md) para interagir com o emulador. Inicie o emulador do [prompt de comando](emulator-command-line-parameters.md) como administrador com "/EnableTableEndpoint". Em seguida, execute o código a seguir para conectar-se à conta de API da tabela:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>API Cassandra

Inicie o emulador de um [prompt de comando](emulator-command-line-parameters.md) de administrador com "/EnableCassandraEndpoint". Como alternativa, você também pode definir a variável de ambiente `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`.

1. [Instalar o Python 2.7](https://www.python.org/downloads/release/python-2716/)

1. [Instalar Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

1. Execute os seguintes comandos em uma janela de prompt de comando regular:

   ```bash
   set Path=c:\Python27;%Path%
   cd /d C:\sdk\apache-cassandra-3.11.3\bin
   set SSL_VERSION=TLSv1_2
   set SSL_VALIDATE=false
   cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
   ```

1. No shell CQLSH, execute os comandos a seguir para se conectar ao ponto de extremidade do Cassandra:

   ```bash
   CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
   DESCRIBE keyspaces;
   USE mykeyspace;
   CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
   INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
   SELECT * from table1;
   EXIT
   ```

### <a name="gremlin-api"></a>API do Gremlin

Inicie o emulador de um [prompt de comando](emulator-command-line-parameters.md)de administrador com "/EnableGremlinEndpoint". Como alternativa, você também pode definir a variável de ambiente `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

1. [Instale o apache-tinkerpop-gremlin-console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

1. No data Explorer do emulador, crie um banco de dados "DB1" e uma coleção "coll1"; para a chave de partição, escolha "/Name"

1. Execute os seguintes comandos em uma janela de prompt de comando regular:

   ```bash
   cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
   copy /y conf\remote.yaml conf\remote-localcompute.yaml
   notepad.exe conf\remote-localcompute.yaml
     hosts: [localhost]
     port: 8901
     username: /dbs/db1/colls/coll1
     password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
     connectionPool: {
     enableSsl: false}
     serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
     config: { serializeResultToString: true  }}

   bin\gremlin.bat
   ```

1. No Shell Gremlin, execute os seguintes comandos para se conectar ao ponto de extremidade Gremlin:

   ```bash
   :remote connect tinkerpop.server conf/remote-localcompute.yaml
   :remote console
   :> g.V()
   :> g.addV('person1').property(id, '1').property('name', 'somename1')
   :> g.addV('person2').property(id, '2').property('name', 'somename2')
   :> g.V()
   ```

## <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>Desinstalar o emulador local

Use as seguintes etapas para desinstalar o emulador:

1. Saia de todas as instâncias abertas do emulador local clicando com o botão direito do mouse no ícone do **emulador de Azure Cosmos DB** na bandeja do sistema e selecione **sair**. Pode levar um minuto para que todas as instâncias saiam.

1. Na caixa de pesquisa do Windows, digite **aplicativos & recursos** e selecione **aplicativos & os recursos (configurações do sistema)** resultado.

1. Na lista de aplicativos, role até o **emulador Azure Cosmos DB**, selecione-o, clique em **desinstalar**, confirme e selecione **desinstalar** novamente.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o emulador local para o desenvolvimento local gratuito. Agora você pode prosseguir para os próximos artigos:

* [Exportar os certificados do emulador Azure Cosmos DB para uso com aplicativos Java, Python e Node.js](local-emulator-export-ssl-certificates.md)
* [Usar parâmetros de linha de comando e comandos do PowerShell para controlar o emulador](emulator-command-line-parameters.md)
* [Problemas de depuração com o emulador](troubleshoot-local-emulator.md)
