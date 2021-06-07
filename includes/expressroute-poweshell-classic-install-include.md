---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018131"
---
Instale as versões mais recentes dos módulos PowerShell do SM (Gerenciamento de Serviços) do Azure e do módulo ExpressRoute. Você não pode usar o ambiente do Azure Cloud Shell para executar módulos SM.

1. Use as instruções contidas no artigo [Instalação do módulo de gerenciamento de serviços](/powershell/azure/servicemanagement/install-azure-ps) para instalar o Módulo de Gerenciamento de Serviços do Azure. Se você tiver o módulo AZ ou RM já instalado, não se esqueça de usar '-AllowClobber'.
2. Importe os módulos instalados. Ao usar o exemplo a seguir, ajuste o caminho para refletir a localização e a versão dos módulos do PowerShell instalados.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Para entrar na conta do Azure, abra o console do PowerShell com privilégios elevados e conecte-se à conta. Use o seguinte exemplo para obter ajuda ao se conectar usando o módulo de gerenciamento de serviços:

   ```powershell
   Add-AzureAccount
   ```