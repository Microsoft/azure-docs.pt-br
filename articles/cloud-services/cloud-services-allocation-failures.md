---
title: Solução de problemas de falha na alocação do serviço de nuvem | Microsoft Docs
description: Solução de problemas de falha de alocação ao implantar Serviços de Nuvem no Azure
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: d1f24c3661a23496d1873f12ce46083bf5258269
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61435469"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Solução de problemas de falha de alocação ao implantar Serviços de Nuvem no Azure
## <a name="summary"></a>Resumo
Quando você implanta instâncias em um Serviço de Nuvem ou adiciona nova instâncias de função da Web ou de trabalho, o Microsoft Azure aloca recursos de computação. Eventualmente, você pode receber mensagens de erro durante a execução dessas operações antes de alcançar os limites da assinatura do Microsoft Azure. Este artigo explica as causas das falhas de alocação mais comuns e sugere possíveis correções. As informações também poderão ser úteis caso você pretenda implantar serviços.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Histórico – Como funciona a alocação
Os servidores são particionados em clusters nos datacenters do Microsoft Azure. Ocorre uma nova tentativa de solicitação de alocação de serviço de nuvem em vários clusters. Quando a primeira instância é implantada em um serviço de nuvem (em preparo ou produção), esse serviço de nuvem é fixado a um cluster. Quaisquer implantações adicionais do serviço de nuvem ocorrerão no mesmo cluster. Neste artigo, chamaremos isso de "fixada a um cluster". O diagrama 1 a seguir exemplifica o caso de uma tentativa de alocação normal feita em vários clusters; o diagrama 2 demonstra o caso de uma alocação que foi fixada ao Cluster 2 por se tratar do local de hospedagem do Serviço de Nuvem CS_1.

![Diagrama de alocação](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Motivos das falhas de alocação
Quando a solicitação de alocação é fixada a um cluster, é mais provável que haja falha na localização de recursos livres, pois o pool de recursos disponíveis está limitado a um cluster. Além disso, quando a solicitação de alocação é fixada a um cluster, mas não há suporte para o tipo de recurso solicitado ao cluster, a solicitação falha mesmo que o cluster tenha recursos gratuitos. O diagrama 3 a seguir demonstra o caso em que uma alocação fixada falha porque o único cluster candidato não dispõe de recursos gratuitos. O diagrama 4 demonstra o caso em que uma alocação fixada falha porque o único cluster candidato não é compatível com o tamanho da VM solicitado, mesmo que o cluster disponha de recursos livres.

![Falha na alocação fixada](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Solucionando problemas de falha de alocação para serviços de nuvem
### <a name="error-message"></a>Mensagem de erro
Você pode ver a seguinte mensagem de erro:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Problemas comuns
Estes são os cenários comuns de alocação que causam a fixação de uma solicitação de alocação em um único cluster.

* Implantação em um Slot de Preparo: se um serviço de nuvem tiver uma implantação em cada slot, todo o serviço de nuvem será fixado em um cluster específico.  Isso significa que se uma implantação já existir no slot de produção, uma nova implantação de preparo só pode ser alocada no mesmo cluster, como o slot de produção. Se o cluster estiver se aproximando da capacidade, a solicitação poderá falhar.
* Dimensionamento: a adição de novas instâncias a um serviço de nuvem existente deve alocar no mesmo cluster.  Solicitações pequenas de dimensionamento normalmente podem ser alocadas, mas nem sempre. Se o cluster estiver se aproximando da capacidade, a solicitação poderá falhar.
* Grupo de afinidade: uma nova implantação para um serviço de nuvem vazio pode ser alocada pela malha em qualquer cluster nessa região, a menos que o serviço de nuvem esteja fixado em um grupo de afinidade. As implantações no mesmo grupo de afinidade serão tentadas no mesmo cluster. Se o cluster estiver se aproximando da capacidade, a solicitação poderá falhar.
* Grupo de Afinidade vNet: redes virtuais mais antigas foram vinculadas a grupos de afinidade em vez de regiões, e os serviços de nuvem nessas Redes Virtuais poderiam ser fixados ao cluster do grupo de afinidade. As implantações para esse tipo de rede virtual serão tentadas no cluster fixado. Se o cluster estiver se aproximando da capacidade, a solicitação poderá falhar.

## <a name="solutions"></a>Soluções
1. Reimplantar em um novo serviço de nuvem: essa solução provavelmente será a mais bem-sucedida, pois permite que a plataforma escolha entre todos os clusters nessa região.

   * Implantar a carga de trabalho em um novo serviço de nuvem  
   * Atualizar o CNAME ou um registro A para apontar o tráfego para o novo serviço de nuvem
   * Quando nenhum tráfego estiver sendo encaminhado ao local antigo, você poderá excluir o serviço de nuvem antigo. Essa solução não causará qualquer tempo de inatividade.
2. Excluir os slots de produção e de preparo: essa solução preservará seu nome DNS existente, mas causará um tempo de inatividade para seu aplicativo.

   * Exclua os slots de produção e de preparo de um serviço de nuvem existente para que o serviço de nuvem fique vazio e, em seguida,
   * Crie uma nova implantação no serviço de nuvem existente. Isso causará uma nova tentativa de alocação em todos os clusters na região. Certifique-se de que o serviço de nuvem não esteja associado a um grupo de afinidade.
3. IP reservado: essa solução preservará o endereço IP existente, mas causará um tempo de inatividade para seu aplicativo.  

   * Criar um IP reservado para sua implantação existente usando o Powershell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Siga o nº 2 acima, lembrando de especificar o novo IP reservado no CSCFG do serviço.
4. Remover o grupo de afinidade para novas implantações: grupos de afinidade não são mais recomendados. Execute as etapas para o nº 1 acima a fim de implantar um novo serviço de nuvem. Certifique-se de que o serviço de nuvem não esteja em um grupo de afinidade.
5. Converter para uma rede virtual regional: confira [Como migrar de grupos de afinidades para uma rede virtual regional (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
