---
title: Como implantar o Windows 10 no Azure com direitos de hospedagem multilocatário
description: Saiba como maximizar os benefícios do Windows Software Assurance para colocar as licenças locais no Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 7f43528c55cd22c2649ca0f1208da6f41695b98e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485418"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Como implantar o Windows 10 no Azure com direitos de hospedagem multilocatário 
Para clientes com Windows 10 Enterprise E3/E5 por usuário ou por Acesso de Área de Trabalho Virtual do Windows por usuário (licenças de assinatura do usuário ou licenças complementares de assinatura do usuário), os direitos de hospedagem multilocatário para Windows 10 permitem que você coloque suas licenças do Windows 10 na nuvem e execute máquinas virtuais do Windows 10 no Azure sem necessidade de pagar por outra licença. Para obter mais informações, consulte [Hospedagem multilocatário para Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Esse artigo mostra como implementar o benefício de licenciamento de imagens do Windows 10 Pro Desktop no Azure Marketplace.
> - Para obter imagens do Windows 7, 8.1 e 10 Enterprise (x64) no Azure Marketplace para Assinaturas do MSDN, consulte [Cliente do Windows no Azure para cenários de desenvolvimento/teste](client-images.md)
> - Para conhecer os benefícios de licenciamento do Windows Server, veja [Benefícios do uso híbrido do Azure para imagens do Windows Server](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Implantando a imagem do Windows 10 do Azure Marketplace 
Para implantações de modelo do Azure Resource Manager, da CLI e do Powershell, a imagem do Windows 10 pode ser encontrada com o publishername, oferta e SKU a seguir.

| SO  |      PublisherName      |  Oferta | Sku |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Carregando o VHD do Windows 10 no Azure
Se você está carregando um VHD de 10 Windows generalizado, observe o que Windows 10 não tem uma conta de administrador interno habilitada por padrão. Para habilitar a conta de administrador interno, inclua o comando a seguir como parte da extensão do script personalizado.

```powershell
Net user <username> /active:yes
```

O snippet do PowerShell a seguir marca todas as contas de administrador como ativas, incluindo a conta de administrador interno. Esse exemplo é útil se o nome de usuário da conta de administrador interno é desconhecido.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Para mais informações: 
* [Como carregar um VHD para o Azure](upload-generalized-managed.md)
* [Como preparar um VHD do Windows para carregar no Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Implantando o Windows 10 com direitos de hospedagem multilocatário
Verifique se você [instalou e configurou o Azure PowerShell mais recente](/powershell/azure/overview). Depois de preparar o VHD, carregue-o em sua conta de Armazenamento do Azure usando o cmdlet `Add-AzVhd` da seguinte maneira:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Implante usando a implantação de modelo do Azure Resource Manager** Dentro de seus modelos do Resource Manager, um parâmetro adicional para `licenseType` pode ser especificado. Você pode ler mais sobre a [criação de modelos do Azure Resource Manager](../../resource-group-authoring-templates.md). Quando o VHD for carregado no Azure, edite o modelo do Resource Manager para incluir o tipo de licença como parte do provedor de computação e implantar o modelo como normal:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Implantar via PowerShell** Ao implantar a VM do Windows Server por meio do PowerShell, você tem um parâmetro adicional para `-LicenseType`. Depois de carregar o VHD no Azure, você cria uma VM usando `New-AzVM` e especifica o tipo de licenciamento da seguinte maneira:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Verifique se que sua VM está utilizando o benefício de licenciamento
Depois de implantar sua VM por meio do método de implantação do PowerShell ou do Resource Manager, verifique o tipo de licença com `Get-AzVM` da seguinte maneira:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

A saída é semelhante ao seguinte exemplo do Windows 10 com o tipo de licença correto:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Esta saída contrasta com a seguinte VM implantada sem o licenciamento do Benefício de Uso Híbrido do Azure, como uma VM implantada diretamente da Galeria do Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Informações adicionais sobre ingresso no Azure AD
>[!NOTE]
>O Azure provisiona todas as VMs do Windows com a conta de administrador interno, que não pode ser usada para ingressar no AAD. Por exemplo, *Configurações > Conta > Acesso Corporativo ou de Estudante > +Conectar* não funcionará. Você deve criar e fazer logon como uma segunda conta de administrador para ingressar manualmente no Microsoft Azure Active Directory. Você também pode configurar o Azure AD usando um pacote de provisionamento, use o link na seção *Próximas etapas* para saber mais.
>
>

## <a name="next-steps"></a>Próximas etapas
- Em [Configurando VDA para Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation), aprenda mais sobre o assunto
- Saiba mais sobre [Hospedagem multilocatário para Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


