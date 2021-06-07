---
title: API SQL Python, SDK e recursos do Azure Cosmos DB
description: Saiba tudo sobre o SDK e a API do SQL Python, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do Python para o Azure Cosmos DB.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 08/12/2020
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: 470ae497acab4c75e83a13e485d1bcb118485ab9
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799506"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>SDK do Python do Azure Cosmos DB para a API do SQL: Notas sobre a versão e recursos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK v3 do .NET](sql-api-sdk-dotnet-standard.md)
> * [SDK do .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 do .NET Core](sql-api-sdk-dotnet-core.md)
> * [SDK do Feed de Alterações do .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK do Java v4](sql-api-sdk-java-v4.md)
> * [SDK do Java Assíncrono v2](sql-api-sdk-async-java.md)
> * [SDK do Java Síncrono v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Conector do Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provedor de recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Executor em massa-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa – Java](sql-api-sdk-bulk-executor-java.md)

| ?| Link |
|---|---|
|**Baixar o SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Documentação da API**|[Documentação de referência da API do Python](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos?view=azure-python&preserve-view=true)|
|**Instruções de instalação do SDK**|[Instruções de instalação do SDK do Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Introdução**|[Introdução ao SDK do Python](create-sql-api-python.md)|
|**Plataforma atual com suporte**|[Python 2,7](https://www.python.org/downloads/) e [Python 3.6 +](https://www.python.org/downloads/)|

## <a name="release-history"></a>Histórico de versões

## <a name="420"></a>4.2.0

**Correções de bugs**
- Corrigido o bug em que o token de continuação não é respeitado quando query_iterable é usado para obter resultados por página.
- Corrigido o bug em que os tokens de recurso não estão sendo respeitados para leituras de documento e exclusões. 

**Novos recursos**
- Adicionado suporte para passagem `partitionKey` ao consultar o feed de alterações.

## <a name="410"></a>4.1.0

- Aviso de substituição adicionado para o modo de indexação "lento". O back-end não permite mais criar contêineres com esse modo e os definirá como consistente.

**Novos recursos**
- Adicionada a capacidade de definir o TTL de armazenamento analítico ao criar um novo contêiner.

**Correções de bugs**
- Corrigido o suporte para `dicts` as entradas do as para Get_client APIs.
- Correção da compatibilidade do Python 2/3 nos iteradores de consulta.
- Erro de dica de tipo fixo.
- Corrigido o bug em que os cabeçalhos de opções não foram adicionados à função upsert_item. 
- Erro fixo gerado quando uma ID que não é de cadeia de caracteres é usada em um item. Agora, ele gera TypeError em vez de AttributeError.


## <a name="400"></a>4.0.0

* Versão estável.
* HttpLoggingPolicy adicionado ao pipeline para habilitar a passagem de um agente de log personalizado para cabeçalhos de solicitação e resposta.

### <a name="400b6"></a>4.0.0b6

* Bug corrigido em synchronized_request para APIs de mídia.
* MediaReadMode e MediaRequestTimeout removidos do ConnectionPolicy, porque não há compatibilidade com solicitações de mídia.

### <a name="400b5"></a>4.0.0b5

* Módulo azure.cosmos.errors preterido e substituído por azure.cosmos.exceptions
* Os parâmetros de condição de acesso (`access_condition`, `if_match`, `if_none_match`) foram preteridos em favor de parâmetros `match_condition` e `etag` separados.
* Bug corrigido no provedor do mapa de roteamento.
* Compatibilidade adicionada a consultas Distinto, Deslocamento e Limite.
* O contexto de execução de consulta de documento padrão agora é usado para

  * Consultas de feed de alterações
  * consultas de partição única ( `partitionkey` , `partitionKeyRangeId` está presente em opções)
  * Consultas sem documento

* Erros para agregações em várias partições, com a habilitação da consulta entre partições definida como verdadeiro, mas nenhuma palavra-chave "value" presente
* Recupera o ponto de extremidade do plano de consulta para outros cenários, para recuperar o plano de consulta
* Compatibilidade com `__repr__` adicionada a objetos de entidade Cosmos.
* Documentação atualizada.

### <a name="400b4"></a>4.0.0b4

* Compatibilidade com argumento de palavra-chave `timeout` adicionada a todas as operações para especificar um tempo limite absoluto em segundos dentro do qual a operação deve ser concluída. Se o valor de tempo limite for excedido, um `azure.cosmos.errors.CosmosClientTimeoutError` será gerado.

* Adicionada uma nova `ConnectionRetryPolicy` para gerenciar o comportamento de repetição durante erros de conexão HTTP.

* Novo construtor e argumentos de palavra-chave de configuração por operação adicionados:

  * `retry_total`: número máximo de novas tentativas.
  * `retry_backoff_max`: o tempo máximo de novas tentativas, em segundos.
  * `retry_fixed_interval`: intervalo fixo de novas tentativas em milissegundos.
  * `retry_read`: o número máximo de tentativas de repetição de leitura de soquetes.
  * `retry_connect`: o número máximo de novas tentativas de erro de conexão.
  * `retry_status`: número máximo de tentativas de repetição em códigos de status de erro.
  * `retry_on_status_codes`: uma lista de códigos de status específicos para tentar novamente.
  * `retry_backoff_factor`: fator para calcular o tempo de espera entre as tentativas de repetição.

### <a name="400b3"></a>4.0.0b3

* `create_database_if_not_exists()` e `create_container_if_not_exists` adicionadas para CosmosClient e banco de dados, respectivamente.

### <a name="400b2"></a>4.0.0b2

* A versão 4.0.0 B2 é a segunda iteração em nossos esforços para criar uma biblioteca de clientes que atenda às práticas recomendadas da linguagem Python.

**Alterações da falha**

* A conexão do cliente foi adaptada para consumir o pipeline HTTP definido em `azure.core.pipeline`.

* Os objetos interativos agora foram renomeados como proxies. Isso inclui:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* O construtor de `CosmosClient` foi atualizado:

  * O parâmetro `auth` foi renomeado para `credential` e agora usará um tipo de autenticação diretamente. Isso significa que o valor de chave primária, um dicionário de tokens de recurso ou uma lista de permissões pode ser passado. No entanto, o formato de dicionário antigo ainda é compatível.

  * O parâmetro `connection_policy` tornou-se um parâmetro somente de palavra-chave e, embora ainda tenha suporte, cada um dos atributos individuais da política agora pode ser passado como argumentos de palavra-chave explícitas:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* Um novo construtor foi adicionado ao `CosmosClient` para habilitar a criação por meio de uma cadeia de conexão recuperada do portal do Azure.

* Algumas operações de `read_all` foram renomeadas para operações `list`:

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Todas as operações que usam parâmetros `request_options` ou `feed_options` foram movidas para os parâmetros apenas de palavra-chave. Além disso, embora esses dicionários de opções ainda sejam compatíveis, cada uma das opções individuais no dicionário agora tem suporte como argumentos de palavra-chave explícitas.

* A hierarquia de erros agora é herdada de `azure.core.AzureError` :

  * `HTTPFailure` foi renomeado para `CosmosHttpResponseError`
  * `JSONParseFailure` removido e substituído por `azure.core.DecodeError`
  * Foram incluídos erros adicionais para códigos de resposta específicos:
    * `CosmosResourceNotFoundError` para status 404
    * `CosmosResourceExistsError` para status 409
    * `CosmosAccessConditionFailedError` para status 412

* `CosmosClient` agora pode ser executado em um gerenciador de contexto para tratar do fechamento da conexão do cliente.

* As respostas iteráveis (por exemplo, respostas de consulta e respostas de lista) agora são do tipo `azure.core.paging.ItemPaged`. O método `fetch_next_block` foi substituído por um iterador secundário, acessado pelo método `by_page`.

### <a name="400b1"></a>4.0.0b1

A versão 4.0.0 B1 é a primeira versão preliminar de nossos esforços para criar uma biblioteca de cliente amigável que atenda às melhores práticas da linguagem Python. Para obter mais informações sobre isso e as versões prévias de outras bibliotecas do SDK do Azure, visite https://aka.ms/azure-sdk-preview1-python.

**Alterações da falha: Novo design de API**

* Agora, as operações estão delimitadas a um cliente específico:

  * `CosmosClient`: Esse cliente lida com operações no nível da conta. Isso inclui o gerenciamento de propriedades de serviço e a listagem dos bancos de dados em uma conta.
  * `Database`: Esse cliente lida com operações no nível do banco de dados. Isso inclui a criação e a exclusão de contêineres, usuários e procedimentos armazenados. Ele pode ser acessado de uma instância do cosmosClient por nome.
  * `Container`: Esse cliente trata das operações de um contêiner específico. Isso inclui consultar e inserir itens e gerenciar propriedades.
  * `User`: Esse cliente trata das operações de um usuário específico. Isso inclui a adição e a exclusão de permissões e o gerenciamento de propriedades de usuário.

    Esses clientes podem ser acessados navegando pela hierarquia do cliente usando o método `get_<child>_client`. Para obter detalhes completos da nova API, consulte a [documentação de referência](https://aka.ms/azsdk-python-cosmos-ref).

* Os clientes são acessados por nome, em vez de por ID. Não é necessário concatenar as cadeias de caracteres para criar links.

* Não é mais necessário importar tipos e métodos de módulos individuais. A área de superfície da API pública está disponível diretamente no pacote `azure.cosmos`.

* As propriedades de solicitação individuais podem ser fornecidas como argumentos de palavra-chave em vez de ser necessário construir uma instância de `RequestOptions` separada.

### <a name="302"></a>3.0.2

* Adição de compatibilidade com o tipo de dados MultiPolygon
* Correção de bugs na sessão de leitura da política de repetição
* Correção de bugs de problemas de preenchimento incorreto durante a decodificação de cadeias de caracteres de base 64

### <a name="301"></a>3.0.1

* Correção de bug em LocationCache
* Correção de bug na lógica de repetição de ponto de extremidade
* Correção na documentação

### <a name="300"></a>3.0.0

* Adição de compatibilidade com a gravação em várias regiões
* Mudanças de nomenclatura
  * DocumentClient para CosmosClient
  * Coleção para contêiner
  * Documento para item
  * Nome do pacote atualizado para "azure-cosmos"
  * Namespace atualizado para "azure-cosmos"

### <a name="233"></a>2.3.3

* Adicionado suporte para proxy
* Adicionado suporte para leitura do feed de alterações
* Adicionado suporte para cabeçalhos de cota de coleção
* Correção de bug de problema de tokens de sessão grandes
* Correção de bug para a API ReadMedia
* Correção de bug no cache de intervalo de chaves de partição

### <a name="232"></a>2.3.2

* Adicionado suporte para repetições padrão em problemas de conexão.

### <a name="231"></a>2.3.1

* Documentação atualizada para fazer referência ao Azure Cosmos DB em vez do Azure DocumentDB.

### <a name="230"></a>2.3.0

* Esta versão do SDK requer a versão mais recente do Emulador do Azure Cosmos DB disponível para fazer o download em https://aka.ms/cosmosdb-emulator.

### <a name="221"></a>2.2.1

* Correção de bug para o dict de agregação
* Correção de bugs de barras de filtragem no link de recursos
* Testes adicionados para codificação Unicode

### <a name="220"></a>2.2.0

* Adição de suporte ao recurso Unidade de Solicitação por Minuto (RU/m).
* Adição de suporte a um novo nível de consistência chamado ConsistentPrefix.

### <a name="210"></a>2.1.0

* Suporte adicionado para consultas de agregação (COUNT, MIN, MAX, SUM e AVG).
* Adição de uma opção para desabilitar a verificação do SSL quando executada no Emulador do DocumentDB.
* Removida a restrição do módulo de solicitações dependentes para serem exatamente 2.10.0.
* Taxa de transferência mínima reduzida em coleções particionadas de 10.100 RU/s a 2500 RU/s.
* Adicionado suporte para habilitar o registro em log de script durante a execução do procedimento armazenado.
* Versão da API REST aumentado para '2017-01-19' nesta versão.

### <a name="201"></a>2.0.1

* Alterações editoriais feitas a comentários de documentação.

### <a name="200"></a>2.0.0

* Suporte ao Python 3.5 adicionado.
* Adição de compatibilidade com o pool de conexões usando o módulo de solicitações.
* Suporte à consistência da sessão adicionado.
* Suporte às consultas TOP/ORDERBY de coleções particionadas adicionado.

### <a name="190"></a>1.9.0

* Suporte à política de repetições para solicitações limitadas adicionado. (As solicitações limitadas recebem uma exceção muito grande de taxa de solicitação, código de erro 429.) Por padrão, o DocumentDB tenta cada solicitação novamente nove vezes quando o código de erro 429 é encontrado, respeitando o tempo retryAfter no cabeçalho de resposta.
  Um intervalo de repetição fixo agora poderá ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy, se você quiser ignorar o tempo retryAfter retornado pelo servidor entre as repetições.
  O DocumentDB agora aguarda um período máximo de 30 segundos para cada solicitação que está sendo limitada (independentemente da contagem de repetições) e retorna a resposta com o código de erro 429.
  Esse tempo também pode ser substituído na propriedade RetryOptions, no objeto ConnectionPolicy.

* O DocumentDB agora retorna x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms como os cabeçalhos de resposta em cada solicitação para denotar a contagem de repetições limitadas e o tempo cumulativo que a solicitação aguardou entre as tentativas.

* A classe RetryPolicy foi removida e a propriedade correspondente (retry_policy) foi exposta na classe document_client. Como alternativa, foi introduzida uma classe RetryOptions, expondo a propriedade RetryOptions na classe ConnectionPolicy, que pode ser usada para substituir algumas das opções de repetição padrão.

### <a name="180"></a>1.8.0

* Adição de compatibilidade com as contas de banco de dados replicados geograficamente.
* Correções de teste para mover o host global e os masterKey para as classes de teste individuais.

### <a name="170"></a>1.7.0

* Adicionado o suporte para o recurso TTL (tempo de vida) para documentos.

### <a name="161"></a>1.6.1

* Correções de bugs relacionadas ao particionamento no lado do servidor a fim de permitir caracteres especiais no caminho de partitionkey.

### <a name="160"></a>1.6.0

* Adição de compatibilidade com o recurso de coleções particionadas do lado do servidor.

### <a name="150"></a>1.5.0

* Estrutura de fragmentação do lado do cliente adicionada ao SDK. Classes HashPartionResolver e RangePartitionResolver implementadas.

### <a name="142"></a>1.4.2

* Implementar o Upsert. Novos métodos UpsertXXX adicionados para dar suporte ao recurso Upsert.
* Implementar o roteamento com base em ID. Nenhuma alteração pública de API, todas as alterações são internas.

### <a name="130"></a>1.3.0

* Versão ignorada para alinhar os números de versão com outros SDKs

### <a name="120"></a>1.2.0

* Oferece suporte ao índice Geoespacial.
* Valida a propriedade de ID de todos os recursos. As IDs de recursos não podem conter caracteres `?, /, #, \\` ou terminar com um espaço.
* Adiciona o novo cabeçalho "andamento de transformação do índice" ao ResourceResponse.

### <a name="110"></a>1.1.0

* Implementa a política de indexação V2

### <a name="101"></a>1.0.1

* Permite a conexão de proxy

## <a name="release--retirement-dates"></a>Datas de lançamento e de baixa

A Microsoft notifica pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte. Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente.

> [!WARNING]
> Após 31 de agosto de 2022, Azure Cosmos DB não fará mais correções de bug ou fornecerá suporte às versões 1. x e 2. x do SDK do Azure Cosmos DB Python para a API do SQL. Se você preferir não atualizar, as solicitações enviadas da versão 1. x e 2. x do SDK continuarão a ser servidas pelo serviço de Azure Cosmos DB.

| Versão | Data de lançamento | Data de desativação |
| --- | --- | --- |
| [4.2.0](#420) |09 de outubro de 2020 |--- |
| [4.1.0](#410) |10 de agosto de 2020 |--- |
| [4.0.0](#400) |20 de maio de 2020 |--- |
| [3.0.2](#302) |15 de novembro de 2018 |--- |
| [3.0.1](#301) |4 de outubro de 2018 |--- |
| [2.3.3](#233) |8 de setembro de 2018 |31 de agosto de 2022 |
| [2.3.2](#232) |08 de maio de 2018 |31 de agosto de 2022 |
| [2.3.1](#231) |21 de dezembro de 2017 |31 de agosto de 2022 |
| [2.3.0](#230) |10 de novembro, 2017 |31 de agosto de 2022 |
| [2.2.1](#221) |29 de setembro de 2017 |31 de agosto de 2022 |
| [2.2.0](#220) |10 de maio de 2017 |31 de agosto de 2022 |
| [2.1.0](#210) |1º de maio de 2017 |31 de agosto de 2022 |
| [2.0.1](#201) |30 de outubro de 2016 |31 de agosto de 2022 |
| [2.0.0](#200) |29 de setembro de 2016 |31 de agosto de 2022 |
| [1.9.0](#190) |07 de julho de 2016 |31 de agosto de 2022 |
| [1.8.0](#180) |14 de junho de 2016 |31 de agosto de 2022 |
| [1.7.0](#170) |26 de abril de 2016 |31 de agosto de 2022 |
| [1.6.1](#161) |08 de abril de 2016 |31 de agosto de 2022 |
| [1.6.0](#160) |29 de março de 2016 |31 de agosto de 2022 |
| [1.5.0](#150) |03 de janeiro de 2016 |31 de agosto de 2022 |
| [1.4.2](#142) |06 de outubro de 2015 |31 de agosto de 2022 |
| 1.4.1 |06 de outubro de 2015 |31 de agosto de 2022 |
| [1.2.0](#120) |06 de agosto de 2015 |31 de agosto de 2022 |
| [1.1.0](#110) |9 de julho de 2015 |31 de agosto de 2022 |
| [1.0.1](#101) |25 de maio de 2015 |31 de agosto de 2022 |
| 1.0.0 |7 de abril de 2015 |31 de agosto de 2022 |
| 0.9.4-prelease |14 de janeiro de 2015 |29 de fevereiro de 2016 |
| 0.9.4-prelease |09 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.2-prelease |25 de novembro de 2014 |29 de fevereiro de 2016 |
| 0.9.1-prelease |23 de setembro de 2014 |29 de fevereiro de 2016 |
| 0.9.0-prelease |21 de agosto de 2014 |29 de fevereiro de 2016 |

## <a name="faq"></a>Perguntas frequentes

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
