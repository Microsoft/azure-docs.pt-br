---
title: Erro do Gerenciador de inicialização do Windows-hash de imagem inválido do status 0xC0000428
titlesuffix: Azure Virtual Machines
description: Este artigo fornece etapas para resolver problemas em que uma imagem de visualização foi usada e o período de avaliação expirou, o que impede a inicialização de uma VM (máquina virtual) do Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 26c411f9-6c22-4f45-a445-c5781e547828
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: v-miegge
ms.openlocfilehash: 22db9c7966b6f988ca0ea799104275f3f86c77ea
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629565"
---
# <a name="windows-boot-manager-error---0xc0000428-status-invalid-image-hash"></a>Erro do Gerenciador de inicialização do Windows-hash de imagem inválido do status 0xC0000428

Este artigo fornece etapas para resolver problemas em que uma imagem de visualização foi usada e o período de avaliação expirou, o que impede a inicialização de uma VM (máquina virtual) do Azure.

## <a name="symptom"></a>Sintoma

Quando você usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, verá que a captura de tela exibe o Gerenciador de inicialização do Windows com a mensagem:

  `File: \windows\system32\boot\winload.exe`

  `Status: 0xc0000428`

  `Info: Windows cannot verify the digital signature for this file.`

  ![A Figura 1 exibe o Gerenciador de inicialização do Windows com o status "Ox0000428" e info "o Windows não pode verificar a assinatura digital para este arquivo".](./media/windows-boot-error-invalid-image-hash/1-cannot-verify-signature.png)

ou a mensagem:

  `File: \Windows\system32\winload.exe`

  `Status: 0xc0000428`

  `Info: The digital signature for this file couldn't be verified.`
    
  ![A Figura 2 exibe o Gerenciador de inicialização do Windows com o status "Ox0000428" e informações "a assinatura digital para este arquivo não pôde ser verificada".](./media/windows-boot-error-invalid-image-hash/2-digital-signature-not-verified.png)

## <a name="cause"></a>Causa

A imagem que foi usada para criar a VM era uma imagem de visualização com uma data de expiração em vez de uma imagem RTM (liberação para fabricação). 

As imagens de visualização têm um ciclo de vida designado e a captura de tela que você vê é exibida quando você passa a data de expiração, o que significa que a avaliação da imagem está acima.

### <a name="example-of-preview-images"></a>Exemplo de imagens de visualização

`a699494373c04fc0bc8f2bb1389d6106__Windows-Server-Technical-Preview-201505.01-en.us-127GB.vhd`

Você não pode estender a data de validade de uma imagem de versão prévia. Quando a versão prévia tiver expirado, a VM não poderá mais ser inicializada.

- Dependendo do produto, o período de avaliação pode variar. Por exemplo, as imagens da versão prévia do Windows têm um período de avaliação de 180 dias.

- No Azure, todas as imagens para Windows que são versões de visualização incluirão uma observação em sua descrição de que elas não são destinadas à produção e que estão disponíveis para uso apenas para um período de avaliação especificado ou como uma "versão de visualização".

## <a name="solution"></a>Solução

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

Se a imagem for uma imagem de visualização, não será possível estender a data de expiração da imagem usada, você precisará [implantar uma nova VM](../windows/quick-create-portal.md) usando uma imagem que não seja de visualização. As etapas a seguir ajudarão você a identificar se você usou uma imagem de visualização, bem como fornecer recursos para ajudá-lo a transferir dados dessa VM para uma nova VM. Se você identificou positivamente a imagem como uma imagem de visualização, a imagem não será recuperável, pois agora ela expirou.

Dependendo da sua preferência, você pode usar Azure PowerShell ou CLI do Azure para consultar sua imagem para determinar se ela é uma imagem de visualização. Você pode usar esses comandos para confirmar que a imagem é uma imagem de visualização.

### <a name="query-using-azure-powershell"></a>Consultar usando Azure PowerShell

1. Abra o aplicativo do Windows PowerShell.
1. Execute os seguintes comandos:

   ```powershell
   $locName = "<LOCATION>" 
   $pubName = "<PUBLISHER NAME>" 
   $offerName = "<OFFER NAME>" 
   $skuName= "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   Get-AzVMImagePublisher -Location $locName | Select $pubName
   Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
   Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
   Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
   ```

- Nos comandos anteriores, substitua `<LOCATION>` ,, `<PUBLISHER NAME>` `<OFFER NAME>` e `<YEAR WHEN THIS IMAGE WAS RELEASED>` pelas informações declaradas. Além disso, remova os símbolos "<" e ">".

  Consulte o seguinte exemplo:

  ```powershell
  $locName = "West US" 
  $pubName = "MicrosoftWindowsServer" 
  $offerName = "WindowsServer" 
  $skuName= "2016-Datacenter"
  Get-AzVMImagePublisher -Location $locName | Select $pubName
  Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
  Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
  Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
  ```

### <a name="query-using-the-azure-cli"></a>Consultar usando o CLI do Azure

1. Se ainda não tiver feito isso, será necessário [instalar o CLI do Azure](/cli/azure/install-azure-cli).
1. Depois de baixado, use o prompt de comando ou o PowerShell para inserir o `az login` comando e, em seguida, entre com suas credenciais de conta.
1. Depois de conectado, insira os seguintes comandos:

   ```powershell
   az vm image list-publishers --location "<LOCATION>"
   az vm image list-offers --location "West US" --publisher "<PUBLISHER NAME>"
   az vm image list-skus --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>"
   az vm image list  --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>" --sku "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   ```

- Nos comandos anteriores, substitua `<LOCATION>` ,, `<PUBLISHER NAME>` `<OFFER NAME>` e `<YEAR WHEN THIS IMAGE WAS RELEASED>` pelas informações declaradas. Além disso, remova os símbolos "<" e ">".

  Consulte o seguinte exemplo:

  ```powershell
  az vm image list-publishers --location "West US"
  az vm image list-offers --location "West US" --publisher "MicrosoftWindowsServer"
  az vm image list-skus --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer"
  az vm image list  --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer" --sku "2016-Datacenter"
  ```