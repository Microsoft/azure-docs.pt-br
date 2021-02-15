---
title: Visão Geral do exemplo de blueprint dos Serviços Compartilhados ISO 27001
description: Visão geral e arquitetura do exemplo de blueprint dos Serviços Compartilhados ISO 27001. Este exemplo de blueprint ajuda os clientes a avaliar controles específicos do ISO 27001.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: d5ac88fc7a4fe21cef74ee23af2336a5a376471a
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627643"
---
# <a name="overview-of-the-iso-27001-shared-services-blueprint-sample"></a>Visão geral do exemplo de blueprint dos Serviços Compartilhados do ISO 27001

O exemplo de blueprint dos Serviços Compartilhados ISO 27001 oferece um conjunto de padrões de infraestrutura e proteções de política em conformidade que ajudam na certificação ISO 27001. Este blueprint ajuda os clientes a implantar arquiteturas baseadas em nuvem que oferecem soluções para os cenários com requisitos de conformidade ou de credenciamento.

O exemplo de blueprint da [carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL](../iso27001-ase-sql-workload/index.md) ISO 27001 estende este exemplo.

## <a name="architecture"></a>Arquitetura

O exemplo de blueprint dos Serviços Compartilhados ISO 27001 implanta uma infraestrutura de base no Azure que pode ser usada por organizações para hospedar várias cargas de trabalho baseadas na abordagem VDC (Virtual Datacenter).
O VDC é um conjunto comprovado de arquiteturas de referência, ferramentas de automação e modelo de participação usado pela Microsoft com seus maiores clientes corporativos. O exemplo de blueprint dos Serviços Compartilhados se baseia em um ambiente de VDC do Azure totalmente nativo, mostrado abaixo.

:::image type="content" source="../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png" alt-text="Design do exemplo de blueprint dos Serviços Compartilhados ISO 27001" border="false":::

Este ambiente é composto de vários serviços do Azure usados para oferecer uma infraestrutura de serviços compartilhados seguros, totalmente monitorados e prontos para empresas baseado nas normas ISO 27001. Esse ambiente é composto de:

- [Funções do Azure](../../../../role-based-access-control/overview.md), usadas para diferenciação de direitos de uma perspectiva de painel de controle. Três funções são definidas antes da implantação de qualquer infraestrutura:
  - A função NetOps tem o direito de gerenciar o ambiente de rede, incluindo configurações de firewall, configurações de NSG, roteamento e outras funcionalidades de rede
  - A função SecOps tem os direitos necessários para implantar e gerenciar a [Central de Segurança do Azure](../../../../security-center/security-center-introduction.md), fazer definições do [Azure Policy](../../../policy/overview.md) e outros direitos de segurança
  - A função SysOps tem os direitos necessários para fazer as definições do [Azure Policy](../../../policy/overview.md) na assinatura, gerenciar o [Log Analytics](../../../../azure-monitor/overview.md) para todo o ambiente, entre outros direitos operacionais
- O [Log Analytics](../../../../azure-monitor/overview.md) é implantado como o primeiro serviço do Azure para garantir que todas as ações e serviços sejam registrados em um local central desde o momento em que você iniciar sua implantação segura
- Uma rede virtual compatível com sub-redes para conectividade com um datacenter local, uma pilha de entrada e saída para conectividade com a Internet e uma sub-rede de serviços compartilhados que usa NSGs e ASGs para microssegmentação completa, contendo:
  - Um host de jumpbox ou bastião usado para fins de gerenciamento, que pode ser acessado somente por um [Firewall do Azure](../../../../firewall/overview.md) implantado na sub-rede de pilha de entrada
  - Duas máquinas virtuais que executam o Azure AD DS (Azure Active Directory Domain Services) e o DNS podem ser acessadas somente por meio da jumpbox e podem ser configuradas apenas para replicar o AD em uma VPN ou conexão do [ExpressRoute](../../../../expressroute/expressroute-introduction.md) (não implantada pelo blueprint)
  - Uso do [Observador de Rede do Azure](../../../../network-watcher/network-watcher-monitoring-overview.md) e proteção contra DDoS padrão
- Uma instância do [Azure Key Vault](../../../../key-vault/general/overview.md) usada para hospedar segredos usados para as VMs implantadas no ambiente de serviços compartilhados

Todos esses elementos cumprem as práticas comprovadas publicadas no [Centro de Arquitetura do Azure – Arquiteturas de Referência](/azure/architecture/reference-architectures/).

> [!NOTE]
> A infraestrutura dos Serviços Compartilhados ISO 27001 define uma arquitetura de base para as cargas de trabalho.
> Ainda é necessário implantar cargas de trabalho por trás dessa arquitetura de base.

Para saber mais, confira a [documentação do Datacenter Virtual](/azure/architecture/vdc/).

## <a name="next-steps"></a>Próximas etapas

Você revisou a visão geral e a arquitetura do exemplo de blueprint dos Serviços Compartilhados ISO 27001.
Em seguida, visite os seguintes artigos para saber mais sobre o mapeamento de controle e como implantar este exemplo:

> [!div class="nextstepaction"]
> [Blueprint dos Serviços Compartilhados ISO 27001 – Mapeamento de controle](./control-mapping.md)
> [Blueprint dos Serviços Compartilhados ISO 27001 – Etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).