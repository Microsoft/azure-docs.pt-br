---
title: "Criar uma imagem de máquina virtual local para o Azure Marketplace | Microsoft Docs"
description: Entenda e execute as etapas para criar uma imagem de VM local e implantar no Azure Marketplace para outras pessoas comprarem.
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 26dfbd5a-8685-4b19-987e-c20ca60540ec
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: 8f6b9a9293dc149586e6e5fd55028170ea825b07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="develop-an-on-premises-virtual-machine-image-for-the-azure-marketplace"></a>Desenvolver uma imagem de máquina virtual local para o Azure Marketplace
É altamente recomendável que você desenvolva discos rígidos virtuais (VHDs) do Azure diretamente na nuvem usando o protocolo RDP. No entanto, se for necessário, é possível baixar um VHD e desenvolvê-lo usando a infraestrutura local.  

Para o desenvolvimento local, você deve baixar o VHD do sistema operacional da VM criada. Essas etapas podem ocorrer como parte da etapa 3.3 acima.  

## <a name="download-a-vhd-image"></a>Baixar uma imagem VHD
### <a name="locate-a-blob-url"></a>Localize uma URL para Blobs
Para baixar o VHD, primeiro localize a URL para Blobs do disco do sistema operacional.

Localize a URL para Blobs do novo [Portal do Microsoft Azure](https://portal.azure.com):

1. Vá para **Procurar** > **VMs** e selecione a VM implantada.
2. Em **Configurar**, selecione o bloco **Discos**, que abre a folha Discos.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. Selecione o **Disco do SO**, que abre outra folha que exibe as propriedades do disco, inclusive o local do VHD.
4. Copie a URL do blob.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. Agora, exclua a VM implantada sem excluir os discos de backup. Você também pode interromper a VM, em vez de excluí-la. Não baixe o VHD do sistema operacional quando a VM estiver em execução.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### <a name="download-a-vhd"></a>Baixe um VHD
Depois de conhecer a URL para Blobs, você pode baixar o VHD usando o [Portal do Azure](http://manage.windowsazure.com/) ou o PowerShell.  

> [!NOTE]
> No momento da criação deste guia, a funcionalidade para baixar um VHD ainda não estava presente no novo Portal do Microsoft Azure.  
> 
> 

**Baixar o VHD do sistema operacional pelo atual [Portal do Azure](http://manage.windowsazure.com/)**

1. Entre no Portal do Azure, se você ainda não tiver feito isso.
2. Clique na guia **Armazenamento** .
3. Selecione a conta de armazenamento na qual o VHD está armazenado.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. Isso exibe as propriedades da conta de armazenamento. Selecione a guia **Contêineres** .
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. Selecione o contêiner no qual o VHD está armazenado. Por padrão, quando criado a partir do portal, o VHD é armazenado em um contêiner de vhds.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. Selecione o VHD do sistema operacional correto comparando a URL com aquela que você salvou.
7. Clique em **Download**.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### <a name="download-a-vhd-by-using-powershell"></a>Baixar um VHD usando o PowerShell
Além de usar o Portal do Azure, você pode usar o cmdlet [Save-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) para baixar o VHD do sistema operacional.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
Por exemplo, Save-AzureVhd -Source “https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\baseimagevm.vhd” -StorageKey <String>

> [!NOTE]
> O **Save-AzureVhd** também tem uma opção de **NumberOfThreads** que pode ser usada para aumentar o paralelismo para fazer o melhor uso da largura de banda disponível para download.
> 
> 

## <a name="upload-vhds-to-an-azure-storage-account"></a>Carregar VHDs para uma conta de armazenamento do Azure
Se você preparou seus VHDs no local, precisa carregá-los em uma conta de armazenamento no Azure. Esta etapa ocorre depois de criar o VHD local, mas antes de obter a certificação para a imagem da VM.

### <a name="create-a-storage-account-and-container"></a>Criar uma conta e um contêiner de armazenamento
É recomendável carregar os VHDs em uma conta de armazenamento em uma região dos Estados Unidos. Todos os VHDs para uma única SKU devem ser colocados em um único contêiner dentro de uma única conta de armazenamento.

Para criar uma conta de armazenamento, você pode usar o [Portal do Microsoft Azure](https://portal.azure.com/), o PowerShell ou a ferramenta de linha de comando do Linux.  

**Criar uma conta de armazenamento a partir do Portal do Microsoft Azure**

1. Clique em **Novo**.
2. Selecione **Armazenamento**.
3. Preencha o nome da conta de armazenamento e selecione um local.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. Clique em **Criar**.
5. A folha da conta de armazenamento criada deve estar aberta. Caso contrário, selecione **Procurar** > **Contas de Armazenamento**. Na folha Conta de Armazenamento, selecione a conta de armazenamento criada.
6. Selecione **Contêineres**.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img09.png) 
7. Na folha Contêineres, selecione **Adicionar**e insira um nome de contêiner e as permissões do contêiner. Selecione **Particular** nas permissões do contêiner.

> [!TIP]
> É recomendável que você crie um contêiner para cada SKU que pretende publicar.
> 
> 

  ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### <a name="create-a-storage-account-by-using-powershell"></a>Criar uma conta de armazenamento usando o PowerShell
Usando o PowerShell, crie uma conta de armazenamento usando o cmdlet [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx) .

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Depois você pode criar um contêiner nessa conta de armazenamento usando o cmdlet [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) .

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> Esses comandos pressupõem que o contexto de conta de armazenamento atual já foi definido no PowerShell.   Consulte [Configurar o Azure PowerShell](marketplace-publishing-powershell-setup.md) para obter mais detalhes sobre a configuração do PowerShell.  
> 
> ### <a name="create-a-storage-account-by-using-the-command-line-tool-for-mac-and-linux"></a>Criar uma conta de armazenamento usando a ferramenta de linha de comando para Mac e Linux
> A partir da [ferramenta de linha de comando do Linux](../virtual-machines/linux/cli-manage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), crie uma conta de armazenamento da seguinte maneira.
> 
> 

        azure storage account create mystorageaccount --location "West US"

Crie um contêiner da seguinte maneira.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## <a name="upload-a-vhd"></a>Carregar um VHD
Depois de criar a conta de armazenamento e o contêiner, você pode carregar seus VHDs preparados. Você pode usar o PowerShell, a ferramenta de linha de comando do Linux ou outras ferramentas de gerenciamento de armazenamento do Azure.

### <a name="upload-a-vhd-via-powershell"></a>Carregar um VHD por meio do PowerShell
Use o cmdlet [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) .

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### <a name="upload-a-vhd-by-using-the-command-line-tool-for-mac-and-linux"></a>Carregar um VHD usando a ferramenta de linha de comando para Mac e Linux
Com a [ferramenta de linha de comando do Linux](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2), use o seguinte: azure vm image create <image name> --location <Location of the data center> --OS Linux <LocationOfLocalVHD>

## <a name="see-also"></a>Confira também
* [Criar uma imagem de máquina virtual para o Marketplace](marketplace-publishing-vm-image-creation.md)
* [Configurando o PowerShell do Azure](marketplace-publishing-powershell-setup.md)

