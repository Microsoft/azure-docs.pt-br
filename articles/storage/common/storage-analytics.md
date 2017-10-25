---
title: "Usar a Análise de Armazenamento do Azure para coletar dados de logs e métricas | Microsoft Docs"
description: "A Análise de Armazenamento permite que você para acompanhe dados de métricas de todos os serviços de armazenamento e para coletar logs para o armazenamento de Tabelas, Blobs e Filas."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 7894993b-ca42-4125-8f17-8f6dfe3dca76
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: tamram
ms.openlocfilehash: 9ae9dd0b078911a695d441cd3891be720dc204ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="storage-analytics"></a>Análise de Armazenamento

A análise de armazenamento do Azure executa registro em log e fornece dados de métrica para uma conta de armazenamento. Você pode usar esses dados para rastrear solicitações, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento.

Para usar a análise de armazenamento, você deve habilitá-la separadamente para cada serviço que você deseja monitorar. Você pode habilitá-la no [portal do Azure](https://portal.azure.com). Para obter detalhes, consulte [Monitorar uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use as operações [Obter propriedades do serviço Blob](https://msdn.microsoft.com/library/hh452239.aspx), [Obter propriedades do serviço Fila](https://msdn.microsoft.com/library/hh452243.aspx), [Obter propriedades do serviço Tabela](https://msdn.microsoft.com/library/hh452238.aspx) e [Obter propriedades do serviço Arquivo](https://msdn.microsoft.com/library/mt427369.aspx) para habilitar a Análise de Armazenamento para cada serviço.

Os dados agregados são armazenados em um blob conhecido (para registro em log) e em tabelas conhecidas (para métricas), que podem ser acessados usando os serviços de Blob e APIs do serviço de tabela

A Análise de Armazenamento tem um limite de 20 TB na quantidade de dados armazenados que é independente do limite total para sua conta de armazenamento. Para saber mais sobre cobrança e políticas de retenção de dados, consulte [Análise de armazenamento e cobrança](https://msdn.microsoft.com/library/hh360997.aspx). Para saber mais sobre limites de contas de armazenamento, consulte [Escalabilidade e metas de desempenho do armazenamento do Azure](storage-scalability-targets.md).

Para um guia aprofundado sobre como usar a Análise de Armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao Armazenamento do Azure, consulte [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="about-storage-analytics-logging"></a>Sobre o registro em log da Análise de Armazenamento
A análise de armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço.

As entradas de log são criadas somente se houver atividade de serviço de armazenamento. Por exemplo, se uma conta de armazenamento tiver atividade em seu serviço Blob, mas não em seus serviços de fila ou tabela, somente os logs referentes ao serviço Blob serão criados.

O Log de análise de armazenamento não está disponível para Arquivos do Azure.

### <a name="logging-authenticated-requests"></a>Solicitações de registro em log autenticadas
Os seguintes tipos de solicitações autenticadas são registrados:

* Solicitações bem sucedidas.
* Solicitações com falha, incluindo o tempo limite, limitação, rede, autorização e outros erros.
* Solicitações que usam uma SAS (Assinatura de Acesso Compartilhado), incluindo solicitações bem-sucedidas e com falha.
* Solicitações para dados de análise.

As solicitações feitas pela própria análise de armazenamento, como criação de log ou exclusão, não estão conectadas. Uma lista completa dos dados registrados está documentada nos tópicos [Storage Analytics Logged Operations and Status Messages](https://msdn.microsoft.com/library/hh343260.aspx) (Mensagens de operações e status registradas do Storage Analytics) e [Storage Analytics Log Format](https://msdn.microsoft.com/library/hh343259.aspx) (Formato do log do Storage Analytics).

### <a name="logging-anonymous-requests"></a>Registro em log de solicitações anônimas
Os seguintes tipos de solicitações anônimas são registrados:

* Solicitações bem sucedidas.
* Erros do servidor.
* Erros de tempo limite para o cliente e servidor.
* Solicitações GET com falha com o código de erro 304 (não modificado).

Todas as outras solicitações anônimas com falha não estão conectadas. Uma lista completa dos dados registrados está documentada nos tópicos [Storage Analytics Logged Operations and Status Messages](https://msdn.microsoft.com/library/hh343260.aspx) (Mensagens de operações e status registradas do Storage Analytics) e [Storage Analytics Log Format](https://msdn.microsoft.com/library/hh343259.aspx) (Formato do log do Storage Analytics).

### <a name="how-logs-are-stored"></a>Como os logs são armazenados
Todos os logs são armazenados em blobs de bloco em um contêiner denominado $logs, que é criado automaticamente quando a análise de armazenamento é habilitada para uma conta de armazenamento. O contêiner $logs está localizado no namespace de blob da conta de armazenamento, por exemplo: `http://<accountname>.blob.core.windows.net/$logs`. Este contêiner não pode ser excluído quando a análise de armazenamento tiver sido habilitada, embora seu conteúdo possa ser excluído.

> [!NOTE]
> O contêiner de $logs não é exibido quando uma operação de listagem do contêiner é executada, como o método [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) . Ele deve ser acessado diretamente. Por exemplo, você pode usar o método [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) para acessar os blobs no contêiner `$logs`.
> Como as solicitações são registradas, a análise de armazenamento carrega resultados intermediários como blocos. Periodicamente, a análise de armazenamento confirmará esses blocos e os disponibilizará como um blob.
> 
> 

Podem existir registros duplicados para os logs criados na mesma hora. Você pode determinar se um registro é uma duplicata verificando **RequestId** e o número da **Operação**.

### <a name="log-naming-conventions"></a>Convenções de nomenclatura de log
Cada log será gravado no formato a seguir.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

A tabela a seguir descreve cada atributo no nome do log.

| Atributo | Descrição |
| --- | --- |
| <service-name> |O nome do serviço de armazenamento. Por exemplo: blob, tabela ou fila. |
| AAAA |O ano de quatro dígitos para o log. Por exemplo: 2011. |
| MM |O mês de dois dígitos do log. Por exemplo: 07. |
| DD |O mês de dois dígitos do log. Por exemplo: 07. |
| hh |A hora de dois dígitos que indica a hora inicial dos logs, no formato de 24 horas UTC. Por exemplo: 18. |
| MM |O número de dois dígitos que indica o minuto inicial dos logs. Esse valor não tem suporte na versão atual da análise de armazenamento e seu valor será sempre 00. |
| <counter> |Um contador baseado em zero com seis dígitos que indica o número de blobs de log gerado para o serviço de armazenamento em um período de uma hora. Esse contador começa em 000000. Por exemplo: 000001. |

Veja a seguir um exemplo de nome de log completo que combina os exemplos anteriores.

    blob/2011/07/31/1800/000001.log

Veja a seguir um exemplo de URI que pode ser usado para acessar o log anterior.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Quando uma solicitação de armazenamento estiver conectada, o nome do log resultante se correlaciona com a hora quando concluir a operação solicitada. Por exemplo, se uma solicitação GetBlob foi concluída às 18:30, em 31/07/2011, o log seria gravado com o seguinte prefixo: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadados de log
Todos os blobs de log são armazenados com metadados que podem ser usados para identificar os dados de log que contém o blob. A tabela a seguir descreve cada atributo de metadados.

| Atributo | Descrição |
| --- | --- |
| LogType |Descreve se o log contém informações referentes a operações de ler, gravar ou de exclusão. Esse valor pode incluir um tipo ou uma combinação dos três, separados por vírgulas. Exemplo 1: gravar. Exemplo 2: ler, gravar. Exemplo 3: ler, gravar e excluir. |
| StartTime |A hora mais antiga de uma entrada de log, no formato AAAA-MM-DDThh:mm:ssZ. Por exemplo: 2011-07-31T18:21:46Z. |
| EndTime |A hora mais recente de uma entrada de log, no formato YYYY-MM-DDThh:mm:ssZ. Por exemplo: 2011-07-31T18:22:09Z. |
| LogVersion |A versão do formato do log. Atualmente, o único valor aceito é 1.0. |

A lista a seguir exibe exemplos completos de metadados que usam os exemplos anteriores.

* LogType=write
* StartTime=2011-07-31T18:21:46Z
* EndTime=2011-07-31T18:22:09Z
* LogVersion=1.0

### <a name="accessing-logging-data"></a>Acessando dados de log
Todos os dados no contêiner `$logs` podem ser acessados usando as APIs do serviço Blob, incluindo as APIs do .NET fornecidas pela biblioteca gerenciada do Azure. O administrador da conta de armazenamento pode ler e excluir logs, mas não é possível criá-los ou atualizá-los. Tanto os metadados do log quanto o nome do log podem ser usados ao consultar em busca de um log. É possível que os logs de uma determinada hora sejam exibidos fora de ordem, mas os metadados sempre especificam o período das entradas de log em um log. Portanto, você pode usar uma combinação de nomes de log e metadados ao procurar um log específico.

## <a name="about-storage-analytics-metrics"></a>Sobre as métricas de Análise de Armazenamento
A análise de armazenamento pode armazenar métricas que incluem estatísticas de transação agregadas e os dados de capacidade sobre solicitações para um serviço de armazenamento. As transações são relatadas no nível de operação da API, bem como no nível de serviço de armazenamento, e a capacidade é relatada no nível de serviço de armazenamento. Os dados de métricas podem ser usados para analisar o uso do serviço de armazenamento, diagnosticar problemas com solicitações feitas no serviço de armazenamento e melhorar o desempenho de aplicativos que usam um serviço.

Para usar a análise de armazenamento, você deve habilitá-la separadamente para cada serviço que você deseja monitorar. Você pode habilitá-la no [portal do Azure](https://portal.azure.com). Para obter detalhes, consulte [Monitorar uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use as operações **Obter Propriedades do Serviço** para habilitar a Análise de Armazenamento para cada serviço.

### <a name="transaction-metrics"></a>Métricas de transação
Um conjunto robusto de dados é registrado em intervalos de horas ou minutos para cada serviço de armazenamento e operações de API solicitadas, incluindo ingresso/egresso, disponibilidade, erros e percentuais da solicitação categorizados. Você pode ver uma lista completa dos detalhes da transação no tópico [Esquema da tabela de métricas da análise de armazenamento](https://msdn.microsoft.com/library/hh343264.aspx) .

Os dados de transação são registrados em dois níveis: nível de serviço e nível de operação da API. No nível de serviço, as estatísticas que resumem todas as operações de API solicitadas são gravadas em uma entidade de tabela a cada hora, mesmo que nenhuma solicitação seja feita ao serviço. No nível de operação da API, as estatísticas serão gravadas somente em uma entidade se a operação foi solicitada nessa hora.

Por exemplo, se você executar uma operação **GetBlob** no serviço Blob, as métricas do Storage Analytics vão registrar a solicitação e as incluirá nos dados agregados para o serviço Blob, bem como a operação **GetBlob**. No entanto, se nenhuma operação **GetBlob** for solicitada durante a hora, uma entidade não será gravada no `$MetricsTransactionsBlob` para essa operação.

As métricas de transações são registradas para solicitações de usuários e solicitações feitas pela própria análise de armazenamento. Por exemplo, solicitações pela análise de armazenamento para gravar logs e entidades de tabela são registradas. Para saber mais sobre como essas solicitações são cobradas, consulte [Análise de armazenamento e cobrança](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Métricas de capacidade
> [!NOTE]
> Atualmente, as métricas de capacidade somente estão disponíveis para o serviço Blob. As métricas de capacidade para os serviços de tabela e fila estarão disponíveis em versões futuras da análise de armazenamento.
> 
> 

Os dados de capacidade são gravados diariamente para o serviço Blob de uma conta de armazenamento e duas entidades de tabela são gravadas. Uma entidade fornece estatísticas para dados de usuário e a outra fornece estatísticas sobre o contêiner de blob `$logs` usado pela análise de armazenamento. A tabela `$MetricsCapacityBlob` inclui as seguintes estatísticas:

* **Capacidade**: a quantidade de armazenamento usado pelo serviço Blob da conta de armazenamento, em bytes.
* **ContainerCount**: o número de contêineres de blob no serviço Blob da conta de armazenamento.
* **ObjectCount**: o número de blobs de páginas ou de blocos confirmados e não confirmados no serviço Blob da conta de armazenamento.

Para saber mais sobre as métricas de capacidade, consulte [Esquema da tabela de métricas da Análise de Armazenamento](https://msdn.microsoft.com/library/hh343264.aspx).

### <a name="how-metrics-are-stored"></a>Como as métricas são armazenadas
Todos os dados de métricas para cada um dos serviços de armazenamento são armazenados em três tabelas reservadas para esse serviço: uma tabela para informações de transação, uma tabela de informações de transações de minutos e outra tabela para informações de capacidade. Informações de transações de transação de minuto consistem em dados de solicitação e resposta, e informações de capacidade consistem em dados de uso de armazenamento. A métrica de horas, a métrica de minutos e a capacidade de um serviço Blob da conta de armazenamento podem ser acessadas nas tabelas que são nomeadas conforme descrito na tabela a seguir.

| Nível de métricas | Nomes da tabela | Versões com suporte |
| --- | --- | --- |
| Métricas por hora, local principal |$MetricsTransactionsBlob  <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue |Versões anteriores a 15-08-2013 apenas. Embora esses nomes ainda tenham suporte, é recomendável que você passe a usar as tabelas listadas abaixo. |
| Métricas por hora, local principal |$MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue |Todas as versões, incluindo 15-08-2013. |
| Métricas por minuto, local principal |$MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue |Todas as versões, incluindo 15-08-2013. |
| Métricas por hora, local secundário |$MetricsHourSecondaryTransactionsBlob  <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue |Todas as versões, incluindo 15-08-2013. A replicação de redundância geográfica com acesso de leitura deve estar habilitada. |
| Métricas por minuto, local secundário |$MetricsMinuteSecondaryTransactionsBlob  <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue |Todas as versões, incluindo 15-08-2013. A replicação de redundância geográfica com acesso de leitura deve estar habilitada. |
| Capacidade (apenas serviço Blob) |$MetricsCapacityBlob |Todas as versões, incluindo 15-08-2013. |

Essas tabelas são criadas automaticamente quando a análise de armazenamento é habilitada para uma conta de armazenamento. Elas são acessadas por meio do namespace da conta de armazenamento, por exemplo: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Acessando dados de métrica
Todos os dados nas tabelas de métricas podem ser acessados usando as APIs do serviço de tabela, incluindo as APIs do .NET fornecidas pela biblioteca gerenciada do Azure. O administrador da conta de armazenamento pode ler e excluir entidades de tabela, mas não pode criá-las ou atualizá-las.

## <a name="billing-for-storage-analytics"></a>Cobrança da análise de armazenamento
Todos os dados de métricas são gravados pelos serviços de uma conta de armazenamento. Como resultado, cada operação de gravação executada pela análise de armazenamento é faturável. Além disso, a quantidade de armazenamento usado por dados de métrica também é faturável.

As seguintes ações executadas pela análise de armazenamento são faturáveis:

* Solicitações para criar blobs para registro em log. 
* Solicitações para criar entidades de tabela para métricas.

Se você configurou uma política de retenção de dados, você não é cobrado para excluir transações quando a análise de armazenamento exclui dados antigos de log e métricas. No entanto, excluir as transações de um cliente são faturáveis. Para saber mais sobre as políticas de retenção, consulte [Definindo uma política de retenção de dados de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Noções básicas sobre solicitações faturáveis
Todas as solicitações feitas ao serviço de armazenamento da conta são faturáveis ou então não faturáveis. A análise de armazenamento registra cada solicitação individual feita a um serviço, incluindo uma mensagem de status que indica como a solicitação foi processada. Da mesma forma, a análise de armazenamento armazena métricas para um serviço e as operações de API desse serviço, incluindo os percentuais e a contagem de determinadas mensagens de status. Juntos, esses recursos podem ajudá-lo a analisar suas solicitações faturáveis, fazer melhorias em seu aplicativo e diagnosticar problemas com solicitações de serviços. Para saber mais sobre a cobrança, confira [Noções básicas sobre cobrança no armazenamento do Azure – largura de banda, transações e capacidade](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Ao analisar os dados de análise de armazenamento, você pode usar as tabelas no tópico [Mensagens de operações e status registradas da análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343260.aspx) para determinar quais solicitações são faturáveis. Em seguida, você pode comparar seus logs e dados de métricas para as mensagens de status para ver se houve cobrança por uma determinada solicitação. Você também pode usar as tabelas no tópico anterior para investigar a disponibilidade de um serviço de armazenamento ou operação de API individual.

## <a name="next-steps"></a>Próximas etapas
### <a name="setting-up-storage-analytics"></a>Configurando a análise de armazenamento
* [Monitorar uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md)
* [Habilitar e configurar a análise de armazenamento](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Registro em log da Análise de Armazenamento
* [Sobre o registro em log da análise de armazenamento](https://msdn.microsoft.com/library/hh343262.aspx)
* [Formato de Log de análise de armazenamento](https://msdn.microsoft.com/library/hh343259.aspx)
* [Mensagens de operações e status registradas de análise de armazenamento](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Métricas da Análise de Armazenamento
* [Sobre as métricas de análise de armazenamento](https://msdn.microsoft.com/library/hh343258.aspx)
* [Esquema da tabela de métricas da análise de armazenamento](https://msdn.microsoft.com/library/hh343264.aspx)
* [Mensagens de operações e status registradas de análise de armazenamento](https://msdn.microsoft.com/library/hh343260.aspx)  

