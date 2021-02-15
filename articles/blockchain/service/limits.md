---
title: Limites do serviço Blockchain do Azure
description: Visão geral dos limites funcionais e de serviço no serviço Blockchain do Azure
ms.date: 04/02/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 71e1bebf10fa0142870d03977182472da1ad031f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80676526"
---
# <a name="limits-in-azure-blockchain-service"></a>Limites no serviço Blockchain do Azure

O serviço Blockchain do Azure tem limites funcionais e de serviço, como o número de nós que um membro pode ter, restrições de consórcio e valores de armazenamento.

## <a name="pricing-tier"></a>Tipo de preço

Os limites máximos em transações e nós de validador dependem se você provisiona o serviço Blockchain do Azure em tipos de preço básico ou padrão.

| Tipo de preço | Máximo de nós de transação | Máximo de nós de validador |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Sua rede do consórcio deve ter um mínimo de dois nós da camada padrão do serviço Blockchain do Azure. Os nós da camada standard incluem dois nós de validação. Quatro nós validadores são necessários para atender ao [consenso de tolerância a falhas símbolos de Istambul](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus).

Use a camada básica para desenvolvimento, teste e prova de conceitos. Use a camada Standard para implantações de nível de produção. Você também deverá usar o nível *Standard* se estiver usando o Gerenciador de Dados do Blockchain ou enviando um alto volume de transações particulares.

Não há suporte para a alteração do tipo de preço entre Básico e Standard após a criação do membro.

## <a name="storage-capacity"></a>Capacidade de armazenamento

A quantidade máxima de armazenamento que pode ser usada por nó para os dados e logs do razão é de 1,8 terabytes.

Não há suporte para a diminuição do tamanho do armazenamento do razão e do log.
## <a name="consortium-limits"></a>Limites do consórcio

* O **consórcio e os nomes de membros devem ser exclusivos** de outros nomes de consórcio e membro no serviço Blockchain do Azure.

* **Os nomes member e Consortium não podem ser alterados**

* **Todos os membros em um consórcio devem estar no mesmo tipo de preço**

* **Todos os membros que participam de um consórcio devem residir na mesma região**

    O primeiro membro criado em um consórcio determina a região. Membros convidados para o consórcio devem residir na mesma região que o primeiro membro. Limitar todos os membros à mesma região ajuda a garantir que o consenso da rede não seja afetado negativamente.

* **Um consórcio deve ter pelo menos um administrador**

    Se houver apenas um administrador em um consórcio, eles não poderão se remover do consórcio nem excluir seus membros até que outro administrador seja adicionado ou promovido no consórcio.

* **Os membros removidos do consórcio não podem ser adicionados novamente**

    Em vez disso, eles devem ser reconvidados para ingressar no consórcio e criar um novo membro. Os recursos de membro existentes não são excluídos para preservar as transações históricas.

* **Todos os membros em um consórcio devem usar a mesma versão do razão**

    Para obter mais informações sobre patches, atualizações e versões do razão disponíveis no serviço Blockchain do Azure, consulte [patches, atualizações e versões](ledger-versions.md).

## <a name="performance"></a>Desempenho

Não use *ETH. estimar* a função de gás para cada envio de transação. A função *ETH. estimation* tem uso intensivo de memória. Chamar a função várias vezes reduz as transações por segundo drasticamente.

Se possível, use um valor de gás conservador para enviar transações e minimizar o uso de *ETH. Estimate*.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas relacionadas a patches e atualizações de sistemas – [patches, atualizações e versões](ledger-versions.md).
