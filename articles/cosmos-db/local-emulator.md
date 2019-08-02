---
title: Desenvolver localmente com o emulador do Azure Cosmos
description: Usando o Emulador do Azure Cosmos, você pode desenvolver e testar seu aplicativo no local gratuitamente, sem criar uma assinatura do Azure.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 07/26/2019
ms.openlocfilehash: 3e07b448e73bf64a3c1ec257948b3d61415480f0
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619828"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Usar o Emulador do Azure Cosmos para desenvolvimento e teste locais

O emulador do Azure Cosmos DB fornece um ambiente local que emula o serviço do Azure Cosmos para fins de desenvolvimento. Com o Emulador do Azure Cosmos, você pode desenvolver e testar seu aplicativo localmente sem criar uma assinatura Azure ou incorrer em custos. Quando estiver satisfeito com o funcionamento de seu aplicativo no Emulador do Azure Cosmos, você poderá passar a usar uma conta do Azure Cosmos na nuvem.

Você pode desenvolver usando o emulador do Azure Cosmos com as contas de [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api), e [API](local-emulator.md#table-api) de tabela. No entanto, neste momento o modo de exibição do Data Explorer no emulador é totalmente compatível com clientes para API do SQL somente. 

## <a name="how-the-emulator-works"></a>Como o emulador funciona

O emulador do Azure Cosmos fornece uma emulação de alta fidelidade do serviço do Azure Cosmos DB. Ele dá suporte a uma funcionalidade idêntica ao Azure Cosmos DB, incluindo suporte para criar e consultar dados, provisionar e dimensionar contêineres, bem como executar procedimentos armazenados e gatilhos. Desenvolva e teste aplicativos usando o emulador do Azure Cosmos e implante-os no Azure em escala global, apenas fazendo uma única alteração de configuração no ponto de extremidade de conexão do Azure Cosmos DB.

Embora a emulação do serviço do Azure Cosmos DB seja fiel, a implementação do emulador é diferente do serviço. Por exemplo, o emulador usa componentes padrão do SO, como sistema de arquivos local para persistência e pilha do protocolo HTTPS para conectividade. A funcionalidade que depende da infraestrutura do Azure, como replicação global, latência de único dígito em milissegundos para leituras/gravações e níveis de consistência ajustáveis, não está disponível.

Também é possível migrar dados entre o emulador do Azure Cosmos e o serviço Azure Cosmos usando a [Ferramenta de Migração de Dados do Microsoft Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

Você pode executar o emulador do Azure Cosmos no contêiner do Docker do Windows, consulte o [Hub do Docker](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) quanto ao comando docker pull e [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) para o código de origem do emulador.

## <a name="differences-between-the-emulator-and-the-service"></a>Diferenças entre o emulador e o serviço

Como o Emulador do Azure Cosmos fornece um ambiente emulado em execução em uma estação de trabalho do desenvolvedor local, há algumas diferenças na funcionalidade entre o emulador e a conta do Azure Cosmos na nuvem:

* Neste momento, o Data Explorer no emulador é totalmente compatível com clientes para a API do SQL. A exibição do Gerenciador de Dados e operações para APIs do Azure Cosmos DB, como o MongoDB, tabela, Graph e APIs de Cassandra não têm suporte total.
* O Emulador do Azure Cosmos dá suporte apenas uma única conta fixa e uma chave mestra conhecida. A regeneração de chave não é possível no emulador do Cosmos do Azure, mas a chave padrão pode ser alterada usando a opção da linha de comando.
* O emulador do Azure Cosmos não é um serviço escalonável e não dará suporte a um grande número de contêineres.
* O emulador do Azure Cosmos não oferta diferentes [níveis de consistência do Azure Cosmos DB](consistency-levels.md).
* O emulador do Azure Cosmos não oferece [replicação de várias regiões](distribute-data-globally.md).
* Como a sua cópia do emulador do Azure Cosmos pode não ser atualizada com as alterações mais recentes com o serviço do Azure Cosmos DB, você deve consultar o [planejador de capacidade do Azure Cosmos DB](https://www.documentdb.com/capacityplanner) para estimar as necessidades de taxa de transferência (RUs) de seu aplicativo.
* Ao usar o emulador do Cosmos do Azure, por padrão, você pode criar até 25 contêineres de tamanho fixo (suportado apenas usando os SDKs do Azure Cosmos DB) ou os 5 contêineres ilimitados usando o emulador do Azure Cosmos. Para saber mais sobre como alterar esse valor, veja [Definição do valor de PartitionCount](#set-partitioncount).

## <a name="system-requirements"></a>Requisitos do sistema

O emulador do Azure Cosmos DB tem os seguintes requisitos de hardware e software:

* Requisitos de software
  * Windows Server 2012 R2, Windows Server 2016 ou Windows 10
  * Sistema operacional de 64 bits
* Requisitos mínimos de hardware
  * 2 GB de RAM
  * Espaço disponível no disco rígido de 10 GB

## <a name="installation"></a>Instalação

Você pode baixar e instalar o Emulador do Azure Cosmos no [Centro de Download da Microsoft](https://aka.ms/cosmosdb-emulator) ou você pode executar o emulador no Docker CE for Windows. Para obter instruções sobre como usar o emulador no Docker for Windows, confira [Em execução no Docker](#running-on-docker).

> [!NOTE]
> Para instalar, configurar e executar o emulador do Azure Cosmos DB, você deve ter privilégios administrativos no computador. O emulador criará ou adicionará um certificado e também definirá as regras de firewall para executar seus serviços. Portanto, é necessário para o emulador ser capaz de executar essas operações.

## <a name="running-on-windows"></a>Em execução no Windows

Para iniciar o emulador do Azure Cosmos, selecione o botão Iniciar ou pressione a tecla Windows. Comece a digitar **emulador do Azure Cosmos** e selecione o emulador na lista de aplicativos.

![Selecione o botão Iniciar ou pressione a tecla Windows, comece a digitar **Emulador do Azure Cosmos** e selecione o emulador na lista de aplicativos](./media/local-emulator/database-local-emulator-start.png)

Quando o emulador estiver em execução, você verá um ícone na área de notificação da barra de tarefas do Windows. ![Notificação na barra de tarefas do emulador local do Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

O Emulador do Azure Cosmos, por padrão, é executado no computador local ("localhost") escutando na porta 8081.

O emulador do Azure Cosmos DB é instalado em `C:\Program Files\Azure Cosmos DB Emulator` por padrão. Você também pode iniciar e parar o emulador pela linha de comando. Para saber mais, veja a [referência da ferramenta de linha de comando](#command-line).

## <a name="start-data-explorer"></a>Iniciar o Data Explorer

Quando o emulador do Azure Cosmos é iniciado, ele abre automaticamente o Data Explorer do Azure Cosmos no seu navegador. O endereço é exibido como `https://localhost:8081/_explorer/index.html`. Se você fechar o Explorer e quiser reabri-lo mais tarde, é possível abrir a URL no navegador ou iniciá-lo no Emulador do Azure Cosmos no Ícone de Bandeja do Windows, como mostrado abaixo.

![Iniciador do Data Explorer do emulador local do Azure Cosmos](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Procurando atualizações

O Data Explorer indica se há uma nova atualização disponível para download.

> [!NOTE]
> Não há garantias de que os dados criados em uma versão do emulador do Azure Cosmos (consulte %LOCALAPPDATA%\CosmosDBEmulator ou configurações opcionais do caminho de dados) possam ser acessados em outras versões. Se você precisar persistir seus dados por longo prazo, é recomendável armazená-los em uma conta do Azure Cosmos e não no Emulador do Azure Cosmos.

## <a name="authenticating-requests"></a>Autenticar solicitações

Assim como no Azure Cosmos DB na nuvem, cada solicitação feita no emulador do Azure Cosmos deve ser autenticada. O Emulador do Azure Cosmos dá suporte a uma única conta fixa e a uma chave de autenticação conhecida para autenticação de chave mestra. Essa conta e a chave são as únicas credenciais permitidas para uso com o Emulador do Azure Cosmos. Eles são:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> A chave mestra com suporte do Emulador do Azure Cosmos destina-se ao uso somente com o emulador. Você não pode usar sua conta do Microsoft Azure Cosmos DB de produção e a chave com o emulador do Azure Cosmos.

> [!NOTE]
> Se você tiver iniciado o emulador com a opção /Key, use a chave gerada em vez de `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`. Para saber mais sobre a opção /Keym veja a [referência da ferramenta da linha de comando](#command-line).

Além disso, assim como o Azure Cosmos DB, o emulador do Azure Cosmos tem suporte apenas à comunicação segura por SSL.

## <a name="running-on-a-local-network"></a>Em execução em uma rede local

Você pode executar o emulador em uma rede local. Para habilitar o acesso à rede, especifique a opção `/AllowNetworkAccess` na [linha de comando](#command-line-syntax), o que também requer que você especifique `/Key=key_string` ou `/KeyFile=file_name`. Você pode usar `/GenKeyFile=file_name` para gerar um arquivo com uma chave aleatória antecipadamente. Em seguida, você pode passá-lo para `/KeyFile=file_name` ou `/Key=contents_of_file`.

Para habilitar o acesso de rede pela primeira vez, o usuário deve desligar o emulador e excluir o diretório de dados do emulador (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Desenvolver com o emulador

### <a name="sql-api"></a>API do SQL

Depois que o emulador do Azure Cosmos estiver em execução na área de trabalho, você poderá usar qualquer [SDK do Azure Cosmos DB](sql-api-sdk-dotnet.md) com suporte ou a [API REST do Azure Cosmos DB](/rest/api/cosmos-db/) para interagir com o emulador. O Emulador do Azure Cosmos também inclui um Data Explorer interno que permite criar contêineres para a API do SQL ou Cosmos DB para API do Mongo DB, bem como exibir e editar itens sem precisar escrever nenhum código.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

Depois que o Emulador do Azure Cosmos estiver em execução na sua área de trabalho, você poderá usar a [API para MongoDB do Azure Cosmos DB](mongodb-introduction.md) para interagir com o emulador. Inicie o emulador no prompt de comando como administrador com "/EnableMongoDbEndpoint". Em seguida, use a seguinte cadeia de conexão para se conectar à conta da API do MongoDB:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>API de Tabela

Depois que o emulador do Azure Cosmos estiver em execução na sua área de trabalho, você poderá usar qualquer SDK da API da Tabela do Azure Cosmos DB com suporte ou o [SDK da API de Tabela do Azure Cosmos DB](table-storage-how-to-use-dotnet.md) para interagir com o emulador. Inicie o emulador de prompt de comando como administrador com “/EnableTableEndpoint”. Em seguida, execute o código a seguir para conectar-se à conta de API da tabela:

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

Inicie o emulador em um prompt de comando do administrador com “/EnableCassandraEndpoint”. Como alternativa, você também pode definir a variável de ambiente `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`.

* [Instalar o Python 2.7](https://www.python.org/downloads/release/python-2716/)

* [Instalar Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

* Execute os seguintes comandos em uma janela de prompt de comando regular:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* No shell CQLSH, execute os comandos a seguir para se conectar ao ponto de extremidade do Cassandra:

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

Inicie o emulador em um prompt de comando do administrador com “/EnableGremlinEndpoint”. Como alternativa, você também pode definir a variável de ambiente `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Instale o apache-tinkerpop-gremlin-console-3.3.4](https://tinkerpop.apache.org/downloads.html)

* No Azure Data Explorer do emulador, crie um banco de dados "db1" e uma coleção "coll1"; para a chave de partição, escolha "/name"

* Execute os seguintes comandos em uma janela de prompt de comando regular:

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

* No shell Gremlin, execute os comandos a seguir para se conectar ao ponto de extremidade do Gremlin:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-ssl-certificate"></a>Exportar o certificado SSL

As linguagens e o tempo de execução .NET usam o Repositório de Certificados do Windows para a conexão segura com o emulador local do Azure Cosmos DB. Outras linguagens têm seu próprio método de gerenciar e usar certificados. O Java usa seu próprio [repositório de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) enquanto o Python usa [wrappers de soquete](https://docs.python.org/2/library/ssl.html).

Para obter um certificado para uso com linguagens e tempos de execução que não se integram ao Repositório de Certificados do Windows, você precisará exportá-lo usando o Gerenciador de Certificados do Windows. Você pode iniciá-lo executando certlm.msc ou seguir as instruções passo a passo em [Exportar os certificados do emulador do Azure Cosmos](./local-emulator-export-ssl-certificates.md). Depois que o gerenciador de certificados estiver em execução, abra os Certificados Pessoais, conforme mostrado abaixo, e exporte o certificado com o nome amigável "DocumentDBEmulatorCertificate" como um arquivo X.509 codificado em BASE-64 (.cer).

![Certificado SSL do emulador local do Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

O certificado X.509 pode ser importado no repositório de certificados Java seguindo as instruções em [Adicionando um certificado ao repositório de certificados de AC do Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Depois que o certificado é importado para o repositório de certificados, os clientes para API do SQL e do Azure Cosmos DB para MongoDB podem se conectar ao Emulador do Azure Cosmos.

Durante a conexão do emulador de SDKs Python e Node.js, a verificação de SSL é desabilitada.

## <a id="command-line"></a>Referência da ferramenta de linha de comando
No local da instalação, você pode usar a linha de comando para iniciar e interromper o emulador, configurar opções e executar outras operações.

### <a name="command-line-syntax"></a>Sintaxe da linha de comando

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

Para exibir a lista de opções, digite `CosmosDB.Emulator.exe /?` no prompt de comando.

|**Opção** | **Descrição** | **Comando**| **Argumentos**|
|---|---|---|---|
|[No arguments] | Inicia o Emulador do Azure Cosmos com as configurações padrão. |CosmosDB.Emulator.exe| |
|[Ajuda] |Exibe a lista de argumentos de linha de comando com suporte.|CosmosDB.Emulator.exe /? | |
| GetStatus |Obtém o status do Emulador do Azure Cosmos. O status é indicado pelo código de saída: 1 = Iniciando, 2 = Em execução, 3 = Parado. Um código de saída negativo indica que ocorreu um erro. Nenhum outro resultado é produzido. | CosmosDB.Emulator.exe /GetStatus| |
| Shutdown| Desliga o Emulador do Azure Cosmos.| CosmosDB.Emulator.exe /Shutdown | |
|DataPath | Especifica o caminho no qual armazenar os arquivos de dados. O valor padrão é %LocalAppdata%\CosmosDBEmulator. | CosmosDB.Emulator.exe /DataPath=\<datapath\> | \<datapath\>: um caminho acessível |
|Porta | Especifica o número da porta a ser usada para o emulador. O valor padrão é de 8081. |CosmosDB.Emulator.exe /Port=\<porta\> | \<port\>: número da porta único |
| ComputePort | Especificado o número da porta a ser usada para o serviço de Gateway de interoperabilidade de computação. A porta de investigação de ponto de extremidade HTTP do Gateway é calculada como ComputePort + 79. Portanto, ComputePort e ComputePort + 79 deve estar aberta e disponível. Os valores padrão são 8900, 8979. | CosmosDB.Emulator.exe /ComputePort = \<computeport\> | \<computeport\>: número da porta único |
| EnableMongoDbEndpoint | Habilita a API do MongoDB | CosmosDB.Emulator.exe /EnableMongoDbEndpoint | |
| MongoPort | Especifica o número da porta a ser usada para API de compatibilidade do MongoDB. O valor padrão é de 10255. |CosmosDB.Emulator.exe /MongoPort= \<mongoport\>|\<mongoport\>: número da porta único|
| EnableCassandraEndpoint | Sobre a API do Cassandra | CosmosDB.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Especifica o número da porta a ser usada para o ponto de extremidade Cassandra. O valor padrão é 10350. | CosmosDB.Emulator.exe /CassandraPort = \<cassandraport\> | \<cassandraport\>: número da porta único |
| EnableGremlinEndpoint | Habilitar a API do Gremlin | CosmosDB.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Número da porta a ser usado para o ponto de extremidade do Gremlin. O valor padrão é: 8901. | CosmosDB.Emulator.exe /GremlinPort=\<port\> | \<port\>: número da porta único |
|EnableTableEndpoint | Permite a API de Tabela do Azure | CosmosDB.Emulator.exe /EnableTableEndpoint | |
|TablePort | O número da porta a ser usado para o ponto de extremidade da Tabela do Azure. O valor padrão é 8902. | CosmosDB.Emulator.exe /TablePort=\<port\> | \<port\>: número da porta único|
| KeyFile | A chave de autorização de leitura do arquivo especificado. Use a opção de /GenKeyFile para gerar um keyfile | CosmosDB.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: Caminho para o compartilhamento o arquivo |
| ResetDataPath | Remove recursivamente todos os arquivos no caminho especificado. Se você não especificar um caminho, o padrão será %LOCALAPPDATA%\CosmosDbEmulator | CosmosDB.Emulator.exe /ResetDataPath[=\<path>] | \<path\>: Caminho do arquivo  |
| StartTraces  |  Iniciar os logs de rastreamento de depuração de coleta. | CosmosDB.Emulator.exe /StartTraces | |
| StopTraces     | Parar os logs de rastreamento de depuração de coleta. | CosmosDB.Emulator.exe /StopTraces  | |
|FailOnSslCertificateNameMismatch | Por padrão o emulador regenera seu certificado SSL autoassinado, se o SAN do certificado não incluir o nome de domínio do host do Emulador, o endereço do Ipv4 local, “localhost” e “127.0.0.1”. Com essa opção, o emulador falhará na inicialização em vez disso. Em seguida, você deve usar a opção /GenCert para criar e instalar um novo certificado SSL autoassinado. | CosmosDB.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Gerar e instalar um novo certificado SSL autoassinado. opcionalmente, incluindo uma lista separada por vírgulas de nomes DNS adicionais para acessar o emulador pela rede. | Lista separada por vírgula do CosmosDB.Emulator.exe /GenCert[ \<dos nomes dns adicionais\>] | |
| DirectPorts |Especifica as portas a serem usadas para conectividade direta. Os padrões são 10251,10252,10253,10254. | CosmosDB.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: lista de 4 portas delimitada por vírgula |
| Chave |Chave de autorização para o emulador. A chave deve ser a codificação de base 64 de um vetor de 64 bytes. | CosmosDB.Emulator.exe /Key:\<chave\> | \<key\>: A chave deve ser a codificação base-64 de um vetor de 64 bytes|
| EnableRateLimiting | Especifica que o comportamento de limitação da taxa de solicitação está habilitado. |CosmosDB.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Especifica que o comportamento de limitação da taxa de solicitação está desabilitado. |CosmosDB.Emulator.exe /DisableRateLimiting | |
| NoUI | Não mostra o emulador de interface do usuário. | CosmosDB.Emulator.exe /NoUI | |
| NoExplorer | Não mostra o Data Explorer na inicialização. |CosmosDB.Emulator.exe /NoExplorer | | 
| PartitionCount | Especifica o número máximo de contêineres particionados. Veja [Alterar o número de contêineres](#set-partitioncount) para saber mais. | CosmosDB.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>: Número máximo permitido de contêineres de partição única. O valor padrão é de 25. O máximo permitido é 250.|
| DefaultPartitionCount| Especifica o número padrão de partições para um contêiner particionado. | CosmosDB.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> O valor padrão é 25.|
| AllowNetworkAccess | Permite o acesso para o emulador através de uma rede. Você também deve passar /Key=\<key_string\> ou /KeyFile=\<file_name\> para habilitar o acesso à rede. | CosmosDB.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> ou CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Não ajuste as regras de firewall quando a opção /AllowNetworkAccess for usada. |CosmosDB.Emulator.exe /NoFirewall | |
| GenKeyFile | Gere uma nova chave de autorização e salve no arquivo especificado. A chave gerada pode ser usada com as opções /Key ou /KeyFile. | CosmosDB.Emulator.exe /GenKeyFile=\<caminho para o arquivo da chave\> | |
| Consistência | Defina o nível de consistência padrão da conta. | CosmosDB.Emulator.exe /Consistency=\<consistência\> | \<consistency\>: O valor deve ser um dos seguintes [níveis de consistência](consistency-levels.md): Session, Strong, Eventual ou BoundedStaleness. O valor padrão é Sessão. |
| ? | Mostre a mensagem de ajuda.| | |

## <a id="set-partitioncount"></a>Altere o número de contêineres

Por padrão, você pode criar até 25 contêineres de tamanho fixo (suportado apenas usando os SDKs do Azure Cosmos DB) ou os 5 contêineres ilimitados usando o emulador do Azure Cosmos. Ao modificar o valor do **PartitionCount**, que você pode criar até 250 contêineres de tamanho fixo ou 50 contêineres ilimitados ou qualquer combinação dos dois que não excedam 250 contêineres de tamanho fixo (em que um contêiner ilimitado = 5 de tamanho fixo contêineres). No entanto, não é recomendado configurar o emulador para executar com mais de 200 contêineres de tamanho fixo. Por causa da sobrecarga que isso adiciona a operações de E/S de disco, que resulta em tempo limite imprevisível ao usar o ponto de extremidade de APIs.

Se você tentar criar contêiner depois que a contagem de partição atual tiver sido excedida, o emulador lançará uma exceção de ServiceUnavailable, com a mensagem de erro a seguir.

"Desculpe, estamos tendo alta demanda nesta região e não podemos atender sua solicitação neste momento. Estamos trabalhando continuamente para trazer cada vez mais capacidade online e incentivamos você a tentar novamente.
Não hesite em enviar um email askcosmosdb@microsoft.com a qualquer momento ou por qualquer motivo.
ActivityId: 12345678-1234-1234-1234-123456789abc"

Para alterar o número de contêineres disponíveis para o emulador do Azure Cosmos DB, faça o seguinte:

1. Exclua todos os dados locais do emulador do Azure Cosmos clicando com o botão direito do mouse no ícone **Emulador do Azure Cosmos DB** na bandeja do sistema e clicando em **Redefinir Dados…** .
2. Excluir todos os dados de emulador desta pasta `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Saia de todas as instâncias abertas clicando com o botão direito do mouse no ícone do **Emulador do Azure Cosmos DB** na bandeja do sistema e clicando em **Sair**. Pode levar um minuto para que todas as instâncias saiam.
4. Instale a versão mais recente do [emulador Azure Cosmos](https://aka.ms/cosmosdb-emulator).
5. Inicie o emulador com o sinalizador PartitionCount definindo um valor <= 250. Por exemplo: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Controlar o emulador

O emulador vem com um módulo PowerShell para iniciar, parar, desinstalar e recuperar o status do serviço. Execute o seguinte cmdlet para usar o módulo do PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

ou coloque o diretório `PSModules` no seu `PSModulesPath` e importe-o conforme mostrado no comando a seguir:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
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

## <a name="running-on-docker"></a>Em execução no Docker

O emulador do Azure Cosmos pode ser executado no Docker CE for Windows. O emulador não funciona no Docker for Oracle Linux.

Uma vez que você tenha o [Docker para Windows](https://www.docker.com/docker-windows) instalado, mude para contêineres do Windows clicando com o botão direito do mouse no ícone do Docker na barra de ferramentas e selecionando **Mudar para contêineres do Windows**.

Em seguida, efetue pull da imagem do emulador do Hub do Docker, executando o comando a seguir do seu shell favorito.

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
Para iniciar a imagem, execute os comandos a seguir.

Na linha de comando:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator --rm
```

> [!NOTE]
> Se você vir um erro de conflito de porta (a porta especificada já está em uso) ao executar o comando de execução do docker, você poderá passar uma porta personalizada alterando os números de porta. Por exemplo, você pode alterar "-p 8081:8081" para "-p 443:8081"

Do PowerShell:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

```

A resposta tem aparência semelhante à seguinte:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

Agora use o ponto de extremidade e a chave mestra da resposta no seu cliente e importe o certificado SSL no seu host. Para importar o certificado SSL, faça o seguinte em um prompt de comando do administrador:

Na linha de comando:

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

Do PowerShell:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

Fechar o shell interativo depois de o emulador ter sido iniciado desligará o contêiner do emulador.

Para abrir o Data Explorer, navegue até a URL a seguir no seu navegador. O ponto de extremidade do emulador é fornecido na mensagem de resposta mostrada acima.

    https://<emulator endpoint provided in response>/_explorer/index.html

## Executar no Mac ou Linux<a id="mac"></a>

Atualmente, o emulador do Cosmos somente pode ser executado no Windows. Os usuários do Mac ou do Linux podem executar o emulador em uma máquina virtual do Windows hospedada em um hipervisor, como o Parallels ou o VirtualBox. Abaixo estão as etapas para habilitá-lo.

Na VM do Windows, execute o comando a seguir e anote o endereço IPv4.

```cmd
ipconfig.exe
```

No seu aplicativo, você precisa alterar o URI do objeto DocumentClient para usar o endereço IPv4 retornado por `ipconfig.exe`. A próxima etapa é contornar a validação de autoridade de certificação ao construir o objeto DocumentClient. Para isso, você precisará fornecer um objeto HttpClientHandler para o construtor do DocumentClient, que tem sua própria implementação para ServerCertificateCustomValidationCallback.

Abaixo é mostrado um exemplo de como deve ser o código.

```csharp
using System;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using System.Net.Http;

namespace emulator
{
    class Program
    {
        static async void Main(string[] args)
        {
            string strEndpoint = "https://10.135.16.197:8081/";  //IPv4 address from ipconfig.exe
            string strKey = "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==";

            //Work around the CA validation
            var httpHandler = new HttpClientHandler()
            {
                ServerCertificateCustomValidationCallback = (req,cert,chain,errors) => true
            };

            //Pass http handler to document client
            using (DocumentClient client = new DocumentClient(new Uri(strEndpoint), strKey, httpHandler))
            {
                Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "myDatabase" });
                Console.WriteLine($"Created Database: id - {database.Id} and selfLink - {database.SelfLink}");
            }
        }
    }
}
```

Por fim, na VM do Windows, inicie o emulador do Cosmos a partir da linha de comando usando as opções a seguir.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

## <a name="troubleshooting"></a>solução de problemas

Use as dicas a seguir para ajudar a solucionar problemas encontrados com o emulador do Azure Cosmos:

- Se você tiver instalado uma nova versão do emulador e se houver erros, redefina os dados. Você pode redefinir seus dados clicando com o botão direito do mouse no ícone emulador do Azure Cosmos DB na bandeja do sistema e clicando em Redefinir Dados... Se isso não resolver os erros, você pode desinstalar o emulador e as versões mais antigas do emulador se encontradas, remova o diretório "C:\Program files\Azure Cosmos DB Emulator" e reinstale o emulador. Veja [Desinstalar o emulador local](#uninstall) para obter instruções.

- Se o emulador do Azure Cosmos falhar, colete os arquivos de despejo na pasta '%LOCALAPPDATA%\CrashDumps', compacte-os e anexe-os a um email que deve ser enviado para [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Se você enfrentar falhas em `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`, isso pode ser um sintoma de onde os contadores de desempenho estão em um estado corrompido. Normalmente, executar o seguinte comando em um prompt de comando do administrador corrige o problema:

  ```cmd
  lodctr /R
   ```

- Se você encontrar um problema de conectividade, [colete os arquivos de rastreamento](#trace-files), compacte-os e anexe-os a um email que deve ser enviado para [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Se você receber uma mensagem de **Serviço Indisponível**, o emulador poderá estar falhando em inicializar a pilha de rede. Verifique se você tem o cliente seguro Pulse ou o cliente de redes Juniper instalado, uma vez que seus drivers de filtro de rede podem causar o problema. Desinstalar os drivers de filtro de rede de terceiros geralmente corrige o problema. Como alternativa, inicie o emulador com /DisableRIO, que mudará a comunicação de rede do emulador para Winsock regular. 

- Enquanto o emulador é executado, se o computador entrar em modo de suspensão ou executar atualizações do sistema operacional, talvez você veja uma mensagem **O serviço não disponível no momento**. Reinicie os dados do emulador clicando com o botão direito do mouse no ícone que aparece na bandeja de notificação do Windows e selecione **Redefinir dados**.

### <a id="trace-files"></a>Coletar arquivos de rastreamento

Para coletar rastreamentos de depuração, execute os seguintes comandos em um prompt de comando administrativo:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Confira a bandeja do sistema para ter certeza de que o programa foi desligado. Isso pode levar um minuto. Você também pode simplesmente clicar em **Sair** na interface do usuário do emulador do Azure Cosmos.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Reproduza o problema. Se o Data Explorer não está funcionando, você só precisa aguardar que o navegador abra por alguns segundos para capturar o erro.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Navegue até `%ProgramFiles%\Azure Cosmos DB Emulator` e localize o arquivo docdbemulator_000001.etl.
7. Envie o arquivo .etl junto com as etapas reproduzidas para [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) para fins de depuração.

### <a id="uninstall"></a>Desinstalar o emulador local

1. Saia de todas as instâncias abertas do emulador local clicando com o botão direito do mouse no ícone do Emulador do Azure Cosmos na bandeja do sistema e clicando em Sair. Pode levar um minuto para que todas as instâncias saiam.
2. Na caixa de pesquisa do Windows, digite **Aplicativos e recursos** e clique no resultado de **Aplicativos e recursos (Configurações do sistema)** .
3. Na lista de aplicativos, vá para **Emulador do Azure Cosmos DB**, selecione-o, clique em **Desinstalar** e então confirme e clique em **Desinstalar** novamente.
4. Após a desinstalação do aplicativo, navegue até `%LOCALAPPDATA%\CosmosDBEmulator` e exclua a pasta.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como usar o emulador local para o desenvolvimento local gratuito. Agora, você pode seguir para o próximo tutorial e aprender como exportar certificados SSL do emulador.

> [!div class="nextstepaction"]
> [Exportar os certificados do Emulador do Azure Cosmos DB](local-emulator-export-ssl-certificates.md)
