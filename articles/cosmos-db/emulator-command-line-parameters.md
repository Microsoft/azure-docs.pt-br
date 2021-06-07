---
title: Referência de linha de comando e PowerShell para emulador de Azure Cosmos DB
description: Conheça os parâmetros de linha de comando para Azure Cosmos DB Emulator, como controlar o emulador com o PowerShell e como alterar o número de contêineres que você pode criar dentro do emulador.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: e0c795484bf860402d05c1dc5779633962a44ec2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979497"
---
# <a name="command-line-and-powershell-reference-for-azure-cosmos-db-emulator"></a>Referência de linha de comando e PowerShell para emulador de Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O emulador de Azure Cosmos DB fornece um ambiente local que emula o serviço de Azure Cosmos DB para fins de desenvolvimento local. Depois [de instalar o emulador](local-emulator.md), você pode controlar o emulador com comandos de linha de comando e do PowerShell. Este artigo descreve como usar os comandos de linha de comando e do PowerShell para iniciar e parar o emulador, configurar opções e executar outras operações. Você precisa executar os comandos do local de instalação.

##  <a name="manage-the-emulator-with-command-line-syntax"></a><a id="command-line"></a>Gerenciar o emulador com sintaxe de linha de comando

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

Para exibir a lista de opções, digite `Microsoft.Azure.Cosmos.Emulator.exe /?` no prompt de comando.

|**Opção** | **Descrição** | **Comando**| **Argumentos**|
|---|---|---|---|
|[No arguments] | Inicia o Emulador do Azure Cosmos DB com as configurações padrão. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Ajuda] |Exibe a lista de argumentos de linha de comando com suporte.|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Obtém o status do Emulador do Azure Cosmos DB. O status é indicado pelo código de saída: 1 = Iniciando, 2 = Em execução, 3 = Parado. Um código de saída negativo indica que ocorreu um erro. Nenhum outro resultado é produzido. | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Shutdown| Desliga o Emulador do Azure Cosmos DB.| Microsoft.Azure.Cosmos.Emulator.exe /Shutdown | |
|DataPath | Especifica o caminho no qual armazenar os arquivos de dados. O valor padrão é %LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<datapath\> | \<datapath\>: um caminho acessível |
|Porta | Especifica o número da porta a ser usada para o emulador. O valor padrão é de 8081. |Microsoft.Azure.Cosmos.Emulator.exe /Port=\<port\> | \<port\>: número da porta único |
| ComputePort | Especificado o número da porta a ser usada para o serviço de Gateway de interoperabilidade de computação. A porta de investigação de ponto de extremidade HTTP do Gateway é calculada como ComputePort + 79. Portanto, ComputePort e ComputePort + 79 deve estar aberta e disponível. O valor padrão é 8900. | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<computeport\> | \<computeport\>: número da porta único |
| EnableMongoDbEndpoint=3.2 | Habilita a API MongoDB 3.2 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | Habilita a API MongoDB 3.6 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | Especifica o número da porta a ser usada para API de compatibilidade do MongoDB. O valor padrão é de 10255. |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongoport\>: número da porta único|
| EnableCassandraEndpoint | Sobre a API do Cassandra | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Especifica o número da porta a ser usada para o ponto de extremidade Cassandra. O valor padrão é 10350. | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\>: número da porta único |
| EnableGremlinEndpoint | Habilitar a API do Gremlin | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Número da porta a ser usado para o ponto de extremidade do Gremlin. O valor padrão é: 8901. | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<port\> | \<port\>: número da porta único |
|EnableTableEndpoint | Permite a API de Tabela do Azure | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort | O número da porta a ser usado para o ponto de extremidade da Tabela do Azure. O valor padrão é 8902. | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<port\> | \<port\>: número da porta único|
| KeyFile | A chave de autorização de leitura do arquivo especificado. Use a opção de /GenKeyFile para gerar um keyfile | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: Caminho para o compartilhamento o arquivo |
| ResetDataPath | Remove recursivamente todos os arquivos no caminho especificado. Se você não especificar um caminho, o padrão será %LOCALAPPDATA%\CosmosDbEmulator | Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<path> | \<path\>: Caminho do arquivo  |
| StartTraces  |  Iniciar os logs de rastreamento de depuração de coleta usando LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | Parar os logs de rastreamento de depuração de coleta usando LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  Comece a coletar logs de rastreamento de depuração usando a ferramenta de Gravação de Desempenho do Windows. | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | Pare de coletar logs de rastreamento de depuração usando a ferramenta de Gravação de Desempenho do Windows. | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Por padrão, o emulador regenera seu certificado TLS/SSL autoassinado, se a SAN do certificado não incluir o nome de domínio do host do emulador, endereço IPv4 local, ' localhost ' e ' 127.0.0.1 '. Com essa opção, o emulador falhará na inicialização em vez disso. Em seguida, você deve usar a opção /GenCert para criar e instalar um certificado TLS/SSL autoassinado. | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Gerar e instalar um novo certificado TLS/SSL autoassinado. Opcionalmente, incluindo uma lista separada por vírgulas de nomes DNS adicionais para acessar o emulador pela rede. | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-names\> |\<dns-names\>: lista opcional separada por vírgula de nomes DNS adicionais  |
| DirectPorts |Especifica as portas a serem usadas para conectividade direta. Os padrões são 10251,10252,10253,10254. | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: lista de 4 portas delimitada por vírgula |
| Chave |Chave de autorização para o emulador. A chave deve ser a codificação de base 64 de um vetor de 64 bytes. | Microsoft.Azure.Cosmos.Emulator.exe /Key:\<key\> | \<key\>: A chave deve ser a codificação base-64 de um vetor de 64 bytes|
| EnableRateLimiting | Especifica que o comportamento de limitação da taxa de solicitação está habilitado. |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Especifica que o comportamento de limitação da taxa de solicitação está desabilitado. |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | Não mostra o emulador de interface do usuário. | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | Não mostra o Data Explorer na inicialização. |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | Especifica o número máximo de contêineres particionados. Veja [Alterar o número de contêineres](#set-partitioncount) para saber mais. | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>: Número máximo permitido de contêineres de partição única. O valor padrão é 25. O máximo permitido é 250.|
| DefaultPartitionCount| Especifica o número padrão de partições para um contêiner particionado. | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> O valor padrão é 25.|
| AllowNetworkAccess | Permite o acesso para o emulador através de uma rede. Também é necessário passar /Key=\<key_string\> ou /KeyFile=\<file_name\> para habilitar o acesso à rede. | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> ou Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Não ajuste as regras de firewall quando a opção /AllowNetworkAccess for usada. |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | Gere uma nova chave de autorização e salve no arquivo especificado. A chave gerada pode ser usada com as opções /Key ou /KeyFile. | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<path to key file\> | |
| Consistência | Defina o nível de consistência padrão da conta. | Microsoft.Azure.Cosmos.Emulator.exe /Consistency=\<consistency\> | \<consistency\>: O valor deve ser um dos seguintes [níveis de consistência](consistency-levels.md): Session, Strong, Eventual ou BoundedStaleness. O valor padrão é Sessão. |
| ? | Mostre a mensagem de ajuda.| | |

## <a name="manage-the-emulator-with-powershell"></a>Gerenciar o emulador com o PowerShell

O emulador vem com um módulo PowerShell para iniciar, parar, desinstalar e recuperar o status do serviço. Execute o seguinte cmdlet para usar o módulo do PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

ou coloque o diretório `PSModules` no seu `PSModulePath` e importe-o conforme mostrado no comando a seguir:

```powershell
$env:PSModulePath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Aqui é apresentado um resumo dos comandos para controlar o emulador do PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Sintaxe**

`Get-CosmosDbEmulatorStatus`

**Comentários**

Retorna um destes valores ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running ou ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Sintaxe**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Comentários**

Inicia o emulador. Por padrão, o comando aguarda até que o emulador esteja pronto para aceitar solicitações. Use a opção -NoWait, se quiser que o cmdlet seja retornado assim que ele iniciar o emulador.

### `Stop-CosmosDbEmulator`

**Sintaxe**

 `Stop-CosmosDbEmulator [-NoWait]`

**Comentários**

Parada do emulador. Por padrão, esse comando aguarda até que o emulador seja completamente desligado. Use a opção -NoWait, se quiser que o cmdlet seja retornado assim que o emulador iniciar o desligamento.

### `Uninstall-CosmosDbEmulator`

**Sintaxe**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Comentários**

Desinstala o emulador e, opcionalmente, remove o conteúdo completo do $env:LOCALAPPDATA\CosmosDbEmulator.
O cmdlet garante que o emulador seja parado antes de desinstalá-lo.

## <a name="change-the-number-of-default-containers"></a><a id="set-partitioncount"></a>Alterar o número de contêineres padrão

Por padrão, você pode criar até 25 contêineres de tamanho fixo (somente com suporte usando SDKs de Azure Cosmos DB) ou 5 contêineres ilimitados usando o emulador de Azure Cosmos DB. Ao modificar o valor do **PartitionCount**, que você pode criar até 250 contêineres de tamanho fixo ou 50 contêineres ilimitados ou qualquer combinação dos dois que não excedam 250 contêineres de tamanho fixo (em que um contêiner ilimitado = 5 de tamanho fixo contêineres). No entanto, não é recomendado configurar o emulador para executar com mais de 200 contêineres de tamanho fixo. Por causa da sobrecarga que isso adiciona a operações de E/S de disco, que resulta em tempo limite imprevisível ao usar o ponto de extremidade de APIs.

Se você tentar criar contêiner depois que a contagem de partição atual tiver sido excedida, o emulador lançará uma exceção de ServiceUnavailable, com a mensagem de erro a seguir.

> No momento, estamos enfrentando alta demanda nesta região e não é possível atender à sua solicitação no momento. Estamos trabalhando continuamente para trazer cada vez mais capacidade online e incentivamos você a tentar novamente.
> ActivityId: 12345678-1234-1234-1234-123456789abc

Para alterar o número de contêineres disponíveis no emulador de Azure Cosmos DB, execute as seguintes etapas:

1. Exclua todos os dados locais do Emulador do Azure Cosmos DB clicando com o botão direito do mouse no ícone **Emulador do Azure Cosmos DB** na bandeja do sistema e clicando em **Redefinir Dados…**.

1. Excluir todos os dados de emulador desta pasta `%LOCALAPPDATA%\CosmosDBEmulator`.

1. Saia de todas as instâncias abertas clicando com o botão direito do mouse no ícone do **Emulador do Azure Cosmos DB** na bandeja do sistema e clicando em **Sair**. Pode levar um minuto para que todas as instâncias saiam.

1. Instale a versão mais recente do [Emulador Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).

1. Inicie o emulador com o sinalizador PartitionCount definindo um valor <= 250. Por exemplo: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.
 
## <a name="next-steps"></a>Próximas etapas

* [Exportar os certificados do emulador Azure Cosmos DB para uso com aplicativos Java, Python e Node.js](local-emulator-export-ssl-certificates.md)
* [Problemas de depuração com o emulador](troubleshoot-local-emulator.md)
