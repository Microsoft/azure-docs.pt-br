---
title: Avere vFXT para projetos de demonstração do Azure
description: 'Esses exemplos mostram os principais recursos e casos de uso do avere vFXT for Azure: renderização de vídeo, computação de alto desempenho, desempenho de vFXT e configuração de cliente.'
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 43162c4c9812015952c749446d160ab1c0d84392
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639407"
---
# <a name="avere-vfxt-demo-projects"></a>Projetos de demonstração do Avere vFXT

Tutoriais de exemplo são fornecidos no [GitHub](https://github.com/Azure/Avere). Esses pequenos projetos demonstram os principais recursos e casos de uso do Avere vFXT para Azure.

## <a name="video-rendering"></a>Renderização de vídeo

* [Renderização usando o lote do Azure e avere vFXT](https://github.com/Azure/Avere/blob/master/docs/maya_azure_batch_avere_vfxt_demo.md) -um projeto de 60 minutos que demonstra como usar Autodesk Maya com o lote do Azure e um cluster avere vFXT para gerar um filme animado

* [Por que usar o Avere vFXT para renderização?](https://github.com/Azure/Avere/blob/master/docs/why_avere_for_rendering.md) -Uma demonstração que compara os tempos de renderização do armazenamento anexado à rede com e sem um cluster avere vFXT

## <a name="high-performance-computing"></a>Computação de alto desempenho

* [Práticas recomendadas para melhorar o tempo de inicialização da VM (máquina virtual) do Azure](https://github.com/Azure/Avere/blob/master/docs/azure_vm_provision_best_practices.md) – um teste reproduzível que usa avere vFXT para o Azure para melhorar os tempos de inicialização ao girar milhares de clientes de computação rapidamente

## <a name="vfxt-performance"></a>desempenho do vFXT

* [Meça o desempenho de vFXT com o vdbench](https://github.com/Azure/Avere/blob/master/docs/vdbench.md) -uma configuração de teste básica para gerar cargas de trabalho pequenas e de médio porte para testar os subsistemas de memória e de disco vFXT

## <a name="client-setup"></a>Configuração do cliente

* [Estação de trabalho do Windows 10 para Avere vFXT](https://github.com/Azure/Avere/blob/master/docs/windows_10_avere_vfxt_mounted_workstation.md) – demonstra como configurar uma estação de trabalho do Windows e recriá-la em um cluster do Avere vFXT
