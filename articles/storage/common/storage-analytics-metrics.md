---
title: Métricas da Análise de Armazenamento do Azure (clássico)
description: Saiba como usar as métricas de Análise de Armazenamento no Armazenamento do Microsoft Azure. Saiba mais sobre métricas de capacidade e transação, como as métricas são armazenadas, habilitando métricas e muito mais.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: d900ffa4481ba2b6deb21a8325f3f8def8084f84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714724"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Métricas da Análise de Armazenamento do Azure (clássico)

Em **31 de agosto de 2023** análise de armazenamento métricas, também conhecidas como *métricas clássicas* serão desativadas. Para saber mais, confira o [anúncio oficial](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Se você usar métricas clássicas, não deixe de fazer a transição para as métricas em Azure Monitor antes dessa data. Este artigo ajuda você com a transição. 

O Armazenamento do Microsoft Azure usa a solução Análise de Armazenamento para armazenar métricas que incluem estatísticas de transação agregadas e dados de capacidade sobre solicitações para um serviço de armazenamento. As transações são relatadas no nível de operação de API e no nível de serviço de armazenamento. A capacidade é relatada no nível de serviço de armazenamento. Os dados de métricas podem ser usados para:
- Analisar o uso do serviço de armazenamento.
- Diagnosticar problemas com solicitações feitas no serviço de armazenamento.
- Melhorar o desempenho dos aplicativos que usam um serviço.

 A métrica da Análise de Armazenamento vem habilitada por padrão nas novas contas de armazenamento. Você pode configurar as métricas no [portal do Azure](https://portal.azure.com/), usando o PowerShell ou usando o CLI do Azure. Para obter orientações passo a passo, consulte [habilitar e gerenciar as métricas analíticas do armazenamento do Azure (clássico)](./manage-storage-analytics-logs.md). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use as operações Definir Propriedades do Serviço para habilitar a Análise de Armazenamento para cada serviço.  

> [!NOTE]
> As métricas da Análise de Armazenamento estão disponíveis para armazenamento de Blobs do Azure, armazenamento de Filas do Azure, armazenamento de Tabelas do Azure e Arquivos do Azure.
> Agora as métricas de Análise de Armazenamento são métricas clássicas. Recomendamos o uso de [métricas de armazenamento no Azure Monitor](../blobs/monitor-blob-storage.md), em vez das métricas de Análise de Armazenamento.

## <a name="transaction-metrics"></a>Métricas de transação  
 Um conjunto robusto de dados é registrado em intervalos de horas ou minutos para cada serviço de armazenamento e operações de API solicitadas, o que inclui entrada e saída, disponibilidade, erros e percentuais de solicitação categorizados. Para obter uma lista completa dos detalhes da transação, confira [Esquema da tabela de métricas da Análise de Armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 Os dados de transação são registrados no nível de serviço e no nível de operação da API. No nível de serviço, as estatísticas que resumem todas as operações de API solicitadas são gravadas em uma entidade de tabela a cada hora, mesmo que nenhuma solicitação seja feita ao serviço. No nível de operação da API, as estatísticas serão gravadas somente em uma entidade se a operação foi solicitada nessa hora.  

 Por exemplo, se você executar uma operação **GetBlob** em seu serviço de blob, a Métrica da Análise de Armazenamento registrará a solicitação e a incluirá nos dados agregados para o serviço de blob e a operação **GetBlob**. Se nenhuma operação **GetBlob** for solicitada durante a hora, uma entidade não será gravada no *$MetricsTransactionsBlob* dessa operação.  

 As métricas de transação são registradas para solicitações de usuários e solicitações feitas pela própria Análise de Armazenamento. Por exemplo, solicitações pela análise de armazenamento para gravar logs e entidades de tabela são registradas.

## <a name="capacity-metrics"></a>Métricas de capacidade  

> [!NOTE]
>  Atualmente, as métricas de capacidade estão disponíveis somente para o serviço blob.

 Os dados de capacidade são gravados diariamente para o serviço blob de uma conta de armazenamento e duas entidades de tabela são gravadas. Uma entidade fornece estatísticas para dados de usuário e a outra fornece estatísticas sobre o contêiner de blob `$logs` usado pela análise de armazenamento. A tabela *$MetricsCapacityBlob* inclui as seguintes estatísticas:  

- **Capacity**: A quantidade de armazenamento utilizada pelo serviço blob da conta de armazenamento em bytes.  
- **ContainerCount**: O número de contêineres de blob no serviço blob da conta de armazenamento.  
- **ObjectCount**: O número de blobs de páginas ou de blocos confirmados e não confirmados no serviço blob da conta de armazenamento.  

  Para saber mais sobre métricas de capacidade, consulte [Esquema da tabela de métricas da Análise de Armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Como as métricas são armazenadas  

 Todos os dados de métricas para cada um dos serviços de armazenamento são armazenados em três tabelas reservadas para o serviço. Uma tabela é para informações de transação, uma tabela é para informações de transação de minuto e outra tabela é para informações de capacidade. As informações de transação e de transação de minuto consistem em dados de solicitação e resposta. As informações de capacidade consistem em dados de uso de armazenamento. A métrica de horas, a métrica de minutos e a capacidade de um serviço blob da conta de armazenamento são acessadas nas tabelas que são nomeadas conforme descrito na tabela a seguir.  

|Nível de métricas|Nomes da tabela|Suporte para versões|  
|-------------------|-----------------|----------------------------|  
|Métricas por hora, local principal|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Somente versões anteriores a 15 de agosto de 2013. Embora esses nomes ainda tenham suporte, recomendamos que você passe a usar as tabelas a seguir.|  
|Métricas por hora, local principal|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|Todas as versões. O suporte para métricas do serviço de arquivo está disponível somente na versão de 5 de abril de 2015 e posterior.|  
|Métricas por minuto, local principal|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|Todas as versões. O suporte para métricas do serviço de arquivo está disponível somente na versão de 5 de abril de 2015 e posterior.|  
|Métricas por hora, local secundário|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Todas as versões. A replicação de redundância geográfica com acesso de leitura deve estar habilitada.|  
|Métricas por minuto, local secundário|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Todas as versões. A replicação de redundância geográfica com acesso de leitura deve estar habilitada.|  
|Capacidade (apenas serviço blob)|$MetricsCapacityBlob|Todas as versões.|  

 Essas tabelas são criadas automaticamente quando a Análise de Armazenamento do Azure é habilitada para um ponto de extremidade de serviço de armazenamento. Elas são acessadas por meio do namespace da conta de armazenamento, por exemplo`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. As tabelas de métricas não são exibidas em uma operação de listagem e devem ser acessadas diretamente por meio do nome da tabela.

## <a name="metrics-alerts"></a>Alertas de métricas
Considere a possibilidade de configurar alertas no [portal do Azure](https://portal.azure.com) para ser avisado automaticamente sobre alterações importantes no comportamento dos serviços de armazenamento. Para obter orientações passo a passo, consulte [criar alertas de métricas](./manage-storage-analytics-logs.md).

Se você usar uma ferramenta de Gerenciador de Armazenamento para baixar esses dados de métricas em um formato delimitado, você pode usar o Microsoft Excel para analisar os dados. Para obter uma lista das ferramentas disponíveis do Gerenciador de Armazenamento, confira [Ferramentas do Cliente de Armazenamento do Microsoft Azure](./storage-explorers.md).

> [!IMPORTANT]
> Pode haver um atraso entre um evento de armazenamento e quando os dados de métricas por hora ou minuto correspondentes são registrados. No caso de métricas por minuto, diversos minutos de dados podem ser gravados de uma só vez. Esse problema pode levar à agregação de transações de minutos anteriores à transação do minuto atual. Quando esse problema acontece, o serviço de alerta pode não ter todos os dados de métricas disponíveis para o intervalo de alerta configurado, o que pode levar ao acionamento inesperado de alertas.
>

## <a name="billing-on-storage-metrics"></a>Cobrança sobre métricas de armazenamento
Gravar solicitações para criar entidades de tabela para métricas são cobradas de acordo com as taxas padrão aplicáveis a todas as operações de armazenamento do Azure.  

As solicitações de leitura e exclusão dos dados de métrica também podem ser cobradas de acordo com as taxas padrão. Se você configurou uma política de retenção de dados, não será cobrado quando o Armazenamento do Microsoft Azure excluir dados de métricas antigos. Se você excluir dados de análise, sua conta será cobrada pelas operações de exclusão.  

A capacidade usada pelas tabelas de métricas também pode ser faturada. Use as seguintes informações para estimar a capacidade usada para o armazenamento de dados de métricas:  

-   Se a cada hora em que um serviço utiliza todas as APIs em todos os serviços, aproximadamente 148 KB de dados são armazenados a cada hora nas tabelas de transações de métricas, se você habilitou um resumo no nível de serviço e no nível de API.  
-   Se, a cada hora, um serviço utiliza todas as APIs do serviço, aproximadamente 12 KB de dados são armazenados a cada hora nas tabelas de transações de métricas, se você habilitou apenas um resumo do nível de serviço.  
-   A tabela de capacidade para blobs tem duas linhas adicionadas a cada dia, desde que o usuário tenha optado pelos logs. Esse cenário implica que, a cada dia, o tamanho dessa tabela aumenta em até cerca de 300 bytes.

## <a name="next-steps"></a>Próximas etapas
* [Monitorar uma conta de armazenamento](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Esquema da tabela de métricas da Análise de Armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Mensagens de operações e status registradas da Análise de Armazenamento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Registro da Análise de Armazenamento](storage-analytics-logging.md)
