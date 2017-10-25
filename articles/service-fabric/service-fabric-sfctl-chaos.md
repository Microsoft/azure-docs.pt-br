---
title: CLI do Azure Service Fabric - sfctl choas | Microsoft Docs
description: Descreve os comandos do sfctl chaos da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 336e74d8f69cb04e6bd0e85fc68ba38b218fabae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-chaos"></a>sfctl chaos
Iniciar, parar e emitir relatórios sobre o serviço de teste de caos.

## <a name="commands"></a>Comandos

|Command|Descrição|
| --- | --- |
|    relatório| Obtém o próximo segmento do relatório do Chaos com base no token de continuação repassado ou no intervalo repassado.|
|    iniciar | Se Chaos já não estiver em execução no cluster, inicie a execução do Chaos os parâmetros de Chaos especificados.|
|    parar  | Interrompe o Chaos no cluster se ele já estiver em execução, caso contrário, ele não faz nada.|


## <a name="sfctl-chaos-report"></a>relatório sfctl chaos
Obtém o próximo segmento do relatório do Chaos com base no token de continuação repassado ou no intervalo repassado.

Você pode especificar o ContinuationToken para obter o próximo segmento do relatório do Chaos ou especificar o intervalo por meio de StartTimeUtc e EndTimeUtc, mas não pode especificar o ContinuationToken e o intervalo de tempo na mesma chamada. Quando há mais de 100 eventos do Chaos, o relatório do Chaos é retornado em segmentos, em que um segmento contém no máximo 100 eventos do Chaos. 

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --continuation-token| O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, então, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL.|
| --hora de término utc   | A contagem de tiques que representa a hora de término do intervalo de tempo para o qual um relatório Chaos deve ser gerado. Consulte [Propriedade DateTime.Ticks](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29) para obter detalhes sobre o tique.|
| --start-time-utc | A contagem de tiques que representa a hora de início do intervalo de tempo para o qual um relatório Chaos deve ser gerado. Consulte [Propriedade DateTime.Ticks](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29) para obter detalhes sobre o tique.|
| --timeout -t     | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug          | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h        | Mostrar esta mensagem de ajuda e sair.|
| --output -o      | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query          | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose        | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-chaos-start"></a>início sfctl chaos
Se Chaos já não estiver em execução no cluster, inicie a execução do Chaos os parâmetros de Chaos especificados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -- mapa de política de integridade do tipo de aplicativo  | Lista codificada JSON com percentual máximo de aplicativos prejudiciais para tipos específicos de aplicativos. Cada entrada especifica como uma chave o nome do tipo de aplicativo e como valor um número inteiro que represente a porcentagem MaxPercentUnhealthyApplications usada para avaliar os aplicativos do tipo de aplicativo especificado.|
| --desabilitar-mover-réplica-falhas | Desabilita as falhas de movimentação primárias e secundárias.|
| --max-cluster-estabilização| A quantidade máxima de tempo de espera para todas as entidades do cluster se tornam estáveis e íntegras.  Padrão: 60.|
| --max-falhas-simultâneas    | O número máximo de falhas simultâneas induzidas por iteração.           Padrão: 1.|
| -max-percentual-aplicativos-prejudiciais  | Ao avaliar a integridade de cluster durante caos, a porcentagem máxima permitida de aplicativos problemáticos antes de relatar um erro.|
| -max-percentual-nós-prejudiciais | Ao avaliar a integridade do cluster durante o Chaos, a porcentagem máxima permitida de nós prejudiciais antes de relatar um erro.|
| -tempo de execução              | Tempo total (em segundos) para o qual o Chaos será executado antes de parar automaticamente. O valor máximo permitido é 4.294.967.295 (System.UInt32.MaxValue).  Padrão: 4294967295.|
| --timeout -t               | Tempo limite do servidor em segundos.  Padrão: 60.|
| --tempo de espera entre falhas | Tempo de espera (em segundos) entre falhas consecutivas dentro de uma única iteração.  Padrão: 20.|
| --tempo de espera entre iterações| Tempo-separação (em segundos) entre as duas iterações consecutivas de Chaos.  Padrão: 30.|
| --aviso-como-erro         | Ao avaliar a integridade do cluster durante o Chaos, trate os avisos com a mesma gravidade dos erros.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                    | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                  | Mostrar esta mensagem de ajuda e sair.|
| --output -o                | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.           Padrão: json.|
| --query                    | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                  | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-chaos-stop"></a>parar sfctl chaos
Interrompe o Chaos no cluster se ele já estiver em execução, caso contrário, ele não faz nada.

Impede que o Chaos agende mais falhas; mas as falhas em andamento não são afetadas.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t| Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug  | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h| Mostrar esta mensagem de ajuda e sair.|
| --output -o | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query  | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose| Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).