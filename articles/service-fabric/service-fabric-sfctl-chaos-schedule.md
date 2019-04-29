---
title: CLI do Azure Service Fabric - agenda do sfctl chao | Microsoft Docs
description: Descreve os comandos da agenda sfctl chaos da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: dc3dd06b5feac1f66598cd65fa79f447a1bbd9be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837497"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Obter e definir a agenda do caos.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| get | Obter a Agenda de Caos define quando e como executar o Caos. |
| set | Defina a agenda usada por Chaos. |

## <a name="sfctl-chaos-schedule-get"></a>obter agenda do sfctl chaos
Obter a Agenda de Caos define quando e como executar o Caos.

Obtém a versão da Agenda de Caos em uso e Agenda de Caos que define quando e como executar o Caos.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-chaos-schedule-set"></a>definir agenda do sfctl chaos
Defina a agenda usada por Chaos.

O Caos agendará automaticamente execuções com base na Agenda do Caos. A versão na agenda de entrada fornecida deve corresponder à versão da Agenda do Caos no servidor. Se a versão fornecida não coincidir com a versão do servidor, a Agenda do Caos não será atualizada. Se a versão fornecida corresponde à versão no servidor, a Agenda do Caos é atualizada, e a versão da Agenda do Caos no servidor é incrementada para cima em um e volta para 0 depois de 2,147,483,647. Se o Caos é executado quando esta chamada é feita, a chamada falhará.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --chaos-parameters-dictionary | Lista codificada JSON que representa um mapeamento de nomes de cadeia de caracteres para ChaosParameters a ser usado por trabalhos codificados para Trabalhos. |
| --expiry-date-utc | Data e hora de quando parar de usar a Agenda para agendar o Caos.  Padrão\: 9999-12-31T23\:59\:59.999Z. |
| --jobs | Lista codificada JSON de ChaosScheduleJobs representando quando executar o Caos e com quais parâmetros executar o Caos. |
| --start-date-utc | Data e hora de quando iniciar o uso da Agenda para agendar o Caos.  Padrão\: 1601-01-01T00\:00\:00.000Z. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --versão | Número de versão da Agenda. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

### <a name="examples"></a>Exemplos

O comando a seguir define uma agenda (supondo que a agenda atual tenha a versão 0) que inicia em 01-01-2016 e expira em 01-01-2038 que executa o Caos 24 horas por dia, 7 dias por semana. O Caos serão agendado no cluster para esse horário.

    sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]


## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
