---
title: Carregar arquivo VHD no Azure DevTest Labs usando o PowerShell | Microsoft Docs
description: Carregar arquivo VHD na conta de armazenamento do laboratório usando o PowerShell
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 56a66c3eb1dad93fad3ad1572989dc0c0aa14632
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622740"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Carregar arquivo VHD na conta de armazenamento do laboratório usando o PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

No Azure DevTest Labs, os arquivos VHD podem ser usados para criar imagens personalizadas, que são usadas para provisionar máquinas virtuais. As etapas a seguir mostrarão como usar o PowerShell para carregar um arquivo VHD em uma conta de armazenamento do laboratório. Depois de carregar seu arquivo VHD, a [seção Próximas etapas](#next-steps) lista alguns dos artigos que ilustram como criar uma imagem personalizada do arquivo VHD carregado. Para saber mais sobre os discos e VHDs do Azure, confira [Introdução aos discos gerenciados](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instruções passo a passo

As etapas a seguir mostram como carregar um arquivo VHD no Azure DevTest Labs usando o PowerShell. 

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.  

1. Na folha do laboratório, selecione **Configuração**. 

1. Na folha **Configuração** do laboratório, selecione **Imagens personalizadas (VHDs)**.

1. Na folha **Imagens personalizadas**, selecione **+Adicionar**. 

1. Na folha **Imagem personalizada**, selecione **VHD**.

1. Na folha **VHD**, selecione **Carregar um VHD usando o PowerShell**.

    ![Carregar o VHD usando o PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. Na folha **Carregar uma imagem usando o PowerShell**, copie o script do PowerShell gerado em um editor de texto.

1. Modifique o parâmetro **LocalFilePath** do cmdlet **Add-AzureVhd** para apontar para o local do arquivo VHD que deseja carregar.

1. Em um prompt do PowerShell, execute o cmdlet **Add-AzureVhd** (com o parâmetro **LocalFilePath** modificado).

> [!WARNING] 
> 
> O processo de carregamento de um arquivo VHD pode ser demorado, dependendo do tamanho do arquivo VHD e da velocidade de conexão.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o Portal do Azure](devtest-lab-create-template.md)
- [Criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
