---
title: Azure NetApp Files benchmarks de desempenho para Linux | Microsoft Docs
description: Descreve os benchmarks de desempenho Azure NetApp Files entregas para Linux.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: b-juche
ms.openlocfilehash: b763a734866dd5fed5bf0500d4d52b9324c92a79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82614584"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>Azure NetApp Files benchmarks de desempenho para Linux

Este artigo descreve os benchmarks de desempenho Azure NetApp Files entregas para Linux.

## <a name="linux-scale-out"></a>Expansão do Linux

Esta seção descreve os benchmarks de desempenho da taxa de transferência de carga de trabalho e do IOPS do Linux.

### <a name="linux-workload-throughput"></a>Produtividade de carga de trabalho do Linux  

O grafo abaixo representa uma carga de trabalho Sequencial de 64-KibiByte (KiB) e um conjunto de trabalhos de TiB. Ele mostra que um único volume de Azure NetApp Files pode manipular entre ~ 1.600 gravações sequenciais puras MiB/s e aproximadamente 4.500 leituras sequenciais puras de MiB/s.  

O grafo ilustra as diminuições em 10% por vez, de leitura pura para gravação pura. Ele demonstra o que você pode esperar ao usar diferentes taxas de leitura/gravação (100%: 0%, 90%: 10%, 80%: 20% e assim por diante).

![Produtividade de carga de trabalho do Linux](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>IOPS de carga de trabalho do Linux  

O grafo a seguir representa uma carga de trabalho aleatória de 4 KibiByte (KiB) e um conjunto de funcionamento de 1 TiB. O grafo mostra que um volume Azure NetApp Files pode manipular entre ~ 130.000 gravações aleatórias puras e ~ 460.000 leituras aleatórias puras.  

Esse grafo ilustra as diminuições em 10% por vez, de leitura pura para gravação pura. Ele demonstra o que você pode esperar ao usar diferentes taxas de leitura/gravação (100%: 0%, 90%: 10%, 80%: 20% e assim por diante).

![IOPS de carga de trabalho do Linux](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Expansão do Linux  

O kernel do Linux 5,3 habilita a rede de expansão de cliente único para NFS- `nconnect` . Os grafos nesta seção mostram os resultados do teste de validação para a opção de montagem do lado do cliente com NFSv3. O recurso está disponível no SUSE (a partir do SLES12SP4) e do Ubuntu (começando com a versão 19,10). Ele é semelhante em conceito ao SMB Multichannel e ao Oracle Direct NFS.

Os grafos comparam as vantagens de `nconnect` um volume montado não conectado. Nos grafos, FIO gerou a carga de trabalho de uma única instância de D32s_v3 na região do Azure US-west2.

### <a name="linux-read-throughput"></a>Taxa de transferência de leitura do Linux  

Os gráficos a seguir mostram leituras sequenciais de ~ 3.500 leituras de MiB/s com `nconnect` , aproximadamente 2.3 x não `nconnect` .

![Taxa de transferência de leitura do Linux](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Produtividade de gravação do Linux  

Os gráficos a seguir mostram gravações sequenciais. Eles indicam que o `nconnect` não tem nenhum benefício perceptível para gravações sequenciais. 1.500 MiB/s é basicamente o limite superior do volume de gravação sequencial e o limite de saída da instância de D32s_v3.

![Produtividade de gravação do Linux](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>IOPS de leitura do Linux  

Os gráficos a seguir mostram leituras aleatórias de ~ 200.000 IOPS de leitura com `nconnect` , aproximadamente 3x não- `nconnect` .

![IOPS de leitura do Linux](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>IOPS de gravação do Linux  

Os gráficos a seguir mostram gravações aleatórias de ~ 135.000 gravação IOPS com `nconnect` , aproximadamente 3x não- `nconnect` .

![IOPS de gravação do Linux](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>Próximas etapas

- [Azure NetApp Files: obtendo o máximo do seu armazenamento em nuvem](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
