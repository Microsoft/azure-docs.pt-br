---
title: Visão geral da Malha do Azure Service Fabric
description: Saiba mais sobre a Malha do Azure Service Fabric. Com a Malha do Service Fabric, você pode implantar e dimensionar seu aplicativo sem se preocupar com as necessidades de infraestrutura dele.
author: georgewallace
ms.author: gwallace
ms.date: 10/1/2018
ms.topic: overview
ms.openlocfilehash: 64880a9ac3d6d48ce6d39d0b3a7a3aff6587f328
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626970"
---
# <a name="what-is-service-fabric-mesh"></a>O que é a Malha do Service Fabric?

> [!IMPORTANT]
> A versão prévia da Malha do Azure Service Fabric foi desativada. Não serão mais permitidas novas implantações por meio da API da Malha do Service Fabric. O suporte para as implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, confira [Desativação da versão prévia da Malha do Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Este vídeo fornece uma visão geral rápida da Malha do Service Fabric.
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

A Malha do Microsoft Azure Service Fabric é um serviço totalmente gerenciado que permite aos desenvolvedores implantar aplicativos de microsserviços sem gerenciar máquinas virtuais, armazenamento ou rede. Aplicativos hospedados na Malha do Service Fabric são executados e dimensionados sem que você se preocupe com a infraestrutura subjacente.  A Malha do Service Fabric é composta por clusters de milhares de computadores.  Todas as operações de cluster ficam ocultas do desenvolvedor. Carregue seu código e especifique os recursos necessários, os requisitos de disponibilidade e os limites de recursos.  A Malha do Service Fabric aloca automaticamente a infraestrutura e lida com falhas de infraestrutura, assegurando a alta disponibilidade de seus aplicativos. Você só precisará cuidar da integridade e da capacidade de resposta de seu aplicativo, não da infraestrutura.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Este artigo fornece uma visão geral dos principais benefícios da Malha do Service Fabric.

## <a name="great-developer-experience"></a>Ótima experiência de desenvolvedor

A Malha do Service Fabric dá suporte a qualquer linguagem de programação ou estrutura que possa ser executada em um contêiner. O suporte das ferramentas do Visual Studio 2019 e do Visual Studio Code fornecem uma experiência avançada de edição e depuração para aplicativos .NET e .NET Core. 

Com a Malha do Service Fabric, você pode:

- "Lift and shift" aplicativos existentes em contêineres a fim de modernizar e executar seus aplicativos atuais em larga escala.
- Criar e implantar novos aplicativos de microsserviço em larga escala no Azure.  Integrar com outros serviços ou aplicativos existentes do Azure em execução em contêineres. Cada microsserviço faz parte de um aplicativo seguro e de rede isolada. O microsserviço tem políticas de governança de recursos definidas para núcleos de CPU, memória, espaço em disco e muito mais.
- Integrar e estender aplicativos existentes sem fazer alterações nesses aplicativos. Use sua própria rede virtual para conectar o aplicativo existente ao novo aplicativo.  
- Modernize seus aplicativos de Serviços de Nuvem existentes migrando para a Malha do Service Fabric.  

## <a name="simple-operational-lifecycle"></a>Ciclo de vida operacional simples

Gerencie facilmente os aplicativos em execução, aplicativo de monitoramento e depuração em ambientes de produção. Esse gerenciamento inclui controle de versão e atualizações de aplicativo. Esses aplicativos podem ser formados por um único microsserviço ou vários microsserviços isolados dentro da própria rede deles. Os aplicativos são executados com eficiência com implantação rápida, posicionamento e tempos de failover.

Com a Malha do Service Fabric, você pode:

- Implante e gerencie aplicativos sem a necessidade de provisionar e gerenciar explicitamente a infraestrutura.  A Malha do Service Fabric provisiona, atualiza, corrige e mantém a infraestrutura subjacente para você.
- Configure a integração contínua usando as ferramentas integradas para empacotar e implantar facilmente os aplicativos.
- Aproveite todos os recursos do Azure Resource Manager. Entre os exemplos desses recursos estão a trilha de auditoria e [Azure RBAC (controle de acesso baseado em função do Azure)](../role-based-access-control/overview.md). Todos os recursos que você implanta no serviço de Malha do Azure Service Fabric são recursos do Azure Resource Manager. Esses recursos incluem aplicativos, serviços, segredos etc.
- Implante e gerencie recursos usando o [Portal do Azure](https://portal.azure.com), modelos do Resource Manager ou bibliotecas da CLI do Azure/Azure PowerShell.
- Configure o monitoramento operacional e alertas usando o [Application Insights](/azure/application-insights/) (ou a ferramenta de sua escolha) para capturar rastreamentos operacionais e de diagnóstico da plataforma.
- Acessar as informações de diagnóstico de aplicativo emitidas do modelo de aplicativo usando [Application Insights](/azure/application-insights/) ou sua ferramenta preferida.
- Otimize o uso de recurso especificando regras de dimensionamento automático para os serviços na definição de aplicativo.

## <a name="mission-critical-platform-capabilities"></a>Recursos de plataforma fundamentais

A Malha do Service Fabric cria uma coleção de clusters que abrange [Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md) e/ou limites regionais geopolíticos. A Malha do Service Fabric descreve aplicativos com um conjunto de intenções como dimensionamento, requisitos de hardware, requisitos de durabilidade e políticas de segurança.  Quando o aplicativo é implantado, a Malha do Service Fabric localiza o lugar ideal para executá-lo.

Com a Malha do Service Fabric, você pode:

- Aproveite a alta disponibilidade, dimensionamento horizontal/vertical, detectabilidade, orquestração, roteamento de mensagens, mensagens confiáveis, atualizações sem tempo de inatividade, gerenciamento de segurança/segredos, recuperação de desastres, gerenciamento de estado, gerenciamento de configuração e transações distribuídas.
- Escolha entre vários modelos de aplicativo durante a criação de aplicativos.
- Use os recursos de plataforma expostos por meio de pontos de extremidade REST consumindo associações específicas a um idioma geradas usando o Swagger.
- Implante aplicativos em toda [Zonas de Disponibilidade](../availability-zones/az-overview.md) e em várias regiões para gerar confiabilidade geográfica.
- Use todos os recursos de segurança e conformidade fornecidos pelo Azure.

## <a name="next-steps"></a>Próximas etapas

Basta apenas algumas etapas para implantar um projeto de exemplo com o Visual Studio. Para saber mais, confira [Criar um site do ASP.NET Core](service-fabric-mesh-quickstart-dotnet-core.md). 

Encontre respostas para [dúvidas comuns](service-fabric-mesh-faq.md).


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
