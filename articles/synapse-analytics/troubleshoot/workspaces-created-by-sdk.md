---
title: 'Solução de problemas: espaços de trabalho criados pelo SDK não podem iniciar o Synapse Studio'
description: Etapas para resolver espaços de trabalho criados pelo SDK não é possível iniciar o Synapse Studio
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: workspace
ms.date: 11/24/2020
ms.openlocfilehash: f5d0aae975d476da47510a1f2fd164cbc5f32945
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465915"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>Solucionar os espaços de trabalho do Azure Synapse Analytics criados usando o SDK

Este artigo fornece etapas de solução de problemas para iniciar o Synapse Studio de um espaço de trabalho do Synapse que foi criado com um Software Development Kit (SDK).


## <a name="prerequisites"></a>Pré-requisitos

- Espaço de trabalho Synapse criado usando o SDK

## <a name="workaround"></a>Solução alternativa

Para iniciar o Synapse Studio no espaço de trabalho criado pelo SDK, conclua as seguintes etapas: 
  1.    Crie um workspace executando `az synapse workspace create`.
  2.    Extraia a ID da identidade gerenciada executando `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`.
  3.    Conceda a função de colaborador de dados de blob de armazenamento à conta de armazenamento de identidade gerenciada executando ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.`
  4.    Adicione regra de firewall executando ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `.

## <a name="next-steps"></a>Próximas etapas

* [O que é o Azure Synapse](../overview-what-is.md)
* [Introdução](../get-started.md)
