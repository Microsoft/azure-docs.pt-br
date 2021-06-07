---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: cdf525499a7229fbb48060fc7846761b7422e364
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501239"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |O Banco de Dados do Azure para MariaDB permite que você escolha a opção de redundância para seu servidor de banco de dados. Ele pode ser definido como um armazenamento de backup com redundância geográfica no qual os dados não são armazenados apenas na região em que o servidor está hospedado, mas também é replicado para uma região emparelhada para dar a opção de recuperação em caso de falha de região. A configuração do armazenamento com redundância geográfica para backup só é permitida durante a criação do servidor. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[O servidor MariaDB deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |As regras de firewall baseadas em rede virtual são usadas para habilitar o tráfego de uma sub-rede específica para o Banco de Dados do Azure para MariaDB e ainda garantir que o tráfego permaneça dentro do limite do Azure. Essa política oferece uma forma de auditar se o Banco de Dados do Azure para MariaDB tem um ponto de extremidade de serviço de rede virtual em uso. |AuditIfNotExists, desabilitado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[O ponto de extremidade privado deve ser habilitado para servidores MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |As conexões de ponto de extremidade privado impõem comunicações seguras habilitando a conectividade privada ao Banco de Dados do Azure para MariaDB. Configure uma conexão de ponto de extremidade privado para habilitar o acesso ao tráfego proveniente somente de redes conhecidas e impedir o acesso de todos os outros endereços IP, incluindo no Azure. |AuditIfNotExists, desabilitado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[O acesso à rede pública deve ser desabilitado para servidores MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Desabilitar a propriedade de acesso à rede pública aprimora a segurança e garantir que o Banco de Dados do Azure para MariaDB só possa ser acessado de um ponto de extremidade privado. Essa configuração desabilita estritamente o acesso de qualquer espaço de endereço público fora do intervalo de IP do Azure e nega todos os logons que correspondam a regras de firewall baseadas em IP ou em rede virtual. |Audit, desabilitado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
