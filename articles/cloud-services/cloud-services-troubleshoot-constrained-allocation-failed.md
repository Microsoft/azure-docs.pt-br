---
title: Solucionar problemas de ConstrainedAllocationFailed ao implantar um serviço de nuvem (clássico) no Azure | Microsoft Docs
description: Este artigo mostra como resolver uma exceção ConstrainedAllocationFailed ao implantar um serviço de nuvem (clássico) no Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 346e7eb77039ab80e6f9dffb8ea8360198040504
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738269"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Solucionar problemas de ConstrainedAllocationFailed ao implantar um serviço de nuvem (clássico) no Azure

Neste artigo, você solucionará problemas de falhas de alocação em que os serviços de nuvem do Azure (clássicos) não podem implantar devido a restrições de alocação.

Quando você implanta instâncias em um serviço de nuvem (clássico) ou adiciona novas instâncias de função Web ou de trabalho, Microsoft Azure aloca recursos de computação.

Ocasionalmente, você pode receber erros durante essas operações mesmo antes de alcançar o limite de assinatura do Azure.

> [!TIP]
> As informações também poderão ser úteis caso você pretenda implantar serviços.

## <a name="symptom"></a>Sintoma

Em portal do Azure, navegue até o serviço de nuvem (clássico) e, na barra lateral, selecione *log de operação (clássico)* para exibir os logs.

![Imagem mostra a folha log de operação (clássico).](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

Quando você estiver inspecionando os logs do serviço de nuvem (clássico), verá a seguinte exceção:

|Tipo de exceção  |Mensagem de erro  |
|---------|---------|
|ConstrainedAllocationFailed     |A operação do Azure ' `{Operation ID}` ' falhou com o código COMPUTE. ConstrainedAllocationFailed. Detalhes: falha na alocação; Não é possível satisfazer as restrições na solicitação. A implantação solicitada do novo serviço está associada a um Grupo de Afinidades ou é destinada a uma Rede Virtual, ou há uma implantação existente para esse serviço hospedado. Essas condições restringem a nova implantação para os recursos específicos do Microsoft Azure. Tente novamente mais tarde ou tente reduzir o tamanho da VM ou o número de instâncias de função. De forma alternativa, remova as restrições mencionadas acima ou tente implantar em uma região diferente, se possível.|

## <a name="cause"></a>Causa

Quando a primeira instância é implantada em um serviço de nuvem (em preparo ou produção), esse serviço de nuvem é fixado em um cluster.

Ao longo do tempo, os recursos nesse cluster podem se tornar totalmente utilizados. Se um serviço de nuvem (clássico) fizer uma solicitação de alocação para mais recursos quando recursos insuficientes estiverem disponíveis no cluster fixado, a solicitação resultará em uma falha de alocação. Para obter mais informações, consulte [problemas comuns de falha de alocação](cloud-services-allocation-failures.md#common-issues).

## <a name="solution"></a>Solução

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