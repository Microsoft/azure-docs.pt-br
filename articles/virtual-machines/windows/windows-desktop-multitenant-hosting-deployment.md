---
title: Como implantar o Windows 10 no Azure com direitos de hospedagem multilocatário
description: Saiba como maximizar seus benefícios do Windows Software Assurance para trazer licenças locais para o Azure com direitos de hospedagem multilocatário.
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 2/2/2021
ms.author: mimckitt
ms.custom: rybaker, chmimckitt
ms.openlocfilehash: 744f265251e9f58ce91085f0e5dd5d1ad13eec69
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625104"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Como implantar o Windows 10 no Azure com direitos de hospedagem multilocatário 
Para clientes com Windows 10 Enterprise E3/E5 por usuário ou por Acesso de Área de Trabalho Virtual do Windows por usuário (licenças de assinatura do usuário ou licenças complementares de assinatura do usuário), os direitos de hospedagem multilocatário para Windows 10 permitem que você coloque suas licenças do Windows 10 na nuvem e execute máquinas virtuais do Windows 10 no Azure sem necessidade de pagar por outra licença. Os direitos de hospedagem multilocatário estão disponíveis somente para Windows 10 (versão 1703 ou posterior).

Para obter mais informações, consulte [hospedagem multilocatário para Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> - Para usar o Windows 7, 8,1 e 10 imagens para desenvolvimento ou teste, consulte [Windows Client no Azure para cenários de desenvolvimento/teste](client-images.md)
> - Para conhecer os benefícios de licenciamento do Windows Server, veja [Benefícios do uso híbrido do Azure para imagens do Windows Server](hybrid-use-benefit-licensing.md).

## <a name="subscription-licenses-that-qualify-for-multitenant-hosting-rights"></a>Licenças de assinatura que se qualificam para direitos de hospedagem multilocatário

Usando o [centro de administração da Microsoft](https://docs.microsoft.com/microsoft-365/admin/admin-overview/about-the-admin-center?view=o365-worldwide&preserve-view=true), você pode confirmar se uma licença com suporte do Windows 10 foi atribuída a um usuário.

> [!IMPORTANT]
> Os usuários devem ter uma das licenças de assinatura abaixo para usar as imagens do Windows 10 no Azure. Se você não tiver uma dessas licenças de assinatura, elas poderão ser adquiridas por meio de seu [parceiro de serviço de nuvem](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/) ou diretamente pela [Microsoft](https://www.microsoft.com/microsoft-365?rtc=1).

**Licenças de assinatura qualificadas:**

-   Microsoft 365 E3/e5 
-   Microsoft 365 F3 
-   Microsoft 365 a3/a5 
-   Windows 10 Enterprise E3/e5
-   Educação do Windows 10 a3/a5 
-   Windows VDA E3/e5


## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Implantando a imagem do Windows 10 do Azure Marketplace 
Para implantações de modelo do PowerShell, CLI e Azure Resource Manager, as imagens do Windows 10 podem ser encontradas usando o `PublisherName: MicrosoftWindowsDesktop` e o `Offer: Windows-10` . A atualização dos criadores de versão do Windows 10 (1809) ou posterior tem suporte para direitos de hospedagem multilocatário. 

```powershell
Get-AzVmImageSku -Location '$location' -PublisherName 'MicrosoftWindowsDesktop' -Offer 'Windows-10'

Skus                        Offer      PublisherName           Location 
----                        -----      -------------           -------- 
rs4-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs4-pron                    Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprise              Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprisen             Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pron                    Windows-10 MicrosoftWindowsDesktop eastus  
```

Para obter mais informações sobre imagens disponíveis, consulte [Localizar e usar imagens de VM do Azure Marketplace com Azure PowerShell](./cli-ps-findimage.md)

## <a name="uploading-windows-10-vhd-to-azure"></a>Carregando o VHD do Windows 10 no Azure
Se você está carregando um VHD de 10 Windows generalizado, observe o que Windows 10 não tem uma conta de administrador interno habilitada por padrão. Para habilitar a conta de administrador interno, inclua o comando a seguir como parte da extensão do script personalizado.

```powershell
Net user <username> /active:yes
```

O trecho do PowerShell a seguir é marcar todas as contas de administrador como ativas, incluindo o administrador interno. Esse exemplo é útil se o nome de usuário da conta de administrador interno é desconhecido.
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
Verifique se você [instalou e configurou o Azure PowerShell mais recente](/powershell/azure/). Depois de preparar o VHD, carregue-o em sua conta de Armazenamento do Azure usando o cmdlet `Add-AzVhd` da seguinte maneira:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Implante usando a implantação de modelo do Azure Resource Manager** Dentro de seus modelos do Resource Manager, um parâmetro adicional para `licenseType` pode ser especificado. Você pode ler mais sobre a [criação de modelos de Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). Quando o VHD for carregado no Azure, edite o modelo do Resource Manager para incluir o tipo de licença como parte do provedor de computação e implantar o modelo como normal:
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
O Azure provisiona todas as VMs do Windows com a conta de administrador interno, que não pode ser usada para ingressar no AAD. Por exemplo, *Configurações > Conta > Acesso Corporativo ou de Estudante > +Conectar* não funcionará. Você deve criar e fazer logon como uma segunda conta de administrador para ingressar manualmente no Microsoft Azure Active Directory. Você também pode configurar o Azure AD usando um pacote de provisionamento, use o link na seção *próximas etapas* para saber mais.

## <a name="next-steps"></a>Próximas etapas
- Em [Configurando VDA para Windows 10](/windows/deployment/vda-subscription-activation), aprenda mais sobre o assunto
- Saiba mais sobre [Hospedagem multilocatário para Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)
