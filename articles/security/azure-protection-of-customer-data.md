---
title: Proteção de dados do cliente no Azure
description: Este artigo aborda como o Azure protege os dados do cliente.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 49615dcb2f077d2e1d8b93a4bb900b435e4c87bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589070"
---
# <a name="azure-customer-data-protection"></a>Proteção de dados do cliente do Azure   
O acesso aos dados de clientes pela equipe de operações e suporte da Microsoft é negado por padrão. Quando é concedido acesso aos dados do cliente, é necessária a aprovação de liderança e, em seguida, o acesso é cuidadosamente gerenciado e registrado. Os requisitos de controle de acesso são estabelecidos pela seguinte Política de Segurança do Azure:

- Nenhum acesso aos dados do cliente, por padrão.
- Nenhuma conta de usuário ou administrador em máquinas virtuais do cliente (VMs).
- Conceda o menor privilégio necessário para concluir a tarefa; auditoria e log de solicitações de acesso.

A equipe de suporte do Azure recebe contas exclusivas do Active Directory corporativas da Microsoft. O Azure conta com o Active Directory corporativo da Microsoft, gerenciado pela Microsoft Information Technology (MSIT), para controlar o acesso aos principais sistemas de informações. A autenticação multifator é necessária e o acesso é concedido apenas por consoles seguros.

Todas as tentativas de acesso são monitoradas e podem ser exibidas por meio de um conjunto básico de relatórios.

## <a name="data-protection"></a>Proteção de dados
O Azure fornece aos clientes segurança de dados forte, por padrão e como opções do cliente.

**Segregação de dados**: o Azure é um serviço multilocatário, o que significa que várias implantações de clientes e VMs ficam armazenadas no mesmo hardware físico. Azure usa isolamento lógico para separar os dados de cada cliente dos dados de outros. A segregação fornece a escala e os benefícios econômicos dos serviços multilocatários, ao mesmo tempo em que evita com rigor que os clientes acessem os dados uns dos outros.

**Proteção de dados em repouso**: os clientes são responsáveis por fazer com que os dados armazenados no Azure sejam criptografados de acordo com seus padrões. O Azure oferece uma ampla variedade de recursos de criptografia, dando aos clientes a flexibilidade para escolher a solução que melhor atende às suas necessidades. O Azure Key Vault ajuda os clientes a manter facilmente o controle de chaves usadas por aplicativos e serviços na nuvem para criptografar dados. A criptografia de disco do Azure permite que os clientes criptografem as VMs. A criptografia de serviço de armazenamento do Azure torna possível criptografar todos os dados colocados na conta de armazenamento de um cliente.

**Proteção de dados em trânsito**: os clientes podem ativar a criptografia para o tráfego entre suas próprias VMs e os usuários finais. Azure protege os dados em trânsito para ou de componentes externos e dados em trânsito internamente, por exemplo, entre duas redes virtuais. O Azure usa o protocolo TLS 1.2 ou posterior, padrão do setor, com chaves de criptografia RSA / SHA256 de 2.048 bits, conforme recomendado pela CESG / NCSC, para criptografar as comunicações entre:

- O cliente e a nuvem.
- Internamente entre sistemas do Azure e datacenters.

**Criptografia**: a criptografia de dados em armazenamento e em trânsito pode ser implantada pelos clientes como uma prática recomendada para garantir a confidencialidade e a integridade dos dados. É simples para os clientes configurar seus serviços de nuvem do Azure para usar SSL para proteger as comunicações da Internet e até mesmo entre suas VMs hospedadas no Azure.

**Redundância de dados**: a Microsoft ajuda a garantir a proteção dos dados em caso de ataque cibernético ou danos físicos a um datacenter. Os clientes podem optar por:

- Armazenamento no país para considerações de conformidade ou latência.
- Armazenamento fora do país para fins de segurança ou recuperação de desastres.

Os dados podem ser replicados em uma área geográfica selecionada para redundância, mas não podem ser transmitidos fora dela. Os clientes têm várias opções para replicar dados, incluindo o número de cópias e o número e a localização dos datacenters de replicação.

Ao criar sua conta de armazenamento, selecione uma das seguintes opções de replicação:

- **LRS (armazenamento com redundância local)**: O armazenamento com redundância local mantém três cópias dos seus dados. O LRS é replicado três vezes em uma única instalação, em uma única região. O LRS protege seus dados de falhas normais de hardware, mas não de uma falha de um único recurso.
- **ZRS (armazenamento com redundância de zona)**: O armazenamento com redundância de zona mantém três cópias dos seus dados. O ZRS é replicado três vezes por duas ou três instalações para fornecer maior durabilidade que o LRS. A replicação ocorre em uma única região ou em duas regiões. O ZRS ajuda a garantir que seus dados sejam duráveis em uma única região.
- **GRS (Armazenamento com redundância geográfica)**: O armazenamento com redundância geográfica é habilitado para sua conta de armazenamento por padrão quando ela é criada. O GRS mantém seis cópias de seus dados. Com o GRS, seus dados são replicados três vezes dentro da região primária. Os dados também são replicados três vezes em uma região secundária a centenas de milhas de distância da região primária, oferecendo o nível mais alto de durabilidade. No caso de uma falha na região principal, o Armazenamento do Azure passará para a região secundária. O GRS ajuda a garantir que seus dados sejam duráveis em duas regiões separadas.

**Destruição de dados**: quando os clientes excluem dados ou saem do Azure, a Microsoft segue padrões rígidos para substituir os recursos de armazenamento antes de serem reutilizados e destruir fisicamente hardware desativado. Microsoft executa uma exclusão completa dos dados na solicitação de cliente e no término do contrato.

## <a name="customer-data-ownership"></a>Propriedade dos dados do cliente
Microsoft não inspeciona, aprova ou monitora os aplicativos que os clientes implantam no Azure. Além disso, a Microsoft não sabe que tipo de dados clientes escolhem armazenar no Azure. A Microsoft não reivindica a propriedade de dados sobre as informações do cliente inseridas no Azure.

## <a name="records-management"></a>Gerenciamento de registros
O Azure estabeleceu requisitos internos de retenção de registros para dados de back-end. Os clientes são responsáveis por identificar seus próprios requisitos de retenção de registros. Para registros armazenados no Azure, os clientes são responsáveis por extrair os dados e reter o conteúdo fora do Azure por um período de retenção especificado pelo cliente.

O Azure permite que os clientes exportem dados e façam auditoria dos relatórios do produto. As exportações são salvas localmente para reter as informações para uma retenção definida pelo cliente de período de tempo.

## <a name="electronic-discovery-e-discovery"></a>Descoberta eletrônica (e-discovery)
Os clientes do Azure são responsáveis por manter a conformidade com os requisitos de descoberta eletrônica no uso de serviços do Azure. Se os clientes do Azure precisarem preservar os dados de seus clientes, eles poderão exportar e salvar os dados localmente. Além disso, os clientes podem solicitar exportações de seus dados do departamento de Suporte ao Cliente do Azure. Além de permitir que os clientes exportar seus dados, o Azure realiza amplo registro em log e monitoramento internamente.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Limites e componentes do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento de infraestrutura do Microsoft Azure](azure-infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](azure-infrastructure-integrity.md)
