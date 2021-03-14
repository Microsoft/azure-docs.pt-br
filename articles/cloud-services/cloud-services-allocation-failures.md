---
title: Solucionando problemas de falhas de alocação do serviço de nuvem (clássico) | Microsoft Docs
description: Solucione uma falha de alocação ao implantar os serviços de nuvem do Azure. Saiba como a alocação funciona e por que a alocação pode falhar.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 95fe4a8e1f6c6ee5f519311f8e756be89a09acf8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738303"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-classic-in-azure"></a>Solucionando problemas de falha de alocação ao implantar serviços de nuvem (clássico) no Azure

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).


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

Em portal do Azure, navegue até o serviço de nuvem e, na barra lateral, selecione *logs de operação (clássico)* para exibir os logs.

Veja mais soluções para as exceções abaixo:

|Tipo de exceção  |Mensagem de erro  |Solução  |
|---------|---------|---------|
|FabricInternalServerError     |A operação falhou com o código de erro ' InternalError ' e errorMessage ' o servidor encontrou um erro interno. Repita a solicitação. '.|[Solucionar problemas de FabricInternalServerError](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|ServiceAllocationFailure     |A operação falhou com o código de erro ' InternalError ' e errorMessage ' o servidor encontrou um erro interno. Repita a solicitação. '.|[Solucionar problemas de ServiceAllocationFailure](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|LocationNotFoundForRoleSize     |A operação ' `{Operation ID}` ' falhou: ' a camada de VM solicitada não está disponível atualmente na região ( `{Region ID}` ) para esta assinatura. Tente outra camada ou implante-a em um local diferente. '.|[Solucionar problemas de LocationNotFoundForRoleSize](cloud-services-troubleshoot-location-not-found-for-role-size.md)|
|ConstrainedAllocationFailed     |A operação do Azure ' `{Operation ID}` ' falhou com o código COMPUTE. ConstrainedAllocationFailed. Detalhes: falha na alocação; Não é possível satisfazer as restrições na solicitação. A implantação solicitada do novo serviço está associada a um Grupo de Afinidades ou é destinada a uma Rede Virtual, ou há uma implantação existente para esse serviço hospedado. Essas condições restringem a nova implantação para os recursos específicos do Microsoft Azure. Tente novamente mais tarde ou tente reduzir o tamanho da VM ou o número de instâncias de função. De forma alternativa, remova as restrições mencionadas acima ou tente implantar em uma região diferente, se possível.|[Solucionar problemas de ConstrainedAllocationFailed](cloud-services-troubleshoot-constrained-allocation-failed.md)|
|OverconstrainedAllocationRequest     |O tamanho da VM (ou a combinação de tamanhos de VM) exigido por essa implantação não pode ser provisionado devido a restrições de solicitação de implantação. Se possível, tente relaxar as restrições, como associações de rede virtual, implantação em um serviço hospedado sem nenhuma outra implantação e em um grupo de afinidade diferente ou sem nenhum grupo de afinidade ou tente implantar em uma região diferente.|[Solucionar problemas de OverconstrainedAllocationRequest](cloud-services-troubleshoot-overconstrained-allocation-request.md)|

Mensagem de erro de exemplo:

> "A operação do Azure ' {ID da operação} ' falhou com o código COMPUTE. ConstrainedAllocationFailed. Detalhes: falha na alocação; Não é possível satisfazer as restrições na solicitação. A implantação solicitada do novo serviço está associada a um Grupo de Afinidades ou é destinada a uma Rede Virtual, ou há uma implantação existente para esse serviço hospedado. Essas condições restringem a nova implantação para os recursos específicos do Microsoft Azure. Tente novamente mais tarde ou tente reduzir o tamanho da VM ou o número de instâncias de função. De forma alternativa, remova as restrições mencionadas acima ou tente implantar em uma região diferente, se possível."

### <a name="common-issues"></a>Problemas comuns

Estes são os cenários comuns de alocação que causam a fixação de uma solicitação de alocação em um único cluster.

* Implantação em um Slot de Preparo: se um serviço de nuvem tiver uma implantação em cada slot, todo o serviço de nuvem será fixado em um cluster específico.  Isso significa que se uma implantação já existir no slot de produção, uma nova implantação de preparo só poderá ser alocada no mesmo cluster, como o slot de produção. Se o cluster estiver se aproximando da capacidade, a solicitação poderá falhar.
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

   * Criar um ReservedIP para sua implantação existente usando o PowerShell

     ```azurepowershell
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```

   * Siga o nº 2 acima, lembrando de especificar o novo IP reservado no CSCFG do serviço.
4. Remover o grupo de afinidade para novas implantações: grupos de afinidade não são mais recomendados. Execute as etapas para o nº 1 acima a fim de implantar um novo serviço de nuvem. Certifique-se de que o serviço de nuvem não esteja em um grupo de afinidade.
5. Converter para uma rede virtual regional: confira [Como migrar de grupos de afinidades para uma rede virtual regional (VNet)](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet).
