---
title: Recursos, local e segurança física do Azure | Microsoft Docs
description: O artigo descreve os datacenters do Azure, incluindo as ofertas de conformidade, segurança e infra-estrutura física.
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
ms.openlocfilehash: 5f8378de18796fd679354c88880942df83399ad5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586775"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Recursos, local e segurança física do Azure
O Azure é composto de uma [infraestrutura de datacenter distribuída globalmente](https://azure.microsoft.com/global-infrastructure/), oferecendo suporte a milhares de serviços on-line e abrangendo mais de 100 instalações altamente seguras em todo o mundo.

A infraestrutura é projetada para aproximar os aplicativos aos usuários em todo o mundo, preservando a residência dos dados e oferecendo opções abrangentes de conformidade e resiliência para os clientes. Azure tem 52 regiões em todo o mundo e está disponível em 140 países.

Uma região é um conjunto de datacenters interconectados por meio de uma rede massiva e resiliente. A rede inclui a distribuição de conteúdo, o balanceamento de carga, redundância e criptografia por padrão. Com mais regiões globais do que qualquer outro provedor de nuvem, o Azure oferece a flexibilidade de implantar aplicativos onde você precisa deles.

As regiões do Azure são organizadas em geografias. Uma geografia do Azure garante que os requisitos de residência, de soberania, de conformidade e de resiliência de dados sejam respeitados dentro de limites geográficos.

As geografias permitem que os clientes com necessidades de conformidade e de residência de dados específicas mantenham seus dados e aplicativos próximos. As geografias são tolerantes a falhas para resistir a falhas completas da região, por meio de sua conexão com a infraestrutura de rede dedicada e de alta capacidade.

As zonas de disponibilidade são locais fisicamente separados dentro de uma região do Azure. Cada zona de disponibilidade é composta de um ou mais datacenters equipados com energia, resfriamento e rede independentes. As zonas de disponibilidade permitem que você execute aplicativos de missão crítica com alta disponibilidade e replicação de baixa latência.

A figura a seguir mostra como a infraestrutura global do Azure associa regiões e zonas de disponibilidade ao mesmo limite de residência de dados para alta disponibilidade, recuperação de desastre e backup.

![Limite de residência de dados de exibição de diagrama][1]

Data centers distribuídos geograficamente permite que a Microsoft estar próximo aos clientes, para reduzir a latência de rede e permitir backup com redundância geográfica e failover.

## <a name="physical-security"></a>Segurança física
A Microsoft projeta, cria e opera datacenters de uma maneira que controla rigorosamente o acesso físico às áreas onde seus dados são armazenados. A Microsoft entende a importância de proteger seus dados e está comprometida em ajudar a proteger os datacenters que contêm seus dados. Temos uma divisão inteira na Microsoft dedicada a projetar, construir e operar as instalações físicas que suportam o Azure. Essa equipe está investida em manter a segurança física de última geração.

A Microsoft adota uma abordagem em camadas para a segurança física, para reduzir o risco de usuários não autorizados obterem acesso físico aos dados e aos recursos do datacenter. Os centros de dados gerenciados pela Microsoft têm extensas camadas de proteção: aprovação de acesso no perímetro da instalação, no perímetro do prédio, dentro do prédio e no datacenter. Camadas de segurança física são:

- **Solicitação de acesso e aprovação.** Você deve solicitar acesso antes de chegar ao data center. Você precisa fornecer uma justificativa comercial válido para sua visita, como conformidade ou fins de auditoria. Todas as solicitações são aprovadas em uma base de necessidade de acesso por funcionários da Microsoft. Uma base de necessidade de acesso ajuda a manter o número de indivíduos necessários para concluir uma tarefa em data centers para o mínimo necessário. Depois que a Microsoft conceder permissão, um indivíduo terá acesso somente à área distinta do datacenter, com base na justificativa comercial aprovada. Permissões são limitadas a um determinado período de tempo e, em seguida, expirarem.

- **Perímetro do recurso.** Quando você chega a um data center, você precisa passar por um ponto de acesso bem definido. Normalmente, cercas altas feitas de aço e concreto abrangem cada centímetro do perímetro. Há câmeras em volta dos datacenters, com uma equipe de segurança monitorando seus vídeos o tempo todo.

- **Entrada de construção.** A entrada do datacenter é composta por oficiais de segurança profissionais que passaram por rigorosos treinamentos e verificação de antecedentes. Esses agentes de segurança também rotineiramente patrulham o datacenter e monitoram os vídeos das câmeras dentro do datacenter em todos os momentos.

- **Dentro do prédio.** Depois de entrar no prédio, você deve passar a autenticação de dois fatores com dados biométricos para continuar avançando pelo datacenter. Se sua identidade for validada, você poderá inserir apenas a parte do datacenter para o qual você aprovou o acesso. Você pode permanecer lá somente durante a duração do tempo aprovado.

- **Piso do Datacenter.** Você só tem permissão para entrar no andar em que você está autorizado a entrar. É necessário para passar uma triagem de detecção de metal corpo completo. Para reduzir o risco de dados não autorizados entrarem ou saírem do datacenter sem o nosso conhecimento, somente dispositivos aprovados podem entrar no chão do datacenter. Além disso, monitor de câmeras de vídeo parte frontal e traseira de todos os servidores em rack. Quando você sai do datacenter, novamente você deve passar pela triagem de detecção de metal de corpo inteiro. Para sair do datacenter, você precisa passar por uma verificação de segurança adicional.

A Microsoft exige que os visitantes entreguem crachás na saída de qualquer instalação da Microsoft.

## <a name="physical-security-reviews"></a>Revisões de segurança física
Periodicamente, realizamos revisões de segurança física das instalações, para garantir que os datacenters atendam adequadamente aos requisitos de segurança do Azure. A equipe do provedor de hospedagem de data center não fornece o gerenciamento de serviço do Azure. O pessoal não pode entrar nos sistemas do Azure e não tem acesso físico à sala de disposição e às gaiolas do Azure.

## <a name="data-bearing-devices"></a>Dispositivos de influência de dados
A Microsoft usa os procedimentos de práticas recomendados e uma solução de apagamento de dados que seja [NIST 800-88 compatíveis](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Para discos rígidos que não podem ser apagados, usamos um processo de destruição que o destrói e impossibilita a recuperação de informações. Esse processo de destruição pode ser desintegrar, destruir, pulverizar ou incinerar. Determinamos os meios de alienação de acordo com o tipo de ativo. Podemos manter registros da destruição.  

## <a name="equipment-disposal"></a>Descarte de equipamentos
No final de vida de um sistema, o pessoal operacional da Microsoft segue procedimentos rigorosos de manipulação de dados e descarte de hardware para garantir que o hardware que contém seus dados não seja disponibilizado para partes não confiáveis. Usamos uma abordagem de exclusão segura para discos rígidos que a suportam. Para discos rígidos que não podem ser limpos, usamos um processo de destruição que destrói a unidade e impossibilita a recuperação de informações. Esse processo de destruição pode ser desintegrar, destruir, pulverizar ou incinerar. Determinamos os meios de alienação de acordo com o tipo de ativo. Podemos manter registros da destruição. Todos os serviços do Azure usam serviços de gerenciamento de armazenamento e descarte de mídia aprovados.

## <a name="compliance"></a>Conformidade
Projetamos e gerenciamos a infraestrutura do Azure para atender a um amplo conjunto de padrões de conformidade internacionais e específicos do setor, como ISO 27001, HIPAA, FedRAMP, SOC 1 e SOC 2. Também atendemos aos padrões específicos do país, incluindo o IRAP da Austrália, o G-Cloud do Reino Unido e o MTCS de Singapura. Auditorias rigorosas de terceiros, como as feitas pelo British Standards Institute, verificam a adesão aos rígidos controles de segurança que esses padrões exigem.

Para obter uma lista completa dos padrões de conformidade que o Azure adota, consulte as [ofertas de Conformidade](https://www.microsoft.com/trustcenter/compliance/complianceofferings). 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, consulte:

- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Limites e componentes do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento de infraestrutura do Microsoft Azure](azure-infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Microsoft Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
