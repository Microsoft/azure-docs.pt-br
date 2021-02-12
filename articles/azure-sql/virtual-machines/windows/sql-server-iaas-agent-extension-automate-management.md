---
title: O que é a extensão do agente IaaS SQL Server?
description: Este artigo descreve como a extensão do agente IaaS SQL Server ajuda a automatizar tarefas de administração específicas de gerenciamento de SQL Server em VMs do Azure. Isso inclui recursos como backup automatizado, aplicação de patch automatizada, integração de Azure Key Vault, gerenciamento de licenciamento, configuração de armazenamento e gerenciamento central de todas as instâncias de VM SQL Server.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 481a4ff21c361e4cf82a21d9e98357a4c8b7b1b4
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663665"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>Automatizar o gerenciamento com a extensão SQL Server IaaS Agent
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


O SQL Server SqlIaasExtension (extensão do agente IaaS) é executado em SQL Server em VMs (máquinas virtuais) do Azure para automatizar tarefas de gerenciamento e administração. 

Este artigo fornece uma visão geral da extensão. Para instalar a extensão SQL Server IaaS para SQL Server em VMs do Azure, consulte os artigos para [instalação automática](sql-agent-extension-automatic-registration-all-vms.md), [VMs únicas](sql-agent-extension-manually-register-single-vm.md)ou [VMs em massa](sql-agent-extension-manually-register-vms-bulk.md). 

## <a name="overview"></a>Visão geral

A extensão do agente IaaS SQL Server permite a integração com o portal do Azure e, dependendo do modo de gerenciamento, o desbloqueia uma série de benefícios de recursos para SQL Server em VMs do Azure: 

- **Benefícios do recurso**: a extensão desbloqueia vários benefícios de recursos de automação, como gerenciamento de portal, flexibilidade de licença, backup automatizado, aplicação de patch automatizada e muito mais. Consulte os [benefícios do recurso](#feature-benefits) mais adiante neste artigo para obter detalhes. 

- **Conformidade**: a extensão oferece um método simplificado para atender à necessidade de notificar a Microsoft de que a benefício híbrido do Azure foi habilitada conforme especificado nos termos do produto. Esse processo nega a necessidade de gerenciar os formulários de registro de licenciamento para cada recurso.  

- **Gratuito**: a extensão em todos os três modos de gerenciamento é completamente gratuita. Não há custo adicional associado à extensão ou com a alteração dos modos de gerenciamento. 

- **Gerenciamento de licenças simplificado**: a extensão simplifica o gerenciamento de licenças SQL Server e permite que você identifique rapidamente SQL Server VMs com o benefício híbrido do Azure habilitado usando o [portal do Azure](manage-sql-vm-portal.md), o PowerShell ou o CLI do Azure: 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```



   ---


> [!IMPORTANT]
> A extensão do agente IaaS do SQL coleta dados para a finalidade expressa de fornecer aos clientes benefícios opcionais ao usar SQL Server nas máquinas virtuais do Azure. A Microsoft não usará esses dados para auditorias de licenciamento sem o consentimento prévio do cliente. Consulte o [suplemento de privacidade SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data) para obter mais informações.


## <a name="feature-benefits"></a>Benefícios do recurso 

O SQL Server extensão do agente IaaS desbloqueia vários benefícios de recursos para gerenciar sua VM SQL Server. 

A tabela a seguir detalha esses benefícios: 


| Recurso | Descrição |
| --- | --- |
| **Gerenciamento de Portal** | O desbloqueia [o gerenciamento no portal](manage-sql-vm-portal.md), para que você possa exibir todas as suas VMs SQL Server em um único local, para que você possa habilitar e desabilitar recursos específicos do SQL diretamente do Portal. <br/> Modo de gerenciamento: leve & completo|  
| **Backup automatizado** |Automatiza o agendamento de backups para todos os bancos de dados, seja da instância padrão ou então de uma instância nomeada [corretamente instalada](frequently-asked-questions-faq.md#administration) do SQL Server na VM. Para obter mais informações, confira [Backup automatizado para SQL Server em Máquinas Virtuais do Azure (Resource Manager)](automated-backup-sql-2014.md). <br/> Modo de gerenciamento: completo|
| **Aplicação de patch automatizada** |Configura uma janela de manutenção durante a qual as atualizações importantes do Windows e SQL Server segurança para sua VM podem ocorrer, para que você possa evitar atualizações durante horários de pico para sua carga de trabalho. Para saber mais, veja [Aplicação de patch automatizada para o SQL Server em Máquinas Virtuais do Azure (Resource Manager)](automated-patching.md). <br/> Modo de gerenciamento: completo|
| **Integração do Azure Key Vault** |Permite a instalação e configuração automática do Cofre de Chaves do Azure em sua VM do SQL Server. Para saber mais, confira [Configurar a integração do Azure Key Vault para SQL Server em Máquinas Virtuais do Azure (Resource Manager)](azure-key-vault-integration-configure.md). <br/> Modo de gerenciamento: completo|
| **Exibir a utilização de disco no portal** | Permite exibir uma representação gráfica da utilização de disco de seus arquivos de dados SQL no portal do Azure.  <br/> Modo de gerenciamento: completo | 
| **Licenciamento flexível** | Economize em custos por meio da [transição direta](licensing-model-azure-hybrid-benefit-ahb-change.md) da licença traga seu próprio (também conhecida como o benefício híbrido do Azure) para o modelo de licenciamento pago conforme o uso e de volta. <br/> Modo de gerenciamento: leve & completo| 
| **Versão/edição flexível** | Se você decidir alterar a [versão](change-sql-server-version.md) ou a [edição](change-sql-server-edition.md) do SQL Server, poderá atualizar os metadados no portal do Azure sem precisar reimplantar toda a VM do SQL Server.  <br/> Modo de gerenciamento: leve & completo| 


## <a name="management-modes"></a>Modos de gerenciamento

Você pode optar por registrar sua extensão de IaaS do SQL em três modos de gerenciamento: 

- O modo **leve** copia os binários de extensão para a VM, mas não instala o agente e não reinicia o serviço de SQL Server. O modo leve dá suporte apenas à alteração do tipo de licença e da edição do SQL Server e fornece gerenciamento de portal limitado. Use essa opção para SQL Server VMs com várias instâncias ou aquelas que participam de uma FCI (instância de cluster de failover). O modo leve é o modo de gerenciamento padrão ao usar o recurso de [registro automático](sql-agent-extension-automatic-registration-all-vms.md) ou quando um tipo de gerenciamento não é especificado durante o registro manual. Não há nenhum impacto à memória ou à CPU ao usar o modo leve e não há nenhum custo associado. É recomendável registrar sua VM do SQL Server no modo leve primeiro e, em seguida, atualizar para o modo completo durante uma janela de manutenção agendada. 

- O modo **completo** instala o agente IaaS do SQL na VM para fornecer todas as funcionalidades, mas requer uma reinicialização das permissões do serviço de SQL Server e do administrador do sistema. Use-a para gerenciar uma VM do SQL Server com uma instância. O modo completo instala dois serviços do Windows que têm um impacto mínimo sobre a memória e a CPU, que podem ser monitoradas por meio do gerenciador de tarefas. Não há nenhum custo associado ao uso do modo de gerenciamento completo. 

- O modo **NoAgent** é dedicado ao SQL Server 2008 e ao SQL Server 2008 R2 instalados no Windows Server 2008. Não há nenhum impacto à memória ou na CPU ao usar o modo NoAgent. Não há nenhum custo associado ao uso do modo de gerenciamento noagent, o SQL Server não é reiniciado e um agente não é instalado na VM. 

Você pode exibir o modo atual de seu SQL Server agente IaaS usando Azure PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="installation"></a>Instalação

Registre sua VM SQL Server com a extensão do agente IaaS SQL Server para criar o _recurso_ de **máquina virtual do SQL** em sua assinatura, que é um recurso _separado_ do recurso de máquina virtual. Cancelar o registro da VM de SQL Server da extensão removerá o _recurso_ de **máquina virtual do SQL** , mas não descartará a máquina virtual real.

A implantação de uma imagem SQL Server VM do Azure Marketplace por meio do portal do Azure registra automaticamente a VM SQL Server com a extensão. No entanto, se você optar por instalar automaticamente o SQL Server em uma máquina virtual do Azure ou provisionar uma máquina virtual do Azure de um VHD personalizado, deverá registrar sua VM do SQL Server com a extensão IaaS do SQL para desbloquear os benefícios do recurso. 

O registro da extensão no modo leve copiará os binários, mas não instalará o agente na VM. O agente é instalado na VM quando a extensão é atualizada para o modo de gerenciamento completo. 

Há três maneiras de se registrar com a extensão: 
- [Automaticamente para todas as VMs atuais e futuras em uma assinatura](sql-agent-extension-automatic-registration-all-vms.md)
- [Manualmente para uma única VM](sql-agent-extension-manually-register-single-vm.md)
- [Manualmente para várias VMs em massa](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>Suporte à instância nomeada

A extensão do agente IaaS SQL Server funciona com uma instância nomeada do SQL Server se ela for a única SQL Server instância disponível na máquina virtual. A extensão não será instalada em VMs que têm várias instâncias nomeadas SQL Server se não houver nenhuma instância padrão na VM. 

Para usar uma instância nomeada do SQL Server, implante uma máquina virtual do Azure, instale uma única instância nomeada do SQL Server e registre-a com a [extensão IaaS do SQL](sql-agent-extension-manually-register-single-vm.md).

Como alternativa, para usar uma instância nomeada com uma imagem de SQL Server do Azure Marketplace, siga estas etapas: 

   1. Implante uma VM do SQL Server do Azure Marketplace. 
   1. [Cancele o registro](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) da VM SQL Server da extensão do agente IaaS do SQL. 
   1. Desinstale o SQL Server por completo de dentro da VM do SQL Server.
   1. Instale o SQL Server com uma instância nomeada dentro da VM do SQL Server. 
   1. [Registre a VM com a extensão do agente IaaS do SQL](sql-agent-extension-manually-register-single-vm.md#register-with-extension). 

## <a name="verify-status-of-extension"></a>Verificar o status da extensão

Use o portal do Azure ou Azure PowerShell para verificar o status da extensão. 

### <a name="azure-portal"></a>Portal do Azure

Verifique se a extensão está instalada no portal do Azure. 

Selecione **todas as configurações** no painel máquina virtual e, em seguida, selecione **extensões**. Você deverá ver a extensão **SqlIaasExtension** na lista.

![Status da extensão do agente IaaS SQL Server no portal do Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

Você também pode usar o cmdlet **Get-AzVMSqlServerExtension** do Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

O comando anterior confirma que o agente está instalado e fornece informações gerais sobre o status. Você pode obter informações específicas de status do backup automatizado e aplicação de patch usando os seguintes comandos:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>Limitações

A extensão do agente IaaS do SQL só dá suporte a: 

- VMs do SQL Server implantadas por meio do Azure Resource Manager. Não há suporte para as VMs do SQL Server implantadas por meio do modelo clássico. 
- VMs do SQL Server implantadas na nuvem pública ou do Azure Government. Não há suporte para implantações em outras nuvens privadas ou governamentais. 


## <a name="frequently-asked-questions"></a>Perguntas frequentes 

**Devo registrar minha VM do SQL Server provisionada de uma imagem do SQL Server no Azure Marketplace?**

Não. A Microsoft registra automaticamente as VMs provisionadas usando imagens do SQL Server no Azure Marketplace. O registro com a extensão será necessário somente se a VM *não* tiver sido provisionada a partir das imagens SQL Server no Azure Marketplace e SQL Server tiver sido autoinstalada.

**A extensão do agente IaaS do SQL está disponível para todos os clientes?** 

Sim. Os clientes devem registrar suas VMs SQL Server com a extensão se não usavam uma imagem de SQL Server do Azure Marketplace e, em vez disso, o SQL Server autoinstalado, ou se eles trouxeram seu VHD personalizado. As VMs de propriedade de todos os tipos de assinaturas (direta, Contrato Enterprise e provedor de soluções na nuvem) podem se registrar com a extensão do SQL IaaS Agent.

**Qual é o modo de gerenciamento padrão ao registrar com a extensão do agente IaaS do SQL?**

O modo de gerenciamento padrão quando você se registra com a extensão do agente IaaS do SQL é *leve*. Se a propriedade de gerenciamento de SQL Server não estiver definida quando você se registrar na extensão, o modo será definido como Lightweight e o serviço de SQL Server não será reiniciado. É recomendável registrar com a extensão do agente IaaS do SQL no modo leve primeiro e, em seguida, atualizar para completo durante uma janela de manutenção. Da mesma forma, o gerenciamento padrão também é leve ao usar o [recurso de registro automático](sql-agent-extension-automatic-registration-all-vms.md).

**Quais são os pré-requisitos para registrar com a extensão do agente IaaS do SQL?**

Não há nenhum pré-requisito para registrar com a extensão do agente IaaS do SQL diferente de ter SQL Server instalado na VM. Observe que, se a extensão do agente IaaS do SQL estiver instalada no modo completo, o serviço de SQL Server será reiniciado, portanto, isso é recomendado durante uma janela de manutenção.

**O registro com a extensão do agente IaaS do SQL instala um agente em minha VM?**

Sim, registrar com a extensão do agente IaaS do SQL no modo de gerenciamento completo instala um agente na VM. O registro no modo lightweight ou noagent não faz isso. 

O registro com a extensão do agente IaaS do SQL no modo leve copia apenas os *binários* de extensão do agente IaaS do SQL para a VM; ele não instala o agente. Esses binários são então usados para instalar o agente quando o modo de gerenciamento é atualizado para completo.


**Será registrado com a reinicialização da extensão do agente IaaS do SQL SQL Server em minha VM?**

Depende do modo especificado durante o registro. Se o modo lightweight ou noagent for especificado, o serviço de SQL Server não será reiniciado. No entanto, especificar o modo de gerenciamento como completo fará com que o serviço de SQL Server seja reiniciado. O recurso de registro automático registra suas VMs SQL Server no modo leve, a menos que a versão do Windows Server seja 2008, caso em que a VM SQL Server será registrada no modo noagent. 

**Qual é a diferença entre os modos de gerenciamento Lightweight e noagent ao registrar com a extensão do agente IaaS do SQL?** 

O modo de gerenciamento noagent é o único modo de gerenciamento disponível para SQL Server 2008 e SQL Server 2008 R2 no Windows Server 2008. Para todas as versões posteriores do Windows Server, os dois modos de gerenciamento disponíveis são leves e completos. 

O modo noagent requer SQL Server Propriedades de versão e edição a serem definidas pelo cliente. O modo leve consulta a VM para localizar a versão e a edição da instância do SQL Server.

**Posso registrar com a extensão do agente IaaS do SQL sem especificar o tipo de licença de SQL Server?**

Não. O tipo de licença SQL Server não é uma propriedade opcional quando você está registrando com a extensão do agente IaaS do SQL. Você precisa definir o tipo de licença de SQL Server como pré-pago ou Benefício Híbrido do Azure ao registrar com a extensão do agente IaaS do SQL em todos os modos de gerenciamento (noagent, Lightweight e Full). Se você tiver uma das versões gratuitas do SQL Server instalado, como Developer ou Evaluation Edition, deverá se registrar no licenciamento pago conforme o uso. Benefício Híbrido do Azure só está disponível para versões pagas do SQL Server como as edições Enterprise e Standard.

**Posso atualizar a extensão de IaaS SQL Server do modo noagent para o modo completo?**

Não. A atualização do modo de gerenciamento para completo ou leve não está disponível para o modo noagent. Essa é uma limitação técnica do Windows Server 2008. Você precisará atualizar o sistema operacional primeiro para o Windows Server 2008 R2 ou superior e, em seguida, poderá atualizar para o modo de gerenciamento completo. 

**Posso atualizar a extensão de IaaS do SQL Server do modo leve para o modo completo?**

Sim. A atualização do modo de gerenciamento de Lightweight para Full tem suporte por meio de Azure PowerShell ou o portal do Azure. Isso irá disparar uma reinicialização do serviço de SQL Server.

**Posso fazer downgrade da extensão SQL Server IaaS do modo completo para o modo noagent ou Lightweight Management?**

Não. Não há suporte para o downgrade do modo de gerenciamento de extensão de IaaS do SQL Server. Não é possível fazer downgrade do modo de gerenciamento completo para o modo lightweight ou noagent, e ele não pode ser desatualizado do modo leve para o modo noagent. 

Para alterar o modo de gerenciamento da capacidade de gerenciamento total, cancele o [registro](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) da VM SQL Server da extensão do agente IaaS do SQL descartando o _recurso_ de máquina virtual do SQL e registre novamente a VM SQL Server com a extensão do agente IaaS do SQL novamente em um modo de gerenciamento diferente.

**Posso registrar com a extensão do agente IaaS do SQL da portal do Azure?**

Não. O registro com a extensão do agente IaaS do SQL não está disponível no portal do Azure. O registro com a extensão do agente IaaS do SQL só tem suporte com o CLI do Azure ou Azure PowerShell. 

**Posso registrar uma VM com a extensão do agente IaaS do SQL antes que o SQL Server seja instalado?**

Não. Uma VM deve ter pelo menos uma instância de SQL Server (Mecanismo de Banco de Dados) para se registrar com êxito com a extensão do agente IaaS do SQL. Se não houver nenhuma instância do SQL Server na VM, o novo recurso Microsoft.SqlVirtualMachine estará em um estado de falha.

**Posso registrar uma VM com a extensão do agente IaaS do SQL se houver várias instâncias de SQL Server?**

Sim, desde que haja uma instância padrão na VM. A extensão do agente IaaS do SQL registrará apenas uma instância do SQL Server (Mecanismo de Banco de Dados). A extensão do agente IaaS do SQL registrará a instância de SQL Server padrão no caso de várias instâncias.

**Posso registrar uma instância de cluster de failover SQL Server com a extensão do agente IaaS do SQL?**

Sim. SQL Server instâncias de cluster de failover em uma VM do Azure podem ser registradas com a extensão do SQL IaaS Agent no modo leve. No entanto, instâncias de cluster de failover do SQL Server não podem ser atualizadas para o modo de gerenciamento completo.

**Posso registrar minha VM com a extensão do agente IaaS do SQL se um grupo de disponibilidade Always On estiver configurado?**

Sim. Não há restrições para registrar uma instância de SQL Server em uma VM do Azure com a extensão do agente IaaS do SQL se você estiver participando de uma configuração de grupo de disponibilidade Always On.

**Qual é o custo de registro com a extensão do agente IaaS do SQL ou com a atualização para o modo de gerenciamento completo?**

Nenhum. Não há nenhuma taxa associada ao registro com a extensão do agente IaaS do SQL ou com o uso de qualquer um dos três modos de gerenciamento. O gerenciamento de sua VM SQL Server com a extensão é totalmente livre. 

**Qual é o impacto no desempenho do uso dos diferentes modos de gerenciamento?**

Não há nenhum impacto ao usar os modos de gerenciamento *NoAgent* e *leve*. Há um impacto mínimo ao usar o modo de gerenciamento *completo* de dois serviços que são instalados no sistema operacional. Eles podem ser monitorados por meio do gerenciador de tarefas e vistos no console interno de serviços do Windows. 

Os dois nomes de serviço são:
- `SqlIaaSExtensionQuery` (Nome de exibição – `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (Nome de exibição – `Microsoft SQL Server IaaS Agent`)

**Como fazer remover a extensão?**

Remova a extensão ao [cancelar o registro](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) da VM SQL Server da extensão do agente IaaS do SQL. 

## <a name="next-steps"></a>Próximas etapas

Para instalar a extensão SQL Server IaaS para SQL Server em VMs do Azure, consulte os artigos para [instalação automática](sql-agent-extension-automatic-registration-all-vms.md), [VMs únicas](sql-agent-extension-manually-register-single-vm.md)ou [VMs em massa](sql-agent-extension-manually-register-vms-bulk.md).

Para obter mais informações sobre como executar o SQL Server em Máquinas Virtuais do Azure, confira [O que é o SQL Server nas Máquinas Virtuais do Azure?](sql-server-on-azure-vm-iaas-what-is-overview.md).
