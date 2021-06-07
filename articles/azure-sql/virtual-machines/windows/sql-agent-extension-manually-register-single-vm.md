---
title: Registrar com extensão do agente IaaS do SQL
description: Registre sua máquina virtual do Azure SQL Server com a extensão do agente IaaS do SQL para habilitar recursos para SQL Server máquinas virtuais implantadas fora do Azure Marketplace, bem como conformidade e capacidade de gerenciamento aprimorada.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 983cdab0c5f5b856537c661c7427a83099f30ed4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181424"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>Registrar SQL Server VM com extensão do agente IaaS do SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Registrar sua VM SQL Server com a [extensão do agente IaaS do SQL](sql-server-iaas-agent-extension-automate-management.md) para desbloquear uma infinidade de benefícios de recursos para seu SQL Server na VM do Azure. 

Este artigo ensina a registrar uma única VM SQL Server com a extensão do agente IaaS do SQL. Como alternativa, você pode registrar todas as VMs SQL Server [automaticamente](sql-agent-extension-automatic-registration-all-vms.md) ou [várias VMs com script em massa](sql-agent-extension-manually-register-vms-bulk.md).


## <a name="overview"></a>Visão geral

O registro com a [extensão do agente IaaS SQL Server](sql-server-iaas-agent-extension-automate-management.md) cria o _recurso_ de **máquina virtual do SQL** em sua assinatura, que é um recurso _separado_ do recurso de máquina virtual. Cancelar o registro da VM de SQL Server da extensão removerá o _recurso_ de **máquina virtual do SQL** , mas não descartará a máquina virtual real.

A implantação de uma imagem SQL Server VM do Azure Marketplace por meio do portal do Azure registra automaticamente a VM SQL Server com a extensão. No entanto, se você optar por instalar automaticamente o SQL Server em uma máquina virtual do Azure ou provisionar uma máquina virtual do Azure de um VHD personalizado, deverá registrar sua VM SQL Server com a extensão do agente IaaS do SQL para para desbloquear os benefícios e a capacidade de gerenciamento completos dos recursos. 

Para utilizar a extensão do agente IaaS do SQL, você deve primeiro [registrar sua assinatura com o provedor **Microsoft. SqlVirtualMachine**](#register-subscription-with-rp), que dá à extensão do SQL IaaS a capacidade de criar recursos dentro dessa assinatura específica.

> [!IMPORTANT]
> A extensão do agente IaaS do SQL coleta dados para a finalidade expressa de fornecer aos clientes benefícios opcionais ao usar SQL Server nas máquinas virtuais do Azure. A Microsoft não usará esses dados para auditorias de licenciamento sem o consentimento prévio do cliente. Consulte o [suplemento de privacidade SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

Para registrar sua VM SQL Server com a extensão, você precisará de: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Uma máquina virtual do modelo de recurso do Azure [Windows Server 2008 (ou superior)](../../../virtual-machines/windows/quick-create-portal.md) com [SQL Server 2008 (ou superior)](https://www.microsoft.com/sql-server/sql-server-downloads) implantada na nuvem pública ou do Azure governamental. 
- A versão mais recente do [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell (mínimo de 5,0)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-rp"></a>Registrar assinatura com RP

Para registrar sua VM SQL Server com a extensão do agente IaaS do SQL, você deve primeiro registrar sua assinatura com o provedor **Microsoft. SqlVirtualMachine** . Isso dá à extensão do agente IaaS do SQL a capacidade de criar recursos em sua assinatura.  Você pode fazer isso usando o portal do Azure, o CLI do Azure ou o Azure PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. Abra o portal do Azure e navegue até **Todos os Serviços**. 
1. Vá para **Assinaturas** e selecione a assinatura de seu interesse.  
1. Na página **assinaturas** , vá para **extensões**. 
1. Insira **SQL** no filtro para abrir as extensões relacionadas ao SQL. 
1. Selecione **Registrar**, **Registrar novamente** ou **Cancelar registro** de para o provedor **Microsoft.SqlVirtualMachine**, dependendo da ação desejada. 

   ![Modificar o provedor](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>Linha de comando

Registre sua assinatura do Azure com o provedor **Microsoft. SqlVirtualMachine** usando o CLI do Azure ou Azure PowerShell. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-extension"></a>Registrar com extensão

Há três modos de gerenciamento para a [extensão do agente IaaS SQL Server](sql-server-iaas-agent-extension-automate-management.md#management-modes). 

O registro da extensão no modo de gerenciamento completo reinicia o serviço de SQL Server, portanto, é recomendável registrar a extensão no modo leve primeiro e, em seguida, [atualizar para completo](#upgrade-to-full) durante uma janela de manutenção. 

### <a name="lightweight-management-mode"></a>Modo de gerenciamento Leve

Use o CLI do Azure ou Azure PowerShell para registrar sua VM do SQL Server com a extensão no modo leve. Isso não reiniciará o serviço SQL Server. Em seguida, você pode atualizar para o modo completo a qualquer momento, mas isso reiniciará o serviço de SQL Server, assim, é recomendável aguardar até uma janela de manutenção agendada. 

Forneça o tipo de licença de SQL Server como pré-pago ( `PAYG` ) para pagar por uso, benefício híbrido do Azure ( `AHUB` ) para usar sua própria licença ou recuperação de desastre ( `DR` ) para ativar a [licença de réplica de Dr gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

As instâncias de cluster de failover e as implantações de várias instâncias só podem ser registradas com a extensão do SQL IaaS Agent no modo leve. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

Registre uma VM SQL Server no modo leve com o CLI do Azure: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registre uma VM SQL Server no modo leve com Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Pacote de gerenciamento completo

Registrar seu SQL Server VM no modo completo reiniciará o serviço SQL Server. Continue com cuidado. 

Para registrar sua VM SQL Server diretamente no modo completo (e possivelmente reiniciar o serviço de SQL Server), use o seguinte comando Azure PowerShell: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Modo de gerenciamento NoAgent 

SQL Server 2008 e 2008 R2 instalados no Windows Server 2008 (_não R2_) podem ser registrados com a extensão do SQL IaaS Agent no [modo noagent](sql-server-iaas-agent-extension-automate-management.md#management-modes). Essa opção garante a conformidade e permite que a VM do SQL Server seja monitorada no portal do Azure com funcionalidade limitada.


Para o **tipo de licença**, especifique: `AHUB` , `PAYG` ou `DR` . Para a **oferta de imagem**, especifique um `SQL2008-WS2008` ou `SQL2008R2-WS2008`

Para registrar seu SQL Server 2008 ( `SQL2008-WS2008` ) ou 2008 R2 ( `SQL2008R2-WS2008` ) na instância do Windows Server 2008, use o seguinte CLI do Azure ou Azure PowerShell trecho de código: 


# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

Registre sua máquina virtual SQL Server no modo noagent com o CLI do Azure: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


Registre sua máquina virtual SQL Server no modo noagent com Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
  ```

---

## <a name="verify-mode"></a>Modo de verificação

Você pode exibir o modo atual de seu SQL Server agente IaaS usando Azure PowerShell: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Atualizar para completo  

SQL Server VMs que registraram a extensão no modo *leve* podem ser atualizadas para _full_ usando o portal do Azure, o CLI do Azure ou o Azure PowerShell. As VMs do SQL Server no modo _NoAgent_ podem ser atualizadas para _completo_ depois que o sistema operacional é atualizado para o Windows 2008 R2 e superior. Não é possível fazer o downgrade – para isso, você precisará cancelar o [registro](#unregister-from-extension) da VM SQL Server da extensão do agente IaaS do SQL. Isso removerá o **recurso** da _máquina virtual do SQL_, mas não excluirá a máquina virtual em si. 

> [!NOTE]
> Quando você atualizar o modo de gerenciamento para a extensão IaaS do SQL para Full, ele reiniciará o serviço SQL Server. Em alguns casos, a reinicialização pode fazer com que os SPNs (nomes da entidade de serviço) associados ao serviço de SQL Server sejam alterados para a conta de usuário incorreta. Se você tiver problemas de conectividade depois de atualizar o modo de gerenciamento para completo, cancele o [registro e registre novamente seus SPNs](/sql/database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections).


### <a name="azure-portal"></a>Portal do Azure

Para atualizar a extensão para o modo completo usando o portal do Azure, siga estas etapas: 

1. Entre no [portal do Azure](https://portal.azure.com).
1. Vá para o recurso [máquinas virtuais do SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). 
1. Selecione sua VM SQL Server e selecione **visão geral**. 
1. Para VMs do SQL Server com o modo NoAgent ou IaaS leve, selecione a mensagem **Somente atualizações de edição e tipo de licença estão disponíveis com a extensão de IaaS do SQL**.

   ![Seleções para alterar o modo usando o portal](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. Marque a caixa de seleção **Eu concordo em reiniciar o serviço SQL Server na máquina virtual** e selecione **Confirmar** para atualizar o modo IaaS para completo. 

    ![Marque a caixa para concordar em reiniciar o serviço SQL Server na máquina virtual](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>Linha de comando

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

Para atualizar a extensão para o modo completo, execute o seguinte trecho de código de CLI do Azure:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Para atualizar a extensão para o modo completo, execute o seguinte trecho de código de Azure PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verificar o status de registro
Você pode verificar se sua VM de SQL Server já foi registrada com a extensão do SQL IaaS Agent usando o portal do Azure, o CLI do Azure ou Azure PowerShell. 

### <a name="azure-portal"></a>Portal do Azure 

Para verificar o status de registro usando o portal do Azure, siga estas etapas: 

1. Entre no [portal do Azure](https://portal.azure.com). 
1. Vá para suas [VMs SQL Server](manage-sql-vm-portal.md).
1. Selecione a VM do SQL Server na lista. Se sua VM de SQL Server não estiver listada aqui, provavelmente ela não foi registrada com a extensão do SQL IaaS Agent. 
1. Ver o valor em **Status**. Se o **status** for **bem-sucedido**, o SQL Server VM foi registrado com êxito com a extensão do agente IaaS do SQL. 

   ![Verificar o status com o registro de RP do SQL](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>Linha de comando

Verifique o status atual de registro da VM SQL Server usando CLI do Azure ou Azure PowerShell. `ProvisioningState` mostrará `Succeeded` se o registro foi bem-sucedido. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

Para verificar o status de registro usando o CLI do Azure, execute o seguinte trecho de código:  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Para verificar o status de registro usando o Azure PowerShell, execute o seguinte trecho de código:

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Um erro indica que a VM SQL Server não foi registrada com a extensão. 


## <a name="unregister-from-extension"></a>Cancelar o registro da extensão

Para cancelar o registro da VM SQL Server com a extensão do agente IaaS do SQL, exclua o *recurso* de máquina virtual do SQL usando o portal do Azure ou CLI do Azure. Excluir o *recurso* de máquina virtual do SQL não exclui a VM SQL Server. No entanto, tenha cuidado e siga as etapas com cuidado porque é possível excluir inadvertidamente a máquina virtual ao tentar remover o *recurso*. 

O cancelamento do registro da máquina virtual do SQL com a extensão do SQL IaaS Agent é necessário para fazer o downgrade completo do modo de gerenciamento. 

### <a name="azure-portal"></a>Portal do Azure

Para cancelar o registro da VM SQL Server da extensão usando o portal do Azure, siga estas etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com).
1. Navegue até o recurso de VM do SQL. 
  
   ![Recurso de máquinas virtuais do SQL](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. Selecione **Excluir**. 

   ![Selecione Excluir na parte superior da navegação](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. Digite o nome da máquina virtual do SQL e **desmarque a caixa de seleção ao lado da máquina virtual**.

   ![Desmarque a VM para impedir a exclusão da máquina virtual real e, em seguida, selecione Excluir para continuar com a exclusão do recurso de VM do SQL](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Falha ao desmarcar a caixa de seleção ao lado do nome da máquina virtual *excluirá* a máquina virtual por completo. Desmarque a caixa de seleção para cancelar o registro da VM SQL Server da extensão, mas *não exclua a máquina virtual real*. 

1. Selecione **excluir** para confirmar a exclusão do *recurso* de máquina virtual do SQL e não a VM SQL Server. 

### <a name="command-line"></a>Linha de comando

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para cancelar o registro da VM SQL Server da extensão com CLI do Azure, use o comando [AZ SQL VM Delete](/cli/azure/sql/vm#az-sql-vm-delete) . Isso removerá o SQL Server *recurso* da VM, mas não excluirá a máquina virtual. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para cancelar o registro da VM SQL Server da extensão com Azure PowerShell, use o comando [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm). Isso removerá o SQL Server *recurso* da VM, mas não excluirá a máquina virtual. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Perguntas frequentes sobre o SQL Server em uma VM do Windows](frequently-asked-questions-faq.md)  
* [Diretrizes de preço para o SQL Server em uma VM do Windows](pricing-guidance.md)
* [Notas sobre a versão do SQL Server em uma VM do Windows](../../database/doc-changes-updates-release-notes.md)
