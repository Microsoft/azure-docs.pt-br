---
title: Balanceamento de métricas de subcluster
description: O efeito de restrições de posicionamento no balanceamento e como tratá-la
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 7f571a851e4da147240c524b742bcd652bc54181
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82183102"
---
# <a name="balancing-of-subclustered-metrics"></a>Balanceamento de métricas de subcluster

## <a name="what-is-subclustering"></a>O que é o subclustering

O agrupamento ocorre quando os serviços com restrições de posicionamento diferentes têm uma métrica comum e ambos relatam carga para ele. Se a carga relatada pelos serviços diferir significativamente, a carga total nos nós terá um grande desvio padrão e parecerá que o cluster está desequilibrado, mesmo quando ele tiver o melhor equilíbrio possível.

## <a name="how-subclustering-affects-load-balancing"></a>Como os subclusters afetam o balanceamento de carga

Se a carga relatada pelos serviços em nós diferentes diferir significativamente, pode parecer que há um grande desequilíbrio onde não há nenhum. Além disso, se o desequilíbrio falso causado pelo subclustering for maior do que o desequilíbrio real, ele terá o potencial de confundir o algoritmo de balanceamento do Gerenciador de recursos e produzir um saldo abaixo do ideal no cluster.

Por exemplo, digamos que temos quatro serviços e todos eles relatam uma carga para a métrica Metric1:

* Serviço A – tem uma restrição de posicionamento "NodeType = = frontend", relata uma carga de 10
* Serviço B – tem uma restrição de posicionamento "NodeType = = frontend", relata uma carga de 10
* Serviço C – tem uma restrição de posicionamento "NodeType = = backend", relata uma carga de 100
* Serviço D – tem uma restrição de posicionamento "NodeType = = backend", relata uma carga de 100
* E temos quatro nós. Dois deles têm NodeType definido como "frontend" e os outros dois são "back-end"

E temos o seguinte posicionamento:

<center>

![Exemplo de posicionamento de subcluster][Image1]
</center>

O cluster pode parecer desbalanceado, temos uma grande carga nos nós 3 e 4, mas esse posicionamento cria o melhor equilíbrio possível nessa situação.

O Resource Manager pode reconhecer situações de subclustering e, em quase todos os casos, ele pode produzir o equilíbrio ideal para a situação em questão.

Para algumas situações excepcionais, quando o Gerenciador de recursos não puder balancear de forma ideal uma métrica subclusterizada, ele ainda detectará o subclustering e gerará um relatório de integridade para avisá-lo para que você possa corrigir o problema.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Tipos de subclustering e como eles são tratados

As situações de subclustering podem ser classificadas em três categorias diferentes. A categoria de uma situação de agrupamento específica determina como ela será manipulada pelo Resource Manager.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>Primeira categoria – subclustering simples com grupos de nós não contíguos

Essa categoria tem a forma mais simples de subclustering em que os nós podem ser separados em grupos diferentes e cada serviço só pode ser colocado em nós em um desses grupos. Cada nó pertence a apenas um grupo e um grupo. A situação descrita acima pertence a essa categoria como a maioria das situações de subclustering. 

Para as situações nessa categoria, o Gerenciador de recursos pode produzir o equilíbrio ideal e nenhuma outra intervenção é necessária.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Segunda categoria – subclustering com grupos de nós hierárquicos

Essa situação ocorre quando um grupo de nós permitidos para um serviço é um subconjunto do grupo de nós permitido para outro serviço. O exemplo mais comum dessa situação é quando algum serviço tem uma restrição de posicionamento definida e outro serviço não tem nenhuma restrição de posicionamento e pode ser colocado em qualquer nó.

Exemplo:

* Serviço A: sem restrição de posicionamento
* Serviço B: restrição de posicionamento "NodeType = = frontend"
* Serviço C: restrição de posicionamento "NodeType = = back-end"

Essa configuração cria uma relação de superconjunto de subconjunto entre grupos de nós para diferentes serviços.

<center>

![Subconjuntos de superconjuntos][Image2]
</center>

Nessa situação, há uma chance de que um saldo ideal seja feito.

O Resource Manager reconhecerá essa situação e produzirá um relatório de integridade que aconselha você a dividir o serviço A em dois serviços – o serviço a1 que pode ser colocado em nós de front-end e no serviço a2 que pode ser colocado em nós de back-end. Isso nos levará de volta à primeira situação de categoria, que pode ser equilibrada de forma ideal.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Terceira categoria – subclustering com sobreposição parcial entre conjuntos de nós

Essa situação ocorre quando há uma sobreposição parcial entre conjuntos de nós nos quais alguns serviços podem ser colocados.

Por exemplo, se tivermos uma propriedade node chamada NodeColor e tivermos três nós:

* Nó 1: NodeColor = vermelho
* Nó 2: NodeColor = azul
* Nó 2: NodeColor = verde

E temos dois serviços:

* Serviço A: com a restrição de posicionamento "cor = = vermelho | | Cor = = azul "
* Serviço B: com a restrição de posicionamento "cor = = azul | | Cor = = verde "

Por isso, o serviço A pode ser colocado nos nós 1 e 2 e o serviço B pode ser colocado nos nós 2 e 3.

Nessa situação, há uma chance de que um saldo ideal seja feito.

O Resource Manager reconhecerá essa situação e produzirá um relatório de integridade que aconselha você a dividir alguns dos serviços.

Para essa situação, o Gerenciador de recursos não pode fornecer uma proposta como dividir os serviços, já que várias divisões podem ser feitas e não há como estimar qual é a melhor maneira de dividir os serviços.

## <a name="configuring-subclustering"></a>Configurando o agrupamento

O comportamento do Gerenciador de recursos sobre os subclusters pode ser modificado modificando os seguintes parâmetros de configuração:
* SubclusteringEnabled-Parameter determina se o Gerenciador de recursos levará o agrupamento em conta ao fazer o balanceamento de carga. Se esse parâmetro for desativado, o Gerenciador de recursos ignorará o subclustering e tentará obter o equilíbrio ideal em um nível global. O valor padrão desse parâmetro é false.
* SubclusteringReportingPolicy-determina como o Gerenciador de recursos emitirá relatórios de integridade para subclusters hierárquicos e de sobreposição parcial. Um valor de zero significa que os relatórios de integridade sobre os subclusters estão desativados, "1" significa que os relatórios de integridade de aviso serão produzidos para situações de subclustering de qualidade inferior e um valor de "2" produzirá relatórios de integridade "OK". O valor padrão para esse parâmetro é "1".

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>Próximas etapas
* Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md)
* Para descobrir como os serviços podem ser restritos apenas para serem colocados em determinados nós [, consulte Propriedades de nó e restrições de posicionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
