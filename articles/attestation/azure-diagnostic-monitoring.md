---
title: Monitoramento de diagnóstico do Azure para Atestado do Azure
description: Monitoramento de diagnóstico do Azure para Atestado do Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d2773be4bc67e125c18d5d38c951685e4f4fceaf
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168341"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Configurar o diagnóstico com o ponto de extremidade TPM (Trusted Platform Module) do Atestado do Azure

Este artigo ajuda você a criar e definir configurações de diagnóstico para enviar métricas de plataforma e logs de plataforma para diferentes destinos. Os [logs de plataforma](/azure/azure-monitor/platform/platform-logs-overview) no Azure, incluindo logs de recursos e log de atividades do Azure, apresentam informações detalhadas de diagnóstico e auditoria para recursos do Azure e para a plataforma do Azure da qual eles dependem. As [métricas da plataforma](/azure/azure-monitor/platform/data-platform-metrics) são coletadas por padrão e armazenadas no banco de dados de métricas do Azure Monitor.

Para começar, [configure o Atestado do Azure com o Azure PowerShell](quickstart-powershell.md).

O serviço de ponto de TPM (Trusted Platform Module) está habilitado nas configurações de diagnóstico e pode ser usado para monitorar a atividade. Configure o [Monitoramento do Azure](/azure/azure-monitor/overview) para o ponto de extremidade do serviço TPM usando o código a seguir.

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

Os logs de atividades podem ser encontrados na seção **Contêineres** da conta de armazenamento. Para obter mais informações, confira [Coletar e analisar logs de recursos de um recurso do Azure](/azure/azure-monitor/learn/tutorial-resource-logs).
