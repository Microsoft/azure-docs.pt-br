---
title: Criar uma imagem de VM de usuário para o Azure Marketplace | Microsoft Docs
description: Lista as etapas e as referências necessárias para criar uma imagem de VM de usuário.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: bf87856dc28e83fb1308f20613338b9bbfd8f896
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744101"
---
# <a name="create-a-user-vm-image"></a>Criar uma imagem VM de usuário

Este artigo explica as duas etapas gerais necessárias para criar uma imagem não gerenciada a partir de um VHD generalizado.  Há referências para orientar em cada etapa: capturar a imagem e generalizar a imagem.


## <a name="capture-the-vm-image"></a>Captura da imagem da VM

Use as instruções no artigo a seguir sobre como capturar a VM que corresponde à sua abordagem de acesso:

-  PowerShell: [Como criar uma imagem de VM não gerenciada a partir de uma VM do Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  CLI do Azure: [Como criar uma imagem de máquina virtual ou um VHD](../../../virtual-machines/linux/capture-image.md)
-  API: [Máquinas Virtuais – Capturar](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Generalizar a imagem VM

Porque você gerou a imagem de usuário de um VHD generalizado anteriormente, a imagem também deve ser generalizada.  Novamente, selecione o artigo a seguir que corresponde ao seu mecanismo de acesso.  (você pode ter já generalizado seu disco quando você o capturou)

-  PowerShell: [generalizar a VM](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  CLI do Azure: [Etapa 2: criar a imagem de VM](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Máquinas Virtuais – Generalizar](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Próximas etapas

Em seguida, você irá [criar um certificado](cpp-create-key-vault-cert.md) e armazená-lo em um novo Azure Key Vault.  É necessário um certificado para estabelecer uma conexão segura do WinRM com a VM.
