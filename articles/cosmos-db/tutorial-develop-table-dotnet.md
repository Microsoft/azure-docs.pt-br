---
title: 'Azure Cosmos DB: Desenvolver com a API de Tabela no .NET | Microsoft Docs'
description: Aprenda a desenvolver com a API de Tabela do Azure Cosmos DB usando o .NET
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4b22cb49-8ea2-483d-bc95-1172cd009498
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: arramac
ms.custom: mvc
ms.openlocfilehash: 5322fa5cc8e841ecea97a69d15cf130a4426ad95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB: Desenvolver com a API de Tabela no .NET

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do gráfico. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB.

Este tutorial cobre as seguintes tarefas: 

> [!div class="checklist"] 
> * Criar uma conta do Azure Cosmos DB 
> * Habilitar a funcionalidade no arquivo app. config 
> * Criar uma tabela usando o [API de Tabela](table-introduction.md) (visualização)
> * Adicionar uma entidade a uma tabela 
> * Inserir um lote de entidades 
> * Recuperar uma única entidade 
> * Consultar entidades usando índices secundários automáticos 
> * Substituir uma entidade 
> * Excluir uma entidade 
> * Excluir uma tabela
 
## <a name="tables-in-azure-cosmos-db"></a>Tabelas no Azure Cosmos DB 

O Azure Cosmos DB fornece a [API de Tabela](table-introduction.md) (visualização) para aplicativos que precisam de um repositório de chave-valor com um design menos esquemático. Os SDKs e as APIs REST do [Armazenamento de Tabelas do Azure](../storage/common/storage-introduction.md) podem ser usados para trabalhar com o Azure Cosmos DB. Você pode usar o Azure Cosmos DB para criar tabelas com requisitos de alta taxa de transferência. O Azure Cosmos DB dá suporte a tabelas com otimização de taxa de transferência (chamadas informalmente de "tabelas premium"), atualmente em visualização pública. 

Você pode continuar usando o Armazenamento de Tabelas do Azure para tabelas com alto requisitos de armazenamento e menores taxa de transferência. O Azure Cosmos DB apresentará o suporte para tabelas com otimização de armazenamento em uma atualização futura, e contas de armazenamento de tabelas do Azure novas e existentes serão atualizadas automaticamente para o Azure Cosmos DB.

Caso utilize o Armazenamento de Tabelas do Azure neste momento, você recebe os seguintes benefícios com a visualização de "tabela premium":

- [Distribuição global](distribute-data-globally.md) turnkey com hospedagem múltipla e [failovers automáticos e manuais](regional-failover.md)
- Suporte para indexação agnóstica de esquema automática em relação a todas as propriedades ("índices secundários") e consultas rápidas 
- Suporte para [dimensionamento independente de armazenamento e taxa de transferência](partition-data.md), em qualquer número de regiões
- Suporte para [taxa de transferência dedicada por tabela](request-units.md) que podem ser dimensionados para centenas a milhões de solicitações por segundo
- Suporte para [cinco níveis de consistência bem definidos](consistency-levels.md) para compensar a disponibilidade, latência e consistência com base nas necessidades do seu aplicativo
- 99,99% de disponibilidade dentro de uma única região e a capacidade de adicionar mais regiões para maior disponibilidade, e [SLAs abrangentes líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/) em disponibilidade geral
- Trabalhar com o SDK do .NET de armazenamento do Azure existente e nenhuma alteração de código no seu aplicativo

Durante a visualização, o Azure Cosmos DB oferece suporte a API de Tabela usando o SDK do .NET. Você pode baixar o [SDK de Visualização do Armazenamento do Azure](https://aka.ms/premiumtablenuget) do NuGet, que tem as mesmas classes e assinaturas de método que o [SDK de Armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage), mas também pode conectar-se às contas do Azure Cosmos DB usando a API de Tabela.

Para saber mais sobre tarefas complexas de armazenamento de Tabelas do Azure, consulte:

* [Introdução ao Azure Cosmos DB: API de Tabela](table-introduction.md)
* Consulte a documentação de referência do serviço Tabela para saber os detalhes completos sobre a referência da [Biblioteca de Clientes do Armazenamento do Azure para .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409) das APIs disponíveis

### <a name="about-this-tutorial"></a>Sobre este tutorial
Este tutorial é para desenvolvedores que estão familiarizados com o SDK de armazenamento de Tabelas do Azure e desejam usar os recursos premium disponíveis usando o Azure Cosmos DB. Ele se baseia na [Introdução ao armazenamento de Tabelas do Azure usando o .NET](table-storage-how-to-use-dotnet.md) e mostra como aproveitar os recursos adicionais, como índices secundários, taxa de transferência provisionada e hospedagem múltipla. Abordaremos como usar o portal do Azure para criar uma conta do Azure Cosmos DB e, em seguida, criar e implantar um aplicativo de Tabela. Também explicaremos detalhadamente os exemplos de .NET para criar e excluir uma tabela e inserir, atualizar, excluir e consultar dados de tabela. 

Se você ainda não tem o Visual 2017 Studio instalado, poderá baixar e usar o **Visual Studio 2017 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Crie uma conta de banco de dados

Vamos começar criando uma conta do Azure Cosmos DB no portal do Azure.  

> [!TIP]
> * Já tem uma conta do Azure Cosmos DB? Nesse caso, pule para [Configurar sua solução do Visual Studio](#SetupVS).
> * Você tinha uma conta do Azure DocumentDB? Se sua conta agora é uma conta do Azure Cosmos DB, você pode pular para [Configurar sua solução do Visual Studio](#SetupVS).  
> * Se estiver usando o Emulador do Azure Cosmos DB, execute as etapas em [Emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e pule para [Configurar sua solução do Visual Studio](#SetupVS).
<!---Loc Comment: Please, check link [Set up your Visual Studio solution] since it's not redirecting to any location.---> 
>
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, clonaremos um aplicativo de Tabela do github, definiremos a cadeia de conexão e o executaremos.

1. Abra uma janela de terminal do Git, tal como git bash, e `cd` para um diretório de trabalho.  

2. Execute o comando a seguir para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started
    ```

3. Em seguida, abra o arquivo da solução no Visual Studio.

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo.

1. No [portal do Azure](http://portal.azure.com/), na sua conta do Azure Cosmos DB, no painel de navegação esquerdo, clique em **Chaves**e, em seguida, clique em **Chaves de leitura/gravação**. Você usará os botões de cópia no lado direito da tela para copiar a cadeia de conexão para o arquivo app.config na próxima etapa.

2. No Visual Studio, abra o arquivo app.config. 

3. Copie o valor do URI do portal (usando o botão de cópia) e torne este o valor de account-key no app.config. Use o nome da conta criado anteriormente para o account-name no app. config.
  
```
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;TableEndpoint=https://account-name.documents.azure.com" />
```

> [!NOTE]
> Para usar este aplicativo com o Armazenamento de Tabela do Azure padrão, você precisa alterar a cadeia de conexão no `app.config file`. Use o nome da conta como nome da conta de tabela e a chave como chave primária de armazenamento do Azure. <br>
>`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;EndpointSuffix=core.windows.net" />`
> 
>

## <a name="build-and-deploy-the-app"></a>Compilar e implantar o aplicativo
1. No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e clique em **Gerenciar Pacotes NuGet**. 

2. Na caixa **procurar** do NuGet, digite ***WindowsAzure.Storage-PremiumTable***. Marque **Incluir versões de pré-lançamento**.

3. Nos resultados, instale o **WindowsAzure. Storage-PremiumTable** e escolha a visualização de compilação `0.0.1-preview`. Essa ação instala o pacote de armazenamento de Tabelas do Azure e todas as dependências.

4. Clique em CTRL + F5 para executar o aplicativo. 

Agora, é possível voltar ao Data Explorer e ver, consultar, modificar e trabalhar com esses dados de tabela. 

> [!NOTE]
> Para usar este aplicativo com um emulador do Azure Cosmos DB, basta alterar a cadeia de conexão no `app.config file`. Use o valor abaixo para o emulador. <br>
>`<add key="StorageConnectionString" value=DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=<insertkey>==;TableEndpoint=https://localhost -->`
> 
>

## <a name="azure-cosmos-db-capabilities"></a>Recursos Azure Cosmos DB
O Azure Cosmos DB oferece suporte a uma série de recursos que não estão disponíveis na API de armazenamento de Tabelas do Azure API. A nova funcionalidade pode ser habilitada através dos seguintes valores de configuração `appSettings`. Nós não introduzimos nenhuma nova assinatura ou sobrecarga para a visualização do SDK do Armazenamento do Azure. Isso permite que você se conecte a tabelas standard e premium e trabalhe com outros serviços de Armazenamento do Azure como Blobs e Filas. 


| Chave | Descrição |
| --- | --- |
| TableConnectionMode  | O Azure Cosmos DB oferece suporte a dois modos de conectividade. No modo `Gateway`, as solicitações são sempre feitas no gateway do Azure Cosmos DB, que encaminha para as partições de dados correspondentes. No modo de conectividade `Direct`, o cliente busca o mapeamento de tabelas de partições e as solicitações são feitas diretamente em partições de dados. Recomendamos `Direct`, o padrão.  |
| TableConnectionProtocol | O Azure Cosmos DB oferece suporte a dois protocolos de conexão - `Https` e `Tcp`. `Tcp` é o padrão e recomendado porque é mais leve. |
| TablePreferredLocations | A lista separada por vírgulas de locais (hospedagem múltipla) preferenciais para leituras. Cada conta do Azure Cosmos DB pode ser associada a 1 ou 30 ou mais regiões. Cada instância do cliente pode especificar um subconjunto dessas regiões na ordem preferida para leituras de baixa latência. As regiões devem ser nomeadas usando seus [nomes de exibição](https://msdn.microsoft.com/library/azure/gg441293.aspx), por exemplo, `West US`. Consulte também [APIs de hospedagem múltipla](tutorial-global-distribution-table.md).
| TableConsistencyLevel | Você pode alternar entre disponibilidade, consistência e latência escolhendo entre cinco níveis de consistência bem definidos: `Strong`, `Session`, `Bounded-Staleness`, `ConsistentPrefix` e `Eventual`. O padrão é `Session`. A escolha do nível de consistência faz uma diferença significativa de desempenho em configurações de várias regiões. Consulte [Níveis de consistência](consistency-levels.md) para saber detalhes. |
| TableThroughput | Taxa de transferência reservada para a tabela expressada em unidades de solicitação (RU) por segundo. Tabelas únicas podem suportar centenas de milhões de RU/s. Consulte [Unidades de solicitação](request-units.md). O padrão é `400` |
| TableIndexingPolicy | Indexação secundária consistente e automática de todas as colunas de tabelas | Cadeia de caracteres de JSON em conformidade com a política de indexação. Consulte [Política de indexação](indexing-policies.md) para ver como você pode alterar a política de indexação para incluir/excluir colunas específicas. | Indexação automática de todas as propriedades (hash para cadeias de caracteres e o intervalo de números) |
| TableQueryMaxItemCount | Configure o número máximo de itens retornados por consultas de tabela em uma única viagem de ida e volta. O padrão é `-1`, que permite ao Azure Cosmos DB determinar dinamicamente o valor em tempo de execução. |
| TableQueryEnableScan | Se a consulta não pode usar o índice para qualquer filtro, em seguida, execute assim mesmo por meio de uma varredura. O padrão é `false`.|
| TableQueryMaxDegreeOfParallelism | O grau de paralelismo para execução de uma consulta entre partições. `0` é serial sem nenhuma busca prévia, `1` é serial com busca prévia e valores mais altos aumentam a taxa de paralelismo. O padrão é `-1`, que permite ao Azure Cosmos DB determinar dinamicamente o valor em tempo de execução. |

Para alterar o valor padrão, abra o arquivo `app.config` do Gerenciador de Soluções no Visual Studio. Adicionar o conteúdo do elemento `<appSettings>` mostrado abaixo. Substitua `account-name` pelo nome da sua conta de armazenamento e `account-key` pela chave de acesso da conta. 

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
      <!-- Client options -->
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />
      <add key="TableConnectionMode" value="Direct"/>
      <add key="TableConnectionProtocol" value="Tcp"/>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>
      <add key="TableConsistencyLevel" value="Eventual"/>

      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
      <add key="TableIndexingPolicy" value="{""indexingMode"": ""Consistent""}"/>

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

Façamos uma rápida revisão do que está acontecendo no aplicativo. Abra o arquivo `Program.cs` e veja que essas linhas de código criam os recursos da Tabela. 

## <a name="create-the-table-client"></a>Criar o cliente da tabela
Você deve inicializar um `CloudTableClient` para conectar-se à conta de tabela.

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
Esse cliente é inicializado usando os valores de configuração `TableConnectionMode`, `TableConnectionProtocol`, `TableConsistencyLevel` e `TablePreferredLocations` se especificados nas configurações do aplicativo.
    
## <a name="create-a-table"></a>Criar uma tabela
Em seguida, você cria uma tabela usando `CloudTable`. As tabelas no Azure Cosmos DB pode ser dimensionada independentemente em termos de armazenamento e taxa de transferência e o particionamento é tratado automaticamente pelo serviço. O Azure Cosmos DB oferece suporte a tabelas ilimitadas e tamanho fixo. Consulte [Particionamento no Azure Cosmos DB](partition-data.md) para saber detalhes. 

```csharp
CloudTable table = tableClient.GetTableReference("people");

table.CreateIfNotExists();
```

Há uma diferença importante em como as tabelas são criadas. O Azure Cosmos DB reserva a taxa de transferência, ao contrário do modelo baseado em consumo do armazenamento do Azure para transações. O modelo de reserva tem dois benefícios principais:

* A taxa de transferência é dedicada/reservada, para que você nunca sofra restrições se a taxa de solicitação está no limite ou abaixo da sua taxa de transferência provisionada
* O modelo de reserva é mais [econômico para cargas de trabalho pesadas de taxa de transferência](key-value-store-cost.md)

Você pode configurar a taxa de transferência padrão definindo a configuração para `TableThroughput` em termos de RU (unidades de solicitação) por segundo. 

Uma leitura de uma entidade de 1 KB é normalizada como 1 RU e outras operações são normalizados para um valor fixo de RU com base em seu consumo de CPU, memória e IOPS. Saiba mais sobre [Unidades de solicitação no Azure Cosmos DB](request-units.md).

> [!NOTE]
> Enquanto atualmente, o SDK de armazenamento de Tabelas não oferece suporte para a modificação de taxa de transferência, você pode alterar a taxa de transferência instantaneamente a qualquer momento usando o portal do Azure ou a CLI do Azure.

Em seguida, percorremos as operações simples de leitura e gravação (CRUD) usando o SDK de armazenamento de Tabelas do Azure. Este tutorial demonstra latências baixas de milissegundos de dígito único previsíveis e consultas rápidas fornecidas pelo Azure Cosmos DB.

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
As entidades no armazenamento de Tabelas do Azure estendem-se a partir da classe `TableEntity` e deve ter as propriedades `PartitionKey` e `RowKey`. Aqui está um exemplo de definição de uma entidade de cliente.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

O trecho a seguir mostra como inserir uma entidade com o SDK do armazenamento do Azure. O Azure Cosmos DB destina-se à garantia de baixa latência em qualquer escala, em todo o mundo.

As gravações são concluídas em menos de 15 ms em p99 e ~6 ms em p50 para aplicativos executados na mesma região que a conta do Azure Cosmos DB. E essa duração de considera o fato de que as gravações são confirmadas para o cliente somente depois que eles são replicados de maneira síncrona, com confirmação permanentemente, e com todo o conteúdo indexado.

A API de Tabela do Azure Cosmos DB está em visualização. Para disponibilidade geral, as garantias de latência p99 contam com os SLAs como outras APIs do Azure Cosmos DB. 

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades
O armazenamento de tabela do Azure suporta uma API de operação em lote, que permite que você combine atualizações, exclusões e inserções na mesma operação de único lote. O Azure Cosmos DB não tem algumas das limitações na API do lote como armazenamento de Tabelas do Azure. Por exemplo, você pode executar várias leituras dentro de um lote, você pode executar várias gravações para a mesma entidade dentro de um lote e não há nenhum limite para 100 operações por lote. 

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```
## <a name="retrieve-a-single-entity"></a>Recuperar uma única entidade
As recuperações (GETs) no Azure Cosmos DB são concluídas em menos de 10 ms em p99 e ~1 ms em p50 na mesma região do Azure. Você pode adicionar a mesma quantidade de regiões à sua conta para leituras de baixa latência e implantar aplicativos para leitura da sua região local (com hospedagem múltipla), definindo `TablePreferredLocations`. 

Você pode recuperar uma única entidade usando o trecho a seguir:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> Saiba mais sobre as APIs de hospedagem múltipla em [Desenvolvendo com várias regiões](tutorial-global-distribution-table.md)
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>Consultar entidades usando índices secundários automáticos
Tabelas podem ser consultadas usando a classe `TableQuery`. O Azure Cosmos DB tem um mecanismo de banco de dados otimizado para gravação que indexa automaticamente todas as colunas em sua tabela. A indexação no Azure Cosmos DB é agnóstica em relação ao esquema. Portanto, mesmo que seu esquema seja diferente entre as linhas, ou se o esquema evoluir ao longo do tempo, ele será indexado automaticamente. Como o Azure Cosmos DB oferece suporte a índices secundários automáticos, as consultas em relação a qualquer propriedade podem usar o índice e serem apresentadas de forma eficiente.

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

Na visualização, o Azure Cosmos DB oferece suporte à mesma funcionalidade de consulta que o armazenamento de tabela do Azure para a API de Tabela. O Azure Cosmos DB também oferece suporte à classificação, agregações, consulta geoespacial, hierarquia e uma ampla variedade de funções internas. Os recursos adicionais serão fornecidos na API de Tabela em uma atualização futura do serviço. Consulte [Consulta do Azure Cosmos DB](documentdb-sql-query.md) para uma visão geral desses recursos. 

## <a name="replace-an-entity"></a>Substituir uma entidade
Para atualizar uma entidade, recupere-a do serviço Tabela, modifique o objeto de entidade e, em seguida, salve as alterações novamente no serviço Tabela. O código a seguir altera o número de telefone de um cliente existente. 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
Da mesma forma, você pode executar as operações `InsertOrMerge` ou `Merge`.  

## <a name="delete-an-entity"></a>Excluir uma entidade
Você pode excluir facilmente uma entidade após a recuperação usando o mesmo padrão mostrado para a atualização de uma entidade. O código a seguir recupera e exclui uma entidade de cliente.

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>Excluir uma tabela
Finalmente, o exemplo de código a seguir exclui uma tabela de uma conta de armazenamento. Você pode excluir e recriar uma tabela imediatamente com o Azure Cosmos DB.

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>Limpar recursos 

Se você não continuar usando este aplicativo, siga as seguintes etapas para excluir todos os recursos criados neste tutorial no portal do Azure.   

1. No menu à esquerda no portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado.  
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, abordamos como começar a usar o Azure Cosmos DB com a API de Tabela e você tiver feito o seguinte: 

> [!div class="checklist"] 
> * Criou uma conta do Azure Cosmos DB 
> * Habilitou a funcionalidade no arquivo app. config 
> * Criou uma tabela 
> * Adicionou uma entidade a uma tabela 
> * Inseriu um lote de entidades 
> * Recuperou uma única entidade 
> * Consultou entidades usando índices secundários automáticos 
> * Substituiu uma entidade 
> * Excluiu uma entidade 
> * Excluiu uma tabela  

Agora você pode prosseguir para o próximo tutorial e saber mais sobre como consultar dados da tabela. 

> [!div class="nextstepaction"]
> [Consulta com a API de Tabela](tutorial-query-table.md)
