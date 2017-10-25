---
title: "Desempenho e escala nas Funções Duráveis – Azure"
description: "Introdução à extensão de Funções Duráveis do Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 10ce74097388a0283797e4692126c5039e8d4dd0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Desempenho e escala nas Funções Duráveis (Azure Functions)

Para otimizar o desempenho e escalabilidade, é importante entender as características únicas da colocação em escala nas [Funções Duráveis](durable-functions-overview.md).

Para entender o comportamento de escala, você precisa entender alguns dos detalhes do provedor subjacente do Armazenamento do Azure usado pelas Funções Duráveis.

## <a name="history-table"></a>Tabela de histórico

A tabela de histórico é uma tabela do Armazenamento do Azure que contém eventos de histórico de todas as instâncias de orquestração. Conforme as instâncias são executadas, novas linhas são adicionadas a essa tabela. A chave de partição da tabela é derivada da ID de instância da orquestração. Esses valores são aleatórios na maioria dos casos, o que garante a distribuição ideal das partições internas do Armazenamento do Azure.

## <a name="internal-queue-triggers"></a>Gatilhos de fila interna

Funções de orquestrador e funções de atividade são disparadas por filas internas na conta de armazenamento padrão do aplicativo de funções. Há dois tipos de filas nas Funções Duráveis: a **fila de controle** e a **fila de itens de trabalho**.

### <a name="the-work-item-queue"></a>A fila de itens de trabalho

Há uma fila de itens de trabalho por hub de tarefas nas Funções Duráveis. Essa é uma fila básica, que se comporta da mesma forma que qualquer outra fila `queueTrigger` no Azure Functions. Essa fila é usada para disparar *funções de atividade* sem monitoração de estado. Quando um aplicativo de Funções Duráveis é expandido para várias VMs, todas essas VMs concorrem para obter trabalho da fila de itens de trabalho.

### <a name="control-queues"></a>Fila(s) de controle

A *fila de controle* é mais sofisticada do que as filas de itens de trabalho mais simples. Ela é usada para disparar as funções de orquestrador com estado. Como as instâncias de função de orquestrador são singletons com estado, não é possível usar um modelo de consumidor concorrente para distribuir a carga entre VMs. Em vez disso, as mensagens do orquestrador sofrem balanceamento de carga em várias filas de controle. Mais detalhes sobre isso são fornecidos nas próximas seções.

Filas de controle contêm uma variedade de tipos de mensagem de ciclo de vida de orquestração. Exemplos incluem [mensagens de controle de orquestrador](durable-functions-instance-management.md), mensagens de *resposta* de função de atividade e mensagens de temporizador.

## <a name="orchestrator-scale-out"></a>Expansão do orquestrador

Funções de atividade sem monitoração de estado e são expandidas automaticamente adicionando VMs. Funções de orquestrador, por outro lado, são *particionadas* entre uma ou mais filas de controle. O número de filas de controle é fixo e não pode ser alterado após você iniciar a criação de carga.

Ao expandir para várias instâncias de host de função (normalmente, em VMs diferentes), cada instância adquire um bloqueio de uma das filas de controle. Esse bloqueio garante que uma instância de orquestração seja executada apenas em uma única VM por vez. Isso significa que, se um hub de tarefas for configurado com três filas de controle, instâncias de orquestração poderão sofrer balanceamento de carga entre até três VMs. VMs adicionais podem ser adicionadas para aumentar a capacidade de execução da função de atividade.  Entretanto, os recursos adicionais não serão usados para executar funções de orquestrador.

O diagrama a seguir ilustra como o host do Azure Functions interage com as entidades de armazenamento em um ambiente expandido.

![Diagrama de escala](media/durable-functions-perf-and-scale/scale-diagram.png)

Como você pode ver, todas as VMs podem concorrer por mensagens na fila de itens de trabalho. No entanto, apenas três VMs podem adquirir mensagens das filas de controle e cada VM bloqueia uma única fila de controle.

Instâncias de orquestração são distribuídas entre instâncias de fila de controle executando uma função de hash interna na ID da instância de orquestração. IDs de instância são geradas automaticamente e são aleatórias por padrão, o que garante que as instâncias sejam balanceadas entre todas as filas de controle disponíveis. O atual número padrão de partições de fila de controle com suporte é **4**.

> [!NOTE]
> No momento, não é possível configurar o número de partições no Azure Functions. [O trabalho para dar suporte a essa opção de configuração está sendo acompanhado](https://github.com/Azure/azure-functions-durable-extension/issues/73).

De modo geral, funções de orquestrador devem ser leves e não devem precisar de muita capacidade de computação. Por esse motivo, não é necessário criar um grande número de partições de fila de controle para obter uma boa taxa de transferência. Em vez disso, a maior parte do trabalho pesado é feito em funções de atividade sem monitoração de estado, que podem ser expandidas infinitamente.

## <a name="auto-scale"></a>Dimensionamento automático

Assim como ocorre com todas as funções do Azure Functions em execução no Plano de Consumo, as Funções Duráveis dão suporte ao dimensionamento automático por meio do [Controlador de escala do Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-scale#runtime-scaling). O Controlador de Escala monitora o tamanho da fila de itens de trabalho e de cada uma das filas de controle, adicionando ou removendo recursos de VM adequadamente. Se os tamanhos das filas de controle aumentarem ao longo do tempo, o controlador de escala continuará adicionando instâncias até atingir a contagem de partições da fila de controle. Se os tamanhos das filas de itens de trabalho aumentarem ao longo do tempo, o controlador de escala continuará adicionando recursos de VM até corresponder `a carga, independentemente da contagem de partições da fila de controle.

## <a name="thread-usage"></a>Uso de thread

Funções de orquestrador são executadas em um único thread. Isso é necessário para garantir que a execução da função de orquestrador seja determinística. Tendo isso em mente, é importante nunca manter o thread da função de orquestrador desnecessariamente ocupado com tarefas como operações de E/S (que é proibida por uma variedade de motivos), bloqueios ou rotações. Qualquer trabalho que possa exigir E/S, bloqueio ou vários threads deve ser movido para funções de atividade.

As funções de atividade têm os mesmos comportamentos que as funções disparadas por filas regulares. Isso significa que elas podem, de maneira segura, fazer E/S, executar operações com uso intensivo de CPU e usar vários threads. Como os gatilhos de atividade são sem monitoração de estado, eles podem ser expandidos livremente para um número ilimitado de VMs.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Instalar a extensão de Funções Duráveis e exemplos](durable-functions-install.md)
