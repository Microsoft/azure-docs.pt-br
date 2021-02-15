---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 091e4dc0759ce1b227349d5d1b6b08b9eda66ee0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017331"
---
* A conversão requer uma reinicialização da VM, programe então a migração de suas VMs durante uma janela de manutenção já existente. 

* A conversão não é reversível. 

* Lembre-se de que quaisquer usuários com a função [Colaborador da Máquina Virtual](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) não poderá alterar o tamanho da VM (como podiam antes da conversão). Isso ocorre porque as VMs com discos gerenciados requerem que o usuário tenha a permissão Microsoft.Compute/disks/write nos discos do sistema operacional.

* Não deixe de testar a conversão. Migre uma máquina virtual de teste antes de executar a migração na produção.

* Durante a conversão, você pode desalocar a VM. A VM recebe um endereço IP novo quando é iniciada após a conversão. Se necessário, você pode [atribuir um endereço IP estático](../articles/virtual-network/public-ip-addresses.md) à VM.

* Examine a versão mínima do agente de VM do Azure necessária para oferecer suporte ao processo de conversão. Para saber mais sobre como verificar e atualizar a versão do seu agente, confira [Suporte de versão mínima para agentes de VM no Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)