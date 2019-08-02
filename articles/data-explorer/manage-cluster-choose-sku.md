---
title: Selecione o SKU de VM correto para o cluster de Data Explorer do Azure
description: Este artigo descreve como selecionar o tamanho de SKU ideal para o cluster de Data Explorer do Azure.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: b0cf6eab86b0b932e44b6824305c23df01f35808
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383826"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Selecione o SKU de VM correto para o cluster de Data Explorer do Azure 

O Azure Data Explorer tem vários SKUs de VM dos quais escolher ao criar um novo cluster ou otimizar o cluster para uma carga de trabalho em alteração. As VMs foram cuidadosamente escolhidas para permitir o custo mais adequado para qualquer carga de trabalho. 

O tamanho e o SKU da VM do cluster de gerenciamento de dados são totalmente gerenciados pelo serviço de Data Explorer do Azure. Ele é determinado por fatores como o tamanho da VM do mecanismo e a carga de trabalho de ingestão. 

A SKU da VM para o cluster do mecanismo pode ser alterada a qualquer momento ao [escalar verticalmente o cluster](manage-cluster-vertical-scaling.md). Portanto, é melhor começar com o tamanho mínimo do SKU que se ajusta ao cenário inicial. Tenha em mente que a expansão do cluster resulta em tempo de inatividade de até 30 minutos enquanto o cluster é recriado com a nova SKU de VM.

> [!TIP]
> A computação [ri (instâncias reservadas)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) é aplicável ao cluster de data Explorer do Azure.  

Este artigo descreve as diferentes opções de SKU de VM e fornece os detalhes técnicos que podem ajudá-lo a fazer a melhor opção.

## <a name="select-the-cluster-type"></a>Selecione o tipo de cluster

O Azure Data Explorer oferece dois tipos de clusters:

* **Produção**: Os clusters de produção contêm dois nós para clusters de gerenciamento de dados e mecanismos e são operados sob o [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)de data Explorer do Azure.

* **Desenvolvimento/teste (sem SLA)** : Os clusters de desenvolvimento/teste têm um único nó D11_v2 para o cluster de mecanismo e um único nó D1 para o cluster de gerenciamento de dados. Esse tipo de cluster é a configuração de custo mais baixo devido à baixa contagem de instâncias e nenhuma cobrança de marcação do mecanismo. Não há SLA para essa configuração de cluster porque ela não tem redundância.

## <a name="sku-types"></a>Tipos de SKU

Ao criar um cluster de Data Explorer do Azure, selecione o SKU de VM *ideal* para a carga de trabalho planejada. O Azure Data Explorer tem duas famílias de SKU das quais escolher:

* **D_V2**: A SKU D é otimizada para computação e fornecida em dois tipos.
    * Própria VM
    * VM agrupada com discos de armazenamento Premium

* **LS**: A SKU L é otimizada para armazenamento. Ele tem um tamanho de SSD muito maior do que o SKU **D** com preço semelhante.

A tabela a seguir fornece as principais diferenças entre os tipos de SKU disponíveis:
 
|**Atributo** | **SKU D** | **SKU L**
|---|---|---
|**SKUs pequenos**|O tamanho mínimo é 11 ' com dois núcleos|O tamanho mínimo é ' L4 ' com quatro núcleos
|**Disponibilidade**|Disponível em todas as regiões (a versão DS + PS tem disponibilidade mais limitada)|Disponível em algumas regiões
|**Custo por cache de GB por núcleo**|Alta com a SKU D, baixa com a versão do DS + PS|Mais barato com a opção *pago pelo uso*
|**Preços de RI (instâncias reservadas)**|Alto desconto (mais de 55% para um compromisso de três anos)|Desconto inferior (20% para um compromisso de três anos)  

## <a name="select-your-cluster-vm"></a>Selecione a VM do cluster 

Para selecionar a VM do cluster, [Configure o dimensionamento vertical](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

As diferentes opções de SKU de VM permitem otimizar os custos para o desempenho necessário e os requisitos de cache ativo para o cenário desejado. Se o cenário exigir o desempenho ideal para um alto volume de consulta, o SKU ideal deverá ser otimizado para computação. Por outro lado, se o cenário exigir a consulta de grandes volumes de dados com carregamento de consulta relativamente menor, a SKU otimizada para armazenamento reduzirá os custos e ainda fornecerá excelente desempenho.

O número de instâncias por cluster para os SKUs pequenos é limitado, portanto, é preferível usar VMs maiores com RAM maior. O tamanho da RAM é necessário para alguns tipos de consulta que colocam mais demanda no recurso de RAM, como as consultas que `joins`usam. Portanto, ao considerar as opções de dimensionamento, é aconselhável escalar verticalmente para uma SKU maior do que a expansão adicionando mais instâncias.

## <a name="vm-options"></a>Opções de VM

A tabela a seguir fornece as especificações técnicas para as VMs do cluster Data Explorer do Azure:

|**Nome**| **Categoria** | **Tamanho do SSD** | **Núcleos** | **RAM** | **Discos de armazenamento Premium (1 TB)**| **Contagem mínima de instâncias por cluster** | **Contagem máxima de instâncias por cluster**
|---|---|---|---|---|---|---|---
|D11_v2| computação otimizada | 75 GB    | 2 | 14 GB | 0 | 1 | 8 (exceto para o SKU de desenvolvimento/teste em que é 1)
|D12_v2| computação otimizada | 150 GB   | 4 | 28 GB | 0 | 2 | 16
|D13_v2| computação otimizada | 307 GB   | 8 | 56 GB | 0 | 2 | 1\.000
|D14_v2| computação otimizada | 614 GB   | 16| 112 GB | 0 | 2 | 1\.000
|DS13_v2 + 1TB PS| armazenamento otimizado | 1 TB | 8 | 56 GB | 1 | 2 | 1\.000
|DS13_v2 + 2TB PS| armazenamento otimizado | 2 TB | 8 | 56 GB | 2 | 2 | 1\.000
|DS14_v2 + 3 TB PS| armazenamento otimizado | 3 TB | 16 | 112 GB | 2 | 2 | 1\.000
|DS14_v2 + 4 TB PS| armazenamento otimizado | 4 TB | 16 | 112 GB | 4 | 2 | 1\.000
|L4s_v1| armazenamento otimizado | 650 GB | 4 | 32 GB | 0 | 2 | 16
|L8s_v1| armazenamento otimizado | 1,3 TB | 8 | 64 GB | 0 | 2 | 1\.000
|L16s_1| armazenamento otimizado | 2,6 TB | 16| 128 GB | 0 | 2 | 1\.000

* Exiba a lista de SKU de VM atualizada por região usando a [API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)do Azure data Explorer ListSkus. 
* Saiba mais sobre as [diferentes SKUs de computação](/azure/virtual-machines/windows/sizes-compute). 

## <a name="next-steps"></a>Próximas etapas

* O cluster de mecanismos pode ser [expandido ou reduzido](manage-cluster-vertical-scaling.md) a qualquer momento, alterando o SKU da VM para necessidades diferentes. 

* O tamanho do cluster do mecanismo pode ser [dimensionado horizontalmente e verticalmente](manage-cluster-horizontal-scaling.md) para alterar a capacidade com as demandas em constante mudança.

