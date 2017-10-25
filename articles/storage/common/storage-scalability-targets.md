---
title: Metas de desempenho e escalabilidade do Armazenamento do Azure | Microsoft Docs
description: "Saiba mais sobre as metas de desempenho e escalabilidade para Armazenamento do Azure, incluindo a capacidade, taxa de solicitação e largura de banda de entrada e saída, tanto para contas de armazenamento standard quanto premium. Entender as metas de desempenho para partições em de cada um dos serviços do Armazenamento do Azure."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 07/12/2017
ms.author: tamram
ms.openlocfilehash: 1ed933493da1842201bb9293f514ea4d0e7a75ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Metas de desempenho e escalabilidade do Armazenamento do Azure
## <a name="overview"></a>Visão geral
Este tópico descreve os tópicos de desempenho e escalabilidade do Armazenamento do Microsoft Azure. Para obter um resumo de outros limites do Azure, confira [Assinatura do Azure e limites de serviços, cotas e restrições](../../azure-subscription-service-limits.md).

> [!NOTE]
> Todas as contas de armazenamento são executadas na nova topologia de rede simples e dão suporte às metas de escalabilidade e desempenho descritas abaixo, independentemente de quando foram criadas. Para obter mais informações sobre a arquitetura de rede simples do armazenamento do Azure e sobre escalabilidade, consulte [Armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 
> [!IMPORTANT]
> As metas de escalabilidade e desempenho listadas aqui são metas avançadas, mas podem ser alcançadas. Em todos os casos, a taxa de solicitação e a largura de banda obtidas por sua conta armazenamento dependem do tamanho dos objetos armazenados, dos padrões de acesso utilizados e do tipo de carga de trabalho executado por seu aplicativo. Teste o serviço para determinar se o desempenho atende às suas necessidades. Se possível, evite picos repentinos na taxa de tráfego e certifique-se de que o tráfego esteja bem distribuído entre as partições.
> 
> Quando seu aplicativo atingir o limite de uma partição, o Armazenamento do Azure passará a retornar respostas com o código de erro 503 (servidor ocupado) ou o código de erro 500 (tempo limite da operação). Quando isso ocorre, o aplicativo deve usar uma política de retirada exponencial para repetições. A retirada exponencial permite que a carga na partição diminua e afasta os picos de tráfego nessa partição.
> 
> 

Se as necessidades de seu aplicativo excederem as metas de escalabilidade de uma única conta de armazenamento, crie seu aplicativo para usar múltiplas contas de armazenamento e faça o particionamento dos seus objetos de dados nessas contas de armazenamento. Consulte [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter informações sobre preço por volume.

## <a name="scalability-targets-for-blobs-queues-tables-and-files"></a>Metas de escalabilidade para blobs, filas, tabelas e arquivos
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
## <a name="scalability-targets-for-virtual-machine-disks"></a>Metas de escalabilidade para discos de máquina virtual
[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Veja [Tamanhos de VM do Windows](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Tamanhos de VM do Linux](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter detalhes adicionais.

## <a name="managed-virtual-machine-disks"></a>Discos de máquina virtual gerenciados

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

## <a name="unmanaged-virtual-machine-disks"></a>Discos de máquina virtual não gerenciados
[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="scalability-targets-for-azure-resource-manager"></a>Metas de escalabilidade para o Gerenciador de recursos do Azure
[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="partitions-in-azure-storage"></a>Partições no Armazenamento do Azure
Cada objeto com dados armazenado no Armazenamento do Azure (blobs, mensagens, entidades e arquivos) pertence a uma partição e é identificado por uma chave de partição. A partição determina como o Armazenamento do Azure equilibra as cargas de blobs, mensagens, entidades e arquivos em servidores a fim de atender às necessidades de tráfego desses objetos. A chave de partição é exclusiva e é usada para localizar um blob, mensagem ou entidade.

A tabela acima em [Metas de escalabilidade para contas de armazenamento padrão](#standard-storage-accounts) lista as metas de desempenho para uma única partição para cada serviço.

As partições afetam o balanceamento de carga e a escalabilidade de cada um dos serviços de armazenamento das seguintes formas:

* **Blobs**: a chave de partição de um blob é o nome da conta + nome do contêiner + nome do blob. Isso significa que cada blob poderá ter sua própria partição se a carga no blob exigir isso. BLOBs podem ser distribuídos em vários servidores para escalar horizontalmente o acesso a eles, mas um único blob só pode ser atendido por um único servidor. Embora os blobs possam ser agrupados logicamente em contêineres de blob, o particionamento não é afetado de forma alguma por esse agrupamento.
* **Arquivos**: A chave de partição para um arquivo é o nome da conta + nome do compartilhamento do arquivos. Isso significa que todos os arquivos em um compartilhamento de arquivos também estão em uma única partição.
* **Mensagens**: a chave de partição de uma mensagem é o nome da conta + nome da fila, portanto, todas mensagens em uma fila são agrupadas em uma única partição e são atendidas por um único servidor. Filas diferentes podem ser processadas por servidores diferentes a fim de equilibrar a carga, não importa a quantidade de filas que uma conta de armazenamento tenha.
* **Entidades**: a chave de partição de uma entidade é nome da conta + nome da tabela + chave de partição, sendo que a chave de partição é o valor da propriedade obrigatória **PartitionKey** definida pelo usuário para a entidade. Todas as entidades com o mesmo valor de chave de partição são agrupadas na mesma partição e são atendidas pelo mesmo servidor de partição. É importante entender isso ao projetar seu aplicativo. Seu aplicativo deve equilibrar os benefícios de escalabilidade da propagação de entidades por várias partições com as vantagens de acesso de dados do agrupamento de entidades em uma única partição.  

Uma vantagem importante de agrupar em uma única partição um conjunto de entidades contidas em uma tabela é a possibilidade de executar operações de lote atômicas nas entidades da mesma partição, já que uma partição existe em um único servidor. Portanto, se você quiser executar operações em lote em um grupo de entidades, considere agrupar essas entidades com a mesma chave de partição. 

Por outro lado, entidades que estão na mesma tabela mas têm chaves de partição diferentes podem ter seu balanceamento de carga realizado em servidores diferentes, possibilitando maior escalabilidade.

Recomendações detalhadas para a criação de estratégias de particionamento de tabelas podem ser encontradas [aqui](https://msdn.microsoft.com/library/azure/hh508997.aspx).

## <a name="see-also"></a>Consulte também
* [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/)
* [Assinatura do Azure e limites de serviços, cotas e restrições](../../azure-subscription-service-limits.md)
* [Armazenamento Premium: Armazenamento de Alto Desempenho para as Cargas de Trabalho da Máquina Virtual do Azure](../storage-premium-storage.md)
* [Replicação de armazenamento do Azure](../storage-redundancy.md)
* [Lista de verificação de desempenho e escalabilidade do Armazenamento do Microsoft Azure](../storage-performance-checklist.md)
* [Armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

