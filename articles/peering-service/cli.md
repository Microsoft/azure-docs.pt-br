---
title: Registrar uma conexão de visualização do serviço de emparelhamento usando o CLI do Azure
description: Saiba como registrar uma conexão de serviço de emparelhamento usando o CLI do Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: e7b696ba052b2aca9e14628327c07275845607ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540579"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Registrar uma conexão de serviço de emparelhamento usando o CLI do Azure

O Serviço de Emparelhamento do Azure é um serviço de rede que aprimora a conectividade do cliente com os serviços em nuvem da Microsoft, como o Microsoft 365, o Dynamics 365, os serviços de SaaS (software como serviço), o Azure ou qualquer serviço da Microsoft acessível por meio da Internet pública. Neste artigo, você aprenderá a registrar uma conexão de serviço de emparelhamento usando o CLI do Azure.

- Este artigo exige a versão 2.0.28 ou posterior da CLI do Azure. Execute [az version](/cli/azure/reference-index#az_version) para localizar a versão e as bibliotecas dependentes que estão instaladas. Para fazer a atualização para a versão mais recente, execute [az upgrade](/cli/azure/reference-index#az_upgrade).

## <a name="prerequisites"></a>Pré-requisitos 

Você deve ter o seguinte:

### <a name="azure-account"></a>Conta do Azure

Você deve ter uma conta válida e ativa do Microsoft Azure. Essa conta é necessária para configurar a conexão do Serviço de Emparelhamento. O Serviço de Emparelhamento é um recurso dentro das assinaturas do Azure.

### <a name="connectivity-provider"></a>Provedor de conectividade

Você pode trabalhar com um provedor de serviços de Internet ou um parceiro do Exchange da Internet para obter o Serviço de Emparelhamento para conectar sua rede com a rede da Microsoft.

Verifique se os provedores de conectividade são parceiros da Microsoft.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Este artigo exige a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

### <a name="1-select-your-subscription"></a>1. Selecione sua assinatura

Selecione a assinatura para a qual você deseja registrar a conexão de serviço de emparelhamento.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

Se você ainda não tiver um grupo de recursos, deverá criar um antes de registrar a conexão do serviço de emparelhamento. Você pode criar um grupo de recursos executando o seguinte comando:

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. Registre sua assinatura com o provedor de recursos e o sinalizador de recurso

Antes de prosseguir para as etapas de registro da conexão de serviço de emparelhamento usando o CLI do Azure, registre sua assinatura com o provedor de recursos e o sinalizador de recurso usando o CLI do Azure. Os comandos CLI do Azure são especificados aqui:

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. registrar a conexão do serviço de emparelhamento

Registre a conexão do serviço de emparelhamento usando o seguinte conjunto de comandos por meio do CLI do Azure. Este exemplo registra a conexão de serviço de emparelhamento chamada myPeeringService.

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. registrar o prefixo

Registre o prefixo fornecido pelo provedor de conectividade executando os comandos a seguir por meio do CLI do Azure. Este exemplo registra o prefixo chamado myPrefix.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a conexão de serviço de emparelhamento, consulte [conexão de serviço de emparelhamento](connection.md).
- Para saber mais sobre telemetria de conexão do Serviço de Emparelhamento, confira [Telemetria de conexão do Serviço de Emparelhamento](connection-telemetry.md).
- Para medir a telemetria, confira [Medir telemetria de conexão](measure-connection-telemetry.md).
- Para registrar a conexão usando o Azure PowerShell, confira [Registrar uma conexão do Serviço de Emparelhamento – Azure PowerShell](powershell.md).
- Para registrar a conexão usando o portal do Azure, consulte [registrar uma conexão de serviço de emparelhamento-portal do Azure](azure-portal.md).
