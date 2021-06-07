---
title: Considerações de desempenho do NFS 3,0 no armazenamento de BLOBs do Azure (visualização) | Microsoft Docs
description: Otimize o desempenho de suas solicitações de armazenamento do NFS (sistema de arquivos de rede) 3,0 usando as recomendações neste artigo.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 28fb5ffc0ce9c554294eaca9e460694856cb95a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589864"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage-preview"></a>Considerações de desempenho do NFS (sistema de arquivos de rede) 3,0 no armazenamento de BLOBs do Azure (versão prévia)

O armazenamento de BLOBs agora dá suporte ao protocolo NFS (Network File System) 3,0. Este artigo contém recomendações que ajudam a otimizar o desempenho de suas solicitações de armazenamento. Para saber mais sobre o suporte a NFS 3,0 no armazenamento de BLOBs do Azure, consulte [suporte ao protocolo NFS (Network File System) 3,0 no armazenamento de BLOBs do Azure (versão prévia)](network-file-system-protocol-support.md).

> [!NOTE]
> O suporte ao protocolo NFS 3,0 no armazenamento de BLOBs do Azure está em visualização pública. Ele dá suporte a contas de armazenamento GPV2 com desempenho de camada Standard nas seguintes regiões: leste da Austrália, centro da Coreia e EUA Central do Sul. A visualização também dá suporte ao blob de blocos com o nível de desempenho premium em todas as regiões públicas.

## <a name="add-clients-to-increase-throughput"></a>Adicionar clientes para aumentar a taxa de transferência 

O armazenamento de BLOBs do Azure é dimensionado linearmente até atingir o limite máximo de egresso e entrada da conta de armazenamento. Portanto, seus aplicativos podem obter uma taxa de transferência mais alta usando mais clientes.  Para exibir os limites de saída e entrada da conta de armazenamento, consulte [metas de escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md).

O gráfico a seguir mostra como a largura de banda aumenta conforme você adiciona mais clientes. Neste gráfico, um cliente é uma VM (máquina virtual) e a conta usa o nível de desempenho padrão. 

> [!div class="mx-imgBorder"]
> ![Desempenho padrão](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

O gráfico a seguir mostra esse mesmo efeito quando aplicado a uma conta que usa o nível de desempenho premium.

> [!div class="mx-imgBorder"]
> ![Desempenho premium](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>Usar o nível de desempenho premium para aplicativos de pequena escala

Nem todos os aplicativos podem escalar verticalmente adicionando mais clientes. Para esses aplicativos, a [conta de armazenamento de blob de blocos Premium do Azure](storage-blob-create-account-block-blob.md) oferece taxas de transações baixas e de baixa latência consistentes. A conta de armazenamento de blob de blocos Premium pode alcançar a largura de banda máxima com menos threads e clientes. Por exemplo, com um único cliente, uma conta de armazenamento de blob de blocos Premium pode atingir uma largura de banda de **2,3 x** em comparação com a mesma configuração usada com uma conta de armazenamento de uso geral v2 de desempenho padrão. 

Cada barra no gráfico a seguir mostra a diferença na largura de banda alcançada entre as contas de armazenamento de desempenho premium e Standard. À medida que aumenta o número de clientes, essa diferença diminui.  

> [!div class="mx-imgBorder"]
> ![Desempenho relativo](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="avoid-frequent-overwrites-on-data"></a>Evite substituições frequentes nos dados

Demora mais tempo para concluir uma operação de substituição do que uma nova operação de gravação. Isso ocorre porque uma operação de substituição de NFS, especialmente uma edição parcial de arquivo in-loco, é uma combinação de várias operações de blob subjacentes: uma operação de leitura, uma modificação e uma de gravação. Portanto, um aplicativo que requer edições frequentes no local não é adequado para contas de armazenamento de BLOBs habilitadas para NFS. 

## <a name="other-best-practice-recommendations"></a>Outras recomendações de práticas recomendadas 

- Use VMs com largura de banda de rede suficiente.

- Use vários pontos de montagem quando suas cargas de trabalho permitirem.

- Use o máximo de threads possível.

- Use grandes tamanhos de bloco.

- Faça solicitações de armazenamento de um cliente que esteja localizado na mesma região que a conta de armazenamento. Isso pode melhorar a latência de rede.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o suporte a NFS 3,0 no armazenamento de BLOBs do Azure, consulte [suporte ao protocolo NFS (Network File System) 3,0 no armazenamento de BLOBs do Azure (versão prévia)](network-file-system-protocol-support.md).

- Para começar, consulte [montar o armazenamento de BLOBs usando o protocolo NFS (sistema de arquivos de rede) 3,0 (versão prévia)](network-file-system-protocol-support-how-to.md).
