---
title: Solucionar problemas de FabricInternalServerError ou ServiceAllocationFailure ao implantar um serviço de nuvem (clássico) no Azure | Microsoft Docs
description: Este artigo mostra como resolver uma exceção de FabricInternalServerError ou ServiceAllocationFailure ao implantar um serviço de nuvem (clássico) no Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 0a4111f569b751ace80a2a886ed2ce7e4968ce16
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744224"
---
# <a name="troubleshoot-fabricinternalservererror-or-serviceallocationfailure-when-deploying-a-cloud-service-classic-to-azure"></a>Solucionar problemas de FabricInternalServerError ou ServiceAllocationFailure ao implantar um serviço de nuvem (clássico) no Azure

Neste artigo, você solucionará problemas de falhas de alocação em que o controlador de malha não pode alocar ao implantar um serviço de nuvem do Azure (clássico).

Quando você implanta instâncias em um Serviço de Nuvem ou adiciona nova instâncias de função da Web ou de trabalho, o Microsoft Azure aloca recursos de computação.

Ocasionalmente, você pode receber erros durante essas operações mesmo antes de alcançar o limite de assinatura do Azure.

> [!TIP]
> As informações também poderão ser úteis caso você pretenda implantar serviços.

## <a name="symptom"></a>Sintoma

Em portal do Azure, navegue até o serviço de nuvem (clássico) e, na barra lateral, selecione *log de operação (clássico)* para exibir os logs.

![Imagem mostra a folha log de operação (clássico).](./media/cloud-services-troubleshoot-fabric-internal-server-error/cloud-services-troubleshoot-allocation-logs.png)

Quando você estiver inspecionando os logs do serviço de nuvem (clássico), verá a seguinte exceção:

|Exceção  |Mensagem de erro  |
|---------|---------|
FabricInternalServerError     |A operação falhou com o código de erro ' InternalError ' e errorMessage ' o servidor encontrou um erro interno. Repita a solicitação. '.|
|ServiceAllocationFailure     |A operação falhou com o código de erro ' InternalError ' e errorMessage ' o servidor encontrou um erro interno. Repita a solicitação. '.|

## <a name="cause"></a>Causa

*FabricInternalServerError* e *ServiceAllocationFailure* são exceções que podem ocorrer quando o controlador de malha falha ao alocar instâncias no cluster. A causa raiz varia se o serviço de nuvem estiver **fixado** ou **não estiver fixado**.

- [**Não fixado:** Falhas de uma primeira implantação de um novo serviço de nuvem](#not-pinned-to-a-cluster)
- [**Fixado:** Falhas de serviços de nuvem existentes](#pinned-to-a-cluster)

> [!NOTE]
> Quando a primeira instância é implantada em um serviço de nuvem (em preparo ou produção), esse serviço de nuvem é fixado em um cluster.
>
> Ao longo do tempo, os recursos nesse pool de recursos podem se tornar totalmente utilizados. Se um serviço de nuvem fizer uma solicitação de alocação para recursos adicionais quando recursos insuficientes estiverem disponíveis no pool de recursos fixado, a solicitação resultará em uma [falha de alocação](cloud-services-allocation-failures.md).

## <a name="solution"></a>Solução

Siga as diretrizes para falhas de alocação nos cenários a seguir.

### <a name="not-pinned-to-a-cluster"></a>Não fixado em um cluster

Na primeira vez que você implantar um serviço de nuvem (clássico), o cluster ainda não foi selecionado, portanto, o serviço de nuvem não será *fixado*. O Azure pode ter uma falha de implantação porque:

- Você selecionou um tamanho específico que não está disponível na região.
- A combinação de tamanhos que são necessários em diferentes funções não está disponível na região.

Quando ocorre um erro de alocação nesse cenário, o curso de ação recomendado é verificar os tamanhos disponíveis na região e alterar o tamanho especificado anteriormente.

1. Você pode verificar os tamanhos disponíveis em uma região na página de [produtos do serviço de nuvem (clássico)](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services) .

    > [!NOTE]
    > A página *produtos* não mostrará a capacidade disponível. Para qualquer nova alocação, o Azure deve ser capaz de escolher o cluster ideal em sua região nesse momento.

1. Atualize o arquivo de definição de serviço para seu serviço de nuvem (clássico) para especificar um [tamanho de produto](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) diferente da sua região.

### <a name="pinned-to-a-cluster"></a>Fixado em um cluster

Os serviços de nuvem existentes são *fixados* em um cluster. Todas as implantações adicionais para o serviço de nuvem (clássico) ocorrerão no mesmo cluster.

Quando ocorre um erro de alocação nesse cenário, o curso de ação recomendado é reimplantar em um novo serviço de nuvem (clássico) (e atualizar o *CNAME*).

> [!TIP]
> Essa solução provavelmente será a mais bem-sucedida, pois permite que a plataforma escolha entre todos os clusters nessa região.

> [!NOTE]
> Essa solução não causará qualquer tempo de inatividade.

1. Implante a carga de trabalho em um novo serviço de nuvem (clássico).
    - Consulte o guia [como criar e implantar um serviço de nuvem (clássico)](cloud-services-how-to-create-deploy-portal.md) para obter mais instruções.

    > [!WARNING]
    > Se não quiser perder o endereço IP associado a este slot de implantação, você poderá usar [a solução 3-manter o endereço IP](cloud-services-allocation-failures.md#solutions).

1. Atualize o *CNAME* ou *um* registro para apontar o tráfego para o novo serviço de nuvem (clássico).
    - Consulte o guia [Configurando um nome de domínio personalizado para um serviço de nuvem do Azure (clássico)](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) para obter mais instruções.

1. Uma vez que o tráfego zero vá para o site antigo, você pode excluir o serviço de nuvem antigo (clássico).
    - Consulte o guia [excluir implantações e um serviço de nuvem (clássico)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) para obter mais instruções.
    - Para ver o tráfego de rede em seu serviço de nuvem (clássico), consulte o [monitoramento introdução ao serviço de nuvem (clássico)](cloud-services-how-to-monitor.md).

Consulte [Solucionando problemas de falhas de alocação do serviço de nuvem (clássico) | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) para obter mais etapas de correção.

## <a name="next-steps"></a>Próximas etapas

Para obter mais soluções de falha de alocação e informações básicas:

> [!div class="nextstepaction"]
> [Falhas de alocação-serviço de nuvem (clássico)](cloud-services-allocation-failures.md)

Se o problema do Azure não for abordado neste artigo, visite os fóruns do Azure no [msdn e Stack Overflow](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou postar para [@AzureSupport no Twitter](https://twitter.com/AzureSupport). Você também pode enviar uma solicitação de suporte do Azure. Para enviar uma solicitação de suporte na página [Suporte do Azure](https://azure.microsoft.com/support/options/), selecione *Obter suporte*.
