---
title: Como alterar o modelo de licenciamento de uma VM do SQL Server no Azure
description: Saiba como mudar o licenciamento para uma máquina virtual do SQL no Azure de 'pré-pago' para 'traga-your-própria licença' usando o benefício híbrido do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 78ad784a45d2b0063932791daedc9b1ec1aafd72
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786756"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Como alterar o modelo de licenciamento para uma máquina de virtual do SQL Server no Azure
Este artigo descreve como alterar o modelo de licenciamento de uma máquina virtual do SQL Server no Azure usando o novo provedor de recursos da VM do SQL – **Microsoft.SqlVirtualMachine**.

Há dois modelos de licenciamento para uma máquina virtual (VM) que hospeda o SQL Server - pago conforme o uso e benefício de híbrido do Azure (AHB). E agora, usando o portal do Azure, CLI do Azure ou PowerShell você pode modificar o modelo de licenciamento de sua VM do SQL Server. 

O **pré-pago** modelo (PAYG) significa que o custo por segundo da execução da VM do Azure inclui o custo da licença do SQL Server.
O [benefício de híbrido do Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) permite que você use sua própria licença do SQL Server com uma VM executando o SQL Server. 

Benefício híbrido do Microsoft Azure para SQL Server permite usar licenças do SQL Server com Software Assurance ("licença qualificado") em máquinas virtuais do Azure. Com o benefício do híbrido do Azure para SQL Server, os clientes não serão cobrados para o uso da licença do SQL Server na VM, mas eles ainda precisa pagar o custo de computação de nuvem subjacente (ou seja, a taxa base), armazenamento e backups, bem como e/s associados com sua u Se os serviços (conforme aplicável).

De acordo com termos de produto da Microsoft "clientes devem indicar que estão usando banco de dados do SQL Azure (instância gerenciada, Pool Elástico e banco de dados único), o Azure Data Factory, SQL Server Integration Services ou máquinas virtuais do SQL Server em híbrido do Azure Benefício para o SQL Server ao configurar cargas de trabalho no Azure".

Para indicar o uso do benefício híbrido do Azure para SQL Server em VM do Azure e estar em conformidade, há três opções:

1. Provisione uma máquina virtual usando uma imagem BYOL SQL Server do marketplace do Azure, disponível somente para clientes com contrato Enterprise.
1. Provisionar uma máquina virtual usando uma imagem PAYG SQL Server do Azure marketplace e ativar AHB.
1. Self instalar o SQL Server em uma VM do Azure manualmente [registrar sua VM do SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) e ativar AHB.

Tipo de licença do SQL Server é definido quando a VM é provisionada e pode ser alterada a qualquer momento posteriormente. Alternar entre os modelos de licença acarreta **sem tempo de inatividade**, reinicie a VM, adiciona **sem custo adicional** (na verdade, ativando AHB *reduz* custo) e é **em vigor imediatamente**. 

## <a name="prerequisites"></a>Pré-requisitos

O uso do provedor de recursos de VM do SQL requer a extensão IaaS do SQL. Assim, para continuar utilizando o provedor de recursos de VM do SQL, você precisará do seguinte:
- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- [Do software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Um [VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrado com o [provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md) instalado. 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>Alterar licença para VMs já registrado com o provedor de recursos 

# <a name="azure-portaltabazure-portal"></a>[Portal do Azure](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Você pode modificar o modelo de licenciamento diretamente do portal. 

1. Abra o [portal do Azure](https://portal.azure.com) e inicie o [recursos de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) para sua VM do SQL Server. 
1. Selecione **Configure** sob **configurações**. 
1. Selecione o **benefício híbrido do Azure** opção e marque a caixa de seleção para confirmar que você tem uma licença do SQL Server com Software Assurance. 
1. Selecione **Apply** na parte inferior a **configurar** página. 

![AHB no Portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Você pode usar a CLI do Azure para alterar seu modelo de licenciamento.  

O trecho de código a seguir alterna o modelo pago conforme o uso de licença para BYOL (ou usando o benefício híbrido do Azure):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

O trecho de código a seguir alterna o modelo traga-your-própria licença para pré-pago: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Você pode usar o PowerShell para alterar seu modelo de licenciamento.

O trecho de código a seguir alterna o modelo pago conforme o uso de licença para BYOL (ou usando o benefício híbrido do Azure):

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

O trecho de código a seguir alterna o modelo BYOL para pré-pago:

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```
---

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Altere a licença para VMs que não está registradas com o provedor de recursos

Se você tiver provisionado uma VM do SQL Server de imagens PAYG do Azure Marketplace, em seguida, o tipo de licença do SQL será PAYG. Se você tiver provisionado uma VM do SQL Server usando uma imagem BYOL do Azure Marketplace, em seguida, o tipo de licença será AHUB. Todas as VMs do SQL Server é provisionado por padrão (PAYG) ou imagens BYOL do Azure Marketplace serão automaticamente registradas com o provedor de recursos de VM do SQL, para que eles possam alterar o [tipo de licença](#change-license-for-vms-already-registered-with-resource-provider)

Você só é elegíveis para instalar automaticamente o SQL Server em VM do Azure por meio do benefício híbrido do Azure e você deve [registrar essas VMs com o provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md) definindo-se a licença do SQL Server como AHB para indicar o uso AHB acordo com Termos de produto da Microsoft.

Você só pode alterar o tipo de licença de uma VM do SQL Server como PAYG ou AHB se a VM do SQL está registrada com o provedor de recursos de VM do SQL; e todas as VMs do SQL deve ser registradas com RP de VM do SQL para conformidade de licenças.

## <a name="remarks"></a>Comentários

 - Os clientes de parceiro de solução de nuvem (CSP) do Azure podem utilizar o benefício híbrido do Azure primeiro Implantando uma VM pago conforme o uso e, em seguida, convertê-lo para trazer-your-própria licença se eles tiverem SA ativo.
 - Se você remover o recurso de VM do SQL Server, você voltará para a configuração de embutido em código de licença da imagem. 
  - A capacidade de alterar o modelo de licenciamento é um recurso do provedor de recursos de VM do SQL. Implantar uma imagem do marketplace no portal do Azure automaticamente registra uma VM do SQL Server com o provedor de recursos. No entanto, os clientes que são de instalação automática do SQL Server precisará manualmente [registrar sua VM do SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md). 
- A adição de uma VM do SQL Server para um conjunto de disponibilidade requer recriar a VM. Como tais, quaisquer VMs adicionadas a um disponibilidade conjunto voltará para o tipo de licença pago conforme o uso do padrão e AHB precisará ser habilitada novamente. 


## <a name="limitations"></a>Limitações

 - Alterar o modelo de licenciamento está disponível apenas para clientes com software assurance.
 - Alterar o modelo de licenciamento, somente há suporte para a edição standard e enterprise do SQL Server. Alterações de licença para Express, Web e desenvolvedores não têm suportadas. 
 - Somente há suporte para alterar o modelo de licenciamento para máquinas virtuais implantadas usando o modelo do Resource Manager. Não há suporte para VMs implantadas usando o modelo clássico. 
 - Alterar o modelo de licenciamento é habilitado apenas para instalações de nuvem pública.
 - Alterar o modelo de licenciamento tem suporte apenas em máquinas virtuais que têm uma única NIC (interface de rede). Em máquinas virtuais que têm mais de uma NIC, você deve primeiro remover uma das NICs (usando o portal do Azure) antes de tentar o procedimento. Caso contrário, você enfrentará um erro semelhante ao seguinte: `The virtual machine '\<vmname\>' has more than one NIC associated.` Embora você possa ser capaz de adicionar o NIC para a VM depois de alterar o modo de licenciamento, operações realizadas por meio da página de configuração do SQL no portal do Azure, como aplicação de patch automática e de backup, não serão consideradas com suporte.


## <a name="known-errors"></a>Erros conhecidos

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>O recurso ' Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<grupo de recursos >' no grupo de recursos '\<grupo de recursos >' não foi encontrado. A propriedade 'sqlServerLicenseType' não pode ser encontrada neste objeto. Verifique se a propriedade existe e pode ser definida.
Esse erro ocorre ao tentar alterar o modelo de licenciamento em uma VM do SQL Server que não foi registrado com o provedor de recursos de VM do SQL. Você precisará registrar o provedor de recursos em sua [assinatura](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription)e, em seguida, registre sua VM do SQL Server com o SQL [provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Não é possível validar o argumento no parâmetro 'Sku'
Você pode encontrar esse erro ao tentar alterar o modelo de licenciamento de VM do SQL Server ao usar o PowerShell do Azure > 4.0: `Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Para resolver esse erro, ao mudar seu modelo de licenciamento, remova a marca de comentário dessas linhas no snippet de código do PowerShell mencionado anteriormente:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
Use o código a seguir para verificar sua versão do Azure PowerShell:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, confira os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server em um FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientações sobre preço do SQL Server em uma VM Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server em um notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


