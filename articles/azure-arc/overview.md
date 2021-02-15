---
title: Visão geral do Azure Arc
description: Saiba o que é o Azure Arc e como ele ajuda os clientes a habilitar o gerenciamento e a governança dos recursos híbridos com outros serviços e recursos do Azure.
ms.date: 09/23/2020
ms.topic: overview
ms.openlocfilehash: 8f39dff16037b5f52a050b7d07193b0ad7cab93d
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672679"
---
# <a name="azure-arc-overview"></a>Visão geral do Azure Arc

Hoje, as empresas estão com dificuldade para controlar e governar um ambiente que se torna cada vez mais complexo. Esses ambientes se estendem por data centers, várias nuvens e Edge. Cada ambiente e nuvem têm o próprio conjunto de ferramentas de gerenciamento distinto que você precisa aprender e operar.

Ao mesmo tempo, novos modelos operacionais DevOps e ITOps são difíceis de implementar, já que as ferramentas existentes não dão suporte para novos padrões nativos de nuvem.

O Azure Arc simplifica a governança e o gerenciamento ao fornecer uma plataforma de gerenciamento local e de várias nuvens consistente. O Azure Arc permite que você gerencie todo o seu ambiente, com um só painel, projetando os seus recursos existentes no Azure Resource Manager. Agora você poderá gerenciar máquinas virtuais, clusters do Kubernetes e bancos de dados como se eles estivessem em execução no Azure. Independentemente de onde eles residem, você pode usar as funcionalidades familiares de gerenciamento e serviços do Azure. O Azure Arc permite que você continue usando ITOps tradicionais, ao mesmo tempo que apresenta as práticas de DevOps para dar suporte a novos padrões de nuvem nativos no seu ambiente.

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Diagrama do painel de controle de gerenciamento do Azure Arc" border="false":::

Hoje, o Azure Arc permite que você gerencie os seguintes tipos de recursos hospedados fora do Azure:

* Servidores – Máquinas virtuais do Azure físicas e virtuais executando Windows ou Linux.
* Clusters do Kubernetes – Dá suporte a várias distribuições do Kubernetes.
* Serviços de Dados do Azure – Serviços de Hiperescala do PostgreSQL e do Banco de Dados SQL do Azure.

## <a name="what-does-azure-arc-deliver"></a>O que o Azure Arc oferece?

Os principais recursos do Azure Arc incluem:

* Implementar inventário, gerenciamento, governança e segurança consistentes nos seus servidores no ambiente.

* Configurar as [extensões de VM do Azure](./servers/manage-vm-extensions.md) para que usem os serviços de gerenciamento do Azure para monitorar, proteger e atualizar os seus servidores.

* Gerenciar e controlar os clusters do Kubernetes em escala.

* Use a configuração baseada em GitOps como gerenciamento de código para implantar aplicativos e configurações em um ou mais clusters diretamente do controle do código-fonte, como o GitHub.

* Conformidade e configuração sem toque para os seus clusters do Kubernetes usando o Azure Policy.

* Execute os Serviços de Dados do Azure em qualquer ambiente do Kubernetes, especialmente na Instância Gerenciada de SQL do Azure e no Banco de Dados do Azure para PostgreSQL – Hiperescala, com benefícios como upgrades/atualizações, segurança e monitoramento como se ele fosse executado no Azure. Aproveite a escala elástica e aplique atualizações sem nenhum tempo de inatividade do aplicativo, mesmo que ele não tenha uma conexão contínua com o Azure.

* Uma experiência unificada de exibição dos seus recursos habilitados para o Azure Arc, independentemente de você estar usando o portal do Azure, a CLI do Azure, o Azure PowerShell ou a API REST do Azure.

## <a name="how-much-does-azure-arc-cost"></a>Quanto custa o Azure Arc?

Veja a seguir os detalhes de preços dos recursos disponíveis hoje com o Azure Arc.

### <a name="arc-enabled-servers"></a>Servidores habilitados para Arc

A funcionalidade do plano de controle do Azure Arc é oferecida sem custo adicional. Isso inclui:

* Organização de recursos por meio de marcas e grupos de gerenciamento do Azure.

* Pesquisa e indexação por meio do Azure Resource Graph.

* Acesso e segurança por meio do RBAC e das assinaturas do Azure.

* Ambientes e automação por meio de modelos e extensões.

* Gerenciamento de atualizações

Qualquer serviço do Azure que seja usado em servidores habilitados para Arc, por exemplo, a Central de Segurança do Azure ou o Azure Monitor, será cobrado de acordo com o preço do serviço. Para obter mais informações, confira a [página Preços do Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes habilitado para Azure Arc

Na fase de versão prévia atual, o Kubernetes habilitado para o Azure Arc é oferecido sem nenhum custo adicional.

### <a name="azure-arc-enabled-data-services"></a>Serviços de Dados habilitados para Azure Arc

Na fase de versão prévia atual, os Serviços de Dados habilitados para o Azure Arc são oferecidos sem nenhum custo adicional.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os servidores habilitados para Arc, confira a [visão geral](./servers/overview.md) a seguir

* Para saber mais sobre o Kubernetes habilitado para Arc, confira a [visão geral](./kubernetes/overview.md) a seguir

* Para saber mais sobre os Serviços de Dados habilitados para Arc, confira a [visão geral](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/) a seguir

* Experimente os serviços habilitados para Arc em [Iniciar rapidamente a prova de conceito](https://azurearcjumpstart.io/azure_arc_jumpstart/)
