---
title: Registrar várias VMs do SQL no Azure com a extensão do SQL IaaS Agent
description: O registro em massa SQL Server VMs com a extensão do agente IaaS do SQL para melhorar a capacidade de gerenciamento.
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
ms.openlocfilehash: 558daede55f6563155d3f54e97d77c0a3ca4de59
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357204"
---
# <a name="register-multiple-sql-vms-in-azure-with-the-sql-iaas-agent-extension"></a>Registrar várias VMs do SQL no Azure com a extensão do SQL IaaS Agent
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como registrar suas máquinas virtuais (VMs) SQL Server em massa no Azure com a [extensão do agente IaaS do SQL](sql-server-iaas-agent-extension-automate-management.md) usando o `Register-SqlVMs` cmdlet Azure PowerShell. 


Este artigo ensina você a registrar SQL Server VMs manualmente em massa. Como alternativa, você pode registrar [todas as VMs de SQL Server automaticamente ou as](sql-agent-extension-automatic-registration-all-vms.md) [VMs SQL Server individuais manualmente](sql-agent-extension-manually-register-single-vm.md). 

## <a name="overview"></a>Visão geral

O cmdlet `Register-SqlVMs` pode ser usado para registrar todas as máquinas virtuais em uma determinada lista de assinaturas, em grupos de recursos ou em uma lista de máquinas virtuais específica. O cmdlet registrará as máquinas virtuais no [modo de gerenciamento lightweight_](sql-server-iaas-agent-extension-automate-management.md#management-modes)e, em seguida, irá gerar um [relatório e um arquivo de log](#output-description). 

O processo de registro não tem risco, não tem nenhum tempo de inatividade e não reiniciará o serviço SQL Server ou a máquina virtual. 

## <a name="prerequisites"></a>Pré-requisitos

Para registrar sua VM SQL Server com a extensão, você precisará do seguinte: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/) que foi [registrada com o provedor **Microsoft. SqlVirtualMachine**](sql-agent-extension-manually-register-single-vm.md#register-subscription-with-rp) e contém máquinas virtuais SQL Server não registradas. 
- As credenciais de cliente usadas para registrar as máquinas virtuais existem em qualquer uma das seguintes funções do Azure: **colaborador de máquina virtual**, **colaborador** ou **proprietário**. 
- A versão mais recente do [AZ PowerShell (mínimo de 5,0)](/powershell/azure/new-azureps-module-az). 


## <a name="get-started"></a>Introdução

Antes de continuar, primeiro você deve criar uma cópia local do script, importá-la como um módulo do PowerShell e conectá-la ao Azure. 

### <a name="create-the-script"></a>Criar o script

Para criar o script, copie o [script completo](#full-script) no fim deste artigo e salve-o localmente como `RegisterSqlVMs.psm1`. 

### <a name="import-the-script"></a>Importar o script

Depois de criar o script, você poderá importá-lo como um módulo no terminal do PowerShell. 

Abra um terminal administrativo do PowerShell e acesse o local em que o arquivo `RegisterSqlVMs.psm1` foi salvo. Execute o seguinte cmdlet do PowerShell para importar o script como um módulo: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Conectar-se ao Azure

Use o seguinte cmdlet do PowerShell para se conectar ao Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-a-list-of-subscriptions"></a>Todas as VMs em uma lista de assinaturas 

Use o seguinte cmdlet para registrar todas as máquinas virtuais do SQL Server em uma lista de assinaturas:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Saída de exemplo: 

```
Number of subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>Todas as VMs em uma única assinatura

Use o seguinte cmdlet para registrar todas as máquinas virtuais do SQL Server em uma assinatura única: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

Saída de exemplo:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>Todas as VMs em vários grupos de recursos

Use o seguinte cmdlet para registrar todas as máquinas virtuais do SQL Server em vários grupos de recursos em uma assinatura única:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

Saída de exemplo:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>Todas as VMs em um grupo de recursos

Use o seguinte cmdlet para registrar todas as máquinas virtuais do SQL Server em um grupo de recursos único: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

Saída de exemplo:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-a-single-resource-group"></a>VMs específicas em um único grupo de recursos

Use o seguinte cmdlet para registrar máquinas virtuais do SQL Server específicas em um grupo de recursos único:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

Saída de exemplo:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="a-specific-vm"></a>Uma VM específica

Use o seguinte cmdlet para registrar uma máquina virtual do SQL Server específica: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

Saída de exemplo:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>Descrição da saída

Um relatório e um arquivo de log são gerados sempre que o cmdlet `Register-SqlVMs` é usado. 

### <a name="report"></a>Relatório

O relatório é gerado como um arquivo `.txt` chamado `RegisterSqlVMScriptReport<Timestamp>.txt`, em que o carimbo de data/hora é o horário em que o cmdlet foi iniciado. O relatório lista os seguintes detalhes:

| **Valor de saída** | **Descrição** |
| :--------------  | :-------------- | 
| Ocorreu falha no registro do número de assinaturas porque você não tem acesso ou as credenciais estão incorretas | Isso fornece o número e a lista de assinaturas que tiveram problemas com a autenticação fornecida. O erro detalhado pode ser encontrado no log ao pesquisar pela ID da assinatura. | 
| Número de assinaturas que não puderam ser tentadas porque não estão registradas no provedor de recursos | Esta seção contém a contagem e a lista de assinaturas que não foram registradas para a extensão do agente IaaS do SQL. |
| Total de VMs encontradas | A contagem de máquinas virtuais encontradas no escopo dos parâmetros passados para o cmdlet. | 
| VMs já registradas | A contagem de máquinas virtuais que foram ignoradas porque já foram registradas com a extensão. |
| Número de VMs registradas com êxito | A contagem de máquinas virtuais que foram registradas com êxito após a execução do cmdlet. Lista as máquinas virtuais registradas no formato `SubscriptionID, Resource Group, Virtual Machine`. | 
| Número de VMs com falha ao registrar devido a um erro | Contagem de máquinas virtuais com falha ao registrar devido a algum erro. Os detalhes do erro podem ser encontrados no arquivo de log. | 
| Número de VMs ignoradas porque a VM ou o agente convidado na VM não estava em execução | A contagem e a lista de máquinas virtuais que não puderam ser registradas porque a máquina virtual ou o agente convidado na máquina virtual não estava em execução. Será possível tentar novamente depois de iniciar a máquina virtual ou o agente convidado. Os detalhes podem ser encontrados no arquivo de log. |
| Número de VMs ignoradas porque não estavam executando o SQL Server no Windows | Contagem de máquinas virtuais que foram ignoradas porque não estavam executando o SQL Server ou não eram máquinas virtuais do Windows. As máquinas virtuais são listadas no formato `SubscriptionID, Resource Group, Virtual Machine`. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Log 

Os erros são registrados no arquivo de log chamado `VMsNotRegisteredDueToError<Timestamp>.log`, em que o carimbo de data/hora é a hora em que o script foi iniciado. Se o erro ocorrer em nível de assinatura, o log conterá a ID da assinatura e a mensagem de erro separadas por vírgulas. Se o erro ocorrer no registro da máquina virtual, o log conterá a ID da assinatura, o nome do grupo de recursos, o nome da máquina virtual, o código de erro e a mensagem separados por vírgulas. 

## <a name="remarks"></a>Comentários

Ao registrar SQL Server VMs com a extensão usando o script fornecido, considere o seguinte:

- O registro com a extensão requer um agente convidado em execução na VM SQL Server. As imagens do Windows Server 2008 não têm um agente convidado, portanto, ocorrerá falha nessas máquinas virtuais e será necessário registrá-las manualmente usando o [modo de gerenciamento NoAgent](sql-server-iaas-agent-extension-automate-management.md#management-modes).
- Há uma lógica de repetição interna para solucionar erros transparentes. Se a máquina virtual for registrada com êxito, a operação será rápida. No entanto, em caso de falha do registro, cada máquina virtual será testada novamente.  Portanto, o processo de registro requer um tempo significativo para ser concluído, embora o requisito de tempo real dependa do tipo e da quantidade de erros. 

## <a name="full-script"></a>Script completo

Para obter o script completo no GitHub, consulte [registro em massa SQL Server VMs com AZ PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Copie o script completo e salve-o como `RegisterSqLVMs.psm1`.

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Perguntas frequentes sobre o SQL Server em uma VM do Windows](frequently-asked-questions-faq.md)
* [Diretrizes de preço para o SQL Server em uma VM do Windows](pricing-guidance.md)
* [Notas sobre a versão do SQL Server em uma VM do Windows](../../database/doc-changes-updates-release-notes.md)
