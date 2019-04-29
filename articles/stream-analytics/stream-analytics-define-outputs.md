---
title: Entender as saídas do Azure Stream Analytics
description: Este artigo descreve as opções de saída de dados disponíveis no Azure Stream Analytics, incluindo o Power BI para resultados da análise.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/25/2019
ms.custom: seodec18
ms.openlocfilehash: 03871c3f3627e85cc2af2f05a5fba38bd8069a15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481056"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Entender as saídas do Azure Stream Analytics
Este artigo descreve os tipos de saídas disponíveis para um trabalho do Azure Stream Analytics. As saídas permitem armazenar e salvar os resultados do trabalho do Stream Analytics. Usando os dados de saída, você pode fazer ainda mais análise de negócios e data warehouse de seus dados.

Quando você projeta sua consulta do Stream Analytics, fazer referência ao nome da saída usando o [cláusula INTO](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Você pode usar uma única saída por trabalho, ou várias saídas por trabalho de streaming (se necessário), fornecendo várias cláusulas INTO na consulta.

Para criar, editar e testar o trabalho do Stream Analytics produz, você pode usar o [portal do Azure](stream-analytics-quick-create-portal.md#configure-job-output), [do Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), e [Visual Studio](stream-analytics-quick-create-vs.md).

Suporte de alguns tipos de saídas [particionamento](#partitioning). [Tamanhos de lote de saída](#output-batch-size) variam para otimizar a taxa de transferência.


## <a name="azure-data-lake-store"></a>Repositório Azure Data Lake
O Stream Analytics dá suporte ao [Repositório Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store é um repositório em hiperescala em toda a empresa, para cargas de trabalho analíticos de big data. Você pode usar o Data Lake Store para armazenar dados de qualquer tamanho, tipo e velocidade de ingestão para análises operacionais e EXPLORATÓRIAS. Stream Analytics deve ser autorizado a acessar o Data Lake Store.

Saída do repositório Azure Data Lake do Stream Analytics não está disponível no Azure na China (21Vianet) e regiões do Azure Alemanha (T-Systems internacional).

### <a name="authorize-an-azure-data-lake-store-account"></a>Autorizar uma conta do Azure Data Lake Store

1. Quando você seleciona o Data Lake Store como uma saída no portal do Azure, você será solicitado a autorizar uma conexão a uma instância existente do Data Lake Store.

   ![Autorizar uma conexão ao Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. Se você já tiver acesso ao Data Lake Store, selecione **autorizar agora**. Uma página pop-up e indica **redirecionando para autorização**. Após a autorização bem-sucedida, você verá a página que permite que você configure a saída do Data Lake Store.

3. Depois de ter a conta do Data Lake Store autenticada, você pode configurar as propriedades de saída do Data Lake Store.

   ![Definir a saída do Data Lake Store como Stream Analytics](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

A tabela a seguir lista os nomes de propriedade e suas descrições para configurar a saída do Data Lake Store.   

| Nome da propriedade | DESCRIÇÃO |
| --- | --- |
| Alias de saída | Um nome amigável utilizado em consultas para direcionar a saída de consulta para o Data Lake Store. |
| Nome da conta | O nome da conta do Data Lake Store onde você está enviando a saída. Você verá uma lista suspensa de contas do Data Lake Store que estão disponíveis em sua assinatura. |
| Padrão de prefixo de caminho | O caminho do arquivo que é usado para gravar seus arquivos na conta do Data Lake Store especificada. Você pode especificar uma ou mais instâncias de {data} e {time} variáveis:<br /><ul><li>Exemplo 1: pasta1/logs/{data}/{hora}</li><li>Exemplo 2: pasta1/logs/{data}</li></ul><br />O carimbo de hora da estrutura de pasta criada segue o horário UTC e não local.<br /><br />Se o padrão de caminho de arquivo não contiver uma barra à direita (/), o último padrão no caminho do arquivo é tratado como um prefixo de nome de arquivo. <br /><br />Novos arquivos são criados nessas circunstâncias:<ul><li>Alteração no esquema de saída</li><li>Reinicialização interna ou externa de um trabalho</li></ul> |
| Formato de data | Opcional. Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD |
|Formato de hora | Opcional. Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH. |
| Formato de serialização do evento | O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro.|
| Codificação | Se você estiver usando o formato CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento.|
| Delimitador | Aplicável somente para serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical.|
| Formatar | Aplicável somente para serialização JSON. **Uma linha separada** Especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. **Matriz** Especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar JSON separado por linha, pois não requer nenhuma manipulação especial enquanto o arquivo de saída ainda está sendo gravado.|

### <a name="renew-data-lake-store-authorization"></a>Renovar autorização do Data Lake Store
Você precisa autenticar novamente sua conta do Data Lake Store caso sua senha tenha sido alterada depois de seu trabalho ser criado ou autenticado pela última vez. Se você não autenticar novamente, o seu trabalho não produz resultados de saída e mostra um erro que indica a necessidade de uma nova autorização nos Logs de operação. 

Atualmente, o token de autenticação precisa ser atualizado manualmente a cada 90 dias para todos os trabalhos com a saída do Data Lake Store. Você pode superar essa limitação por [autenticando por meio de identidades (visualização) de gerenciadas](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls).

Renovar a autorização:

1. Selecione **parar** para interromper seu trabalho.
1. Vá para a Store lago de dados de saída e selecione o **renovar a autorização** link.

   Por um curto período, uma página pop-up indica **redirecionando para autorização**. Se a autorização for bem-sucedida, a página indica **autorização foi renovada com êxito** e, em seguida, fecha automaticamente. 
   
1. Selecione **salvar** na parte inferior da página. Você pode, em seguida, reinicie o trabalho do **hora da última interrupção** para evitar a perda de dados.

![Renovar autorização do Data Lake Store na saída](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## <a name="sql-database"></a>Banco de dados SQL
Você pode usar [banco de dados SQL](https://azure.microsoft.com/services/sql-database/) como uma saída de dados que são relacionais por natureza ou para aplicativos que dependem do conteúdo hospedado em um banco de dados relacional. Trabalhos do Stream Analytics gravar em uma tabela existente no banco de dados SQL. O esquema da tabela deve corresponder exatamente os campos e seus tipos na saída do trabalho. Você também pode especificar [do Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) opção de saída como uma saída por meio do banco de dados SQL. Para saber mais sobre maneiras de melhorar a taxa de transferência de gravação, consulte a [Stream Analytics com o banco de dados SQL Azure como saída](stream-analytics-sql-output-perf.md) artigo. 

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída de banco de dados SQL.

| Nome da propriedade | DESCRIÇÃO |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse banco de dados. |
| Banco de dados | O nome do banco de dados no qual você está enviando a saída. |
| Nome do servidor | Nome do servidor do Banco de Dados SQL do Azure. |
| Nome de Usuário | O nome de usuário que tenha acesso de gravação ao banco de dados. Stream Analytics dá suporte à autenticação do SQL. |
| Senha | A senha para se conectar ao banco de dados. |
| Tabela | O nome da tabela em que a saída é gravada. O nome da tabela diferencia maiusculas de minúsculas. O esquema da tabela deve corresponder exatamente ao número de campos e seus tipos que gera a saída do trabalho. |
|Herdar esquema de partição| Uma opção para herdar o esquema de particionamento de sua etapa de consulta anterior, para habilitar totalmente paralela topologia com vários gravadores à tabela. Para obter mais informações, confira [Saída do Azure Stream Analytics para Banco de Dados SQL do Azure](stream-analytics-sql-output-perf.md).|
|Corresponder contagem do lote| O limite recomendado no número de registros enviados com cada bulk insert a transação.|

> [!NOTE]
> Atualmente, há suporte para a oferta do Banco de Dados SQL do Azure uma saída do trabalho do Stream Analytics. Não há suporte para uma máquina virtual do Azure executando o SQL Server com um banco de dados anexado. Isso está sujeito à mudança em versões futuras.
>

## <a name="blob-storage"></a>Armazenamento de blob
O armazenamento de BLOBs do Azure oferece uma solução econômica e escalonável para armazenar grandes quantidades de dados não estruturados na nuvem. Para obter uma introdução sobre o armazenamento de Blob e seu uso, consulte [como usar blobs](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de blob.

| Nome da propriedade       | DESCRIÇÃO                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias de saída        | Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de blob. |
| Conta de armazenamento     | O nome da conta de armazenamento no qual você está enviando a saída.               |
| Chave da conta de armazenamento | A chave secreta associada à conta de armazenamento.                              |
| Contêiner de armazenamento   | Um agrupamento lógico de blobs armazenados no serviço Blob do Azure. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob. |
| Padrão de caminho | Opcional. O padrão de caminho de arquivo que é usado para gravar seus blobs no contêiner especificado. <br /><br /> No padrão de caminho, você pode optar por usar uma ou mais instâncias das variáveis de data e hora para especificar a frequência com que os blobs são gravados: <br /> {data}, {hora} <br /><br />Você pode usar um particionamento de blob para especificar um nome de {field} personalizado dos dados do seu evento para os blobs de partição. O nome do campo é alfanumérico e pode incluir espaços, hífens e sublinhados. Restrições em campos personalizados incluem o seguinte: <ul><li>Nomes de campo não diferencia maiusculas de minúsculas. Por exemplo, o serviço não pode diferenciar entre a coluna "ID" e a coluna "id".</li><li>Campos aninhados não são permitidos. Em vez disso, use um alias na consulta de trabalho para o campo de "mesclar".</li><li>Expressões não podem ser usadas como um nome de campo.</li></ul> <br />Esse recurso permite o uso de configurações de especificador de formato personalizado de data/hora no caminho. Os formatos personalizados de data e hora devem ser especificados um de cada vez, entre a palavra-chave {datetime:\<specifier>}. Entradas permitidas para \<especificador > são AAAA, MM, M, dd, d, HH, H, mm, m, ss ou s. O {datetime:\<especificador >} palavra-chave pode ser usada várias vezes no caminho para formar as configurações de data/hora personalizado. <br /><br />Exemplos: <ul><li>Exemplo 1: cluster1/logs /{data}/{hora}</li><li>Exemplo 2: cluster1/logs/{data}</li><li>Exemplo 3: cluster1/{client_id}/{data}/{hora}</li><li>Exemplo 4: cluster1/{datetime:ss}/{myField} em que a consulta é: SELECT data.myField AS myField FROM Input;</li><li>Exemplo 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />O carimbo de hora da estrutura de pasta criada segue o horário UTC e não local.<br /><br />A nomenclatura de arquivo usa a seguinte convenção: <br /><br />{Padrão de prefixo de caminho}/schemaHashcode_Guid_Number.extension<br /><br />Exemplo de arquivos de saída:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Para obter mais informações sobre esse recurso, consulte [particionamento de saída de BLOBs do Azure Stream Analytics personalizado](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato de data | Opcional. Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD |
| Formato de hora | Opcional. Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH. |
| Formato de serialização do evento | Formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação    | Se você estiver usando o formato CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador   | Aplicável somente para serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar      | Aplicável somente para serialização JSON. **Uma linha separada** Especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. **Matriz** Especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar JSON separado por linha, pois não requer nenhuma manipulação especial enquanto o arquivo de saída ainda está sendo gravado. |

Quando você estiver usando o armazenamento de BLOBs como saída, um novo arquivo é criado no blob nos seguintes casos:

* Se o arquivo atual excede o número máximo permitido de blocos (atualmente 50.000). Você pode alcançar o número máximo permitido de blocos sem atingir o tamanho máximo permitido do blob. Por exemplo, se a taxa de saída for alta, você pode ver mais bytes por bloco, e o tamanho do arquivo é maior. Se a taxa de saída for baixa, cada bloco tem menos dados e o tamanho do arquivo é menor.
* Se houver uma alteração de esquema na saída, e o formato de saída requer esquema fixo (CSV e Avro).
* Se um trabalho for reiniciado externamente por um usuário, parando e iniciando-o, ou internamente para manutenção do sistema ou recuperação de erro.
* Se a consulta for totalmente particionada e um novo arquivo é criado para cada partição de saída.
* Se o usuário exclui um arquivo ou um contêiner da conta de armazenamento.
* Se a saída for tempo particionado usando o padrão de prefixo de caminho e um novo blob é usado quando a consulta se move para a próxima hora.
* Se a saída for particionada por um campo personalizado e um novo blob é criado por if de chave de partição não existe.
* Se a saída for particionada por um campo personalizado no qual a partição de cardinalidade da chave excede 8.000 e um novo blob é criado por chave de partição.

## <a name="event-hubs"></a>Hubs de Eventos
Os serviço de [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) são um ingestor de eventos altamente escalonável de publicação/assinatura. Ele pode coletar milhões de eventos por segundo. Um uso de um hub de eventos como saída é quando a saída de um trabalho do Stream Analytics torna-se a entrada de outro trabalho de streaming.

Você precisa de alguns parâmetros para configurar fluxos de dados dos hubs de eventos como saída.

| Nome da propriedade | DESCRIÇÃO |
| --- | --- |
| Alias de saída | Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse hub de eventos. |
| Namespace do Hub de Eventos |Um contêiner para um conjunto de entidades de mensagens. Quando você criou um novo hub de eventos, você também criou um namespace de hub de eventos. |
| Nome do Hub de Eventos | O nome da sua saída do hub de eventos. |
| Nome da política do hub de eventos | A política de acesso compartilhado, o que você pode criar no hub de eventos **configurar** guia. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política do hub de eventos | A chave de acesso compartilhado que é usada para autenticar o acesso ao namespace de hub de eventos. |
| Coluna de chave de partição | Opcional. Uma coluna que contém a chave de partição para a saída do hub de eventos. |
| Formato de serialização do evento | O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação | Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador | Aplicável somente para serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar | Aplicável somente para serialização JSON. **Uma linha separada** Especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. **Matriz** Especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar JSON separado por linha, pois não requer nenhuma manipulação especial enquanto o arquivo de saída ainda está sendo gravado. |
| Colunas da propriedade | Opcional. Colunas separada por vírgulas que precisem ser anexados como propriedades de usuário da mensagem de saída, em vez da carga. Obter mais informações sobre esse recurso estão na seção [propriedades de metadados personalizados para a saída](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI
Você pode usar [Power BI](https://powerbi.microsoft.com/) como uma saída de um trabalho de Stream Analytics para fornecer uma experiência rica de visualização dos resultados da análise. Você pode usar esse recurso para painéis operacionais, geração de relatórios e relatórios controlados por métrica.

A saída do Power BI do Stream Analytics atualmente não está disponível nas regiões Azure China (21Vianet) e Azure Germany (T-Systems International).

### <a name="authorize-a-power-bi-account"></a>Autorizar uma conta do Power BI
1. Quando o Power BI é selecionado como uma saída no portal do Azure, você será solicitado a autorizar um usuário existente do Power BI ou criar uma nova conta do Power BI.
   
   ![Autorizar um usuário do Power BI para configurar a saída](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. Criar uma nova conta se você ainda não tiver um e, em seguida, selecione **autorizar agora**. A página a seguir será exibida:
   
   ![Autenticar-se no Power BI de conta do Azure](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. Forneça a conta corporativa ou de estudante para autorizar a saída do Power BI. Se você ainda não esteja inscrito no Power BI, selecione **Inscreva-se agora**. A conta corporativa ou de estudante que você usa para o Power BI pode ser diferente da conta de assinatura do Azure que você está conectado com.

### <a name="configure-the-power-bi-output-properties"></a>Configurar as propriedades de saída do Power BI
Depois de ter a conta do Power BI foi autenticada, você pode configurar as propriedades de saída do Power BI. A tabela a seguir lista os nomes de propriedade e suas descrições para configurar a saída do Power BI.

| Nome da propriedade | DESCRIÇÃO |
| --- | --- |
| Alias de saída |Forneça um nome amigável que é usado em consultas para direcionar a saída da consulta para essa saída do Power BI. |
| Agrupar o workspace |Para habilitar o compartilhamento de dados com outros usuários do Power BI, você pode selecionar grupos dentro de sua conta do Power BI ou escolher **meu espaço de trabalho** se você não quiser gravar em um grupo. Atualizar um grupo existente requer a renovação da autenticação do Power BI. |
| Nome do conjunto de dados |Forneça um nome de conjunto de dados que você deseja que a saída do Power BI para uso. |
| Nome da tabela |Forneça um nome de tabela sob o conjunto de dados da saída do Power BI. Atualmente, a saída do Power BI de trabalhos do Stream Analytics só podem ter uma tabela em um conjunto de dados. |

Para obter instruções de configuração de uma saída do Power BI e o painel, consulte o [do Azure Stream Analytics e Power BI](stream-analytics-power-bi-dashboard.md) artigo.

> [!NOTE]
> Não crie explicitamente o conjunto de dados e a tabela no painel do Power BI. O conjunto de dados e tabela são preenchidos automaticamente quando o trabalho é iniciado e o trabalho começa a produzir a saída no Power BI. Se a consulta do trabalho não gerar resultados, o conjunto de dados e a tabela não são criados. Se o Power BI já tiver um conjunto de dados e uma tabela com o mesmo nome que aquele fornecido no trabalho do Stream Analytics, os dados existentes são substituídos.
>

### <a name="create-a-schema"></a>Criar um esquema
O Azure Stream Analytics cria um esquema de conjunto de dados e tabela do Power BI para o usuário se eles ainda não existirem. Em todos os outros casos, a tabela é atualizada com novos valores. Atualmente, apenas uma tabela pode existir dentro de um conjunto de dados. 

O Power BI usa primeiro a entrar, primeiro a sair (PEPS) política de retenção. Os dados coletarão em uma tabela até que ele atinja 200.000 linhas.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Converter um tipo de dados do Stream Analytics ao Power BI
O Stream Analytics do Azure atualiza o modelo de dados dinamicamente no tempo de execução se o esquema de saída mudar. Alterações de nome de coluna, alterações de tipo de coluna e a adição ou remoção de colunas são controladas.

Esta tabela abrange as conversões de tipo de dados do [tipos de dados do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835065.aspx) Power BI [tipos de modelo de dados de entidade (EDM)](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/entity-data-model), se um conjunto de dados do Power BI e uma tabela não existirem.

Do Stream Analytics | Para o Power BI
-----|-----
bigint | Int64
nvarchar(max) | Cadeia de caracteres
Datetime | DateTime
flutuante | Double
Matriz de registro | Cadeia de caracteres de tipo, o valor da constante "IRecord" ou "IArray"

### <a name="update-the-schema"></a>Atualizar o esquema
o Stream Analytics infere o esquema do modelo de dados com base no primeiro conjunto de eventos na saída. Posteriormente, se necessário, o esquema de modelo de dados é atualizado para acomodar os eventos de entrada podem não caber no esquema original.

Evite o `SELECT *` consulta para impedir que a atualização de esquema dinâmico em linhas. Além das possíveis implicações de desempenho, ele pode resultar na incerteza do tempo necessário para os resultados. Selecione os campos exatos que precisam ser mostradas no painel do Power BI. Além disso, os valores de dados devem estar em conformidade com o tipo de dados escolhido.


Anterior/atual | Int64 | Cadeia de caracteres | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Cadeia de caracteres | Cadeia de caracteres | Double
Double | Double | Cadeia de caracteres | Cadeia de caracteres | Double
Cadeia de caracteres | Cadeia de caracteres | Cadeia de caracteres | Cadeia de caracteres | Cadeia de caracteres 
DateTime | Cadeia de caracteres | Cadeia de caracteres |  DateTime | Cadeia de caracteres


### <a name="renew-power-bi-authorization"></a>Renovar a autorização do Power BI
Se a senha da conta do Power BI for alterado depois que o trabalho do Stream Analytics foi criado ou autenticado pela última vez, você precisa autenticar novamente o Stream Analytics. Se a Autenticação Multifator estiver configurada no locatário do Azure Active Directory (Azure AD) também será necessário renovar a autorização do Power BI a cada duas semanas. Um sintoma desse problema é nenhuma saída de trabalho e um "erro de Autenticar usuário" nos Logs de Operação:

  ![Erro do usuário de autenticação do Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Para resolver esse problema, pare seu trabalho em execução e vá para a saída do Power BI. Selecione o link **Renovar autorização** e reinicie o trabalho da **Hora da Última Interrupção** para evitar a perda de dados.

  ![Renovar a autorização do Power BI para saída](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## <a name="table-storage"></a>Armazenamento de tabela
[Armazenamento de Tabelas do Azure](../storage/common/storage-introduction.md) oferece armazenamento altamente disponível e altamente escalonável, para que um aplicativo possa ser dimensionado automaticamente para atender à demanda dos usuários. Armazenamento de tabela é o repositório de chave/atributo NoSQL da Microsoft, você pode usar para dados estruturados com menos restrições no esquema. O armazenamento de Tabela do Azure pode ser usado para armazenar dados de persistência e para recuperação eficiente.

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de tabela.

| Nome da propriedade | DESCRIÇÃO |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de tabelas. |
| Conta de armazenamento |O nome da conta de armazenamento no qual você está enviando a saída. |
| Chave da conta de armazenamento |A chave de acesso associada à conta de armazenamento. |
| Nome da tabela |O nome da tabela. A tabela será criada se ela não existir. |
| Chave de partição |O nome da coluna de saída que contém a chave de partição. A chave de partição é um identificador exclusivo para a partição dentro de uma tabela que forma a primeira parte da chave primária da entidade. É um valor de cadeia de caracteres que pode ser de até 1 KB de tamanho. |
| Chave de linha |O nome da coluna de saída que contém a chave de linha. A chave de linha é um identificador exclusivo para uma entidade dentro de uma partição. Ela forma a segunda parte da chave primária da entidade. A chave de linha é um valor de cadeia de caracteres que pode ser de até 1 KB de tamanho. |
| Tamanho do lote |É o número de registros para uma operação em lote. O padrão (100) é suficiente para a maioria dos trabalhos. Consulte a [especificação da operação de lote de tabela](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para obter mais detalhes sobre como modificar essa configuração. |

## <a name="service-bus-queues"></a>Filas do Barramento de Serviço
[As filas do barramento de serviço](https://msdn.microsoft.com/library/azure/hh367516.aspx) oferecem uma entrega de mensagens PEPS para um ou mais consumidores concorrentes. Normalmente, as mensagens são recebidas e processadas pelos receptores na ordem cronológica na qual eles foram adicionados à fila. Cada mensagem é recebida e processada por apenas um consumidor de mensagem.

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de fila.

| Nome da propriedade | DESCRIÇÃO |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para essa fila do barramento de serviço. |
| Namespace do Barramento de Serviço |Um contêiner para um conjunto de entidades de mensagens. |
| Nome da fila |O nome da fila do barramento de serviço. |
| Nome da política de fila |Quando você cria uma fila, você também pode criar políticas de acesso compartilhado na fila do **configurar** guia. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política de fila |A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. |
| Formato de serialização do evento |O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação |Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador |Aplicável somente para serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar |Aplicável somente para o tipo JSON. **Uma linha separada** Especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. **Matriz** Especifica que a saída é formatada como uma matriz de objetos JSON. |
| Colunas da propriedade | Opcional. Colunas separada por vírgulas que precisem ser anexados como propriedades de usuário da mensagem de saída, em vez da carga. Obter mais informações sobre esse recurso estão na seção [propriedades de metadados personalizados para a saída](#custom-metadata-properties-for-output). |

O número de partições baseia-se [no tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). Chave de partição é um valor inteiro exclusivo para cada partição.

## <a name="service-bus-topics"></a>Tópicos do Barramento de Serviço
As filas do barramento de serviço fornecem um método de comunicação de um para um do remetente ao destinatário. [Tópicos do barramento de serviço](https://msdn.microsoft.com/library/azure/hh367516.aspx) fornecem uma forma de um-para-muitos de comunicação.

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de tópico.

| Nome da propriedade | DESCRIÇÃO |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse tópico do barramento de serviço. |
| Namespace do Barramento de Serviço |Um contêiner para um conjunto de entidades de mensagens. Ao criar um novo hub de eventos, você também criou um namespace Barramento de Serviço. |
| Nome do tópico |Tópicos são entidades de envio de mensagens, semelhantes a filas e hubs de eventos. Eles são projetados para coletar fluxos de eventos de dispositivos e serviços. Quando um tópico é criado, ele também forneceu um nome específico. As mensagens enviadas para um tópico não estão disponíveis, a menos que uma assinatura for criada, portanto, verifique se há uma ou mais assinaturas sob o tópico. |
| Nome da política de tópico |Quando você cria um tópico, você também pode criar políticas de acesso compartilhado sobre o tópico **configurar** guia. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política de tópico |A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. |
| Formato de serialização do evento |O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação |Se você estiver usando o formato CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador |Aplicável somente para serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Colunas da propriedade | Opcional. Colunas separada por vírgulas que precisem ser anexados como propriedades de usuário da mensagem de saída, em vez da carga. Obter mais informações sobre esse recurso estão na seção [propriedades de metadados personalizados para a saída](#custom-metadata-properties-for-output). |

O número de partições baseia-se [no tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro exclusivo para cada partição.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[O Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) é um serviço de banco de dados distribuído globalmente que oferece dimensionamento Elástico ilimitado ao redor do mundo, consulta avançada e indexação automática em modelos de dados independente de esquema. Para saber mais sobre as opções de coleção do Azure Cosmos DB para Stream Analytics, consulte o [Stream Analytics com o Azure Cosmos DB como saída](stream-analytics-documentdb-output.md) artigo.

A saída do Azure Cosmos DB do Stream Analytics não está disponível atualmente nas regiões Azure China (21Vianet) e Azure Germany (T-Systems International).

> [!Note]
> Neste momento, o Azure Stream Analytics suporta apenas a conexão ao Azure Cosmos DB usando a API do SQL.
> Ainda não há suporte para outras APIs do Azure Cosmos DB. Se você apontar o Azure Stream Analytics para as contas do Azure Cosmos DB criado com as outras APIs, talvez os dados não sejam armazenados corretamente.

A tabela a seguir descreve as propriedades para a criação de uma saída do Azure Cosmos DB.

| Nome da propriedade | DESCRIÇÃO |
| --- | --- |
| Alias de saída | Um alias para se referir a essa saída na sua consulta do Stream Analytics. |
| Coletor | Azure Cosmos DB. |
| Importar opção | Escolha **selecione Cosmos DB de sua assinatura** ou **do Cosmos DB fornecem configurações manualmente**.
| ID da Conta | O nome ou o URI do ponto de extremidade da conta do Azure Cosmos DB. |
| Chave de conta | A chave de acesso compartilhado da conta do Azure Cosmos DB. |
| Banco de dados | O nome do banco de dados do Azure Cosmos DB. |
| Padrão de nome da coleção | O nome da coleção ou o padrão para as coleções a serem usados. <br />Você pode construir o formato de nome de coleção usando o token opcional {partition}, onde as partições iniciam em 0. Dois exemplos:  <br /><ul><li> _MyCollection_: Uma coleção denominada "MyCollection" deve existir.</li>  <li> _A MyCollection {partition}_: Com base na coluna de particionamento.</li></ul> Coleções de colunas de particionamento devem existir: "MyCollection0," "MyCollection1", "MyCollection2" e assim por diante. |
| Chave de partição | Opcional. Você precisa apenas se você estiver usando um token {partition} no seu padrão de nome de coleção.<br /> A chave de partição é o nome do campo nos eventos de saída que é usado para especificar a chave de particionamento de saída em várias coleções.<br /> Para saída de coleção única, você pode usar qualquer coluna de saída arbitrária. Um exemplo é PartitionId. |
| ID do documento |Opcional. O nome do campo nos eventos de saída que é usado para especificar a chave primária na qual inserir ou atualizar operações se baseiam.

## <a name="azure-functions"></a>Funções do Azure
O Azure Functions é um serviço de computação sem servidor que você pode usar para executar código sob demanda sem a necessidade de provisionar explicitamente ou gerenciar a infraestrutura. Ele permite que você implemente código que é disparado por eventos que ocorrem nos serviços do Azure ou o parceiro. Essa capacidade do Azure Functions de responder a gatilhos torna uma saída natural para o Azure Stream Analytics. Este adaptador de saída permite aos usuários conectar-se o Stream Analytics ao Azure Functions e executar um script ou trecho de código em resposta a uma variedade de eventos.

A saída das Funções do Azure do Stream Analytics não está disponível atualmente nas regiões Azure China (21Vianet) e Azure Germany (T-Systems International).

O Azure Stream Analytics chama o Azure Functions por meio de gatilhos de HTTP. O adaptador de saída do Azure Functions está disponível com as seguintes propriedades configuráveis:

| Nome da propriedade | DESCRIÇÃO |
| --- | --- |
| Aplicativo de funções |O nome do seu aplicativo do Azure Functions. |
| Função |O nome da função em seu aplicativo do Azure Functions. |
| Chave |Se você quiser usar um Azure Function de outra assinatura, você pode fazer isso fornecendo a chave para acessar sua função. |
| Tamanho máximo do lote |Uma propriedade que permite que você defina o tamanho máximo para cada lote de saída que é enviado para a função do Azure. A unidade de entrada é em bytes. Por padrão, esse valor é de 262.144 bytes (256 KB). |
| Contagem máxima do lote  |Uma propriedade que permite especificar o número máximo de eventos em cada lote que é enviada para o Azure Functions. O valor padrão é 100. |

Quando o Azure Stream Analytics recebe uma 413 "http (entidade de solicitação muito grande") a exceção de uma função do Azure, ele reduz o tamanho dos lotes que ele envia para o Azure Functions. Em seu código de função do Azure, use essa exceção para certificar-se de que o Azure Stream Analytics não envie lotes muito grandes. Além disso, certifique-se de que os valores de contagem e tamanho de lote máximo usados na função sejam consistentes com os valores inseridos no portal do Stream Analytics.

Além disso, em uma situação em que não há nenhum evento caindo em uma janela de tempo, nenhuma saída é gerada. Como resultado, o **computeResult** função não for chamada. Esse comportamento é consistente com as funções de agregação em janelas internas.

## <a name="custom-metadata-properties-for-output"></a>Propriedades de metadados personalizados para saída 

Você pode anexar as colunas de consulta como propriedades de usuário para as mensagens enviadas. Essas colunas não passam na carga de. As propriedades estão presentes na forma de um dicionário na mensagem de saída. *Chave* é o nome da coluna e *valor* é o valor de coluna no dicionário de propriedades. Todos os tipos de dados do Stream Analytics são suportados, exceto o registro e a matriz.  

Saídas com suporte: 
* Fila do Barramento de Serviço 
* Tópico do Barramento de Serviço 
* Hub de Eventos 

No exemplo a seguir, podemos adicionar os dois campos `DeviceId` e `DeviceStatus` aos metadados. 
* Consulta: `select *, DeviceId, DeviceStatus from iotHubInput`
* Configuração de saída: `DeviceId,DeviceStatus`

![Colunas da propriedade](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

A captura de tela a seguir mostra as propriedades da mensagem inspecionadas no hub de eventos por meio de saída [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Propriedades personalizadas do evento](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Particionamento

A tabela a seguir resume o suporte de partição e o número de gravadores de saída para cada tipo de saída:

| Tipo de saída | Suporte ao particionamento | Chave de partição  | Número de gravadores de saída |
| --- | --- | --- | --- |
| Repositório Azure Data Lake | Sim | Use {data} e tokens de {time} no padrão de prefixo de caminho. Escolha o formato de data como AAAA/MM/DD, MM/DD/AAAA ou MM-DD-AAAA. HH é usado para o formato de hora. | Segue o particionamento de entrada para [consultas totalmente paralelizáveis](stream-analytics-scale-jobs.md). |
| Banco de Dados SQL do Azure | Sim | Com base na cláusula PARTITION BY na consulta. | Segue o particionamento de entrada para [consultas totalmente paralelizáveis](stream-analytics-scale-jobs.md). Para saber mais sobre como alcançar melhor desempenho de gravação taxa de transferência quando você estiver carregando dados no banco de dados SQL, consulte [saída do Azure Stream Analytics para o banco de dados do Azure SQL](stream-analytics-sql-output-perf.md). |
| Armazenamento de Blobs do Azure | Sim | Use {data} e tokens de {time} de seus campos de evento no padrão de caminho. Escolha o formato de data como AAAA/MM/DD, MM/DD/AAAA ou MM-DD-AAAA. HH é usado para o formato de hora. A saída de blob pode ser particionada por um atributo de evento personalizado único {fieldname} ou {datetime:\<specifier>}. | Segue o particionamento de entrada para [consultas totalmente paralelizáveis](stream-analytics-scale-jobs.md). |
| Hubs de eventos do Azure | Sim | Sim | Varia dependendo do alinhamento da partição.<br /> Quando a chave de partição para a saída do hub de eventos é igualmente alinhada com a etapa de consulta upstream (anterior), o número de gravadores é o mesmo que o número de partições na saída do hub de eventos. Cada gravador usa o [EventHubSender classe](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) para enviar eventos para a partição específica. <br /> Quando a chave de partição para a saída do hub de eventos não está alinhada com a etapa de consulta upstream (anterior), o número de gravadores é igual ao número de partições na etapa anterior. Cada gravador usa o [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) na **EventHubClient** para enviar eventos para todas as partições de saída. |
| Power BI | Não  | Nenhum | Não aplicável. |
| Armazenamento da tabela do Azure | Sim | Qualquer coluna de saída.  | Segue o particionamento de entrada para as [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). |
| Tópico do Barramento de Serviço do Azure | Sim | Escolhido automaticamente. O número de partições baseia-se no [tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro exclusivo para cada partição.| Mesmo que o número de partições no tópico de saída.  |
| Fila do Barramento de Serviço do Azure | Sim | Escolhido automaticamente. O número de partições baseia-se no [tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro exclusivo para cada partição.| Mesmo que o número de partições na fila de saída. |
| Azure Cosmos DB | Sim | Use o token {partition} no padrão de nome de coleção. O valor de {partition} baseia-se a cláusula PARTITION BY na consulta. | Segue o particionamento de entrada para as [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). |
| Funções do Azure | Não  | Nenhum | Não aplicável. |

Se o seu adaptador de saída não estiver particionado, a falta de dados em uma partição de entrada causará um atraso até a quantidade de tempo de chegada tardia. Nesses casos, a saída será mesclada a um único gravador, que pode causar gargalos em seu pipeline. Para saber mais sobre a política de entrada tardia, consulte [considerações sobre ordem de eventos do Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída
O Azure Stream Analytics usa lotes de tamanho variável para processar eventos e gravar em saídas. Normalmente o mecanismo de análise de Stream não grava uma mensagem por vez e usa os lotes para maior eficiência. Quando a taxa de ambos os eventos de entrada e de saída for alta, o Stream Analytics usa lotes maiores. Quando a taxa de saída é baixa, ele usa lotes menores para manter a latência baixa.

A tabela a seguir explica algumas das considerações para o envio em lote de saída:

| Tipo de saída | Tamanho máximo de mensagem | Otimização de tamanho de lote |
| :--- | :--- | :--- |
| Repositório Azure Data Lake | Ver [limites de armazenamento do Data Lake](../azure-subscription-service-limits.md#data-lake-store-limits). | Use até 4 MB por operação de gravação. |
| Banco de Dados SQL do Azure | 10.000 máximo de linhas por único bulk insert.<br />mínimo 100 linhas por único bulk insert. <br />Ver [SQL Azure limita](../sql-database/sql-database-resource-limits.md). |  Cada lote é inicialmente em massa inserida com tamanho máximo de lote. Você pode dividir o lote em metade (até que o tamanho mínimo do lote) com base em erros com nova tentativa do SQL. |
| Armazenamento de Blobs do Azure | Ver [limites de armazenamento do Azure](../azure-subscription-service-limits.md#storage-limits). | O tamanho máximo do blob do bloco é 4 MB.<br />A contagem do máximo do blob bock é 50.000. |
| Hubs de eventos do Azure  | 256 KB por mensagem. <br />Ver [limita os Hubs de eventos](../event-hubs/event-hubs-quotas.md). |  Quando o particionamento de entrada/saída não está alinhado, cada evento é fornecido individualmente nas **EventData** e enviadas em um lote de até o tamanho máximo da mensagem (1 MB para o SKU Premium). <br /><br />  Quando o particionamento de entrada/saída é aligned, vários eventos são incluídos em uma única **EventData** da instância, até o tamanho máximo da mensagem e enviada.  |
| Power BI | Ver [limita a API Rest do Power BI](https://msdn.microsoft.com/library/dn950053.aspx). |
| Armazenamento da tabela do Azure | Ver [limites de armazenamento do Azure](../azure-subscription-service-limits.md#storage-limits). | O padrão é 100 entidades por transação única. Você pode configurá-lo para um valor menor, conforme necessário. |
| Fila do Barramento de Serviço do Azure   | 256 KB por mensagem.<br /> Ver [do barramento de serviço limita](../service-bus-messaging/service-bus-quotas.md). | Use um único evento por mensagem. |
| Tópico do Barramento de Serviço do Azure | 256 KB por mensagem.<br /> Ver [do barramento de serviço limita](../service-bus-messaging/service-bus-quotas.md). | Use um único evento por mensagem. |
| Azure Cosmos DB   | Ver [limita o Azure Cosmos DB](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Tamanho do lote e gravar frequência são ajustados dinamicamente com base em respostas do Azure Cosmos DB. <br /> Não há nenhuma limitação predeterminada do Stream Analytics. |
| Funções do Azure   | | O tamanho de lote padrão é de 262.144 bytes (256 KB). <br /> A contagem de eventos por lote padrão é 100. <br /> O tamanho do lote é configurável e pode ser aumentado ou diminuído nas [opções de saída](#azure-functions) do Stream Analytics.

## <a name="next-steps"></a>Próximos passos
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
