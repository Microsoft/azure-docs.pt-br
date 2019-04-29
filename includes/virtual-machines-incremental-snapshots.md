---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 06e6e491fa1e9a047527efb78149855b125771ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543749"
---
# <a name="back-up-azure-unmanaged-vm-disks-with-incremental-snapshots"></a>Faça backup dos discos não gerenciados de VM do Azure com instantâneos incrementais
## <a name="overview"></a>Visão geral
O Armazenamento do Azure oferece a capacidade de fazer instantâneos dos blobs. Os instantâneos capturam o estado do blob no momento em questão. Neste artigo, descrevemos um cenário no qual você pode manter backups dos discos de máquinas virtuais usando instantâneos. Você pode usar essa metodologia quando optar por não usar o Serviço de Backup e Recuperação do Azure e desejar criar uma estratégia de backup personalizada para seus discos da máquina virtual.

Os discos de máquinas virtuais do Azure são armazenados como blobs de página no Armazenamento do Azure. Como estamos descrevendo uma estratégia de backup para discos de máquina virtual neste artigo, faremos referência aos instantâneos no contexto dos blobs de página. Para saber mais sobre instantâneos, consulte [Criando um instantâneo de um Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>O que é um instantâneo?
Um instantâneo de blob é uma versão somente leitura de um blob capturada em um dado momento. Quando um instantâneo tiver sido criado, ele pode ser lido, copiado ou excluído, mas não modificado. Os instantâneos fornecem uma maneira de fazer backup de um blob da maneira como ele aparece em um momento específico. Até a versão 2015-04-05 do REST você podia copiar instantâneos completos. Com a versão 2015-07-08 do REST e as versões superiores, você pode copiar instantâneos incrementais.

## <a name="full-snapshot-copy"></a>Cópia de instantâneo completo
Instantâneos podem ser copiados para outra conta de armazenamento como um blob para manter backups do blob de base. Você também pode copiar um instantâneo sobre seu blob de base, que é semelhante a restaurar o blob para uma versão anterior. Quando um instantâneo é copiado de uma conta de armazenamento para outra, ele ocupa o mesmo espaço que o blob de páginas de base. Portanto, copiar instantâneos inteiros de uma conta de armazenamento para outra é um processo lento que consume muito espaço na conta de armazenamento de destino.

> [!NOTE]
> Se você copiar o blob de base para outro destino, os instantâneos do blob não serão copiados com ele. Da mesma forma, se você substituir um blob de base por uma cópia, os instantâneos associados ao blob de base não serão afetados e permanecerão intactos com o nome do blob de base.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Fazer backup de discos usando instantâneos
Como estratégia de backup para seus discos de máquina virtual, você pode fazer instantâneos periódicos do blob de páginas ou de disco e copiá-los para outra conta de armazenamento usando ferramentas como a operação [Copiar Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) ou [AzCopy](../articles/storage/common/storage-use-azcopy.md). Você pode copiar um instantâneo para um blob de páginas de destino com um nome diferente. O blob de páginas de destino resultante é um blob de páginas gravável, e não um instantâneo. Mais adiante neste artigo, descreveremos as etapas para fazer backups dos discos de máquinas virtuais usando instantâneos.

### <a name="restore-disks-using-snapshots"></a>Restaurar discos usando instantâneos
Quando for o momento de restaurar o disco para uma versão estável que foi anteriormente capturada em um dos instantâneos de backup, você pode copiar um instantâneo sobre o blob de páginas de base. Após ser promovido a blob de páginas de base, o instantâneo permanece, mas sua origem é substituída por uma cópia que pode ser lida e gravada. Mais adiante neste artigo, descreveremos as etapas para restaurar uma versão anterior do disco por meio do instantâneo.

### <a name="implementing-full-snapshot-copy"></a>Implementando uma cópia completa do instantâneo
Você pode implementar uma cópia completa do instantâneo fazendo o seguinte

* Primeiro, faça um instantâneo do blob de base usando a operação de [Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) .
* Em seguida, copie o instantâneo para uma conta de armazenamento de destino usando [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Repita esse processo para manter cópias de backup de seu blob de base.

## <a name="incremental-snapshot-copy"></a>Cópia de instantâneo incremental
O novo recurso na API [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) é uma maneira muito melhor para fazer backup de instantâneos de seus blobs de página ou discos. A API retorna a lista de alterações entre o blob de base e os instantâneos, o que reduz a quantidade de espaço de armazenamento usada na conta de backup. A API dá suporte a blobs de página no Armazenamento Premium e no Armazenamento Standard. Com essa API, você pode criar soluções de backup mais rápidas e eficientes para VMs do Azure. Essa API será disponibilizada com o REST versão 2015-07-08 ou superior.

A Cópia de instantâneo incremental permite copiar de uma conta de armazenamento para outra a diferença entre:

* o blob de base e seu instantâneo OU
* quaisquer dois instantâneos do blob de base

Se as condições a seguir forem atendidas,

* O blob foi criado em 1º de janeiro de 2016 ou posteriormente.
* O blob não foi substituído por [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) ou [Copiar Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) entre dois instantâneos.

**Observação**: Esse recurso está disponível para Blobs de páginas do Azure Standard e Premium.

Quando você tem uma estratégia de backup personalizada usando instantâneos, copiar os instantâneos de uma conta de armazenamento para outra pode ser um processo lento que consome muito espaço de armazenamento. Em vez de copiar todo o instantâneo para uma conta de armazenamento de backup, você pode gravar a diferença em um blob de páginas de backup. Dessa forma, o tempo para copiar e o espaço para armazenar backups são reduzidos substancialmente.

### <a name="implementing-incremental-snapshot-copy"></a>Implementando a Cópia de instantâneo incremental
Você pode implementar a cópia de instantâneo incremental fazendo o seguinte

* Faça um instantâneo do blob de base usando [Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* Copie o instantâneo para a conta de armazenamento de backup de destino na mesma ou em qualquer outra região do Azure usando [Copiar Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Esse é o blob de páginas de backup. Tire um instantâneo do blob de páginas de backup e armazene-o na conta de backup.
* Faça outro instantâneo do blob de base usando Blob de Instantâneo.
* Obtenha a diferença entre o primeiro e o segundo instantâneo do blob de base usando [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Use o novo parâmetro **prevsnapshot** para especificar o valor de DateTime do instantâneo do qual você deseja obter a diferença. Quando esse parâmetro estiver presente, a resposta REST inclui apenas as páginas que foram alteradas entre o instantâneo de destino e o instantâneo anterior, incluindo páginas em branco.
* Use [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) para aplicar essas alterações ao blob de páginas de backup.
* Por fim, tire um instantâneo do blob de páginas de backup e armazene-o na conta de armazenamento de backup.

Na próxima seção, descreveremos com mais detalhes como você pode manter backups de discos usando a Cópia de instantâneo incremental

## <a name="scenario"></a>Cenário
Nesta seção, descrevemos um cenário que envolve uma estratégia de backup personalizada para discos de máquina virtual usando instantâneos.

Considere uma VM do Azure da série DS com um disco P30 de armazenamento premium anexado. O disco P30 chamado *mypremiumdisk* é armazenado em uma conta de armazenamento premium chamada *mypremiumaccount*. Uma conta de armazenamento standard chamada *mybackupstdaccount* é usada para armazenar o backup do *mypremiumdisk*. Gostaríamos de manter um instantâneo de *mypremiumdisk* a cada 12 horas.

Para saber mais sobre como criar uma conta de armazenamento, consulte [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

Para saber mais sobre como fazer backup de VMs do Azure, consulte [Planejar backups de VMs do Azure](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Etapas para manter backups de um disco usando instantâneos incrementais
As etapas descritas a seguir descrevem como tirar instantâneos do *mypremiumdisk* e manter os backups em *mybackupstdaccount*. O backup será um blob de páginas padrão chamado *mybackupstdpageblob*. O blob de páginas de backup sempre reflete o mesmo estado que o último instantâneo de *mypremiumdisk*.

1. Crie o blob de páginas de backup para o disco de armazenamento premium, tirando um instantâneo de *mypremiumdisk* chamado *mypremiumdisk_ss1*.
2. Copie esse instantâneo para mybackupstdaccount como um blob de páginas chamado *mybackupstdpageblob*.
3. Faça um instantâneo de *mybackupstdpageblob* chamado *mybackupstdpageblob_ss1* usando [Blob de Instantâneo](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) e armazene-o em *mybackupstdaccount*.
4. Durante a janela de backup, crie outro instantâneo de *mypremiumdisk*, digamos que *mypremiumdisk_ss2* e armazene-o em *mypremiumaccount*.
5. Obtenha as alterações incrementais entre os dois instantâneos, *mypremiumdisk_ss2* e *mypremiumdisk_ss1*, usando [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) na *mypremiumdisk_ss2* com o parâmetro **prevsnapshot** definido como o carimbo de data/hora de *mypremiumdisk_ss1*. Grave essas alterações incrementais do blob de páginas de backup *mybackupstdpageblob* em *mybackupstdaccount*. Se houver intervalos excluídos nas alterações incrementais, eles deverão ser excluídos do blob de páginas de backup. Use [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) para gravar as alterações incrementais no blob de páginas de backup.
6. Faça um instantâneo do blob de páginas de backup *mybackupstdpageblob*, denominado *mybackupstdpageblob_ss2*. Exclua o instantâneo anterior *mypremiumdisk_ss1* da conta de armazenamento premium.
7. Repita as etapas 4 a 6 em cada janela de backup. Dessa forma, você pode manter backups de *mypremiumdisk* em uma conta de armazenamento padrão.

![Fazer backup de disco usando instantâneos incrementais](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Etapas para restaurar um disco por meio de instantâneos
As etapas a seguir descrevem como restaurar o disco premium *mypremiumdisk* para um instantâneo anterior da conta de armazenamento de backup *mybackupstdaccount*.

1. Identifique o ponto para o qual você deseja restaurar o disco premium. Digamos que seja o instantâneo *mybackupstdpageblob_ss2*, que está armazenado na conta de armazenamento de backup *mybackupstdaccount*.
2. Em mybackupstdaccount, promova o instantâneo *mybackupstdpageblob_ss2* como o novo blob de páginas de base de backup *mybackupstdpageblobrestored*.
3. Faça um instantâneo do blob de páginas de backup denominado *mybackupstdpageblobrestored_ss1*.
4. Copie o blob de páginas restauradas *mybackupstdpageblobrestored* de *mybackupstdaccount* para *mypremiumaccount* como o novo disco *premium mypremiumdiskrestored*.
5. Tirar um instantâneo do *mypremiumdiskrestored*, chamado *mypremiumdiskrestored_ss1* para fazer backups incrementais futuros.
6. Aponte a VM da série DS para o disco restaurado *mypremiumdiskrestored* e desanexe o antigo *mypremiumdisk da VM*.
7. Inicie o processo de backup descrito na seção anterior para o disco restaurado *mypremiumdiskrestored* usando *mybackupstdpageblobrestored* como o blob de páginas de backup.

![Restaurar disco por meio de instantâneos](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Próximas etapas
Use os links a seguir para saber mais sobre como criar instantâneos de um blob e planejar a infraestrutura de backup da VM.

* [Criando um instantâneo de um Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Planejar sua Infraestrutura de backup de VM](../articles/backup/backup-azure-vms-introduction.md)

