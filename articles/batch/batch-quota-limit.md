---
title: Cotas e limites de serviço
description: Saiba mais sobre as restrições, limites e cotas padrão do Lote do Azure e como aumentar a cota da solicitação
ms.topic: conceptual
ms.date: 01/28/2021
ms.custom: seodec18
ms.openlocfilehash: 433272c76b9ff27d9cad542cf65a8ec0d8fc0378
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052373"
---
# <a name="batch-service-quotas-and-limits"></a>Cotas e limites de serviço do Lote

Como com outros serviços do Azure, há limites em determinados recursos associados ao serviço de Lote. Muitos desses limites são cotas padrão aplicadas pelo Azure no nível da assinatura ou da conta.

Lembre-se dessas cotas quando estiver projetando e dimensionando suas cargas de trabalho do Lote. Por exemplo, se seu pool não alcançar o número de destino de nós de computação especificado, talvez você tenha atingido o limite de cota de núcleos para sua conta do Lote.

Você pode executar várias cargas de trabalho do Lote em uma única conta do Lote ou distribuir suas cargas de trabalho entre contas do Lote que estão na mesma assinatura, mas em diferentes regiões do Azure.

Se você planeja executar cargas de trabalho de produção em Lote, talvez seja necessário aumentar uma ou mais cotas para acima do padrão. Se desejar aumentar a cota, você poderá abrir uma [solicitação de atendimento ao cliente](#increase-a-quota) online gratuitamente.

## <a name="resource-quotas"></a>Cotas de recursos

Uma cota é um limite, não uma garantia de capacidade. Se você precisar de capacidade em larga escala, entre em contato com o suporte do Azure.

Observe também que as cotas não são valores garantidos. As cotas podem variar dependendo das alterações do serviço de lote ou de uma solicitação de usuário para alterar o valor de uma cota.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>Cotas de núcleo

### <a name="cores-quotas-in-batch-service-mode"></a>Cotas de núcleos no modo de serviço do lote

Existem cotas de núcleo para cada série de VMs com suporte do lote e são exibidas na página **cotas** no Portal. Os limites de cota da série de VMs podem ser atualizados com uma solicitação de suporte, conforme detalhado abaixo. Para nós dedicados, o lote impõe um limite de cota de núcleo para cada série de VM, bem como um limite de cota de núcleo total para toda a conta do lote. Para nós de baixa prioridade, o lote impõe apenas uma cota de núcleo total para a conta do lote sem qualquer distinção entre séries de VM diferentes.

### <a name="cores-quotas-in-user-subscription-mode"></a>Cotas de núcleo no modo de assinatura de usuário

Se você criou uma [conta do lote](accounts.md) com o modo de alocação do pool definido como **assinatura do usuário**, as VMs do lote e outros recursos serão criados diretamente em sua assinatura quando um pool for criado ou redimensionado. As cotas de núcleo do lote do Azure não se aplicam e as cotas em sua assinatura para núcleos de computação regionais, núcleos de computação por série e outros recursos são usadas e impostas.

Para saber mais sobre essas cotas, confira [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Limites de tamanho do pool

O serviço de lote define os limites de tamanho do pool. Ao contrário das [cotas de recursos](#resource-quotas), esses valores não podem ser alterados. Apenas pools com comunicação entre nós e imagens personalizadas têm restrições diferentes da cota padrão.

| **Recurso** | **Limite máximo** |
| --- | --- |
| **Nós de computação em [pool de computação entre nós habilitado](batch-mpi.md)**  ||
| Modo de alocação de pool de serviço de Lote | 100 |
| Modo de alocação de pool de assinatura do Lote | 80 |
| **Nós de computação no [pool criado com um recurso de imagem gerenciada](batch-custom-images.md)** <sup>1</sup> ||
| Nós dedicados | 2000 |
| Nós de baixa prioridade | 1000 |

<sup>1</sup> Para pools que não têm comunicação entre nós ativada.

## <a name="other-limits"></a>Outros limites

Esses limites adicionais são definidos pelo serviço de lote. Ao contrário das [cotas de recursos](#resource-quotas), esses valores não podem ser alterados.

| **Recurso** | **Limite máximo** |
| --- | --- |
| [Tarefas simultâneas](batch-parallel-node-tasks.md) por nó de computação | 4 vezes o número de núcleos de nó |
| [Aplicativos](batch-application-packages.md) por conta do Lote | 20 |
| Pacotes de aplicativos por aplicativo | 40 |
| Pacotes de aplicativos por pool | 10 |
| Tempo de vida máximo da tarefa | 180 dias<sup>1</sup> |
| [Montagens](virtual-file-mount.md) por nó de computação | 10 |
| Certificados por pool | 12 |

<sup>1</sup> O tempo de vida máximo de uma tarefa, desde quando é adicionada ao trabalho até ser concluída, é de 180 dias. As tarefas concluídas persistem por sete dias. Os dados das tarefas não concluídas dentro do tempo de vida máximo não podem ser acessados.

## <a name="view-batch-quotas"></a>Exibir cotas do Lote

Para exibir suas cotas de conta do Lote no [portal do Azure](https://portal.azure.com):

1. Selecione **Contas do Lote** e selecione a conta do Lote na qual você tem interesse.
1. Selecione **Cotas** no menu da conta do Lote.
1. Exiba as cotas atualmente aplicadas à conta do Lote.

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Captura de tela mostrando cotas de conta do lote no portal do Azure.":::

## <a name="increase-a-quota"></a>Aumentar uma cota

Você pode solicitar um aumento de cota para sua conta do Lote ou sua assinatura usando o [portal do Azure](https://portal.azure.com). O tipo de aumento de cota depende do modo de alocação de pool de sua conta do lote. Para solicitar um aumento de cota, você deve incluir a série de VM para a qual deseja aumentar a cota. Quando o aumento da cota é aplicado, ele é aplicado a todas as séries de VMs.

1. Selecione o bloco **Ajuda + suporte** no painel do portal ou o ponto de interrogação ( **?** ) no canto superior direito do portal.
1. Selecione **Nova solicitação de suporte** > **Fundamentos**.
1. Em **Noções básicas**:

    1. **Tipo de problema** > **Limites de serviço e assinatura (cotas)**

    1. Selecione sua assinatura.

    1. **Tipo de cota** > **Lote**

       Selecione **Avançar**.

1. Em **Detalhes**:

    1. Em **Fornecer detalhes**, especifique o local, o tipo de cota e a conta do Lote.

       :::image type="content" source="media/batch-quota-limit/quota-increase.png" alt-text="Captura da tela de detalhes da cota ao solicitar um aumento de cota.":::

       Os tipos de cota incluem:

       * **Por conta do Lote**  
         Valores específicos para uma única conta do Lote, incluindo núcleos dedicados e de baixa prioridade, e número de trabalhos e pools.

       * **Por região**  
         Valores que se aplicam a todas as contas do Lote em uma região e incluem o número de contas do Lote por região por assinatura.

       A cota de baixa prioridade é um valor único em todas as séries da VM. Se precisar de SKUs restritos, você deverá selecionar **Núcleos de baixa prioridade** e incluir as famílias da VM a serem solicitadas.

    1. Selecione uma **Gravidade** de acordo com o [impacto nos negócios](https://aka.ms/supportseverity).

       Selecione **Avançar**.

1. Em **Informações de contato**:

    1. Selecione um **método de contato preferencial**.

    1. Verifique e insira os detalhes de contato necessários.

       Selecione **Criar** para enviar a solicitação de suporte.

Depois que a solicitação de suporte foi enviada, o suporte do Azure entrará em contato com você. As solicitações de cota podem ser concluídas em alguns minutos ou em até dois dias úteis.

## <a name="related-quotas-for-vm-pools"></a>Cotas relacionadas para pools VM

Os pools de lota na Configuração de Máquina virtual implantada em uma rede virtual do Azure automaticamente aloca os recursos de rede adicionais do Azure. Os recursos a seguir são necessárias para cada nós de pool de 50 em uma rede virtual:

- Um [grupo de segurança de rede](../virtual-network/network-security-groups-overview.md#network-security-groups)
- Um [endereço IP público](../virtual-network/public-ip-addresses.md)
- Um [balanceador de carga](../load-balancer/load-balancer-overview.md)

Esses recursos são alocados na assinatura que contém a rede virtual fornecida ao criar o pool de Lote. Esses recursos são limitados pelas [cotas de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md) da assinatura. Se você planejar implantações de grande pool em uma rede virtual, verifique as cotas da assinatura para esses recursos. Se necessário, solicite um aumento no portal do Azure, selecionando **Ajuda + suporte**.

## <a name="next-steps"></a>Próximas etapas

* [Crie uma conta do Lote do Azure usando o portal do Azure](batch-account-create-portal.md).
* Saiba mais sobre o [Fluxo de trabalho e recursos primários do serviço de lote](batch-service-workflow-features.md) como pools, nós, trabalhos e tarefas.
* Conheça a [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png