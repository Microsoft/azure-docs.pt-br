---
title: Alterar o modelo de licença para uma VM do SQL no Azure
description: Saiba como alternar o licenciamento para uma VM do SQL Server no Azure de "pago conforme o uso" para "traga sua própria licença" com o Benefício Híbrido do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: management
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5813331d5eafd953d776dd19d9cc885ff71b8be0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361546"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Alterar o modelo de licença para uma máquina virtual do SQL no Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Este artigo descreve como alterar o modelo de licença para uma VM (máquina virtual) SQL Server no Azure usando a [extensão do agente IaaS do SQL](./sql-server-iaas-agent-extension-automate-management.md).

## <a name="overview"></a>Visão geral

Há três modelos de licença para uma VM do Azure que está hospedando SQL Server: pré-pago, Benefício Híbrido do Azure (AHB) e alta disponibilidade/recuperação de desastres (HA/DR). É possível modificar o modelo de licença da VM do SQL Server no portal do Azure, na CLI do Azure ou no PowerShell. 

- No modelo **pago conforme o uso**, o custo por segundo da execução da VM do Azure inclui o custo da licença do SQL Server.
- O [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) permite que você use sua licença do SQL Server em uma VM que está executando o SQL Server. 
- O tipo de licença **ha/Dr** é usado para a [réplica gratuita de ha/Dr](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) no Azure. 

O Benefício Híbrido do Azure permite o uso de licenças do SQL Server com o Software Assurance ("Licença Qualificada") em máquinas virtuais do Azure. Com o Benefício Híbrido do Azure, os clientes não são cobrados pelo uso de uma licença do SQL Server em uma VM. Mas eles ainda precisam pagar pelo custo da computação em nuvem subjacente (ou seja, a taxa base), o armazenamento e os backups. Também devem pagar pela E/S associada ao uso dos serviços (conforme aplicável).

De acordo com os [termos de produto](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS)da Microsoft: "os clientes devem indicar que estão usando o banco de dados SQL do Azure (Instância Gerenciada, Pool Elástico e banco de dados individual), Azure Data Factory, SQL Server Integration Services ou SQL Server máquinas virtuais em Benefício Híbrido do Azure para SQL Server ao configurar cargas de trabalho no Azure."

Existem três opções para indicar o uso do Benefício Híbrido do Azure para SQL Server em uma VM do Azure e ficar em conformidade:

- Provisionar uma máquina virtual usando uma imagem do SQL Server do tipo "traga sua própria licença" do Azure Marketplace. Essa opção está disponível somente para clientes que têm um Contrato Enterprise.
- Provisionar uma máquina virtual usando uma imagem do SQL Server do tipo "pago conforme o uso" do Azure Marketplace e ativar o Benefício Híbrido do Azure.
- SQL Server de instalação automática na VM do Azure, [Registre-se manualmente com a extensão do agente IaaS do SQL](sql-agent-extension-manually-register-single-vm.md)e ative benefício híbrido do Azure.

O tipo de licença do SQL Server pode ser configurado quando a VM é provisionada ou a qualquer momento depois disso. Alternar entre os modelos de licença não gera tempo de inatividade, não reinicia a VM ou o serviço SQL Server, não gera custos adicionais e entra em vigor imediatamente. Na verdade, ativar o Benefício Híbrido do Azure *reduz* o custo.

## <a name="prerequisites"></a>Pré-requisitos

Para alterar o modelo de licenciamento da VM do SQL Server, é preciso cumprir os seguintes requisitos: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Uma [VM SQL Server](./create-sql-vm-portal.md) registrada com a [extensão do SQL IaaS Agent](./sql-server-iaas-agent-extension-automate-management.md).
- O [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) é um requisito para usar o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="change-license-model"></a>Alterar modelo de licença

# <a name="azure-portal"></a>[Portal do Azure](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

É possível modificar o modelo de licença diretamente no portal: 

1. Abra o [portal do Azure](https://portal.azure.com) e o [recurso de máquinas virtuais do SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) da VM do SQL Server. 
1. Selecione **Configurar** em **Configurações**. 
1. Selecione a opção **Benefício Híbrido do Azure** e marque a caixa de seleção para confirmar que você tem uma licença do SQL Server com Software Assurance. 
1. Clique em **Aplicar** na parte inferior da página **Configurar**. 

![Benefício Híbrido do Azure no portal](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

É possível usar a CLI do Azure para alterar o modelo de licença.  

Especifique os seguintes valores para **licença-tipo**:
- `AHUB` para o Benefício Híbrido do Azure
- `PAYG` para pagamento conforme o uso
- `DR` para ativar a réplica gratuita de HA/DR


```azurecli-interactive
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type <license-type>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

É possível usar o PowerShell para alterar o modelo de licença.

Especifique os seguintes valores para **licença-tipo**:
- `AHUB` para o Benefício Híbrido do Azure
- `PAYG` para pagamento conforme o uso
- `DR` para ativar a réplica gratuita de HA/DR


```powershell-interactive
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType <license-type>
```

---

## <a name="remarks"></a>Comentários

- Os clientes do CSP (Provedor de Soluções de Nuvem) do Azure poderão usar o Benefício Híbrido do Azure primeiro implantando uma VM paga conforme o uso e, em seguida, convertendo-a para traga sua própria licença, se tiverem o Software Assurance ativo.
- Se você descartar o recurso de máquina virtual do SQL, voltará para a configuração de licença embutida em código da imagem. 
- A capacidade de alterar o modelo de licença é um recurso da extensão do SQL IaaS Agent. A implantação de uma imagem do Azure Marketplace por meio do portal do Azure registra automaticamente uma VM SQL Server com a extensão. Mas os clientes que estão instalando automaticamente o SQL Server precisarão [registrar manualmente a VM do SQL Server](sql-agent-extension-manually-register-single-vm.md). 
- Para adicionar uma VM do SQL Server a um conjunto de disponibilidade, é preciso criar a VM novamente. Assim, todas as VMs adicionadas a um conjunto de disponibilidade voltarão para o tipo de licença padrão paga conforme o uso. Será necessário habilitar o Benefício Híbrido do Azure novamente. 


## <a name="limitations"></a>Limitações

A alteração do modelo de licença:
   - Está disponível somente para clientes com [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Só é compatível com as edições Standard e Enterprise do SQL Server. As alterações de licença não são compatíveis com o Express, o Web e o Developer. 
   - Só é compatível com as máquinas virtuais implantadas por meio do modelo do Azure Resource Manager. As máquinas virtuais implantadas por meio do modelo clássico não são compatíveis. 
   - Está disponível somente para as nuvens públicas ou do Azure Government. 

> [!Note]
> Somente SQL Server licenciamento baseado em núcleo com licenças de assinatura ou Software Assurance são elegíveis para Benefício Híbrido do Azure. Se você estiver usando o licenciamento do servidor + CAL para SQL Server e tiver o Software Assurance, poderá usar o traga sua própria licença para uma imagem de máquina virtual do Azure SQL Server para aproveitar a mobilidade de licenças para esses servidores, mas não poderá aproveitar os outros recursos do Benefício Híbrido do Azure. 

## <a name="known-errors"></a>Erros conhecidos

Examine os erros comumente conhecidos e suas resoluções. 

**O recurso ' Microsoft. SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> ' no grupo de recursos ' \<resource-group> ' não foi encontrado.**

Esse erro ocorre quando você tenta alterar o modelo de licença em uma VM SQL Server que não foi registrada com a extensão do agente IaaS SQL Server:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Você precisará registrar sua assinatura com o provedor de recursos e [registrar sua VM de SQL Server com a extensão do agente IaaS do SQL](sql-agent-extension-manually-register-single-vm.md). 



## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Perguntas frequentes sobre o SQL Server em uma VM do Windows](frequently-asked-questions-faq.md)
* [Diretrizes de preço para o SQL Server em uma VM do Windows](pricing-guidance.md)
* [Notas sobre a versão do SQL Server em uma VM do Windows](../../database/doc-changes-updates-release-notes.md)
* [Visão geral da extensão do agente IaaS do SQL](./sql-server-iaas-agent-extension-automate-management.md)
