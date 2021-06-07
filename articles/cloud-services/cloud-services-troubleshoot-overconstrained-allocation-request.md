---
title: Solucionar problemas de OverconstrainedAllocationRequest ao implantar um serviço de nuvem (clássico) no Azure | Microsoft Docs
description: Este artigo mostra como resolver uma exceção OverconstrainedAllocationRequest ao implantar um serviço de nuvem (clássico) no Azure.
services: cloud-services
documentationcenter: ''
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 1b50ded166b3f62b38830b4c2d18da7c4c4f0d35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744192"
---
# <a name="troubleshoot-overconstrainedallocationrequest-when-deploying-cloud-services-classic-to-azure"></a>Solucionar problemas de OverconstrainedAllocationRequest ao implantar serviços de nuvem (clássico) no Azure

Neste artigo, você solucionará problemas de falhas de alocação restritas que impedem a implantação de serviços de nuvem do Azure (clássico).

Quando você implanta instâncias em um Serviço de Nuvem ou adiciona nova instâncias de função da Web ou de trabalho, o Microsoft Azure aloca recursos de computação.

Ocasionalmente, você pode receber erros durante essas operações mesmo antes de alcançar o limite de assinatura do Azure.

> [!TIP]
> As informações também poderão ser úteis caso você pretenda implantar serviços.

## <a name="symptom"></a>Sintoma

![Imagem mostra a folha log de operação (clássico).](./media/cloud-services-troubleshoot-overconstrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

|Tipo de exceção  |Mensagem de erro  |
|---------|---------|
|OverconstrainedAllocationRequest |O tamanho da VM (ou a combinação de tamanhos de VM) exigido por essa implantação não pode ser provisionado devido a restrições de solicitação de implantação. Se possível, tente relaxar as restrições, como associações de rede virtual, implantação em um serviço hospedado sem nenhuma outra implantação e em um grupo de afinidade diferente ou sem nenhum grupo de afinidade ou tente implantar em uma região diferente.|

## <a name="cause"></a>Causa

A causa raiz varia se o serviço de nuvem estiver **fixado** ou **não estiver fixado**.

- [**Não fixado:** Falhas de uma primeira implantação de um novo serviço de nuvem (clássico)](#not-pinned-to-a-cluster)
- [**Fixado:** Falhas de um serviço de nuvem existente (clássico)](#pinned-to-a-cluster)

> [!NOTE]
> Quando a primeira instância é implantada em um serviço de nuvem (em preparo ou produção), esse serviço de nuvem é fixado em um cluster.
>
> Ao longo do tempo, os recursos no cluster podem se tornar totalmente utilizados. Se um serviço de nuvem (clássico) fizer uma solicitação de alocação para recursos adicionais quando recursos insuficientes estiverem disponíveis no cluster fixado, a solicitação resultará em uma [falha de alocação](cloud-services-allocation-failures.md).

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
