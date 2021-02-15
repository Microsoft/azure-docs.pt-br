---
title: Monitorando padrões de rotatividade em máquinas virtuais
description: Saiba como monitorar padrões de rotatividade em máquinas virtuais protegidas usando o Azure Site Recovery
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 09/09/2020
ms.author: sharrai
ms.openlocfilehash: aeb89a9d18e4550fa1d6162920d60507fd50c208
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359859"
---
# <a name="monitoring-churn-patterns-on-virtual-machines"></a>Monitorando padrões de rotatividade em máquinas virtuais

Este artigo fornece uma visão geral de várias ferramentas que podem ser usadas para monitorar padrões de rotatividade em uma máquina virtual. Usando as ferramentas adequadas, é fácil descobrir exatamente qual aplicativo está causando alta rotatividade e, em seguida, ações adicionais para esse aplicativo podem ser realizadas.

## <a name="for-azure-virtual-machines-windows-or-linux"></a>Para máquinas virtuais do Azure (Windows ou Linux)

Se seu computador estiver hospedado no Azure e estiver usando um disco gerenciado ou não gerenciado para armazenamento, você poderá controlar o desempenho com facilidade, rastreando as métricas de disco. Isso permite que você monitore com atenção e faça com que a seleção de disco correta se adapte ao padrão de uso do aplicativo. Você também pode usá-lo para criar alertas, diagnóstico e automação de compilação. [Saiba mais](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

Depois de proteger seus computadores com o Azure Site Recovery, você pode monitorar seus computadores usando Azure Monitor logs e Log Analytics. [Saiba mais](./monitor-log-analytics.md).

Também há algumas ferramentas específicas do sistema operacional que você pode usar.

## <a name="for-windows-machines"></a>Para computadores Windows

Caso você tenha um computador, seja local ou não, que esteja executando o sistema operacional Windows, há mais algumas ferramentas disponíveis.

Além de verificar o uso do disco no Gerenciador de tarefas, você sempre pode consultar o **Monitor de recursos** e o **Monitor de desempenho**. Essas ferramentas já estão presentes em computadores Windows.

### <a name="resource-monitor"></a>Monitor de Recursos

**Monitor de recursos** exibe informações sobre o uso de recursos de hardware e software em tempo real. Para executar Monitor de Recursos em um computador Windows, siga as etapas abaixo –

1. Pressione Win + R e digite _resmon_.
1. Quando o resmon, ou seja, Monitor de Recursos, a janela é aberta para a guia disco. Ele fornece a exibição a seguir-

    ![Guia Monitor de Recursos disco](./media/monitoring-high-churn/resmon-disk-tab.png)

1. Essa guia deve ser monitorada continuamente por algum tempo para obter uma visão clara. No exemplo acima, vemos que _wmiprv.exe_ está se transalturando alto.

Depois de identificar os aplicativos que estão causando alta rotatividade em seu computador, você pode tomar as medidas necessárias para lidar com a rotatividade relacionada a esses aplicativos.

### <a name="performance-monitor"></a>Monitor de desempenho

O **Monitor de desempenho** monitora várias atividades em um computador, como uso de CPU ou memória. Para executar o monitor de desempenho em um computador Windows, siga as etapas abaixo –

1. Pressione Win + R e digite _Perfmon_.
1. Quando o PerfMon, ou seja, o monitor de desempenho, a janela é aberto, ele fornece a exibição a seguir-

    ![Etapa 1 do monitor de desempenho](./media/monitoring-high-churn/perfmon-step1.png)

1. Expanda a pasta **ferramentas de monitoramento** à direita e clique em monitor de desempenho. Isso abrirá o modo de exibição abaixo, fornecendo a você informações em tempo real sobre o desempenho atual-

    ![Etapa 2 do monitor de desempenho](./media/monitoring-high-churn/perfmon-step1.png)

1. Atualmente, esse grafo está monitorando apenas um monitor, ou seja, o '% Processor Time ' – conforme indicado pela tabela logo abaixo do grafo. Você pode adicionar mais itens para monitoramento clicando em **"+"** na parte superior da ferramenta.
1. Abaixo está um Visual de como o monitor de desempenho é parecido com a adição de mais contadores a ele.

    ![Etapa 3 do monitor de desempenho](./media/monitoring-high-churn/perfmon-step3.png)

Saiba mais sobre o monitor de desempenho [aqui](/dynamics365/business-central/dev-itpro/administration/monitor-use-performance-monitor-collect-event-trace-data).

## <a name="for-linux-machines"></a>Para computadores Linux

Caso você tenha uma máquina, seja local ou não, que esteja executando o sistema operacional Linux, há mais algumas ferramentas disponíveis para monitorar os padrões de rotatividade.

### <a name="iotop"></a>Iotop

Uma das ferramentas mais usadas é o _iotop_. É um utilitário para exibir a atividade de disco em tempo real. Ele pode listar os processos que estão executando e/s, juntamente com a largura de banda do disco que estão usando.

Abra o prompt de comando e execute o comando `iotop` .

### <a name="iostat"></a>IoStat

IoStat é uma ferramenta simples que coletará e mostrará as estatísticas do dispositivo de armazenamento de entrada e saída do sistema. Essa ferramenta é geralmente usada para rastrear problemas de desempenho do dispositivo de armazenamento, incluindo dispositivos, discos locais, discos remotos.

Abra o prompt de comando e execute o comando `iostat` .

## <a name="next-steps"></a>Próximas etapas

Saiba como monitorar com o [Azure monitor](monitor-log-analytics.md).