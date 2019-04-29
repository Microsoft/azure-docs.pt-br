---
title: Mover VMs do AWS do Windows para o Azure | Microsoft Docs
description: Mova uma instância do Windows de EC2 da Amazon Web Services (AWS) para uma máquina virtual do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: d6a4c5b2b6d9818dffdb1c1fee8c4c0df7cad77c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61456199"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Mover uma VM Windows do Amazon AWS (Web Services) para uma máquina virtual do Azure

Se você estiver avaliando máquinas virtuais do Azure para hospedar suas cargas de trabalho, poderá exportar uma instância existente de VM do Windows EC2 do AWS (Amazon Web Services) e carregar o VHD (disco rígido virtual) no Azure. Após o carregamento do VHD, você pode criar uma nova VM no Azure a partir do VHD. 

Este artigo aborda a movimentação de uma VM individual do AWS para o Azure. Se você quiser migrar VMs do AWS para o Azure em escala, confira [Migrar máquinas virtuais no AWS (Amazon Web Services) para o Azure com o Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Preparar a VM 
 
Você pode carregar VHDs generalizados e especializados no Azure. Cada tipo exige a preparação da VM antes de exportar do AWS. 

- **VHD Generalizado** – um VHD generalizado teve todas suas informações de conta pessoal removidas usando o Sysprep. Se você pretende criar novas VMs usando o VHD como uma imagem, você deve: 
 
    * [Preparar uma VM do Windows](prepare-for-upload-vhd-image.md).  
    * Generalizar a máquina virtual usando Sysprep.  

 
- **VHD Especializado** – um VHD especializado mantém as contas de usuário, aplicativos e outros dados de estado de sua VM original. Se você pretende usar o VHD no estado em que se encontra para criar uma nova VM, certifique-se de que as seguintes etapas sejam concluídas.  
    * [Preparar um VHD do Windows para carregar no Azure](prepare-for-upload-vhd-image.md). **Não** generalizar a VM usando o Sysprep. 
    * Remover quaisquer ferramentas e agentes de virtualização de convidado que estejam instalados na VM (ou seja, ferramentas do VMware). 
    * Verifique se a VM está configurada para obter o endereço IP e as configurações de DNS por meio de DHCP. Isso garante que o servidor obtém um endereço IP na VNet quando ele é iniciado.  


## <a name="export-and-download-the-vhd"></a>Exportar e baixar o VHD 

Exporte a instância do EC2 para um VHD em um bucket do Amazon S3. Execute as etapas no tópico do artigo de documentação da Amazon [Exportar uma instância como VM usando Importação/Exportação de VM](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) e execute o comando [create-instance-export-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) para exportar a instância do EC2 para um arquivo VHD. 

O arquivo VHD exportado é salvo no bucket do Amazon S3 especificado. A sintaxe básica para exportar o VHD está abaixo, basta substituir o texto de espaço reservado \<colchetes > com suas informações.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Após a exportação do VHD, siga as instruções em [Como baixar um objeto de um bucket S3?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) para baixar o arquivo VHD do bucket S3. 

> [!IMPORTANT]
> O AWS cobra encargos por transferência de dados para baixar o VHD. Confira [Preços do Amazon S3](https://aws.amazon.com/s3/pricing/) para saber mais.


## <a name="next-steps"></a>Próximas etapas

Agora você pode carregar o VHD no Azure e criar uma nova VM. 

- Se você executou o Sysprep em seu código-fonte para **generalizá-lo** antes de exportar, consulte [Carregar um VHD generalizado e usá-lo para criar novas VMs no Azure](upload-generalized-managed.md)
- Se você não tiver executado o Sysprep antes da exportação, o VHD será considerado **especializado**, consulte [Carregar um VHD especializado no Azure e criar uma nova VM](create-vm-specialized.md)

 
