---
title: Entender as saídas do Azure Stream Analytics
description: Este artigo descreve as opções de saída de dados disponíveis no Azure Stream Analytics, incluindo o Power BI para resultados da análise.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/31/2019
ms.openlocfilehash: a0da13e82811d500dee50c2231500245c7e011a6
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383439"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Entender as saídas do Azure Stream Analytics

Este artigo descreve os tipos de saídas disponíveis para um trabalho Azure Stream Analytics. As saídas permitem armazenar e salvar os resultados do trabalho do Stream Analytics. Usando os dados de saída, você pode fazer análises de negócios e data warehousing adicionais de seus dados.

Ao projetar sua consulta Stream Analytics, consulte o nome da saída usando a [cláusula Into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Você pode usar uma única saída por trabalho ou várias saídas por trabalho de streaming (se precisar delas) fornecendo várias cláusulas INTO na consulta.

Para criar, editar e testar Stream Analytics saídas de trabalho, você pode usar o [portal do Azure](stream-analytics-quick-create-portal.md#configure-job-output), o [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), a [API do .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), a [API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)e o [Visual Studio](stream-analytics-quick-create-vs.md).

Alguns tipos de saídas dão suporte ao [particionamento](#partitioning). Os [tamanhos de lote de saída](#output-batch-size) variam para otimizar a taxa de transferência.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Store Gen 1

O Stream Analytics dá suporte a [Azure data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage é um repositório de hiperescala em toda a empresa para Big Data cargas de trabalho analíticas. Você pode usar Data Lake Storage para armazenar dados de qualquer tamanho, tipo e velocidade de ingestão para análise operacional e exploratório. Stream Analytics precisa estar autorizado a acessar Data Lake Storage.

Azure Data Lake Storage saída do Stream Analytics não está disponível no momento nas regiões do Azure China 21Vianet e do Azure Alemanha (T-Systems International).

A tabela a seguir lista os nomes de propriedade e suas descrições para configurar sua saída Data Lake Storage Gen 1.   

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável usado em consultas para direcionar a saída da consulta para Data Lake Store. |
| Assinatura | A assinatura que contém sua conta de Azure Data Lake Storage. |
| Nome da conta | O nome da conta de Data Lake Store em que você está enviando a saída. Você verá uma lista suspensa de contas de Data Lake Store que estão disponíveis em sua assinatura. |
| Padrão de prefixo de caminho | O caminho do arquivo usado para gravar os arquivos dentro da conta de Data Lake Store especificada. Você pode especificar uma ou mais instâncias das variáveis {Date} e {time}:<br /><ul><li>Exemplo 1: pasta1/logs/{data}/{hora}</li><li>Exemplo 2: pasta1/logs/{data}</li></ul><br />O carimbo de data/hora da estrutura de pastas criada segue o UTC e não a hora local.<br /><br />Se o padrão de caminho do arquivo não contiver uma barra à direita (/), o último padrão no caminho do arquivo será tratado como um prefixo de nome de arquivo. <br /><br />Novos arquivos são criados nessas circunstâncias:<ul><li>Alteração no esquema de saída</li><li>Reinicialização interna ou externa de um trabalho</li></ul> |
| Formato de data | Opcional. Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD |
|Formato de hora | Opcional. Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH. |
| Formato de serialização de evento | O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro.|
| Codificando | Se você estiver usando o formato CSV ou JSON, uma codificação deverá ser especificada. No momento, UTF-8 é o único formato de codificação com suporte.|
| Delimitador | Aplicável somente para serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical.|
| Formatar | Aplicável somente para serialização JSON. **Linha separada** especifica que a saída é formatada por ter cada objeto JSON separado por uma nova linha. **Matriz** especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar JSON separado por linha, pois não requer tratamento especial enquanto o arquivo de saída ainda está sendo gravado.|
| Modo de autenticação | Você pode autorizar o acesso à sua conta do Data Lake Storage usando a [identidade gerenciada](stream-analytics-managed-identities-adls.md) ou o token do usuário. Depois de conceder acesso, você pode revogar o acesso alterando a senha da conta do usuário, excluindo a saída Data Lake Storage para esse trabalho ou excluindo o trabalho de Stream Analytics. |

## <a name="sql-database"></a>Banco de Dados SQL

Você pode usar o banco de dados [SQL do Azure](https://azure.microsoft.com/services/sql-database/) como uma saída para o que é relacional por natureza ou para aplicativos que dependem do conteúdo que está sendo hospedado em um banco de dados relacional. Stream Analytics trabalhos gravam em uma tabela existente no banco de dados SQL. O esquema de tabela deve corresponder exatamente aos campos e seus tipos na saída do trabalho. Você também pode especificar o [Azure SQL data warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) como uma saída por meio da opção saída do banco de dados SQL. Para saber mais sobre maneiras de melhorar a taxa de transferência de gravação, consulte o artigo [Stream Analytics com o banco de dados SQL do Azure como saída](stream-analytics-sql-output-perf.md) .

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída de banco de dados SQL.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse banco de dados. |
| Banco de dados | O nome do banco de dados no qual você está enviando a saída. |
| Nome do servidor | Nome do servidor do Banco de Dados SQL do Azure. |
| Nome de usuário | O nome de usuário que tem acesso de gravação ao banco de dados. Stream Analytics dá suporte apenas à autenticação do SQL. |
| Senha | A senha para se conectar ao banco de dados. |
| Tabela | O nome da tabela em que a saída é gravada. O nome da tabela diferencia maiúsculas de minúsculas. O esquema dessa tabela deve corresponder exatamente ao número de campos e seus tipos que sua saída de trabalho gera. |
|Herdar esquema de partição| Uma opção para herdar o esquema de particionamento da etapa de consulta anterior, para habilitar a topologia totalmente paralela com vários gravadores na tabela. Para obter mais informações, confira [Saída do Azure Stream Analytics para Banco de Dados SQL do Azure](stream-analytics-sql-output-perf.md).|
|Contagem de lote máxima| O limite superior recomendado para o número de registros enviados com cada transação de inserção em massa.|

> [!NOTE]
> A oferta do banco de dados SQL do Azure tem suporte para uma saída de trabalho no Stream Analytics, mas uma máquina virtual do Azure executando SQL Server com um banco de dados anexado ou em um SQL Azure Instância Gerenciada ainda não tem suporte. Isso está sujeito à mudança em versões futuras.

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Armazenamento de BLOBs e Azure Data Lake Gen2

A saída para Azure Data Lake Gen2 é oferecida como um recurso de visualização em regiões limitadas em todo o mundo. Você pode solicitar acesso à visualização fornecendo detalhes adicionais em nosso formulário de [solicitação](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u).

O armazenamento de BLOBs do Azure oferece uma solução econômica e escalonável para armazenar grandes quantidades de dados não estruturados na nuvem. Para obter uma introdução sobre o armazenamento de BLOBs e seu uso, consulte [carregar, baixar e listar BLOBs com o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de BLOB.

| Nome da propriedade       | Descrição                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias de saída        | Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de blob. |
| Conta de armazenamento     | O nome da conta de armazenamento em que você está enviando a saída.               |
| Chave da conta de armazenamento | A chave secreta associada à conta de armazenamento.                              |
| Contêiner de armazenamento   | Um agrupamento lógico para BLOBs armazenados no serviço blob do Azure. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob. |
| Padrão de caminho | Opcional. O padrão de caminho de arquivo que é usado para gravar seus BLOBs dentro do contêiner especificado. <br /><br /> No padrão de caminho, você pode optar por usar uma ou mais instâncias das variáveis de data e hora para especificar a frequência em que os BLOBs são gravados: <br /> {data}, {hora} <br /><br />Você pode usar um particionamento de blob para especificar um nome de {field} personalizado dos dados do seu evento para os blobs de partição. O nome do campo é alfanumérico e pode incluir espaços, hífens e sublinhados. Restrições em campos personalizados incluem o seguinte: <ul><li>Os nomes de campo não diferenciam maiúsculas de minúsculas. Por exemplo, o serviço não pode diferenciar entre a coluna "ID" e a coluna "ID".</li><li>Campos aninhados não são permitidos. Em vez disso, use um alias na consulta de trabalho para "Mesclar" o campo.</li><li>As expressões não podem ser usadas como um nome de campo.</li></ul> <br />Esse recurso permite o uso de configurações de especificador de formato personalizado de data/hora no caminho. Os formatos personalizados de data e hora devem ser especificados um de cada vez, entre a palavra-chave {datetime:\<specifier>}. As entradas permitidas \<para o especificador > são AAAA, mm, m, DD, d, hh, H, mm, M, SS ou s. A palavra-chave\<{DateTime: especificador >} pode ser usada várias vezes no caminho para formar configurações personalizadas de data/hora. <br /><br />Exemplos: <ul><li>Exemplo 1: cluster1/logs /{data}/{hora}</li><li>Exemplo 2: cluster1/logs/{data}</li><li>Exemplo 3: cluster1/{client_id}/{data}/{hora}</li><li>Exemplo 4: cluster1/{datetime:ss}/{myField} em que a consulta é: SELECT data.myField AS myField FROM Input;</li><li>Exemplo 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />O carimbo de data/hora da estrutura de pastas criada segue o UTC e não a hora local.<br /><br />A nomenclatura de arquivo usa a seguinte convenção: <br /><br />{Padrão de prefixo de caminho}/schemaHashcode_Guid_Number.extension<br /><br />Exemplo de arquivos de saída:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Para obter mais informações sobre esse recurso, consulte [Azure Stream Analytics particionamento de saída de blob personalizado](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato de data | Opcional. Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD |
| Formato de hora | Opcional. Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH. |
| Formato de serialização de evento | Formato de serialização para dados de saída. Há suporte para JSON, CSV, Avro e parquet. |
|Mínimo de linhas (somente parquet)|O número mínimo de linhas por lote. Para parquet, todo lote criará um novo arquivo. O valor padrão atual é 2.000 linhas e o máximo permitido é de 10.000 linhas.|
|Tempo máximo (somente parquet)|O tempo de espera máximo por lote. Após esse período, o lote será gravado na saída mesmo se o requisito de linhas mínimas não for atendido. O valor padrão atual é 1 minuto e o máximo permitido é de 2 horas. Se a saída de blob tiver a frequência de padrão do caminho, o tempo de espera não poderá ser maior que o intervalo de tempo da partição.|
| Codificando    | Se você estiver usando o formato CSV ou JSON, uma codificação deverá ser especificada. No momento, UTF-8 é o único formato de codificação com suporte. |
| Delimitador   | Aplicável somente para serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar      | Aplicável somente para serialização JSON. **Linha separada** especifica que a saída é formatada por ter cada objeto JSON separado por uma nova linha. **Matriz** especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar JSON separado por linha, pois não requer tratamento especial enquanto o arquivo de saída ainda está sendo gravado. |

Quando você estiver usando o armazenamento de BLOBs como saída, um novo arquivo será criado no blob nos seguintes casos:

* Se o arquivo atual excede o número máximo permitido de blocos (atualmente 50.000). Você pode alcançar o número máximo permitido de blocos sem atingir o tamanho máximo de blob permitido. Por exemplo, se a taxa de saída for alta, você pode ver mais bytes por bloco, e o tamanho do arquivo é maior. Se a taxa de saída for baixa, cada bloco tem menos dados e o tamanho do arquivo é menor.
* Se houver uma alteração de esquema na saída, e o formato de saída exigir esquema fixo (CSV e Avro).
* Se um trabalho for reiniciado externamente por um usuário, parando e iniciando-o, ou internamente para manutenção do sistema ou recuperação de erro.
* Se a consulta estiver totalmente particionada, e um novo arquivo for criado para cada partição de saída.
* Se o usuário excluir um arquivo ou um contêiner da conta de armazenamento.
* Se a saída for o tempo particionado usando o padrão de prefixo de caminho e um novo blob for usado quando a consulta for movida para a próxima hora.
* Se a saída for particionada por um campo personalizado, e um novo BLOB será criado por chave de partição, caso não exista.
* Se a saída for particionada por um campo personalizado em que a cardinalidade da chave de partição exceda 8.000, e um novo BLOB será criado por chave de partição.

## <a name="event-hubs"></a>Hubs de Eventos

Os serviço de [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) são um ingestor de eventos altamente escalonável de publicação/assinatura. Ele pode coletar milhões de eventos por segundo. Um uso de um hub de eventos como saída é quando a saída de um trabalho de Stream Analytics se torna a entrada de outro trabalho de streaming.

Você precisa de alguns parâmetros para configurar os fluxos de dados dos hubs de eventos como uma saída.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável usado em consultas para direcionar a saída da consulta para esse Hub de eventos. |
| Namespace de Hub de Eventos | Um contêiner para um conjunto de entidades de mensagens. Ao criar um novo hub de eventos, você também criou um namespace de Hub de eventos. |
| Nome do Hub de Eventos | O nome da saída do hub de eventos. |
| Nome da política do hub de eventos | A política de acesso compartilhado, que pode ser criada na guia **Configurar** do hub de eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política do hub de eventos | A chave de acesso compartilhado usada para autenticar o acesso ao namespace do hub de eventos. |
| Coluna de chave de partição | Opcional. Uma coluna que contém a chave de partição para saída do hub de eventos. |
| Formato de serialização de evento | O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificando | Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador | Aplicável somente para serialização de CSV. O Stream Analytics dá suporte a vários delimitadores comuns para a serialização de dados em formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar | Aplicável somente para serialização JSON. **Linha separada** especifica que a saída é formatada por ter cada objeto JSON separado por uma nova linha. **Matriz** especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar JSON separado por linha, pois não requer tratamento especial enquanto o arquivo de saída ainda está sendo gravado. |
| Colunas da propriedade | Opcional. Colunas separadas por vírgula que precisam ser anexadas como propriedades de usuário da mensagem de saída em vez da carga. Mais informações sobre esse recurso estão na seção [Propriedades de metadados personalizados para saída](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

Você pode usar [Power bi](https://powerbi.microsoft.com/) como uma saída para que um trabalho de Stream Analytics forneça uma experiência de visualização avançada dos resultados da análise. Você pode usar esse recurso para dashboards operacionais, geração de relatórios e relatórios orientados por métricas.

Power BI saída do Stream Analytics não está disponível no momento nas regiões do Azure China 21Vianet e do Azure Alemanha (T-Systems International).

A tabela a seguir lista os nomes de propriedade e suas descrições para configurar a saída Power BI.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Forneça um nome amigável que é usado em consultas para direcionar a saída da consulta para essa Power BI saída. |
| Workspace de grupo |Para habilitar o compartilhamento de dados com outros usuários do Power BI, você pode selecionar grupos dentro de sua conta do Power BI ou escolher **meu espaço de trabalho** se não quiser gravar em um grupo. Atualizar um grupo existente requer a renovação da autenticação do Power BI. |
| Nome do conjunto de dados |Forneça um nome de conjunto de resultados que você deseja que a Power BI saída use. |
| Nome da tabela |Forneça um nome de tabela sob o conjunto de dados da saída do Power BI. Atualmente, a saída do Power BI de trabalhos do Stream Analytics só podem ter uma tabela em um conjunto de dados. |
| Autorizar conexão | Você precisa autorizar com Power BI para definir as configurações de saída. Depois de conceder esse acesso de saída ao seu painel de Power BI, você pode revogar o acesso alterando a senha da conta de usuário, excluindo a saída do trabalho ou excluindo o trabalho de Stream Analytics. | 

Para obter uma explicação sobre como configurar um Power BI saída e painel, consulte o [Azure Stream Analytics e Power bi](stream-analytics-power-bi-dashboard.md) tutorial.

> [!NOTE]
> Não crie explicitamente o conjunto de tabelas e a tabela no painel Power BI. O conjunto de resultados e a tabela são preenchidos automaticamente quando o trabalho é iniciado e o trabalho começa a bombear a saída para Power BI. Se a consulta de trabalho não gerar nenhum resultado, o conjunto de tabelas e a tabela não serão criados. Se Power BI já tiver um conjunto de dados e uma tabela com o mesmo nome daquele fornecido neste trabalho de Stream Analytics, os dados existentes serão substituídos.
>

### <a name="create-a-schema"></a>Criar um esquema
Azure Stream Analytics cria um conjunto de Power BI e um esquema de tabela para o usuário, se eles ainda não existirem. Em todos os outros casos, a tabela é atualizada com novos valores. Atualmente, somente uma tabela pode existir em um conjunto de um DataSet. 

Power BI usa a política de retenção PEPS (primeiro a entrar, primeiro a sair). Os dados serão coletados em uma tabela até atingirem 200.000 linhas.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Converter um tipo de dados de Stream Analytics para Power BI
O Stream Analytics do Azure atualiza o modelo de dados dinamicamente no tempo de execução se o esquema de saída mudar. Alterações de nome de coluna, alterações de tipo de coluna e a adição ou remoção de colunas são controladas.

Esta tabela aborda as conversões de tipo de dados de [Stream Analytics tipos de dados](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) para tipos de Power bi [modelo de dados de entidade (EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), se um conjunto de dado Power bi e uma tabela não existirem.

Do Stream Analytics | Para o Power BI
-----|-----
bigint | Int64
nvarchar(max) | Cadeia
datetime | DateTime
float | Duplo
Matriz de registro | Tipo de cadeia de caracteres, valor constante "IRecord" ou "IArray"

### <a name="update-the-schema"></a>Atualizar o esquema
o Stream Analytics infere o esquema do modelo de dados com base no primeiro conjunto de eventos na saída. Posteriormente, se necessário, o esquema do modelo de dados é atualizado para acomodar os eventos de entrada que podem não se ajustar ao esquema original.

Evite a `SELECT *` consulta para impedir a atualização dinâmica do esquema em linhas. Além das possíveis implicações de desempenho, isso pode resultar em incerteza do tempo necessário para os resultados. Selecione os campos exatos que precisam ser mostrados no painel de Power BI. Além disso, os valores de dados devem estar em conformidade com o tipo de dados escolhido.


Anterior/atual | Int64 | Cadeia | DateTime | Duplo
-----------------|-------|--------|----------|-------
Int64 | Int64 | Cadeia | Cadeia | Duplo
Duplo | Duplo | Cadeia | Cadeia | Duplo
Cadeia | String | String | String | Cadeia 
DateTime | Cadeia | Cadeia |  DateTime | Cadeia

## <a name="table-storage"></a>Armazenamento de tabelas

[Armazenamento de Tabelas do Azure](../storage/common/storage-introduction.md) oferece armazenamento altamente disponível e altamente escalonável, para que um aplicativo possa ser dimensionado automaticamente para atender à demanda dos usuários. O armazenamento de tabela é o repositório de chave/atributo NoSQL da Microsoft, que pode ser usado para dados estruturados com menos restrições no esquema. O armazenamento de Tabela do Azure pode ser usado para armazenar dados de persistência e para recuperação eficiente.

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de tabela.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de tabelas. |
| Conta de armazenamento |O nome da conta de armazenamento em que você está enviando a saída. |
| Chave da conta de armazenamento |A chave de acesso associada à conta de armazenamento. |
| Nome da tabela |O nome da tabela. A tabela será criada se ela não existir. |
| Chave de partição |O nome da coluna de saída que contém a chave de partição. A chave de partição é um identificador exclusivo para a partição dentro de uma tabela que forma a primeira parte da chave primária de uma entidade. É um valor de cadeia de caracteres que pode ter até 1 KB de tamanho. |
| Chave de linha |O nome da coluna de saída que contém a chave de linha. A chave de linha é um identificador exclusivo para uma entidade em uma partição. Ela forma a segunda parte da chave primária da entidade. A chave de linha é um valor de cadeia de caracteres que pode ter até 1 KB de tamanho. |
| Tamanho do lote |É o número de registros para uma operação em lote. O padrão (100) é suficiente para a maioria dos trabalhos. Consulte a [especificação de operação em lote de tabela](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) para obter mais detalhes sobre como modificar essa configuração. |

## <a name="service-bus-queues"></a>Filas do Barramento de Serviço

As [filas do barramento de serviço](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) oferecem uma entrega de mensagem FIFO para um ou mais consumidores concorrentes. Normalmente, as mensagens são recebidas e processadas pelos receptores na ordem temporal em que foram adicionadas à fila. Cada mensagem é recebida e processada por apenas um consumidor de mensagem.

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de fila.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável usado em consultas para direcionar a saída da consulta para esta fila do barramento de serviço. |
| Namespace do Barramento de Serviço |Um contêiner para um conjunto de entidades de mensagens. |
| Nome da fila |O nome da fila do barramento de serviço. |
| Nome da política de fila |Ao criar uma fila, você também pode criar políticas de acesso compartilhado na guia **Configurar** da fila. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política de fila |A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. |
| Formato de serialização de evento |O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificando |Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador |Aplicável somente para serialização de CSV. O Stream Analytics dá suporte a vários delimitadores comuns para a serialização de dados em formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar |Aplicável somente para o tipo JSON. **Linha separada** especifica que a saída é formatada por ter cada objeto JSON separado por uma nova linha. **Matriz** especifica que a saída é formatada como uma matriz de objetos JSON. |
| Colunas da propriedade | Opcional. Colunas separadas por vírgula que precisam ser anexadas como propriedades de usuário da mensagem de saída em vez da carga. Mais informações sobre esse recurso estão na seção [Propriedades de metadados personalizados para saída](#custom-metadata-properties-for-output). |

O número de partições baseia-se [no tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). Chave de partição é um valor inteiro exclusivo para cada partição.

## <a name="service-bus-topics"></a>Tópicos do Barramento de Serviço
As filas do barramento de serviço fornecem um método de comunicação um-para-um do remetente para o destinatário. Os [Tópicos do barramento de serviço](https://msdn.microsoft.com/library/azure/hh367516.aspx) fornecem uma forma de comunicação de um para muitos.

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de tópico do barramento de serviço.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável usado em consultas para direcionar a saída da consulta para este tópico do barramento de serviço. |
| Namespace do Barramento de Serviço |Um contêiner para um conjunto de entidades de mensagens. Ao criar um novo hub de eventos, você também criou um namespace Barramento de Serviço. |
| Nome do tópico |Tópicos são entidades de envio de mensagens, semelhantes a filas e hubs de eventos. Elas foram projetadas para coletar fluxos de eventos de dispositivos e serviços. Quando um tópico é criado, ele também recebe um nome específico. As mensagens enviadas a um tópico não estão disponíveis, a menos que uma assinatura seja criada, portanto, verifique se há uma ou mais assinaturas no tópico. |
| Nome de política de tópico |Ao criar um tópico do barramento de serviço, você também pode criar políticas de acesso compartilhado na guia **Configurar** do tópico. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política de tópico |A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. |
| Formato de serialização de evento |O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificando |Se você estiver usando o formato CSV ou JSON, uma codificação deverá ser especificada. No momento, UTF-8 é o único formato de codificação com suporte. |
| Delimitador |Aplicável somente para serialização de CSV. O Stream Analytics dá suporte a vários delimitadores comuns para a serialização de dados em formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Colunas da propriedade | Opcional. Colunas separadas por vírgula que precisam ser anexadas como propriedades de usuário da mensagem de saída em vez da carga. Mais informações sobre esse recurso estão na seção [Propriedades de metadados personalizados para saída](#custom-metadata-properties-for-output). |

O número de partições baseia-se [no tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro exclusivo para cada partição.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) é um serviço de banco de dados distribuído globalmente que oferece escala elástica ilimitada em todo o mundo, consulta avançada e indexação automática em modelos de dados independentes de esquema. Para saber mais sobre Azure Cosmos DB opções de contêiner para Stream Analytics, consulte o artigo [Stream Analytics com Azure Cosmos DB como saída](stream-analytics-documentdb-output.md) .

Azure Cosmos DB saída do Stream Analytics não está disponível no momento nas regiões do Azure China 21Vianet e do Azure Alemanha (T-Systems International).

> [!Note]
> Neste momento, Azure Stream Analytics só dá suporte à conexão com o Azure Cosmos DB usando a API do SQL.
> Ainda não há suporte para outras APIs do Azure Cosmos DB. Se você apontar o Azure Stream Analytics para as contas do Azure Cosmos DB criado com as outras APIs, talvez os dados não sejam armazenados corretamente.

A tabela a seguir descreve as propriedades para a criação de uma saída do Azure Cosmos DB.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um alias para se referir a essa saída na sua consulta do Stream Analytics. |
| Coletor | Azure Cosmos DB. |
| Importar opção | Escolha **selecionar Cosmos DB da sua assinatura** ou **forneça as configurações de Cosmos DB manualmente**.
| ID da Conta | O nome ou o URI do ponto de extremidade da conta do Azure Cosmos DB. |
| Chave da conta | A chave de acesso compartilhado da conta do Azure Cosmos DB. |
| Banco de dados | O nome do banco de dados do Azure Cosmos DB. |
| Nome do contêiner | O nome do contêiner a ser usado, que deve existir no Cosmos DB. Exemplo:  <br /><ul><li> _MyContainer_: Deve existir um contêiner chamado "MyContainer".</li>|
| ID do Documento |Opcional. O nome do campo em eventos de saída que é usado para especificar a chave primária na qual as operações de inserção ou atualização são baseadas.

## <a name="azure-functions"></a>Verificação de
Azure Functions é um serviço de computação sem servidor que você pode usar para executar o código sob demanda não precisar provisionar explicitamente ou gerenciar a infraestrutura. Ele permite que você implemente o código disparado por eventos que ocorrem no Azure ou nos serviços de parceiros. Essa capacidade de Azure Functions responder a gatilhos faz com que seja uma saída natural para Azure Stream Analytics. Esse adaptador de saída permite que os usuários se conectem Stream Analytics ao Azure Functions e executem um script ou parte do código em resposta a uma variedade de eventos.

Azure Functions saída do Stream Analytics não está disponível no momento nas regiões do Azure China 21Vianet e do Azure Alemanha (T-Systems International).

O Azure Stream Analytics chama o Azure Functions por meio de gatilhos de HTTP. O adaptador de saída Azure Functions está disponível com as seguintes propriedades configuráveis:

| Nome da propriedade | Descrição |
| --- | --- |
| Aplicativo de função |O nome do seu aplicativo Azure Functions. |
| Função |O nome da função em seu aplicativo Azure Functions. |
| Chave |Se você quiser usar uma função do Azure de outra assinatura, poderá fazer isso fornecendo a chave para acessar sua função. |
| Tamanho de lote máximo |Uma propriedade que permite que você defina o tamanho máximo para cada lote de saída que é enviado para sua função do Azure. A unidade de entrada é em bytes. Por padrão, esse valor é 262.144 bytes (256 KB). |
| Contagem de lote máxima  |Uma propriedade que permite especificar o número máximo de eventos em cada lote enviado para Azure Functions. O valor padrão é 100. |

Quando Azure Stream Analytics recebe uma exceção de 413 ("entidade de solicitação HTTP muito grande") de uma função do Azure, ela reduz o tamanho dos lotes que ele envia para Azure Functions. Em seu código de função do Azure, use essa exceção para certificar-se de que o Azure Stream Analytics não envie lotes muito grandes. Além disso, certifique-se de que a contagem máxima de lotes e os valores de tamanho usados na função sejam consistentes com os valores inseridos no portal de Stream Analytics.

Além disso, em uma situação em que não há nenhuma aterrissagem de evento em uma janela de tempo, nenhuma saída é gerada. Como resultado, a função **computeResult** não é chamada. Esse comportamento é consistente com as funções de agregação em janelas internas.

## <a name="custom-metadata-properties-for-output"></a>Propriedades de metadados personalizadas para saída 

Você pode anexar colunas de consulta como propriedades de usuário às suas mensagens de saída. Essas colunas não entram no conteúdo. As propriedades estão presentes na forma de um dicionário na mensagem de saída. *Key* é o nome da coluna e *Value* é o valor da coluna no dicionário Properties. Todos os tipos de dados de Stream Analytics têm suporte, exceto registro e matriz.  

Saídas com suporte: 
* Fila do Barramento de Serviço 
* Tópico do Barramento de Serviço 
* Hub de eventos 

No exemplo a seguir, adicionamos os dois campos `DeviceId` e `DeviceStatus` aos metadados. 
* Consulta: `select *, DeviceId, DeviceStatus from iotHubInput`
* Configuração de saída:`DeviceId,DeviceStatus`

![Colunas da propriedade](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

A captura de tela a seguir mostra as propriedades da mensagem de saída inspecionadas no EventHub por meio do [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer).

![Propriedades personalizadas do evento](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Particionamento

A tabela a seguir resume o suporte de partição e o número de gravadores de saída para cada tipo de saída:

| Tipo de saída | Suporte ao particionamento | Chave de partição  | Número de gravadores de saída |
| --- | --- | --- | --- |
| Azure Data Lake Store | Sim | Use tokens {Date} e {time} no padrão de prefixo de caminho. Escolha o formato de data, como aaaa/MM/DD, DD/MM/aaaa ou MM-DD-AAAA. HH é usado para o formato de hora. | Segue o particionamento de entrada para [consultas totalmente paralelizáveis](stream-analytics-scale-jobs.md). |
| Banco de Dados SQL do Azure | Sim, precisa ser habilitado. | Com base na cláusula PARTITION BY na consulta. | Quando a opção herdar particionamento estiver habilitada, o seguirá o particionamento de entrada para [consultas totalmente paralelizáveiss](stream-analytics-scale-jobs.md). Para saber mais sobre como obter um melhor desempenho de taxa de transferência de gravação quando você estiver carregando dados no Azure SQL Database, consulte [Azure Stream Analytics saída para o banco de dados SQL do Azure](stream-analytics-sql-output-perf.md). |
| Armazenamento de Blob do Azure | Sim | Use os tokens {Date} e {time} de seus campos de evento no padrão de caminho. Escolha o formato de data, como aaaa/MM/DD, DD/MM/aaaa ou MM-DD-AAAA. HH é usado para o formato de hora. A saída de blob pode ser particionada por um atributo de evento personalizado único {fieldname} ou {datetime:\<specifier>}. | Segue o particionamento de entrada para [consultas totalmente paralelizáveis](stream-analytics-scale-jobs.md). |
| Hubs de Eventos do Azure | Sim | Sim | Varia dependendo do alinhamento da partição.<br /> Quando a chave de partição para saída do hub de eventos é alinhada igualmente com a etapa de consulta upstream (anterior), o número de gravadores é igual ao número de partições na saída do hub de eventos. Cada gravador usa a [classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) para enviar eventos para a partição específica. <br /> Quando a chave de partição para saída do hub de eventos não está alinhada com a etapa de consulta upstream (anterior), o número de gravadores é o mesmo que o número de partições na etapa anterior. Cada gravador usa a [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) em **EventHubClient** para enviar eventos para todas as partições de saída. |
| Power BI | Não | Nenhum | Não aplicável. |
| Armazenamento da tabela do Azure | Sim | Qualquer coluna de saída.  | Segue o particionamento de entrada para as [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). |
| Tópico do Barramento de Serviço do Azure | Sim | Escolhido automaticamente. O número de partições baseia-se no [tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro exclusivo para cada partição.| Mesmo que o número de partições no tópico de saída.  |
| Fila do Barramento de Serviço do Azure | Sim | Escolhido automaticamente. O número de partições baseia-se no [tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro exclusivo para cada partição.| Mesmo que o número de partições na fila de saída. |
| Azure Cosmos DB | Sim | Com base na cláusula PARTITION BY na consulta. | Segue o particionamento de entrada para as [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). |
| Verificação de | Não | Nenhum | Não aplicável. |

O número de gravadores de saída também pode ser `INTO <partition count>` controlado usando a cláusula (consulte [into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) em sua consulta, o que pode ser útil para atingir uma topologia de trabalho desejada. Se o seu adaptador de saída não estiver particionado, a falta de dados em uma partição de entrada causará um atraso até a quantidade de tempo de chegada tardia. Nesses casos, a saída é mesclada a um único gravador, o que pode causar afunilamentos em seu pipeline. Para saber mais sobre a política de chegada tardia, consulte [Azure Stream Analytics considerações sobre ordem de evento](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída
O Azure Stream Analytics usa lotes de tamanho variável para processar eventos e gravar em saídas. Normalmente, o mecanismo de Stream Analytics não grava uma mensagem por vez e usa lotes para obter eficiência. Quando a taxa de eventos de entrada e saída é alta, Stream Analytics usa lotes maiores. Quando a taxa de saída é baixa, ele usa lotes menores para manter a latência baixa.

A tabela a seguir explica algumas das considerações sobre o envio em lote de saída:

| Tipo de saída | Tamanho máximo de mensagem | Otimização de tamanho de lote |
| :--- | :--- | :--- |
| Azure Data Lake Store | Consulte [limites de data Lake Storage](../azure-subscription-service-limits.md#data-lake-store-limits). | Use até 4 MB por operação de gravação. |
| Banco de Dados SQL do Azure | Configurável usando a contagem máxima de lotes. máximo de 10.000 e 100 mínimos de linhas por única inserção em massa por padrão.<br />Consulte [limites do SQL do Azure](../sql-database/sql-database-resource-limits.md). |  Cada lote é inicialmente inserido em massa com a contagem máxima de lotes. O lote é dividido na metade (até a contagem de lote mínima) com base em erros com nova tentativa do SQL. |
| Armazenamento de Blob do Azure | Consulte [limites de armazenamento do Azure](../azure-subscription-service-limits.md#storage-limits). | O tamanho máximo do bloco de BLOBs é 4 MB.<br />A contagem máxima de Bock de blob é 50.000. |
| Hubs de Eventos do Azure  | 256 KB ou 1 MB por mensagem. <br />Consulte [limites de hubs de eventos](../event-hubs/event-hubs-quotas.md). |  Quando o particionamento de entrada/saída não está alinhado, cada evento é `EventData` empacotado individualmente no e enviado em um lote de até o tamanho máximo da mensagem. Isso também ocorrerá se [as propriedades de metadados personalizados](#custom-metadata-properties-for-output) forem usadas. <br /><br />  Quando o particionamento de entrada/saída está alinhado, vários eventos são empacotados `EventData` em uma única instância, até o tamanho máximo da mensagem e enviados. |
| Power BI | Confira [Power bi limites da API REST](https://msdn.microsoft.com/library/dn950053.aspx). |
| Armazenamento da tabela do Azure | Consulte [limites de armazenamento do Azure](../azure-subscription-service-limits.md#storage-limits). | O padrão é 100 entidades por transação única. Você pode configurá-lo para um valor menor, conforme necessário. |
| Fila do Barramento de Serviço do Azure   | 256 KB por mensagem para a camada Standard, 1MB para a camada Premium.<br /> Consulte [limites do barramento de serviço](../service-bus-messaging/service-bus-quotas.md). | Use um único evento por mensagem. |
| Tópico do Barramento de Serviço do Azure | 256 KB por mensagem para a camada Standard, 1MB para a camada Premium.<br /> Consulte [limites do barramento de serviço](../service-bus-messaging/service-bus-quotas.md). | Use um único evento por mensagem. |
| Azure Cosmos DB   | Consulte [limites de Azure Cosmos DB](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | O tamanho do lote e a frequência de gravação são ajustados dinamicamente com base nas respostas Azure Cosmos DB. <br /> Não há limitações predeterminadas de Stream Analytics. |
| Verificação de   | | O tamanho do lote padrão é 262.144 bytes (256 KB). <br /> A contagem de eventos padrão por lote é 100. <br /> O tamanho do lote é configurável e pode ser aumentado ou diminuído nas [opções de saída](#azure-functions) do Stream Analytics.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> 
> [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
