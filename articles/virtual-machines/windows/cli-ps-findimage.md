---
title: Localizar e usar planos e imagens do Azure Marketplace
description: Use Azure PowerShell para localizar e usar as informações de editor, oferta, SKU, versão e plano para imagens de VM do Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 45e6b157dba5ef7410d8a5c0223fd3ecb52f39d0
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906260"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Localizar e usar imagens de VM do Azure Marketplace com Azure PowerShell     

Este tópico descreve como usar o Azure PowerShell para localizar imagens de VM no Azure Marketplace. Em seguida, você pode especificar uma imagem do Marketplace e planejar informações ao criar uma VM.

Você também pode procurar imagens e ofertas disponíveis usando a vitrine do [Azure Marketplace](https://azuremarketplace.microsoft.com/), o [portal do Azure](https://portal.azure.com) ou a [CLI do Azure](../linux/cli-ps-findimage.md). 


[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]


## <a name="create-a-vm-from-vhd-with-plan-information"></a>Criar uma VM do VHD com informações do plano

Se você tiver um VHD existente que foi criado usando uma imagem do Azure Marketplace, talvez seja necessário fornecer as informações do plano de compra ao criar uma nova VM a partir desse VHD.

Se você ainda tiver a VM original ou outra VM criada na mesma imagem, poderá obter o nome do plano, o editor e as informações do produto usando Get-AzVM. Este exemplo obtém uma VM chamada *myVM* no grupo de recursos *MyResource* Group e, em seguida, exibe as informações do plano de compra.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

Se você não obtiver as informações do plano antes da exclusão da VM original, poderá arquivar uma [solicitação de suporte](https://ms.portal.azure.com/#create/Microsoft.Support). Eles precisarão do nome da VM, da ID da assinatura e do carimbo de data/hora da operação de exclusão.

Para criar uma VM usando um VHD, consulte este artigo [criar uma VM de um VHD especializado](create-vm-specialized.md) e adicionar uma linha para adicionar as informações do plano à configuração da VM usando [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) semelhante ao seguinte:

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```

## <a name="create-a-new-vm-from-a-marketplace-image"></a>Criar uma nova VM com base em uma imagem do Marketplace

Se você já tiver as informações sobre qual imagem deseja usar, poderá passar essas informações para o cmdlet [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) para adicionar informações de imagem à configuração da VM. Consulte as próximas seções para pesquisar e listar as imagens disponíveis no Marketplace.

Algumas imagens pagas também exigem que você forneça informações do plano de compra usando o [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan). 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

Em seguida, você passará a configuração da VM junto com os outros objetos de configuração para o `New-AzVM` cmdlet. Para obter um exemplo detalhado de como usar uma configuração de VM com o PowerShell, consulte este [script](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1).

Se você receber uma mensagem sobre como aceitar os termos da imagem, consulte a seção [aceitar os termos](#accept-the-terms) mais adiante neste artigo.

## <a name="list-images"></a>Listar imagens

Uma maneira de localizar uma imagem em um local é executar os cmdlets [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher), [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) e [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) na ordem:

1. Liste os editores de imagem.
2. Para um determinado editor, liste suas ofertas.
3. Para uma determinada oferta, liste seus SKUs.

Em seguida, para uma SKU escolhida, execute [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage) para listar as versões para implantar.

1. Lista os editores:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Preencha o nome do editor escolhido e liste as ofertas:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Preencha o nome da oferta escolhida e listar os SKUs:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Preencha o nome da SKU escolhida e obter a versão da imagem:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Na saída do comando `Get-AzVMImage`, você pode selecionar uma imagem de versão para implantar uma nova máquina virtual.

O exemplo a seguir mostra a sequência completa de comandos e suas saídas:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

Resultado parcial:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

Para o editor de *MicrosoftWindowsServer* :

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Saída:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Para a oferta de *WindowsServer*:

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

Resultado parcial:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

Em seguida, para a SKU *2019-Datacenter*:

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Agora você pode combinar o editor, a oferta, a SKU e a versão selecionados em um URN (valores separados por :). Passe esse URN com o parâmetro `--image` quando criar uma VM com o cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm). Opcionalmente, você pode substituir o número da versão no URN por "latest" para obter a versão mais recente da imagem.

Se você implantar uma VM com um modelo do Resource Manager, definirá os parâmetros da imagem individualmente nas propriedades `imageReference`. Consulte a [referência de modelo](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Exibir propriedades de plano

Para exibir as informações do plano de compra de uma imagem, execute o cmdlet `Get-AzVMImage`. Se a propriedade `PurchasePlan` na saída não for `null`, isso significa que a imagem tem termos que você precisa aceitar antes da implantação programática.  

Por exemplo, a imagem do *Windows Server 2016 Datacenter* não tem termos adicionais, portanto, as informações de `PurchasePlan`são`null`:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Saída:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

O exemplo a seguir mostra um comando semelhante para a imagem *máquina virtual de ciência de dados-Windows 2016* , que tem as seguintes `PurchasePlan` Propriedades: `name` , `product` e `publisher` . Algumas imagens também têm um `promotion code` propriedade. Para implantar essa imagem, consulte as seções a seguir para aceitar os termos e ativar a implantação programática.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Saída:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Aceitar os termos

Para exibir os termos da licença, use o cmdlet [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) e passe os parâmetros do plano de compra. A saída fornece um link para os termos da imagem do Marketplace e mostra se você aceitou os termos anteriormente. Certifique-se de usar todas as letras minúsculas nos valores de parâmetros.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Saída:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Use o cmlet [Set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) para aceitar ou rejeitar os termos. Você só precisa aceitar os termos uma vez por assinatura para a imagem. Certifique-se de usar todas as letras minúsculas nos valores de parâmetros. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Saída:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```



## <a name="next-steps"></a>Próximas etapas

Para criar uma máquina virtual rapidamente com o cmdlet `New-AzVM` usando informações básicas de imagem, consulte [Criar uma máquina virtual do Windows com o PowerShell](quick-create-powershell.md).

Para obter mais informações sobre como usar imagens do Azure Marketplace para criar imagens personalizadas em uma galeria de imagens compartilhadas, consulte [fornecer informações do plano de compra do Azure Marketplace ao criar imagens](../marketplace-images.md).
