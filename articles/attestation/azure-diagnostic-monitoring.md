---
title: Monitoramento de diagnóstico do Azure – Atestado do Azure
description: Monitoramento de diagnóstico do Azure para Atestado do Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d01e7817906927295591353b710afe2899aacdf1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726471"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Como configurar o diagnóstico com o ponto de extremidade TPM (Trusted Platform Module) do Atestado do Azure

Os [logs de plataforma](../azure-monitor/essentials/platform-logs-overview.md) no Azure, incluindo logs de recursos e log de atividades do Azure, apresentam informações detalhadas de diagnóstico e auditoria para recursos do Azure e para a plataforma do Azure da qual eles dependem. As [métricas da plataforma](../azure-monitor/essentials/data-platform-metrics.md) são coletadas por padrão e normalmente armazenadas no banco de dados de métricas do Azure Monitor. Este artigo fornece detalhes sobre como criar e definir configurações de diagnóstico para enviar métricas de plataforma e logs de plataforma para diferentes destinos. 

O serviço de ponto de extremidade TPM está habilitado com a configuração de diagnóstico e pode ser usado para monitorar a atividade. Para configurar [Monitoramento do Azure](../azure-monitor/overview.md) para o ponto de extremidade de serviço TPM usando o PowerShell, siga as etapas abaixo. 

Configure o serviço do Atestado do Azure. 

[Configurar o Atestado do Azure com o Azure PowerShell](./quickstart-powershell.md)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
Os logs de atividades podem ser encontrados na seção Contêineres da conta de armazenamento. Informações detalhadas podem ser encontradas em [Coletar logs de recursos de um recurso do Azure e analisá-los com o Azure Monitor – Azure Monitor](../azure-monitor/essentials/tutorial-resource-logs.md)
