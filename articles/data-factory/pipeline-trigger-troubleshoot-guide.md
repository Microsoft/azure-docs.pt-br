---
title: Solucionar problemas de orquestração e gatilhos de pipeline no Azure Data Factory
description: Use métodos diferentes para solucionar problemas de gatilho de pipeline no Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 2950c175acfdda33394c93649a3e2c41d1264dd2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705986"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Solucionar problemas de orquestração e gatilhos de pipeline no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uma execução de pipeline no Azure Data Factory define uma instância de uma execução de pipeline. Por exemplo, digamos que você tenha um pipeline que é executado às 8:00 A.M., 9:00 AM e 10:00 AM. Nesse caso, há três execuções de pipeline separadas. Cada execução de pipeline possui uma ID de execução de pipeline exclusiva. Uma ID de execução é um GUID (identificador global exclusivo) que define essa execução de pipeline específica.

As execuções de pipeline normalmente são instanciadas por meio da passagem de argumentos para parâmetros definidos no pipeline. Você pode executar um pipeline manualmente ou usando um gatilho. Consulte [gatilhos e execução de pipeline no Azure data Factory](concepts-pipeline-execution-triggers.md) para obter detalhes.

## <a name="common-issues-causes-and-solutions"></a>Problemas comuns, causas e soluções

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Um pipeline de aplicativo Azure Functions gera um erro com conectividade de ponto de extremidade privada
 
Você tem Data Factory e um aplicativo de funções do Azure em execução em um ponto de extremidade privado. Você está tentando executar um pipeline que interage com o aplicativo de funções. Você tentou três métodos diferentes, mas um retorna erro "solicitação inadequada" e os outros dois métodos retornam "erro proibido de 103".

**Causa**: data Factory atualmente não dá suporte a um conector de ponto de extremidade privado para aplicativos de funções. Azure Functions rejeita chamadas porque está configurada para permitir somente conexões de um link privado.

**Resolução**: Crie um ponto de extremidade **PrivateLinkService** e forneça o DNS do seu aplicativo de funções.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>Uma execução de pipeline é cancelada, mas o monitor ainda mostra o status de progresso

Quando você cancela uma execução de pipeline, o monitoramento de pipeline muitas vezes mostra o status do progresso. Isso ocorre devido a um problema de cache do navegador. Você também pode não ter os filtros de monitoramento corretos.

**Resolução**: Atualize o navegador e aplique os filtros de monitoramento corretos.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>Você verá um erro "DelimitedTextMoreColumnsThanDefined" ao copiar um pipeline
 
Se uma pasta que você está copiando contém arquivos com esquemas diferentes, como número variável de colunas, delimitadores diferentes, configurações de caractere de cotação ou algum problema de dados, o pipeline de Data Factory pode gerar esse erro:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Resolução**: selecione a opção **cópia binária** ao criar a atividade de cópia. Dessa forma, para cópias em massa ou migrar seus dados de um data Lake para outro, Data Factory não abrirá os arquivos para ler o esquema. Em vez disso, Data Factory tratará cada arquivo como binário e o copiará para o outro local.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime"></a>Uma execução de pipeline falha quando você atinge o limite de capacidade do tempo de execução de integração

Mensagem de erro:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Causa**: você atingiu o limite de capacidade do Integration Runtime. Você pode estar executando uma grande quantidade de fluxo de dados usando o mesmo Integration Runtime ao mesmo tempo. Confira [assinatura do Azure e limites de serviço, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2) para obter detalhes.

**Resolução**:
 
- Execute seus pipelines em tempos de gatilho diferentes.
- Crie um novo Integration Runtime e divida seus pipelines entre vários tempos de execução de integração.

### <a name="you-have-activity-level-errors-and-failures-in-pipelines"></a>Você tem erros de nível de atividade e falhas em pipelines

Azure Data Factory orquestração permite a lógica condicional e permite que os usuários realizem caminhos diferentes com base no resultado de uma atividade anterior. Ele permite quatro caminhos condicionais: **após êxito** (aprovação padrão), **após a falha**, após a **conclusão** e **após ignorar**. 

Azure Data Factory avalia o resultado de todas as atividades de nível folha. Os resultados do pipeline serão bem-sucedidos somente se todas as folhas forem bem-sucedidas. Se uma atividade folha tiver sido ignorada, avaliaremos sua atividade pai em vez disso. 

**Resolução**

1. Implemente verificações de nível de atividade seguindo [como lidar com falhas e erros de pipeline](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
1. Use os aplicativos lógicos do Azure para monitorar pipelines em intervalos regulares seguindo [a consulta por fábrica](/rest/api/datafactory/pipelineruns/querybyfactory).

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>Como monitorar falhas de pipeline em intervalos regulares

Talvez seja necessário monitorar pipelines de Data Factory com falha em intervalos, digamos 5 minutos. Você pode consultar e filtrar as execuções de pipeline de um data factory usando o ponto de extremidade. 

**Resolução** Você pode configurar um aplicativo lógico do Azure para consultar todos os pipelines com falha a cada 5 minutos, conforme descrito em [consulta por fábrica](/rest/api/datafactory/pipelineruns/querybyfactory). Em seguida, você pode relatar incidentes para seu sistema de tíquetes.

Para obter mais informações, acesse [enviar notificações de data Factory, parte 2](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/).

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>O grau de aumento de paralelismo não resulta em maior taxa de transferência

**Causa** 

O grau de paralelismo em *foreach* é, na verdade, o grau máximo de paralelismo. Não podemos garantir que um número específico de execuções ocorressem ao mesmo tempo, mas esse parâmetro garantirá que nunca vamos além do valor definido. Você deve ver isso como um limite para ser aproveitado ao controlar o acesso simultâneo às suas fontes e coletores.

Fatos conhecidos sobre *foreach*
 * Foreach tem uma propriedade chamada contagem de lotes (n) em que o valor padrão é 20 e o máximo é 50.
 * A contagem de lotes, n, é usada para construir n filas. Posteriormente, discutiremos alguns detalhes sobre como essas filas são construídas.
 * Cada fila é executada sequencialmente, mas você pode ter várias filas em execução em paralelo.
 * As filas são criadas previamente. Isso significa que não há nenhum rebalanceamento das filas durante o tempo de execução.
 * A qualquer momento, você tem no máximo um item sendo processado por fila. Isso significa no máximo n itens sendo processados em um determinado momento.
 * O tempo total de processamento foreach é igual ao tempo de processamento da fila mais longa. Isso significa que a atividade foreach depende de como as filas são construídas.
 
**Resolução**

 * Você não deve usar a atividade *Setvariant* dentro *para cada* que é executado em paralelo.
 * Levando em consideração a maneira como as filas são construídas, o cliente pode melhorar o desempenho de foreach definindo vários *foreachs* , em que cada foreach terá itens com tempo de processamento semelhante. Isso garantirá que as execuções longas sejam processadas em paralelo em sequência.

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente estes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Página de P e R da Microsoft](/answers/topics/azure-data-factory.html)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
