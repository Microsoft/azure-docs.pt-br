---
title: Diagnóstico e monitoramento do Azure ServiceFabric| Microsoft Docs
description: Este artigo descreve os recursos de monitoramento de desempenho no tempo de execução de Service Fabric Reliable ServiceRemoting, como os contadores de desempenho emitidos por ele.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: chackdan
ms.openlocfilehash: 4e9aa2bbb99cac2ffc2b57ccb9299bf4ee7a729e
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876253"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnóstico e monitoramento de desempenho para Reliable Service Remoting
O tempo de execução do Reliable ServiceRemoting emite [contadores de desempenho](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Esses recursos fornecem informações sobre como o ServiceRemoting está operando e ajudam na solução de problemas e no monitoramento de desempenho.


## <a name="performance-counters"></a>contadores de desempenho
O tempo de execução do Reliable ServiceRemoting define as seguintes categorias de contador de desempenho:

| Categoria | DESCRIÇÃO |
| --- | --- |
| Serviço do Service Fabric |Contadores específicos do Azure Service Fabric Remoting, por exemplo, média de tempo gasto para processar a solicitação |
| Método de Serviço do Service Fabric |Contadores específicos aos métodos implementados pelo Service Fabric Remoting Service; por exemplo, quantas vezes um método de serviço é invocado |

Cada uma das categorias anteriores tem um ou mais contadores.

O aplicativo [Monitor de Desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) está disponível por padrão no sistema operacional Windows e pode ser usado para coletar e exibir dados do contador de desempenho. [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para coletar dados do contador de desempenho e carregá-los nas tabelas do Azure.

### <a name="performance-counter-instance-names"></a>Nomes da instância do contador de desempenho
Um cluster que tem um grande número de serviços do ServiceRemoting ou partições tem um grande número de instâncias do contador de desempenho. Os nomes da instância do contador de desempenho podem ajudar a identificar a partição e o método de Serviço (se aplicável) aos quais a instância do contador de desempenho está associada.

#### <a name="service-fabric-service-category"></a>Categoria de Serviço do Service Fabric
Para a categoria `Service Fabric Service`, os nomes da instância do contador estão no seguinte formato:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* é a representação da cadeia de caracteres da ID da partição do Service Fabric à qual a instância do contador de desempenho está associada. A ID da partição é um GUID e sua representação da cadeia de caracteres é gerada por meio do método [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) com o especificador de formato "D".

*ServiceReplicaOrInstanceID* é a representação da cadeia de caracteres da ID da Réplica/Instância do Service Fabric à qual a instância do contador de desempenho está associada.

*ServiceRuntimeInternalID* é a representação da cadeia de caracteres de um inteiro de 64 bits que é gerada pelo tempo de execução do Fabric Service para seu uso interno. Isso está incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflito com outros nomes da instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

Veja a seguir o exemplo de um nome da instância do contador para um contador que pertence à categoria `Service Fabric Service` :

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

No exemplo amterior, `2740af29-78aa-44bc-a20b-7e60fb783264` é a representação da cadeia de caracteres da ID da partição do Service Fabric, `635650083799324046` é a representação da cadeia de caracteres da ID da Réplica/Instância e `5008379932` é a ID de 64 bits que é gerada para uso interno do tempo de execução.

#### <a name="service-fabric-service-method-category"></a>Categoria Método de Serviço do Service Fabric
Para a categoria `Service Fabric Service Method`, os nomes da instância do contador estão no seguinte formato:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* é o nome do método do serviço ao qual a instância do contador de desempenho está associada. O formato do nome do método é determinado com base em alguma lógica no tempo de execução do Fabric Service que equilibra a legibilidade do nome com restrições no comprimento máximo dos nomes da instância do contador de desempenho no Windows.

*ServiceRuntimeMethodID* é a representação da cadeia de caracteres de um inteiro de 32 bits que é gerada pelo tempo de execução do Fabric Service para seu uso interno. Isso está incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflito com outros nomes da instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

*ServiceFabricPartitionID* é a representação da cadeia de caracteres da ID da partição do Service Fabric à qual a instância do contador de desempenho está associada. A ID da partição é um GUID e sua representação da cadeia de caracteres é gerada por meio do método [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) com o especificador de formato "D".

*ServiceReplicaOrInstanceID* é a representação da cadeia de caracteres da ID da Réplica/Instância do Service Fabric à qual a instância do contador de desempenho está associada.

*ServiceRuntimeInternalID* é a representação da cadeia de caracteres de um inteiro de 64 bits que é gerada pelo tempo de execução do Fabric Service para seu uso interno. Isso está incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflito com outros nomes da instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

Veja a seguir o exemplo de um nome da instância do contador para um contador que pertence à categoria `Service Fabric Service Method` :

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

No exemplo anterior, `ivoicemailboxservice.leavemessageasync` é o nome do método, `2` é a ID de 32 bits gerada para uso interno do tempo de execução, `89383d32-e57e-4a9b-a6ad-57c6792aa521` é a representação da cadeia de caracteres da ID da partição do Service Fabric, `635650083804480486` é a representação da cadeia de caracteres da ID da Réplica/Instância e `5008380` é a ID de 64 bits gerada para uso interno do tempo de execução.

## <a name="list-of-performance-counters"></a>Lista de Contadores de desempenho
### <a name="service-method-performance-counters"></a>Contadores de desempenho do método de serviço

O tempo de execução do Reliable Service publica os contadores de desempenho relacionados à execução dos métodos de serviço a seguir.

| Nome da categoria | Nome do contador | DESCRIÇÃO |
| --- | --- | --- |
| Método de Serviço do Service Fabric |Invocação/s |Número de vezes que o método de serviço é invocado por segundo |
| Método de Serviço do Service Fabric |Média de milissegundos por invocação |Tempo usado para executar o método de serviço em milissegundos |
| Método de Serviço do Service Fabric |Exceções lançadas/s |Número de vezes que o método de serviço lançou uma exceção por segundo |

### <a name="service-request-processing-performance-counters"></a>Solicitação de serviço processando contadores de desempenho
Quando um cliente chama um método por meio de um objeto de proxy do serviço, isso resulta no envio de uma mensagem de solicitação pela rede para o serviço de comunicação remota. O serviço processa a mensagem de solicitação e envia uma resposta de volta ao cliente. O tempo de execução do Reliable ServiceRemoting publica os seguintes contadores de desempenho relacionados ao processamento de solicitação de serviço.

| Nome da categoria | Nome do contador | DESCRIÇÃO |
| --- | --- | --- |
| Serviço do Service Fabric |nº de solicitações pendentes |Número de solicitações sendo processadas no serviço |
| Serviço do Service Fabric |Média de milissegundos por solicitação |Tempo (em milissegundos) que o serviço leva para processar uma solicitação |
| Serviço do Service Fabric |Média de milissegundos para desserialização de solicitação |Tempo (em milissegundos) para desserializar a mensagem de solicitação de serviço quando ela é recebida no serviço |
| Serviço do Service Fabric |Média de milissegundos para serialização de resposta |Tempo (em milissegundos) para serializar a mensagem de resposta do serviço no serviço antes de a resposta ser enviada ao cliente |

## <a name="next-steps"></a>Próximas etapas
* [Exemplo de código](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [Provedores de EventSource no PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
