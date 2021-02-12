---
title: Quais são as soluções para executar o Oracle WebLogic Server no serviço kubernetes do Azure
description: Saiba como executar o Oracle WebLogic Server no serviço kubernetes do Azure.
author: rezar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 10/28/2020
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: cf44ad8d81656248329d993d86e9922dc4985258
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486667"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Quais são as soluções para executar o Oracle WebLogic Server no serviço kubernetes do Azure?

Esta página descreve as soluções para executar o Oracle WebLogic Server (WLS) no serviço kubernetes do Azure (AKS). Essas soluções são desenvolvidas e suportadas em conjunto pela Oracle e pela Microsoft.

Também é possível executar o WebLogic Server em máquinas virtuais do Azure. As soluções para fazer isso são descritas neste [artigo da Microsoft](./oracle-weblogic.md).

O WebLogic Server é um servidor de aplicativos Java líder que executa alguns dos aplicativos Java empresariais de missão crítica em todo o mundo. O WebLogic Server forma a base de middleware para o Oracle Software Suite. A Oracle e a Microsoft estão comprometidas em capacitar os clientes do WebLogic Server com opções e flexibilidade para executar cargas de trabalho no Azure como uma plataforma de nuvem líder.

## <a name="wls-on-aks-certified-and-supported"></a>WLS no AKS certificado e com suporte
O WebLogic Server é certificado pela Oracle e pela Microsoft para ser executado bem no AKS. O WebLogic Server em AKS Solutions tem como objetivo tornar-se o mais fácil possível para executar seus aplicativos Java EE em contêineres e orquestrados na infraestrutura do Docker e do kubernetes. As soluções concentram-se em confiabilidade, escalabilidade, capacidade de gerenciamento e suporte empresarial.

Os clusters do WebLogic Server são totalmente habilitados para serem executados em kubernetes por meio do operador WebLogic kubernetes (chamado simplesmente de ' operator ' daqui em diante). O operador segue o padrão de operador kubernetes padrão. Ele simplifica o gerenciamento e a operação de domínios e implantações WebLogic no kubernetes automatizando tarefas manuais de outra forma e adicionando recursos de confiabilidade operacionais adicionais. O operador dá suporte ao Oracle WebLogic Server 12c, à infraestrutura de middleware do Oracle Fusion 12C e posterior. Testamos as imagens oficiais do Docker para o WebLogic Server 12.2.1.3 e o 12.2.1.4 com o operador. Para obter detalhes sobre o operador, consulte a [documentação oficial da Oracle](https://oracle.github.io/weblogic-kubernetes-operator/).

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>Orientações, scripts e exemplos para o WLS no AKS
Além de certificar o WebLogic Server em AKS, a Oracle e a Microsoft conjuntas fornecem instruções, scripts e exemplos detalhados para executar o WebLogic Server no AKS. A orientação é incorporada na seção exemplo do serviço kubernetes do Azure da [documentação do operador](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/). A orientação destina-se a tornar o WebLogic Server de produção em implantações AKS o mais fácil possível. A orientação usa as imagens oficiais do WebLogic Server Docker fornecidas pela Oracle. O failover é obtido por meio de arquivos do Azure acessados por meio de declarações de volume persistente kubernetes Os balanceadores de carga do Azure têm suporte quando provisionados usando um serviço kubernetes do tipo ' Load Balancer '. A orientação permite um alto grau de configuração e personalização.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="Você pode usar os scripts de exemplo para implantar o WebLogic Server no AKS":::

Atualmente, as diretrizes pressupõem a implantação do domínio fora da imagem do Docker e o uso das imagens padrão do Docker da Oracle. Vamos adicionar orientações para habilitar imagens personalizadas com seu domínio dentro de uma imagem do Docker. As mais fáceis de usar e integrações de serviço do Azure são possíveis no futuro por meio das ofertas do Marketplace espelhando o Oracle WebLogic Server em soluções de máquinas virtuais do Azure.

_Essas soluções são traga sua própria licença_. Eles supõem que você já tenha as licenças apropriadas com o Oracle e que estejam devidamente licenciados para executar ofertas no Azure.

_Se você estiver interessado em trabalhar de acordo com seus cenários de migração com a equipe de engenharia desenvolvendo essas soluções, preencha [esta breve pesquisa](https://aka.ms/wls-on-azure-survey) e inclua suas informações de contato_. Gerentes de programas, arquitetos e engenheiros entrarão em contato novamente em breve e começarão a colaboração. A oportunidade de colaborar em um cenário de migração é gratuita enquanto as soluções estão sob o desenvolvimento inicial ativo.

## <a name="deployment-architectures"></a>Arquiteturas de implantação

As soluções para executar o Oracle WebLogic Server no serviço kubernetes do Azure permitirão uma ampla gama de arquiteturas de implantação prontas para produção com uma facilidade relativa.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="Implantações complexas do WebLogic Server estão habilitadas em AKS":::

Além do que as soluções fornecem, os clientes têm flexibilidade total para personalizar ainda mais suas implantações. É provável que, na parte superior da implantação de aplicativos, os clientes integrem outros recursos do Azure às suas implantações. Os clientes são incentivados a fornecer comentários na pesquisa sobre como melhorar ainda mais as soluções.

## <a name="next-steps"></a>Próximas etapas

Explore a execução do Oracle WebLogic Server no serviço kubernetes do Azure.

> [!div class="nextstepaction"]
> [Diretrizes, scripts e exemplos para executar o WLS no AKS](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)

> [!div class="nextstepaction"]
> [Operador WebLogic kubernetes](https://oracle.github.io/weblogic-kubernetes-operator/)