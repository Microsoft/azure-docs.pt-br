---
title: Registrar a máquina virtual do SQL Server no Azure com o provedor de recursos de VM do SQL | Microsoft Docs
description: Registre sua VM do SQL Server com o provedor de recursos de VM do SQL para melhorar a capacidade de gerenciamento.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7d7d04299c2d6c7a498701df9fb39c8990484a79
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807263"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrar a máquina virtual do SQL Server no Azure com o provedor de recursos de VM do SQL

Este artigo descreve como registrar seu servidor SQL do Azure VM (máquina virtual) com o provedor de recursos de VM do SQL. 

Implantar uma imagem do marketplace de VM do SQL Server por meio do portal do Azure automaticamente registra a VM do SQL Server com o provedor de recursos. No entanto, se você optar por instalar automaticamente o SQL Server em uma máquina Virtual do Azure em vez de escolher uma imagem do Azure Marketplace, você deve registrar sua VM do SQL Server com o provedor de recursos hoje para:

-  **Conformidade** – de acordo com os termos de produto da Microsoft, os clientes que usam o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) deve indicar à Microsoft, ao usar o benefício híbrido do Azure – e para fazer isso, eles devem se registrar com o provedor de recursos de VM do SQL. 

-  **Benefícios de recursos** -registrar sua VM do SQL Server com o provedor de recursos desbloqueia [aplicação de patch automática](virtual-machines-windows-sql-automated-patching.md), [backup automático](virtual-machines-windows-sql-automated-backup-v2.md), monitoramento e a capacidade de gerenciamento, bem como [licenciamento](virtual-machines-windows-sql-ahb.md) e [edition](virtual-machines-windows-sql-change-edition.md) flexibilidade. Anteriormente, elas eram apenas disponíveis para imagens de VM do SQL Server no Azure Marketplace.

Instalação automática do SQL Server em uma VM do Azure ou o provisionamento de uma VM do Azure de um VHD personalizado com o SQL Server é compatível com por meio do benefício híbrido do Azure para o SQL Server com a condição de que os clientes indicam que usam para a Microsoft ao se registrar com o recurso de VM do SQL provedor. 

Para utilizar o provedor de recursos de VM do SQL, você também deve registrar o provedor de recursos de VM do SQL com a sua assinatura. Isso pode ser feito com o portal do Azure, a CLI do Azure e o PowerShell. 

## <a name="prerequisites"></a>Pré-requisitos

Para registrar sua VM do SQL Server com o provedor de recursos, você precisará do seguinte: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Um [VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [CLI do Azure](/cli/azure/install-azure-cli) e [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>Registrar com o provedor de recursos de VM do SQL
Se o [extensão SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) já está instalado na VM, em seguida, registrando com o provedor de recursos de VM do SQL é simplesmente criar um recurso de metadados do tipo Microsoft.SqlVirtualMachine/SqlVirtualMachines. Abaixo está o trecho de código para registrar o provedor de recursos de VM do SQL se a extensão IaaS do SQL já estiver instalada na VM. Você precisa fornecer o tipo de licença do SQL Server desejado ao registrar com o provedor de recursos de VM do SQL como ' PAYG ' ou 'AHUB'. 

Registre-se a VM do SQL Server usando o PowerShell com o trecho de código a seguir:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

Se a extensão IaaS do SQL não está instalada na VM, em seguida, você pode registrar com o provedor de recursos de VM do SQL, especificando o modo de gerenciamento leve do SQL. No modo leve de gerenciamento do SQL, a VM do SQL do provedor de recursos serão automaticamente instalar extensão SQL IaaS na [modo leve](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) e verifique se o SQL Server metadados da instância; essa será não reiniciar o serviço do SQL Server. Você precisa fornecer o tipo de licença do SQL Server desejado ao registrar com o provedor de recursos de VM do SQL como ' PAYG ' ou 'AHUB'. 

Registre a VM do SQL Server no modo de gerenciamento leve do SQL usando o PowerShell com o trecho de código a seguir:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

Registrando com o provedor de recursos de VM do SQL na [modo leve](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) será garantir a conformidade e habilitar o licenciamento flexível, bem como atualizações de edição do SQL Server no local. Instâncias de Cluster de failover e implantações com várias instâncias podem ser registradas com o provedor de recursos de VM do SQL somente no modo leve. Você pode seguir as instruções encontradas no portal do Azure para atualizar para o [modo completo](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) e habilitar a capacidade de gerenciamento abrangente conjunto de recursos com uma reinicialização do SQL Server a qualquer momento. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Registrar o SQL Server 2008/R2 em VMs do Windows Server 2008

SQL Server 2008 e 2008 R2 instalado no Windows Server 2008 pode ser registrado com a VM do SQL no provedor de recursos do [NoAgent](virtual-machines-windows-sql-server-agent-extension.md) modo. Essa opção garante a conformidade e permite que a VM do SQL Server a ser monitorado no portal do Azure com funcionalidade limitada.

A tabela a seguir fornece detalhes sobre os valores aceitáveis para os parâmetros fornecidos durante o registro:

| Parâmetro | Valores aceitáveis                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`, ou `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` ou `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Para registrar o SQL Server 2008 ou 2008 R2 na instância do Windows Server 2008, use o seguinte trecho de código do Powershell:  

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>Verificar o status do registro
Você pode verificar se o SQL Server já foi registrado com o provedor de recursos de VM do SQL usando o portal do Azure, CLI do Azure ou PowerShell. 

### <a name="azure-portal"></a>Portal do Azure 
Para verificar o status do registro usando o portal do Azure, faça o seguinte:

1. Faça logon no [Portal do Azure](https://portal.azure.com). 
1. Navegue até sua [máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md).
1. Selecione sua VM do SQL Server na lista. Se sua VM do SQL Server não estiver listada aqui, é provável que sua VM do SQL Server não foi registrado com o provedor de recursos de VM do SQL. 
1. Exibir o valor em `Status`. Se `Status = Succeeded`, em seguida, a VM do SQL Server foi registrado com o provedor de recursos de VM do SQL com êxito. 

    ![Verificar o status de registro RP de SQL](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>Az CLI

Verifique se o status do registro com o seguinte comando CLI AZ VM do SQL Server atual. `ProvisioningState` mostrará `Succeeded` se o registro foi bem-sucedido. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

Verifique se o atual status de registro de VM do SQL Server com o seguinte cmdlet do PowerShell. `ProvisioningState` mostrará `Succeeded` se o registro foi bem-sucedido. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
Um erro indica que a VM do SQL Server não foi registrado com o provedor de recursos. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Registrar o provedor de recursos de VM do SQL com a assinatura 

Para registrar sua VM do SQL Server com o provedor de recursos de VM do SQL, você deve registrar o provedor de recursos à sua assinatura. Você pode fazer isso com o portal do Azure ou CLI do Azure.

### <a name="azure-portal"></a>Portal do Azure

As etapas a seguir registrará o provedor de recursos de VM do SQL à sua assinatura do Azure usando o portal do Azure. 

1. Abra o portal do Azure e navegue até **All Services**. 
1. Navegue até **assinaturas** e selecione a assinatura de seu interesse.  
1. Sobre o **inscrições** página, navegue até **provedores de recursos**. 
1. Tipo `sql` no filtro para exibir os provedores de recursos relacionados ao SQL. 
1. Selecione *Registrar*, *Registrar novamente* ou *Cancelar registro* de para o provedor **Microsoft.SqlVirtualMachine**, dependendo da ação desejada. 

   ![Modificar o provedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>Az CLI
O trecho de código a seguir irá registrar o provedor de recursos de VM do SQL à sua assinatura do Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

O seguinte trecho de código do PowerShell registrará o provedor de recursos de VM do SQL à sua assinatura do Azure.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Comentários

 - O provedor de recursos de VM do SQL só dá suporte a VMs do SQL Server implantado usando o Gerenciador de recursos ''. VMs do SQL Server implantado usando que o 'modelo clássico' não têm suporte. 
 - O provedor de recursos de VM do SQL só dá suporte a VMs do SQL Server implantado para a nuvem pública. Não há suporte para implantações em particular ou nuvem de governo. 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, confira os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server em um FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientações sobre preço do SQL Server em uma VM Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server em um notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


