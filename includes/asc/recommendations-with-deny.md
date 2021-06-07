---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 03/21/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 5fc36e327a9530105182f0a23b3ef22ab324e01c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803231"
---
- O acesso às contas de armazenamento com configurações de firewall e de rede virtual deve ser restrito
- As variáveis da conta de automação devem ser criptografadas
- O Cache do Azure para Redis deve residir em uma rede virtual
- As contas do Azure Cosmos DB devem usar chaves gerenciadas pelo cliente para criptografar os dados inativos
- Os workspaces do Azure Machine Learning devem ser criptografados com uma CMK (chave gerenciada pelo cliente)
- O Azure Spring Cloud deve usar injeção de rede
- As contas dos Serviços Cognitivos devem habilitar a criptografia de dados com uma CMK (chave gerenciada pelo cliente)
- Deverão ser aplicados limites de memória e CPU ao contêiner
- As imagens de contêiner deverão ser implantadas somente se forem de registros confiáveis
- Os registros de contêiner devem ser criptografados com uma CMK (chave gerenciada pelo cliente)
- Os contêineres com elevação de privilégio deverão ser evitados
- Os contêineres que compartilham namespaces de host confidenciais deverão ser evitados
- Os contêineres deverão escutar somente em portas permitidas
- Um sistema de arquivos raiz imutável (somente leitura) deverá ser aplicado aos contêineres
- As chaves do Key Vault devem ter uma data de validade
- Os segredos do Key Vault devem ter uma data de validade
- Os cofres de chaves devem ter a proteção contra limpeza habilitada
- Os cofres de chaves devem ter a exclusão temporária habilitada
- Deverão ser aplicadas aos contêineres funcionalidades do Linux com privilégios mínimos
- Apenas conexões seguras com o Cache Redis devem ser habilitadas
- As ações para substituir ou desabilitar o perfil do AppArmor de contêineres deverão ser restritas
- Os contêineres com privilégios deverão ser evitados
- Executar contêineres como usuário raiz deverá ser evitado
- A transferência segura para contas de armazenamento deve ser habilitada
- A propriedade ClusterProtectionLevel dos clusters do Service Fabric deve ser definida como EncryptAndSign
- Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente
- Os serviços deverão escutar somente em portas permitidas
- O acesso público da conta de armazenamento não deve ser permitido
- As contas de armazenamento devem ser migradas para os novos recursos do Azure Resource Manager
- As contas de armazenamento devem restringir o acesso à rede usando regras de rede virtual
- Usar redes e portas do host deverá ser restrito
- O uso de montagens de volume do Pod HostPath deve ser restrito a uma lista conhecida para restringir o acesso ao nó de contêineres comprometidos
- O período de validade dos certificados armazenados no Azure Key Vault não deve exceder 12 meses
- As máquinas virtuais devem ser migradas para os novos recursos do Azure Resource Manager
- O WAF (Firewall do Aplicativo Web) deve ser habilitado para o Gateway de Aplicativo
- O WAF (Firewall de Aplicativo Web) deve ser habilitado para o serviço do Azure Front Door Service

