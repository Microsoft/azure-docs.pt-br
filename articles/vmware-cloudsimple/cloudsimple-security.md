---
title: Solução do Azure VMware por CloudSimple-Security para serviços CloudSimples
description: Descreve os modelos de responsabilidade compartilhada para segurança dos serviços CloudSimples
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d62eec72a884470ade03897a0533e62c6f1b0f71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898786"
---
# <a name="cloudsimple-security-overview"></a>Visão geral da segurança do CloudSimple

Este artigo fornece uma visão geral de como a segurança é implementada na solução do Azure VMware por meio do serviço CloudSimple, da infraestrutura e do datacenter. Você aprende sobre proteção de dados e segurança, segurança de rede e como as vulnerabilidades e os patches são gerenciados.

## <a name="shared-responsibility"></a>Responsabilidade compartilhada

A solução Azure VMware por CloudSimple usa um modelo de responsabilidade compartilhada para segurança. A segurança confiável na nuvem é obtida por meio das responsabilidades compartilhadas de clientes e da Microsoft como um provedor de serviços. Essa matriz de responsabilidade fornece maior segurança e elimina pontos únicos de falha.

## <a name="azure-infrastructure"></a>Infraestrutura do Azure

As considerações de segurança de infraestrutura do Azure incluem os data centers e o local do equipamento.

### <a name="datacenter-security"></a>Segurança do datacenter

A Microsoft tem uma divisão completa dedicada à criação, à criação e à operação de instalações físicas que dão suporte ao Azure. Essa equipe está investida em manter a segurança física de última geração. Para obter detalhes sobre segurança física, consulte [instalações do Azure, local e segurança física](../security/fundamentals/physical-security.md).

### <a name="equipment-location"></a>Local do equipamento

O equipamento de hardware bare-metal que executa suas nuvens privadas é hospedado em locais de datacenter do Azure.  Os compartimentos em que esse equipamento se encontra, exigem a autenticação de dois fatores baseada em biométrica para obter acesso.

## <a name="dedicated-hardware"></a>Hardware dedicado

Como parte do serviço CloudSimple, todos os clientes do CloudSimple recebem hosts bare-metal dedicados com discos anexados locais que são fisicamente isolados de outro hardware de locatário. Um hipervisor ESXi com vSAN é executado em todos os nós. Os nós são gerenciados por meio do VMware vCenter e do NSX dedicados ao cliente. O compartilhamento de hardware entre locatários fornece uma camada adicional de isolamento e proteção de segurança.

## <a name="data-security"></a>Segurança de dados

Os clientes mantêm o controle e a propriedade de seus dados. A administração de dados dos dados do cliente é responsabilidade do cliente.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Proteção de dados para dados em repouso e dados em movimento em redes internas

Para dados em repouso no ambiente de nuvem privada, você pode usar a criptografia vSAN. a criptografia vSAN funciona com os servidores de gerenciamento de chaves externas (KMS) certificados pela VMware em sua própria rede virtual ou local.  Você controla as chaves de criptografia de dados por conta própria. Para dados em movimento na nuvem privada, o vSphere dá suporte à criptografia de dados pela conexão para todo o tráfego de VMkernel (incluindo o tráfego do vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Proteção de dados para dados que são necessários para se mover por meio de redes públicas

Para proteger os dados que se movem por meio de redes públicas, você pode criar túneis de VPN IPsec e TLS para suas nuvens privadas. Há suporte para métodos de criptografia comuns, incluindo o AES de 128 bytes e de 256 bytes. Os dados em trânsito (incluindo autenticação, acesso administrativo e dados do cliente) são criptografados com mecanismos de criptografia padrão (SSH, TLS 1,2 e RDP seguro). A comunicação que transporta informações confidenciais usa os mecanismos de criptografia padrão.

### <a name="secure-disposal"></a>Eliminação segura

Se o serviço CloudSimple expirar ou for encerrado, você será responsável por remover ou excluir seus dados. O CloudSimple irá cooperar com você para excluir ou retornar todos os dados do cliente, conforme fornecido no contrato do cliente, exceto até a extensão CloudSimple é exigida pela lei aplicável para reter alguns ou todos os dados pessoais. Se necessário, para manter todos os dados pessoais, o CloudSimple arquivará os dados e implementará medidas razoáveis para evitar que os dados do cliente sejam processados.

### <a name="data-location"></a>Local dos dados

Ao configurar suas nuvens privadas, você escolhe a região do Azure onde elas serão implantadas. Os dados da máquina virtual do VMware não são movidos desse datacenter físico, a menos que você execute a migração de dados ou o backup de dados externo. Você também pode hospedar cargas de trabalho e armazenar dados em várias regiões do Azure, se apropriado para suas necessidades.

Os dados do cliente residentes em nós hiperconvergentes de nuvem privada não atravessam locais sem a ação explícita do administrador de locatários. É sua responsabilidade implementar suas cargas de trabalho de uma maneira altamente disponível.

### <a name="data-backups"></a>Backups de dados

O CloudSimple não faz backup ou arquiva dados do cliente. O CloudSimple executa backup periódico de dados do vCenter e do NSX para fornecer alta disponibilidade de servidores de gerenciamento. Antes do backup, todos os dados são criptografados na fonte do vCenter usando APIs do VMware. Os dados criptografados são transportados e armazenados no blob do Azure. As chaves de criptografia para backups são armazenadas em um cofre gerenciado CloudSimple altamente seguro em execução na rede virtual CloudSimple no Azure.

## <a name="network-security"></a>Segurança de rede

A solução CloudSimple se baseia em camadas de segurança de rede.

### <a name="azure-edge-security"></a>Segurança do Azure Edge

Os serviços CloudSimples são criados sobre a segurança de rede base fornecida pelo Azure. O Azure aplica técnicas de defesa aprofundada para detecção e resposta oportuna a ataques baseados em rede associados a padrões de tráfego de entrada ou saída anormais e ataques de DDoS (negação de serviço distribuído). Esse controle de segurança se aplica a ambientes de nuvem privada e ao software do plano de controle desenvolvido pelo CloudSimple.

### <a name="segmentation"></a>Segmentação

O serviço CloudSimple tem redes de camada 2 separadas logicamente que restringem o acesso às suas próprias redes privadas em seu ambiente de nuvem privada. Você pode proteger ainda mais suas redes de nuvem privada usando um firewall. O portal do CloudSimple permite que você defina as regras de controle de tráfego de rede do ovo e do NS para todo o tráfego de rede, incluindo tráfego de nuvem interna, tráfego de nuvem interprivate, tráfego geral para a Internet e tráfego de rede para o local por VPN IPsec ou conexão de ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Gerenciamento de vulnerabilidades e patches

O CloudSimple é responsável pela aplicação de patch de segurança periódica do software VMware gerenciado (ESXi, vCenter e NSX).

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

Os clientes podem se autenticar em sua conta do Azure (no Azure AD) usando a autenticação multifator ou SSO como preferencial. No portal do Azure, você pode iniciar o portal do CloudSimple sem reinserir as credenciais.

O CloudSimple dá suporte à configuração opcional de uma origem de identidade para o vCenter de nuvem privada. Você pode usar uma [fonte de identidade local](set-vcenter-identity.md), uma nova fonte de identidade para a nuvem privada ou o [Azure ad](azure-ad.md).

Por padrão, os clientes recebem os privilégios necessários para as operações diárias do vCenter na nuvem privada. Esse nível de permissão não inclui acesso administrativo ao vCenter. Se o acesso administrativo for temporariamente necessário, você poderá [escalonar seus privilégios](escalate-private-cloud-privileges.md) por um período limitado enquanto concluir as tarefas administrativas.
