---
title: Grupos inteligentes
description: Grupos inteligentes são as agregações de alertas que ajudam você a reduzir o ruído de alerta
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: 8a164fe6e5fd5be58da969d9266329755705ea82
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037958"
---
# <a name="smart-groups"></a>Grupos inteligentes

Um desafio comum enfrentado ao lidar com alertas é ignorar o ruído para descobrir o que realmente importa – grupos inteligentes devem ser a solução para esse problema.  

Grupos inteligentes são criados automaticamente usando algoritmos de aprendizado de máquina para combinar alertas relacionados que representam um único problema.  Quando um alerta é criado, o algoritmo o adiciona a um novo grupo inteligente ou a um grupo inteligente existente com base em informações, como padrões históricos, propriedades semelhantes e estrutura semelhante. Por exemplo, se a porcentagem de CPU em várias máquinas virtuais em uma assinatura tiver picos simultâneos, levando a vários alertas individuais, e se esses alertas ocorreram juntos no passado, esses alertas provavelmente serão agrupados em um único Grupo inteligente, sugerindo uma possível causa raiz comum. Isso significa que, para alguém solucionando problemas de alertas, grupos inteligentes não só permitem reduzir o ruído ao gerenciar alertas relacionados como uma única unidade agregada, como também orientam na direção de possíveis causas raiz comuns para os alertas.

Atualmente, o algoritmo considera apenas alertas do mesmo serviço de monitor em uma assinatura. Os grupos inteligentes podem reduzir até 99% do ruído de alerta por meio dessa consolidação. É possível ver o motivo pelo qual os alertas foram incluídos em um grupo na página de detalhes do grupo inteligente.

Você pode exibir os detalhes dos grupos inteligentes e definir o estado, de forma semelhante como é possível com alertas. Cada alerta é um membro de apenas um grupo inteligente. 

## <a name="smart-group-state"></a>Estado do grupo inteligente

O estado do grupo inteligente é um conceito semelhante para o estado de alerta, que permite que você gerencie o processo de resolução no nível de um grupo inteligente. Assim como o estado de alerta, quando um grupo inteligente é criado, ele tem o estado **Novo**, que pode ser alterado para **Confirmado** ou **Fechado**.

Os seguintes estados de grupo inteligente são compatíveis.

| Estado | Descrição |
|:---|:---|
| Novo | O problema acaba de ser detectado e ainda não foi analisado. |
| Confirmado | Um administrador examinou o grupo inteligente e começou a trabalhar nele. |
| Fechadas | O problema foi resolvido. Depois que um grupo inteligente for fechado, será possível reabri-lo, alterando-o para outro estado. |

[Saiba como alterar o estado do seu grupo inteligente.](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

> [!NOTE]
>  Alterar o estado de um grupo inteligente não altera o estado dos alertas de membro individual.

## <a name="smart-group-details-page"></a>Página de detalhes do grupo inteligente

A página de detalhes do grupo inteligente é exibida aos selecionar um grupo inteligente. Ela fornece detalhes sobre o grupo inteligente, incluindo o motivo usado para criar o grupo e permite alterar o estado.
 
![Detalhe do grupo inteligente](media/alerts-smartgroups-overview/smart-group-detail.png)


A página de detalhes do grupo inteligente inclui as seções a seguir.

| Seção | Descrição |
|:---|:---|
| Alertas | Lista os alertas individuais que estão incluídos no grupo inteligente. Selecione um alerta para abrir a página de detalhes do alerta. |
| Histórico | Lista cada ação tomada pelo grupo inteligente e quaisquer alterações feitas no grupo. Atualmente, isso se limita a alterações de estado e alterações de associação do alerta. |

## <a name="smart-group-taxonomy"></a>Taxonomia de grupo inteligente

O nome de um grupo inteligente é o nome do seu primeiro alerta. Você não pode criar ou renomear um grupo inteligente.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar grupos inteligentes](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Alterar o estado do grupo inteligente e o alerta](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)