---
title: Proteção de dados do cliente no Azure
description: Saiba como o Azure protege os dados do cliente por meio da separação de dados, redundância de dados e destruição de dados.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2020
ms.author: terrylan
ms.openlocfilehash: 14589e4efe22d89468b069bf6ff7e3d9babcc714
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87543785"
---
# <a name="azure-customer-data-protection"></a>Proteção de dados do cliente do Azure   
O acesso aos dados de clientes pela equipe de operações e suporte da Microsoft é negado por padrão. Quando o acesso a dados relacionados a um caso de suporte é concedido, ele só é concedido usando um modelo JIT (just-in-time) usando políticas que são auditadas e verificados em relação a nossas políticas de conformidade e privacidade.  Os requisitos de controle de acesso são estabelecidos pela seguinte Política de Segurança do Azure:

- Nenhum acesso aos dados do cliente, por padrão.
- Nenhuma conta de usuário ou administrador em máquinas virtuais do cliente (VMs).
- Conceda o menor privilégio necessário para concluir a tarefa; auditoria e log de solicitações de acesso.

A equipe de suporte do Azure recebe contas exclusivas do Active Directory corporativas da Microsoft. O Azure conta com o Active Directory corporativo da Microsoft, gerenciado pela Microsoft Information Technology (MSIT), para controlar o acesso aos principais sistemas de informações. A autenticação multifator é necessária e o acesso é concedido apenas por consoles seguros.

Todas as tentativas de acesso são monitoradas e podem ser exibidas por meio de um conjunto básico de relatórios.

## <a name="data-protection"></a>Proteção de dados
O Azure fornece aos clientes segurança de dados forte, por padrão e como opções do cliente.

**Segregação de dados**: o Azure é um serviço de vários inquilinos, o que significa que várias implantações de clientes e VMs são armazenadas no mesmo hardware físico. Azure usa isolamento lógico para separar os dados de cada cliente dos dados de outros. A segregação fornece a escala e os benefícios econômicos dos serviços multilocatários, ao mesmo tempo em que evita com rigor que os clientes acessem os dados uns dos outros.

**Proteção de dados em repouso**: os clientes são responsáveis por garantir que os dados armazenados no Azure sejam criptografados de acordo com seus padrões. O Azure oferece uma ampla variedade de recursos de criptografia, dando aos clientes a flexibilidade para escolher a solução que melhor atende às suas necessidades. O Azure Key Vault ajuda os clientes a manter facilmente o controle de chaves usadas por aplicativos e serviços na nuvem para criptografar dados. A criptografia de disco do Azure permite que os clientes criptografem as VMs. A Criptografia do Serviço de Armazenamento do Microsoft Azure permite criptografar todos os dados colocados na conta de armazenamento de um cliente.

**Proteção de dados em trânsito**: a Microsoft fornece várias opções que podem ser utilizadas pelos clientes para proteger os dados em trânsito internamente na rede do Azure e externamente pela Internet para o usuário final.  Isso inclui a comunicação por meio de redes virtuais privadas (utilizando criptografia IPsec/IKE), TLS 1,2 ou posterior (por meio de componentes do Azure, como gateway de aplicativo ou porta frontal do Azure), protocolos diretamente nas máquinas virtuais do Azure (como o Windows IPsec ou SMB) e muito mais. 

Além disso, "a criptografia por padrão" usando MACsec (um padrão IEEE na camada de vínculo de dados) está habilitada para todos os viajam de tráfego do Azure entre os datacenters do Azure para garantir a confidencialidade e a integridade dos dados do cliente. 

**Redundância de dados**: a Microsoft ajuda a garantir que os dados estejam protegidos se houver um ataque cibernético ou danos físicos em um datacenter. Os clientes podem optar por:

- Armazenamento no país/região para considerações de conformidade ou latência.
- Armazenamento fora do país/fora de região para fins de segurança ou recuperação de desastre.

Os dados podem ser replicados em uma área geográfica selecionada para redundância, mas não podem ser transmitidos fora dela. Os clientes têm várias opções para replicar dados, incluindo o número de cópias e o número e a localização dos datacenters de replicação.

Ao criar sua conta de armazenamento, selecione uma das seguintes opções de replicação:

- **Armazenamento redundante local (LRS)**: o armazenamento localmente redundante mantém três cópias de seus dados. O LRS é replicado três vezes em uma única instalação, em uma única região. O LRS protege seus dados de falhas normais de hardware, mas não de uma falha de um único recurso.
- **Armazenamento com redundância de zona (ZRS)**: O armazenamento redundante de zona mantém três cópias de seus dados. O ZRS é replicado três vezes por duas ou três instalações para fornecer maior durabilidade que o LRS. A replicação ocorre em uma única região ou em duas regiões. O ZRS ajuda a garantir que seus dados sejam duráveis em uma única região.
- **Armazenamento Geo-redundante (GRS)**: o armazenamento geo-redundante é ativado para sua conta de armazenamento por padrão quando você o cria. O GRS mantém seis cópias de seus dados. Com o GRS, seus dados são replicados três vezes dentro da região primária. Os dados também são replicados três vezes em uma região secundária a centenas de milhas de distância da região primária, oferecendo o nível mais alto de durabilidade. No caso de uma falha na região principal, o Armazenamento do Azure passará para a região secundária. O GRS ajuda a garantir que seus dados sejam duráveis em duas regiões separadas.

**Destruição de dados**: quando os clientes excluem dados ou saem do Azure, a Microsoft segue padrões rígidos para substituir os recursos de armazenamento antes de serem reutilizados, bem como a destruição física de hardware descomissionado. Microsoft executa uma exclusão completa dos dados na solicitação de cliente e no término do contrato.

## <a name="customer-data-ownership"></a>Propriedade dos dados do cliente
Microsoft não inspeciona, aprova ou monitora os aplicativos que os clientes implantam no Azure. Além disso, a Microsoft não sabe que tipo de dados clientes escolhem armazenar no Azure. A Microsoft não reivindica a propriedade de dados sobre as informações do cliente inseridas no Azure.

## <a name="records-management"></a>Gerenciamento de registros
O Azure estabeleceu requisitos internos de retenção de registros para dados de back-end. Os clientes são responsáveis por identificar seus próprios requisitos de retenção de registros. Para registros armazenados no Azure, os clientes são responsáveis por extrair os dados e reter o conteúdo fora do Azure por um período de retenção especificado pelo cliente.

O Azure permite que os clientes exportem dados e façam auditoria dos relatórios do produto. As exportações são salvas localmente para reter as informações para uma retenção definida pelo cliente de período de tempo.

## <a name="electronic-discovery-e-discovery"></a>Descoberta eletrônica (e-discovery)
Os clientes do Azure são responsáveis por manter a conformidade com os requisitos de descoberta eletrônica no uso de serviços do Azure. Se os clientes do Azure precisarem preservar os dados de seus clientes, eles poderão exportar e salvar os dados localmente. Além disso, os clientes podem solicitar exportações de seus dados do departamento de Suporte ao Cliente do Azure. Além de permitir que os clientes exportar seus dados, o Azure realiza amplo registro em log e monitoramento internamente.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](physical-security.md)
- [Disponibilidade da infraestrutura do Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Rede de produção do Azure](production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Azure](infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](infrastructure-operations.md)
- [Monitoramento de infraestrutura do Microsoft Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
