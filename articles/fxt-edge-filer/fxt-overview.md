---
title: Visão geral do Microsoft Azure FXT Edge Filer
description: Descreve o cache de armazenamento híbrido Azure FXT Edge Filer, uma solução aceleradora de acesso a arquivos e à camada de armazenamento de arquivos ativos para computação de alto desempenho
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 58d4a2a52757b6ace1059fcccf379df3de5fd75c
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542898"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>O que é o cache de armazenamento híbrido Azure FXT Edge Filer?

O Azure FXT Edge Filer é um dispositivo de cache de armazenamento híbrido que fornece acesso rápido a arquivos e à camada de armazenamento de arquivos ativos para tarefas HPC (computação de alto desempenho).

Ele funciona com várias fontes de dados, se armazenado em um data center local, remotamente ou na nuvem. O Azure FXT Edge Filer pode fornecer um namespace unificado para os dados em sistemas de armazenamento diferentes.

Três ou mais dispositivos de hardware do FXT Edge Filer trabalham juntos como um sistema de arquivos clusterizado para fornecer o cache. Para obter detalhes sobre como comprar o hardware necessário, entre em contato com seu representante da Microsoft. 

Para saber mais, leia a folha de dados e informações de produto sobre o [Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Casos de uso

O Azure FXT Edge Filer é mais adequado para aumento de produtividade em fluxos de trabalho como estes:

* Fluxo de trabalho de acesso a arquivos com leitura intensa 
* Protocolos NFSv3 ou SMB2
* Farms de computação com 1000 a 100.000 núcleos de CPU

### <a name="nas-optimization-and-scaling"></a>Otimização e dimensionamento de NAS

Você pode usar o cache do Azure FXT Edge Filer para acessar com tranquilidade sistemas NAS do NetApp e Dell EMC Isilon. Também é possível adicionar Blob do Azure ou outro armazenamento em nuvem para fornecer escalabilidade sem precisar refazer os processos de acesso a dados no lado do cliente. 

### <a name="wan-caching"></a>Armazenamento em cache da WAN

O Azure FXT Edge Filer pode ser usado para dar suporte ao acesso rápido a arquivos de usuários avançados quando os dados necessários estiverem armazenados em qualquer outro lugar. Forneça acesso enquanto mantém o backup e outros sistemas de gerenciamento de dados em um data center centralizado. 

### <a name="active-archive-in-azure-blob"></a>Camada de armazenamento de arquivos ativos no Blob do Azure

Expanda seu data center para o armazenamento em nuvem com o Azure FXT Edge Filer como o ponto de acesso. 

## <a name="features"></a>Recursos 

Dois modelos de hardware estão disponíveis. 

| Modelo | DRAM | SSD NVMe | Portas de rede | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25 Gb/10 Gb + 2 x 1 Gb |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25 Gb/10 Gb + 2 x 1 Gb |


## <a name="next-steps"></a>Próximas etapas

* Continue aprendendo sobre o Azure FXT Edge Filer lendo as [especificações](fxt-specs.md) ou o [tutorial de instalação](fxt-install.md).
* Saiba como comprar o Azure FXT Edge Filer na [página de produto do Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).