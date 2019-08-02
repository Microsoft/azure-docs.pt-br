---
title: Cotas do serviço e limites - Lote do Azure | Microsoft Docs
description: Saiba mais sobre as restrições, limites e cotas padrão do Lote do Azure e como aumentar a cota da solicitação
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 312f6746cb02aa66b0e7f8b47cb10e52558fa542
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323169"
---
# <a name="batch-service-quotas-and-limits"></a>Cotas e limites de serviço do Lote

Como com outros serviços do Azure, há limites em determinados recursos associados ao serviço de Lote. Muitos desses limites são cotas padrão aplicadas pelo Azure no nível da assinatura ou da conta. Este artigo discute esses padrões e como você pode solicitar aumentos de cotas.

Lembre-se dessas cotas quando estiver projetando e dimensionando suas cargas de trabalho do Lote. Por exemplo, se seu pool não alcançar o número de destino de nós de computação especificado, talvez você tenha atingido o limite de cota de núcleos para sua conta do Lote.

Você pode executar várias cargas de trabalho do Lote em uma única conta do Lote ou distribuir suas cargas de trabalho entre contas do Lote que estão na mesma assinatura mas em diferentes regiões do Azure.

Se você planeja executar cargas de trabalho de produção em Lote, talvez seja necessário aumentar uma ou mais cotas para acima do padrão. Se desejar aumentar a cota, você poderá abrir uma [solicitação de atendimento ao cliente](#increase-a-quota) online gratuitamente.

## <a name="resource-quotas"></a>Cotas de recursos

Uma cota é um limite de crédito, não uma garantia de capacidade. Se você precisar de capacidade em larga escala, entre em contato com o suporte do Azure.

Observe também que as cotas não são valores garantidos. As cotas podem variar com base nas alterações do serviço de lote ou de uma solicitação de usuário para alterar um valor de cota.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Cotas de núcleo no modo de assinatura de usuário

Se você tiver criado uma conta de Lote com modo de alocação de pool definido como **assinatura de usuário**, as cotas serão aplicadas de forma diferente. Nesse modo, as VMs do Lote e outros recursos são criados diretamente em sua assinatura, quando um pool é criado. As cotas de núcleos de Lote do Azure não se aplicam a uma conta criada nesse modo. Em vez disso, as cotas em sua assinatura para regionais núcleos de computação e outros recursos são aplicados. Saiba mais sobre a [assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Limites de tamanho do pool

Os limites de tamanho do pool são definidos pelo serviço de lote. Ao contrário das cotas de [recursos](#resource-quotas), esses valores não podem ser alterados. Somente os pools com comunicação entre nós e imagens personalizadas têm restrições diferentes da cota padrão.

| **Recurso** | **Limite máximo** |
| --- | --- |
| **Nós de computação em [pool de computação entre nós habilitado](batch-mpi.md)**  ||
| Modo de alocação de pool de serviço de Lote | 100 |
| Modo de alocação de pool de assinatura do Lote | 80 |
| **Nós de computação dedicados em [pool criado com imagem de VM personalizada](batch-custom-images.md)** <sup>1</sup> ||
| Nós dedicados | 2000 |
| Nós de baixa prioridade | 1000 |

<sup>1</sup> Para pools que não têm comunicação entre nós ativada.

## <a name="other-limits"></a>Outros limites

Limites adicionais definidos pelo serviço de lote. Ao contrário das cotas de [recursos](#resource-quotas), esses valores não podem ser alterados.

| **Recurso** | **Limite máximo** |
| --- | --- |
| [Tarefas simultâneas](batch-parallel-node-tasks.md) por nó de computação | 4 vezes o número de núcleos de nó |
| [Aplicativos](batch-application-packages.md) por conta do Lote | 20 |
| Pacotes de aplicativos por aplicativo | 40 |
| Pacotes de aplicativos por pool | 10 |
| Tempo de vida máximo da tarefa | 180 dias<sup>1</sup> |

<sup>1</sup> O tempo de vida máximo de uma tarefa, desde quando é adicionada ao trabalho até ser concluída, é de 180 dias. As tarefas concluídas persistem por sete dias; os dados para tarefas não concluídas no tempo de vida máximo não estão acessíveis.

## <a name="view-batch-quotas"></a>Exibir cotas do Lote

Exiba suas cotas de conta do lote no [portal do Azure][portal].

1. Selecione **Contas do Lote** no portal e selecione a conta do Lote na qual você está interessado.
1. Selecione **Cotas** no menu da conta do Lote.
1. Exibe as cotas atualmente aplicadas à conta do Lote

    ![Cotas para conta do Lote][account_quotas]

## <a name="increase-a-quota"></a>Aumentar uma cota

Siga estas etapas para solicitar um aumento de cota para sua conta do lote ou sua assinatura usando o [portal do Azure][portal]. O tipo de aumento de cota depende do modo de alocação de pool de sua conta do lote. Para solicitar um aumento de cota, você deve incluir a série de VMs para a qual deseja aumentar a cota. Quando o aumento da cota é aplicado, ele é aplicado a todas as séries de VMs.

### <a name="increase-cores-quota-in-batch"></a>Aumentar a cota de núcleos no lote 

1. Selecione o bloco **Ajuda + suporte** no painel do portal ou o ponto de interrogação ( **?** ) no canto superior direito do portal.
1. Selecione **Nova solicitação de suporte** > **Fundamentos**.
1. Em **Noções básicas**:
   
    a.  > **Serviço do tipo de problema e limites de assinatura (cotas)**
   
    b. Selecione sua assinatura.
   
    c. **Tipo de cota** > **Lote**
      
    Selecione **Avançar**.
    
1. Em **Detalhes**:
      
    a. Em **fornecer detalhes**, especifique o local, o tipo de cota e a conta do lote.
    
    ![Aumento da cota de lote][quota_increase]

    Os tipos de cota incluem:

    * **Por conta do lote**  
        Valores específicos de uma única conta do lote, incluindo núcleos dedicados e de baixa prioridade, e o número de trabalhos e pools.
        
    * **Por região**  
        Valores que se aplicam a todas as contas do lote em uma região e incluem o número de contas do lote por região por assinatura.

    A cota de baixa prioridade é um único valor em todas as séries da VM. Se você precisar de SKUs restritos, deverá selecionar núcleos de **baixa prioridade** e incluir as famílias de VMs a serem solicitadas.

    b. Selecione uma **severidade** de acordo com o impacto do seu [negócio][support_sev].

    Selecione **Avançar**.

1. Em **Informações de contato**:
   
    a. Selecione um **método de contato preferencial**.
   
    b. Verifique e insira os detalhes de contato necessários.
   
    Selecione **criar** para enviar a solicitação de suporte.

Depois que a solicitação de suporte foi enviada, o suporte do Azure entrará em contato com você. As solicitações de cota podem ser concluídas em alguns minutos ou até dois dias úteis.

## <a name="related-quotas-for-vm-pools"></a>Cotas relacionadas para pools VM

Os pools de lota na Configuração de Máquina virtual implantada em uma rede virtual do Azure automaticamente aloca os recursos de rede adicionais do Azure. Os recursos a seguir são necessárias para cada nós de pool de 50 em uma rede virtual:

* Um [grupo de segurança de rede](../virtual-network/security-overview.md#network-security-groups)
* Um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Um [balanceador de carga](../load-balancer/load-balancer-overview.md)

Esses recursos são alocados na assinatura que contém a rede virtual fornecida ao criar o pool de Lote. Esses recursos são limitados pelas [cotas de recursos](../azure-subscription-service-limits.md) da assinatura. Se você planejar implantações de grande pool em uma rede virtual, verifique as cotas da assinatura para esses recursos. Se necessário, solicite um aumento no portal do Azure, selecionando **Ajuda + suporte**.


## <a name="related-topics"></a>Tópicos relacionados
* [Criar uma conta do Lote do Azure usando o portal do Azure](batch-account-create-portal.md)
* [Visão geral dos recursos do Lote do Azure](batch-api-basics.md)
* [Assinatura do Azure e limite de serviços, cotas e restrições](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
