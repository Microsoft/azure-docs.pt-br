---
title: "Armazenamento Standard econômico baseado em HD e discos de VM do Azure | Microsoft Docs"
description: "Discuta o Armazenamento Standard econômico e discos de VM gerenciados e não gerenciados."
services: storage
documentationcenter: 
author: yuemlu
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: yuemlu
ms.openlocfilehash: b432426cb5cc5401fa2e8f7aaa6bc0955aff0931
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Armazenamento Standard econômico e discos de VM do Azure gerenciados e não gerenciados

O Armazenamento Standard do Azure oferece suporte de disco confiável e de baixo custo para VMs que executam cargas de trabalho insensíveis a latência. Ele também oferece suporte a tabelas, blobs, filas e arquivos. Com o Armazenamento Standard, os dados são armazenados em unidades de disco rígido (HDDs - hard disk drives). Ao trabalhar com VMs, você pode usar discos de armazenamento padrão para cenários de desenvolvimento/teste e cargas de trabalho menos críticas e discos de armazenamento premium para aplicativos de produção de missão crítica. O Armazenamento Standard está disponível em todas as regiões do Azure por Standard. 

Este artigo se concentra no uso de armazenamento padrão para discos de VM. Para obter mais informações sobre o uso de armazenamento com tabelas, blobs, filas e arquivos, consulte [Introdução ao armazenamento](../storage-introduction.md).

## <a name="disk-types"></a>Tipos de disco

Há duas maneiras de criar discos padrão para VMs do Azure:

**Discos não gerenciados**: esse é o método original onde você gerencia as contas de armazenamento usadas para armazenar os arquivos VHD que correspondem aos discos de VM. Os arquivos VHD são armazenados como blobs de páginas nas contas de armazenamento. Discos não gerenciados podem ser anexados a qualquer tamanho de VM do Azure, incluindo as VMs que usam principalmente o armazenamento Premium, como a série DSv2 e GS. As VMs do Azure suportam anexar vários discos padrão, permitindo que até 256 TB de armazenamento por VM.

[**Azure Managed Disks**](../../virtual-machines/windows/managed-disks-overview.md): esse recurso gerencia as contas de armazenamento usadas para os discos de VM para você. Você só precisa especificar o tipo (Premium ou Standard) e o tamanho do disco que você precisa, e o Azure criará e gerenciará o disco para você. Você não precisa se preocupar com o local em que colocará os discos em várias contas de armazenamento para garantir que fique dentro dos limites de escalabilidade das contas de armazenamento – o Azure cuida disso para você.

Embora os dois tipos de discos estejam disponíveis, é recomendável usar o Managed Disks para aproveitar seus muitos recursos.

Para começar a usar o Armazenamento Standard do Azure, visite a página [Introdução gratuita](https://azure.microsoft.com/pricing/free-trial/). 

Para obter informações sobre como criar uma VM com Managed Disks, consulte um dos seguintes artigos.

* [Criar uma VM do Windows usando o Gerenciador de Recursos e o PowerShell](/azure/virtual-machines/windows/quick-create-powershell.md)
* [Criar uma VM Linux usando a CLI do Azure 2.0](../../virtual-machines/windows/quick-create-cli.md)

## <a name="standard-storage-features"></a>Recursos do Armazenamento Standard 

Vamos dar uma olhada em alguns dos recursos do Armazenamento Standard. Para saber mais, confira [Introdução ao Armazenamento do Azure](../storage-introduction.md).

**Armazenamento Standard**: o Armazenamento Standard do Azure oferece suporte a discos do Azure, blobs do Azure, arquivos do Azure, tabelas do Azure e filas do Azure. Para usar os serviços de Armazenamento Standard, o primeiro passo é [Criar uma conta de armazenamento do Azure](storage-create-storage-account.md#create-a-storage-account).

**Discos de armazenamento Standard:** discos de armazenamento Standard podem ser anexados a todas as VMs do Azure, incluindo as VMs da série de tamanho usadas com o armazenamento Premium, como as séries DSv2 e GS. Um disco de armazenamento padrão só pode ser anexado a uma VM. No entanto, você pode anexar um ou mais desses discos em uma VM até a contagem máxima do disco definida para aquele tamanho de VM. Na seção a seguir sobre Metas de desempenho e escalabilidade do Armazenamento Standard, descreveremos as especificações em mais detalhes. 

**Blob de páginas Standard**: blobs de páginas Standard são usados para manter discos persistentes para VMs e também podem ser acessados diretamente por meio do REST como outros tipos de Blobs do Azure. [Blobs de páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) são uma coleção de páginas de 512 bytes otimizadas para leitura aleatória e operações de gravação. 

**Replicação de armazenamento:** na maioria das regiões, dados em uma conta de armazenamento padrão podem ser replicados geograficamente ou replicados localmente em vários data centers. Os quatro tipos de replicação disponíveis são Armazenamento Localmente Redundante (LRS), Armazenamento com Redundância de Zona (ZRS), Armazenamento com Redundância Geográfica (GRS) e Armazenamento com Redundância Geográfica com Acesso de Leitura (RA-GRS). O Managed Disks no Armazenamento Standard atualmente suportam apenas o Armazenamento Localmente Redundante (LRS). Para saber mais, consulte [Replicação de armazenamento](../storage-redundancy.md).

## <a name="scalability-and-performance-targets"></a>Metas de Escalabilidade e Desempenho

Nesta seção, descreveremos todas as metas de escalabilidade e desempenho que você deve considerar ao usar o Armazenamento Standard.

### <a name="account-limits--does-not-apply-to-managed-disks"></a>Limites de conta – não se aplicam aos discos gerenciados

| **Recurso** | **Limite padrão** |
|--------------|-------------------|
| TB por conta de armazenamento  | 500 TB |
| Entrada máxima<sup>1</sup> por conta de armazenamento (regiões dos EUA) | 10 Gbps se o GRS/ZRS estiver habilitado, 20 Gbps para o LRS |
| Saída máxima<sup>1</sup> por conta de armazenamento (regiões dos EUA) | 20 Gbps se o RA-GRS/GRS/ZRS estiver habilitado, 30 Gbps para o LRS |
| Entrada máxima<sup>1</sup> por conta de armazenamento (regiões da Ásia e Europa) | 5 Gbps se o GRS/ZRS estiver habilitado, 10 Gbps para o LRS |
| Saída máxima<sup>1</sup> por conta de armazenamento (regiões da Ásia e Europa) | 10 Gbps se o RA-GRS/GRS/ZRS estiver habilitado, 15 Gbps para o LRS |
| Taxa de solicitação total (presumindo um tamanho de objeto de 1KB) por conta de armazenamento | Até 20.000 IOPS, entidades por segundo ou mensagens por segundo |

<sup>1</sup> Entrada refere-se a todos os dados (solicitações) que estão sendo enviados a uma conta de armazenamento. Saída refere-se a todos os dados (respostas) recebidos de uma conta de armazenamento.

Para saber mais, consulte [Metas de desempenho e escalabilidade do Armazenamento do Azure](../storage-scalability-targets.md).

Se as necessidades do seu aplicativo excederem as metas de escalabilidade de uma única conta de armazenamento, crie seu aplicativo para usar várias contas de armazenamento e particionar seus dados nessas contas de armazenamento. Como alternativa, você pode usar o Azure Managed Disks e o Azure gerenciará o particionamento e o posicionamento de seus dados para você.

### <a name="standard-disks-limits"></a>Limites de discos Standard

Ao contrário dos discos Premium, as operações de entrada/saída por segundo (IOPS) e a taxa de transferência (largura de banda) de discos Standard não são provisionadas. O desempenho de discos padrão varia de acordo com o tamanho da VM à qual o disco está anexado, não com o tamanho do disco. Você pode esperar alcançar até o limite de desempenho listado na tabela a seguir.

**Limites de discos Standard (gerenciados e não gerenciados)**

| **Camada de VM**            | **VM de camada básica** | **VM de camada Standard** |
|------------------------|-------------------|----------------------|
| Tamanho máximo do disco          | 4095 GB           | 4095 GB              |
| Máx. de 8 KB de IOPS por disco | Até 300         | Até 500            |
| Largura de banda máxima por disco | Até 60 MB/s     | Até 60 MB/s        |

Se a sua carga de trabalho requer suporte de disco de alto desempenho e baixa latência, considere usar o armazenamento Premium. Para conhecer mais benefícios do Armazenamento Premium, visite [Armazenamento Premium de alto desempenho e discos de VM do Azure](../storage-premium-storage.md). 

## <a name="snapshots-and-copy-blob"></a>Instantâneos e blob de cópia

Para o serviço de Armazenamento, o arquivo VHD é um blob de páginas. Você pode tirar instantâneos dos blobs de páginas e copiá-los para outro local, como uma conta de armazenamento diferente.

### <a name="unmanaged-disks"></a>Discos não gerenciados

Você pode criar [instantâneos incrementais](../../virtual-machines/windows/incremental-snapshots.md) para discos Standard não gerenciados da mesma maneira que usa os instantâneos com o Armazenamento Standard. É recomendável que você crie instantâneos e depois os copie para uma conta de armazenamento padrão com redundância geográfica padrão se o seu disco de origem estiver em uma conta de armazenamento localmente redundante. Para saber mais, consulte [Opções de redundância do Armazenamento do Azure](../storage-redundancy.md).

Se um disco estiver anexado a uma VM, determinadas operações de API não são permitidas nos discos. Por exemplo, você não pode executar uma operação de [Cópia de Blob](/rest/api/storageservices/Copy-Blob) nesse blob enquanto o disco estiver anexado a uma VM. Em vez disso, primeiro crie um instantâneo desse blob usando o método de API REST [Instantâneo de Blob](/rest/api/storageservices/Snapshot-Blob) e depois execute a [Cópia de Blob](/rest/api/storageservices/Copy-Blob) do instantâneo para copiar o disco anexado. Como alternativa, você pode desanexar o disco e, em seguida, executar as operações necessárias.

Para manter cópias com redundância geográfica dos seus instantâneos, você pode copiar instantâneos de uma conta de armazenamento com redundância local para uma conta de armazenamento padrão com redundância geográfica usando AzCopy ou Copiar Blob. Para obter mais informações, confira [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md) e [Copy Blob](/rest/api/storageservices/Copy-Blob) (Cópia de Blob).

Para obter informações detalhadas sobre como executar operações REST em blobs de páginas nas contas de armazenamento padrão, consulte [API REST de serviços de armazenamento do Azure](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

### <a name="managed-disks"></a>Discos gerenciados

Um instantâneo de um disco gerenciado é uma cópia somente leitura do disco gerenciado que é armazenado como um disco gerenciado padrão. Instantâneos incrementais não têm suporte no momento para Managed Disks, mas terão suporte no futuro.

Se um disco gerenciado estiver anexado a uma VM, determinadas operações de API não serão permitidas nos discos. Por exemplo, você não pode gerar uma assinatura de acesso compartilhado (SAS) para executar uma operação de cópia enquanto o disco estiver anexado a uma VM. Em vez disso, primeiro crie um instantâneo do disco e, em seguida, execute a cópia do instantâneo. Como alternativa, você pode desanexar o disco e, em seguida, gerar uma assinatura de acesso compartilhado (SAS) para executar a operação de cópia.

## <a name="pricing-and-billing"></a>Preços e cobrança

Ao usar o Armazenamento Standard, as seguintes considerações de cobrança se aplicam:

* Tamanho de discos/dados não gerenciados de armazenamento Standard 
* Standard Managed Disks
* Instantâneos de Armazenamento Standard
* Transferências de dados de saída
* Transações

**Tamanho de discos e dados de armazenamento não gerenciados:** para discos e outros dados não gerenciados (blobs, tabelas, filas e arquivos), você é cobrado apenas pela quantidade de espaço que estiver usando. Por exemplo, se você tiver uma VM cujo blob de páginas é provisionado como 127 GB, mas a VM usa apenas 10 GB de espaço, você será cobrado por 10 GB de espaço. Há suporte para armazenamento padrão até 8191 GB e discos padrão não gerenciados até 4095 GB. 

**Discos gerenciados:** discos gerenciados serão cobrados no tamanho provisionado. Se o seu disco for provisionado como um disco de 10 GB e você estiver usando apenas 5 GB, você ainda será cobrado pelo tamanho de provisão de 10 GB.

**Instantâneos**: os instantâneos de discos padrão serão cobrados pela capacidade adicional usada pelos instantâneos. Para saber mais sobre instantâneos, consulte [Criando um instantâneo de um Blob](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

**Transferências de dados de saída**: as [transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/) (dados saindo dos datacenters do Azure) incorrem em cobrança por uso de largura de banda.

**Transação**: o Azure cobra US $0.0036 por 100.000 transações de armazenamento padrão. As transações incluem operações de leitura e gravação para o armazenamento.

Para obter informações detalhadas sobre os preços de armazenamento Standard, Máquinas Virtuais e Managed Disks, consulte:

* [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Preços de Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Preços do Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Suporte de serviço do Backup do Azure 

O backup das máquinas virtuais com discos não gerenciados pode ser feito usando o Backup do Azure. [Mais detalhes](../../backup/backup-azure-vms-first-look-arm.md).

Você também pode usar o serviço de Backup do Azure com o Managed Disks para criar um trabalho de backup com backups baseados em tempo, fácil restauração de VM e políticas de retenção de backup. Você pode ler mais sobre isso em [Usando o serviço de backup do Azure para VMs com Managed Disks](../../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Armazenamento do Azure](../storage-introduction.md)

* [Criar uma conta de armazenamento](../storage-create-storage-account.md)

* [Visão Geral do Managed Disks](../../virtual-machines/windows/managed-disks-overview.md)

* [Criar uma VM do Windows usando o Gerenciador de Recursos e o PowerShell](/azure/virtual-machines/windows/quick-create-powershell.md)

* [Criar uma VM Linux usando a CLI do Azure 2.0](../../virtual-machines/windows/quick-create-cli.md)
