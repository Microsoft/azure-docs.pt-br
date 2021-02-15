---
title: Co-localizar VMs
description: Saiba mais sobre como colocalizar recursos de VM do Azure para melhorar a latência.
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 12/07/2020
ms.reviewer: zivr
ms.openlocfilehash: e193b7d084723ad50ddc0d1a40e0b6c8a111af73
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96924151"
---
# <a name="co-locate-resources-for-improved-latency"></a>Colocalizar recursos para maior latência

Ao implantar seu aplicativo no Azure, a difusão de instâncias entre regiões ou zonas de disponibilidade cria a latência de rede, o que pode afetar o desempenho geral do seu aplicativo. 

## <a name="proximity-placement-groups"></a>Grupos de posicionamento de proximidade

Colocar VMs em uma única região reduz a distância física entre as instâncias. Colocá-los em uma única zona de disponibilidade também os colocará fisicamente juntos. No entanto, à medida que a superfície do Azure cresce, uma única zona de disponibilidade pode abranger vários data centers físicos, o que pode resultar em uma latência de rede impactando seu aplicativo. 

Para obter as VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-las em um grupo de posicionamento de proximidade.

Um grupo de posicionamento por proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos uns dos outros. Os grupos de posicionamento por proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.


- Baixa latência entre VMs autônomas.
- Baixa latência entre VMs em um único conjunto de disponibilidade ou um conjunto de dimensionamento de máquinas virtuais. 
- Baixa latência entre VMs autônomas, VMs em vários conjuntos de disponibilidade ou vários conjuntos de dimensionamento. Você pode ter vários recursos de computação em um único grupo de posicionamento para reunir um aplicativo em várias camadas. 
- Baixa latência entre várias camadas de aplicativo usando tipos de hardware diferentes. Por exemplo, executar o back-end usando a série M em um conjunto de disponibilidade e o front-end em uma instância da série D, em um conjunto de dimensionamento, em um único grupo de posicionamento de proximidade.


![Gráfico para grupos de posicionamento de proximidade](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Usando grupos de posicionamento de proximidade 

Um grupo de posicionamento de proximidade é um novo tipo de recurso no Azure. Você precisa criar um antes de usá-lo com outros recursos. Depois de criado, ele pode ser usado com máquinas virtuais, conjuntos de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais. Você especifica um grupo de posicionamento de proximidade ao criar recursos de computação, fornecendo a ID de grupo de posicionamento de proximidade. 

Você também pode mover um recurso existente para um grupo de posicionamento de proximidade. Ao mover um recurso para um grupo de posicionamento de proximidade, você deve parar (desalocar) o ativo primeiro, pois ele será reimplantado potencialmente em um data center diferente na região para atender à restrição de colocalização. 

No caso de conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais, você deve definir o grupo de posicionamento de proximidade no nível de recurso em vez de máquinas virtuais individuais. 

Um grupo de posicionamento de proximidade é uma restrição de colocalização em vez de um mecanismo de fixação. Ele é fixado em um data center específico com a implantação do primeiro recurso para usá-lo. Depois que todos os recursos que usam o grupo de posicionamento de proximidade tiverem sido interrompidos (desalocados) ou excluídos, eles não serão mais fixados. Portanto, ao usar um grupo de posicionamento de proximidade com várias séries de VM, é importante especificar todos os tipos necessários antecipadamente em um modelo quando possível ou seguir uma sequência de implantação que melhorará suas chances de uma implantação bem-sucedida. Se sua implantação falhar, reinicie a implantação com o tamanho da VM que falhou como o primeiro tamanho a ser implantado.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>O que esperar ao usar grupos de posicionamento de proximidade 
Os grupos de posicionamento de proximidade oferecem colocalização na mesma data center. No entanto, como os grupos de posicionamento de proximidade representam uma restrição de implantação adicional, podem ocorrer falhas de alocação. Há alguns casos de uso em que você pode ver falhas de alocação ao usar grupos de posicionamento de proximidade:

- Quando você solicitar a primeira máquina virtual no grupo de posicionamento de proximidade, a data center será selecionada automaticamente. Em alguns casos, uma segunda solicitação para um SKU de máquina virtual diferente poderá falhar se não existir nesse data center. Nesse caso, um erro **OverconstrainedAllocationRequest** é retornado. Para evitar isso, tente alterar a ordem na qual você implanta suas SKUs ou ter os dois recursos implantados usando um único modelo ARM.
-   No caso de cargas de trabalho elásticas, em que você adiciona e remove instâncias de VM, ter uma restrição de grupo de posicionamento de proximidade em sua implantação pode resultar em uma falha ao atender à solicitação, resultando em erro **AllocationFailure** . 
- Parar (desalocar) e iniciar suas VMs conforme necessário é outra maneira de obter elasticidade. Como a capacidade não é mantida quando você parar (desalocar) uma VM, iniciá-la novamente pode resultar em um erro de **AllocationFailure** .

## <a name="planned-maintenance-and-proximity-placement-groups"></a>Manutenção planejada e grupos de posicionamento de proximidade

Os eventos de manutenção planejada, como o encerramento de hardware em um datacenter do Azure, poderiam afetar o alinhamento dos recursos nos grupos de posicionamento de proximidade. Os recursos podem ser movidos para um data center diferente, interrompendo as expectativas de colocação e latência associadas ao grupo de posicionamento de proximidade.

### <a name="check-the-alignment-status"></a>Verificar o status de alinhamento

Você pode fazer o seguinte para verificar o status de alinhamento de seus grupos de posicionamento de proximidade.


- O status de colocação do grupo de posicionamento de proximidade pode ser exibido usando o portal, a CLI e o PowerShell.

    -   Ao usar o PowerShell, o status de colocalização pode ser obtido usando Get-AzProximityPlacementGroup cmdlet, incluindo o parâmetro opcional '-ColocationStatus '.

    -   Ao usar a CLI, o status de colocalização pode ser obtido usando `az ppg show` incluindo o parâmetro opcional '--include-outlocation-status '.

- Para cada grupo de posicionamento de proximidade, uma propriedade de **status de colocalização** fornece o resumo do status de alinhamento atual dos recursos agrupados. 

    - **Alinhado**: o recurso está dentro do mesmo envelope de latência do grupo de posicionamento de proximidade.

    - **Desconhecido**: pelo menos um dos recursos da VM é desalocado. Depois de reiniciá-los com êxito, o status deve voltar para **alinhado**.

    - **Não alinhado**: pelo menos um recurso de VM não está alinhado ao grupo de posicionamento de proximidade. Os recursos específicos que não estão alinhados também serão chamados separadamente na seção de associação

- Para conjuntos de disponibilidade, você pode ver informações sobre o alinhamento de VMs individuais na página Visão geral do conjunto de disponibilidade.

- Para conjuntos de dimensionamento, informações sobre alinhamento de instâncias individuais podem ser vistas na guia **instâncias** da página **visão geral** do conjunto de dimensionamento. 


### <a name="re-align-resources"></a>Alinhar recursos novamente 

Se um grupo de posicionamento de proximidade for `Not Aligned` , você poderá stop\deallocate e reiniciar os recursos afetados. Se a VM estiver em um conjunto de disponibilidade ou em um conjunto de dimensionamento, todas as VMs no conjunto de disponibilidade ou conjunto de dimensionamento devem ser stopped\deallocated primeiro antes de reiniciá-las.

Se houver uma falha de alocação devido a restrições de implantação, talvez você precise stop\deallocate todos os recursos no grupo de posicionamento de proximidade afetado (incluindo os recursos alinhados) primeiro e, em seguida, reiniciá-los para restaurar o alinhamento.

## <a name="best-practices"></a>Práticas recomendadas 
- Para a menor latência, use grupos de posicionamento de proximidade junto com a rede acelerada. Para obter mais informações, consulte [criar uma máquina virtual Linux com rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md) ou [criar uma máquina virtual do Windows com rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md).
- Implante todos os tamanhos de VM em um único modelo. Para evitar a aterrissagem de hardware que não dá suporte a todos os tamanhos e SKUs de VM necessários, inclua todas as camadas de aplicativo em um único modelo para que todas sejam implantadas ao mesmo tempo.
- Se você estiver criando scripts para sua implantação usando o PowerShell, a CLI ou o SDK, poderá obter um erro de alocação `OverconstrainedAllocationRequest` . Nesse caso, você deve parar/desalocar todas as VMs existentes e alterar a sequência no script de implantação para começar com os tamanhos/SKU da VM que falharam. 
- Ao reutilizar um grupo de posicionamento existente do qual as VMs foram excluídas, aguarde até que a exclusão seja concluída completamente antes de adicionar VMs a ela.
- Se a latência for sua primeira prioridade, coloque as VMs em um grupo de posicionamento de proximidade e toda a solução em uma zona de disponibilidade. Mas, se a resiliência for sua prioridade máxima, espalhe suas instâncias entre várias zonas de disponibilidade (um único grupo de posicionamento de proximidade não pode abranger zonas).

## <a name="next-steps"></a>Próximas etapas

Implantar uma VM em um grupo de posicionamento de proximidade usando o [CLI do Azure](./linux/proximity-placement-groups.md) ou o [PowerShell](./windows/proximity-placement-groups.md)

Saiba como [testar a latência de rede](../virtual-network/virtual-network-test-latency.md).

Saiba como [otimizar a taxa de transferência de rede](../virtual-network/virtual-network-optimize-network-bandwidth.md).  

Saiba como [usar grupos de posicionamento de proximidade com aplicativos SAP](./workloads/sap/sap-proximity-placement-scenarios.md).