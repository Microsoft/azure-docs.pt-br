---
title: Suporte a FTP ativo do firewall do Azure
description: Por padrão, o FTP ativo é desabilitado no firewall do Azure. Você pode habilitá-lo usando o PowerShell, a CLI e o modelo ARM.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: victorh
ms.openlocfilehash: adbc2a9eb6cd3b054df84911604143ddb711ad20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499128"
---
# <a name="azure-firewall-active-ftp-support"></a>Suporte a FTP ativo do firewall do Azure

Com o FTP ativo, o servidor FTP inicia a conexão de dados com a porta de dados do cliente FTP designado. Os firewalls na rede do lado do cliente normalmente bloqueiam uma solicitação de conexão externa a uma porta interna do cliente. Para obter mais informações, consulte [FTP ativo versus FTP passivo, uma explicação definitiva](https://slacksite.com/other/ftp.html).

Por padrão, o suporte a FTP ativo é desabilitado no firewall do Azure para proteger contra ataques de retorno de FTP usando o `PORT` comando FTP. No entanto, você pode habilitar o FTP ativo ao implantar usando Azure PowerShell, o CLI do Azure ou um modelo do Azure ARM.


## <a name="azure-powershell"></a>Azure PowerShell

Para implantar usando Azure PowerShell, use o `AllowActiveFTP` parâmetro. Para obter mais informações, consulte [criar um firewall com permitir FTP ativo](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-).

## <a name="azure-cli"></a>CLI do Azure

Para implantar usando o CLI do Azure, use o `--allow-active-ftp` parâmetro. Para obter mais informações, consulte [AZ Network firewall Create](/cli/azure/ext/azure-firewall/network/firewall#ext_azure_firewall_az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Modelo do ARM (Azure Resource Manager)

Para implantar usando um modelo do ARM, use o `AdditionalProperties` campo:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Para obter mais informações, consulte [Microsoft. Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Próximas etapas

Para saber como implantar um firewall do Azure, consulte [implantar e configurar o Firewall do Azure usando o Azure PowerShell](deploy-ps.md).