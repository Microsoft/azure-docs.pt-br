---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 8eff9da82fdfa5749fd1c2bc04652d5c8ce8dfd2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518035"
---
![Captura de tela da saída de f i o mostrando r = 22.8 k realçado.](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

O Standard_D8s_v3 pode alcançar um total de 28.600 IOPS. Usando as métricas, vamos investigar o que está acontecendo e identificar nosso afunilamento de e/s de armazenamento. No painel esquerdo, selecione **métricas**:

![Captura de tela mostrando métricas realçadas no painel esquerdo.](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Primeiro, vamos dar uma olhada em nossa métrica **percentual consumida de IOPS em cache de VM** :

![Captura de tela mostrando a porcentagem consumida de V M em cache s t.](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Essa métrica nos informa que 61% dos 16.000 IOPS alocados para o IOPS armazenado em cache na VM estão sendo usados. Essa porcentagem significa que o afunilamento de e/s de armazenamento não está com os discos armazenados em cache porque não está em 100%. Agora, vamos examinar nossa métrica de **percentual consumida de IOPS sem cache de VM** :

![Captura de tela mostrando a porcentagem consumida de V M não armazenado em cache.](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Esta métrica está em 100%. Ele nos informa que todos os 12.800 IOPS alocados para o IOPS não armazenado em cache na VM estão sendo usados. Uma maneira de corrigir esse problema é alterar o tamanho de nossa VM para um tamanho maior que possa lidar com a e/s adicional. Mas antes de fazermos isso, vamos examinar o disco anexado para descobrir quantos IOPS eles estão vendo. Verifique o disco do sistema operacional examinando a **porcentagem consumida de IOPS de disco do so**:

![Captura de tela mostrando O percentual de e/s do disco consumido.](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Essa métrica nos informa que cerca de 90% dos 5.000 IOPS provisionados para esse disco de so p30 está sendo usado. Essa porcentagem significa que não há nenhum afunilamento no disco do sistema operacional. Agora, vamos verificar os discos de dados que estão anexados à VM examinando a **porcentagem consumida IOPS do disco de dados**:

![Captura de tela mostrando porcentagem de e/s de disco de dados consumida.](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Essa métrica nos informa que a porcentagem média de IOPS consumidas em todos os discos anexados está em cerca de 42%. Esse percentual é calculado com base nos IOPS usados pelos discos e que não estão sendo servidos do cache do host. Vamos aprofundar essa métrica aplicando a *divisão* nessas métricas e dividindo pelo valor de LUN:

![Captura de tela mostrando porcentagem de e/s de disco de dados consumida com divisão.](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Essa métrica nos informa que os discos de dados anexados no LUN 3 e 2 estão usando cerca de 85% de seus IOPS provisionados. Aqui está um diagrama de como a e/s se parece da arquitetura de VM e de discos:

![Diagrama de exemplo de métricas de e/s de armazenamento.](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
