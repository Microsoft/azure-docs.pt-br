---
title: Carregar arquivo VHD no Azure DevTest Labs usando o AzCopy | Microsoft Docs
description: Carregar arquivo VHD na conta de armazenamento do laboratório usando o AzCopy
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
ms.openlocfilehash: 8cd778762bebf4a9dda3688292ac0a3674e446e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634960"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Carregar arquivo VHD na conta de armazenamento do laboratório usando o AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

No Azure DevTest Labs, os arquivos VHD podem ser usados para criar imagens personalizadas, que são usadas para provisionar máquinas virtuais. As etapas a seguir mostrarão como usar o utilitário de linha de comando AzCopy para carregar um arquivo VHD em uma conta de armazenamento do laboratório. Depois de carregar seu arquivo VHD, a [seção Próximas etapas](#next-steps) lista alguns dos artigos que ilustram como criar uma imagem personalizada do arquivo VHD carregado. Para saber mais sobre os discos e VHDs do Azure, confira [Introdução aos discos gerenciados](../virtual-machines/linux/managed-disks-overview.md)

> [!NOTE] 
>  
> O AzCopy é um utilitário de linha de comando somente para Windows.

## <a name="step-by-step-instructions"></a>Instruções passo a passo

As etapas a seguir mostram como carregar um arquivo VHD no Azure DevTest Labs usando o [AzCopy](https://aka.ms/downloadazcopy). 

1. Obtenha o nome da conta de armazenamento do laboratório usando o Portal do Azure:

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.  

1. Na folha do laboratório, selecione **Configuração**. 

1. Na folha **Configuração** do laboratório, selecione **Imagens personalizadas (VHDs)**.

1. Na folha **Imagens personalizadas**, selecione **+Adicionar**. 

1. Na folha **Imagem personalizada**, selecione **VHD**.

1. Na folha **VHD**, selecione **Carregar um VHD usando o PowerShell**.

    ![Carregar o VHD usando o PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. A folha **Carregar uma imagem usando o PowerShell** exibe uma chamada ao cmdlet **Add-AzureVhd**. O primeiro parâmetro (*Destino*) contém o URI do contêiner de blob (*carregamentos*) no seguinte formato:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Anote o URI completo, pois ele será usado em etapas posteriores.

1. Carregar o arquivo VHD usando AzCopy:
 
1. [Baixe e instale a versão mais recente do AzCopy](https://aka.ms/downloadazcopy).

1. Abra uma janela de comando e navegue até o diretório de instalação do AzCopy. Se quiser, você pode alterar o local da instalação do AzCopy para o caminho do sistema. Por padrão, o AzCopy é instalado no seguinte diretório:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Usando a chave da conta de armazenamento e o URI do contêiner de blob, execute o seguinte comando no prompt de comando. O valor *vhdFileName* precisa estar entre aspas. O processo de carregamento de um arquivo VHD pode ser demorado, dependendo do tamanho do arquivo VHD e da velocidade de conexão.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Próximas etapas

- [Criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o Portal do Azure](devtest-lab-create-template.md)
- [Criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)