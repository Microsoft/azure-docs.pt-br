---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: a9dbedd8516f3a3a592c7fd4f4f5563011d6c6db
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99490997"
---
#### <a name="requirements-for-key-vault-firewall"></a>Requisitos para Key Vault firewall

Se [Key Vault firewall](../articles/key-vault/general/network-security.md) estiver habilitado no cofre de chaves, os requisitos adicionais a seguir serão:

* Você deve usar a identidade gerenciada **atribuída pelo sistema** da instância de gerenciamento de API para acessar o cofre de chaves.
* No Key Vault firewall, habilite a opção **permitir que os serviços confiáveis da Microsoft ignorem esse firewall** .

#### <a name="virtual-network-requirements"></a>Requisitos de rede virtual

Se a instância de gerenciamento de API for implantada em uma rede virtual, defina também as seguintes configurações de rede:

* Habilite um [ponto de extremidade de serviço](../articles/key-vault/general/overview-vnet-service-endpoints.md) para Azure Key Vault na sub-rede de gerenciamento de API.
* Configure uma regra de NSG (grupo de segurança de rede) para permitir o tráfego de saída para as [marcas de serviço](../articles/virtual-network/service-tags-overview.md)AzureKeyVault e AzureActiveDirectory. 

Para obter detalhes, consulte detalhes de configuração de rede em [conectar-se a uma rede virtual](../articles/api-management/api-management-using-with-vnet.md#-common-network-configuration-issues).