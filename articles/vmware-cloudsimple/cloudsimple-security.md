---
title: Segurança para serviços CloudSimple
description: Descreve os modelos de responsabilidade compartilhada para a segurança dos serviços de CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/27/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 325915aae43c905236910382f650730a6daa127a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595338"
---
# <a name="cloudsimple-security-overview"></a>Visão geral de segurança CloudSimple

Este artigo fornece uma visão geral de como segurança é implementada na solução de VMware do Azure por serviço CloudSimple, infraestrutura e datacenter. Você aprenderá sobre a proteção de dados e segurança, segurança de rede e como vulnerabilidades e patches são gerenciados.

## <a name="shared-responsibility"></a>Responsabilidade compartilhada

Solução de VMware do Azure por CloudSimple usa um modelo de responsabilidade compartilhada para a segurança. Segurança confiável na nuvem é obtida por meio das responsabilidades compartilhadas de clientes e a Microsoft como um provedor de serviços. Esta matriz de responsabilidade fornece maior segurança e elimina pontos únicos de falha.

## <a name="azure-infrastructure"></a>Infraestrutura do Azure 

Considerações de segurança de infraestrutura do Azure incluem o local de data centers e equipamento.

### <a name="datacenter-security"></a>Segurança de Datacenter 

A Microsoft tem uma divisão inteira dedicada a projetar, compilar e operar as instalações físicas que dão suporte ao Azure. Essa equipe está investida em manter a segurança física de última geração. Para obter mais informações sobre a segurança física, consulte [recursos do Azure, o local e a segurança física](https://docs.microsoft.com/azure/security/azure-physical-security).

### <a name="equipment-location"></a>Localização do equipamento

O equipamento de hardware bare-metal que executa suas nuvens privadas é hospedado em locais de datacenter do Azure. Autenticação de dois fatores com base em de biometria é necessária para acessar os compartimentos no qual reside o equipamento.

## <a name="dedicated-hardware"></a>Hardware dedicado

Como parte do serviço CloudSimple, todos os clientes CloudSimple obtém hosts de bare-metal dedicados com discos anexados locais que estão fisicamente isolados de outros hardwares de locatário. Um hipervisor de ESXi com vSAN é executado em cada nó. Os nós são gerenciados por meio do VMware, vCenter e NSX dedicada ao cliente. Compartilhar o hardware entre locatários não fornece uma camada adicional de proteção de segurança e isolamento.

## <a name="data-security"></a>Segurança de dados

Clientes mantêm o controle e a propriedade de seus dados. Administração de dados dos dados do cliente é de responsabilidade do cliente.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Proteção de dados para dados em repouso e dados em movimento em redes internas

Para dados em repouso no ambiente de nuvem privada, você pode usar criptografia vSAN. criptografia vSAN funciona com servidores de gerenciamento de chaves externas VMware certificada (KMS) em sua própria rede virtual ou local. Você controla as chaves de criptografia de dados por conta própria. Para dados em movimento na nuvem particular, o vSphere dá suporte à criptografia de dados durante a transmissão para todo tráfego VMkernel, que inclui o tráfego de vMotion.

### <a name="data-protection-for-data-thats-required-to-move-through-public-networks"></a>Proteção de dados para os dados necessários para se mover pelas redes públicas

Para proteger os dados que se movimentam através de redes públicas, você pode criar IPsec e SSL VPN túneis para suas nuvens privadas. Métodos de criptografia comum têm suporte, incluindo AES 128 e 256 bytes. Dados em trânsito, o que inclui autenticação, acesso administrativo e os dados do cliente, são criptografados com mecanismos de criptografia padrão, como RDP seguro, TLS 1.2 e SSH. A comunicação que transporta informações confidenciais utiliza os mecanismos de criptografia padrão.

### <a name="secure-disposal"></a>Eliminação segura 

Se seu serviço CloudSimple expira ou for encerrado, você é responsável por remover ou excluir seus dados. CloudSimple coopera com você para excluir ou retornar todos os dados do cliente, conforme fornecido no contrato do cliente, exceto para a extensão CloudSimple é exigido pela lei aplicável, para manter alguns ou todos os dados pessoais. Se necessário, para manter todos os dados pessoais, CloudSimple arquiva os dados e implementa medidas razoáveis para impedir que os dados do cliente nenhum processamento adicional.

### <a name="data-location"></a>Local dos dados

Quando você configura suas nuvens privadas, você pode escolher a região do Azure em que são implantados. Dados da máquina virtual VMware não são movidos de naquele datacenter físico, a menos que você executar a migração de dados ou backup de dados externo. Você também pode hospedar cargas de trabalho e armazenar dados em várias regiões do Azure se for apropriado para suas necessidades.

Os dados do cliente que é residentes em nós de hiperconvergente de nuvem privada não atravessam locais sem ação explícita do administrador de locatários. É sua responsabilidade implementar suas cargas de trabalho de maneira altamente disponível.

### <a name="data-backups"></a>Backups de dados
CloudSimple não fazer backup ou arquivar os dados do cliente. CloudSimple executar backup periódico do vCenter e dados NSX para fornecer alta disponibilidade de servidores de gerenciamento. Antes do backup, todos os dados são criptografados na origem do vCenter por meio de APIs do VMware. Os dados criptografados são transportados e armazenados em um blob do Azure. Chaves de criptografia para backups são armazenadas em um cofre gerenciado altamente seguro CloudSimple que é executado na rede virtual CloudSimple no Azure.

## <a name="network-security"></a>Segurança de rede

A solução CloudSimple se baseia em camadas de segurança de rede.

### <a name="azure-edge-security"></a>Segurança de borda do Azure

Os serviços CloudSimple são criados sobre a segurança da rede base fornecida pelo Azure. Azure aplica técnicas de defesa em profundidade para detecção e resposta em tempo hábil a ataques de rede associado à entrada anormal ou padrões de tráfego de saída e ataques de (DDoS) de negação de serviço distribuído. Esse controle de segurança se aplica a ambientes de nuvem privada e o software de plano de controle desenvolvidos pela CloudSimple.

### <a name="segmentation"></a>Segmentação

O serviço de CloudSimple tem redes de camada 2 logicamente separadas que restringem o acesso a suas próprias redes privadas em seu ambiente de nuvem privada. Você pode proteger ainda mais suas redes de nuvem privada por meio de um firewall. No portal do CloudSimple, você define regras de controle de tráfego de rede Oeste Leste e Norte-Sul para todo tráfego de rede, que inclui o tráfego de nuvem privada redes internas, tráfego de nuvem privada entre, tráfego geral para a Internet e tráfego de rede local sobre a conexão de VPN IPsec ou ExpressRoute do Azure.

## <a name="vulnerability-and-patch-management"></a>Gerenciamento de patches e vulnerabilidade 

CloudSimple é responsável pela aplicação de patch de segurança periódica de softwares gerenciados do VMware, como NSX, vCenter e ESXi.

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

Os clientes podem autenticar sua conta do Azure (no Azure Active Directory) usando a autenticação multifator ou SSO como preferencial. No portal do Azure, você pode iniciar o portal de CloudSimple sem precisar reinserir as credenciais.

CloudSimple dá suporte a configuração opcional de uma fonte de identidade para o vCenter de nuvem privada. Você pode usar um [fonte de identidade local](https://docs.azure.cloudsimple.com/set-vcenter-identity), uma nova fonte de identidade para a nuvem privada, ou [Azure Active Directory](https://docs.azure.cloudsimple.com/azure-ad).

Por padrão, os clientes recebem os privilégios necessários para as operações diárias do vCenter na nuvem particular. Esse nível de permissão não inclui o acesso administrativo ao vCenter. Se for necessário acesso administrativo, você poderá [escalonar os privilégios](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) por um período limitado, enquanto você concluir as tarefas administrativas.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar um serviço CloudSimple no Azure](quickstart-create-cloudsimple-service.md).
* Saiba como [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/).
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md).
