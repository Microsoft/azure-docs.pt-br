---
title: Tipos de resolução de conflitos e políticas de resolução com múltiplas regiões de gravação no Azure Cosmos DB
description: Este artigo descreve as categorias de conflito e políticas de resolução de conflitos no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ebea55f769ca16bfa344d0a100fe16cec6d784d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892584"
---
# <a name="conflict-types-and-resolution-policies"></a>Tipos e políticas de resolução de conflitos

Conflitos e políticas de resolução de conflitos são aplicáveis se a conta do Azure Cosmos DB for configurada com várias regiões de gravação.

Para contas do Azure Cosmos configuradas com várias regiões de gravação, podem ocorrer conflitos de atualização quando os escritores atualizam simultaneamente o mesmo item em várias regiões. Conflitos de atualização podem ser de três tipos:

* **Conflitos da importação**: Esses conflitos podem ocorrer quando um aplicativo insere simultaneamente dois ou mais itens com o mesmo índice exclusivo em dois ou mais regiões. Por exemplo, esse conflito pode ocorrer com uma propriedade de ID.

* **Substituir conflitos**: Esses conflitos podem ocorrer quando um aplicativo atualiza o mesmo item simultaneamente em duas ou mais regiões.

* **Excluir conflitos**: Esses conflitos podem ocorrer quando um aplicativo exclui um item em uma região simultaneamente e atualiza-la em outra região.

## <a name="conflict-resolution-policies"></a>Políticas de resolução de conflitos

O Azure Cosmos DB oferece um mecanismo flexível orientada por diretivas para resolver conflitos de gravação. Você pode selecionar entre duas políticas de resolução de conflito em um contêiner do Azure Cosmos:

- **LWW (Última Gravação Vence)**: Essa política de resolução, por padrão, usa uma propriedade de carimbo de hora definida pelo sistema. Ele se baseia no protocolo de sincronização de hora de relógio. Se você usar a API do SQL, você pode especificar qualquer outra numérica propriedade personalizada (por exemplo, sua própria noção de um carimbo de hora) para ser usado para resolução de conflitos. Uma propriedade numérica personalizada também é conhecida como o *caminho de resolução de conflito*. 

  Se dois ou mais itens entram em conflito ou substituem operações, o item com o valor mais alto para o "caminho de resolução de conflitos" torna-se o "vencedor". O sistema determina o ganhador se vários itens tiverem o mesmo valor numérico para o caminho de resolução de conflito. Todas as regiões têm a garantia de convergir para um único vencedor e terminarão com a versão igual do item confirmado. Ao excluir conflitos que estão envolvidos, a versão excluída sempre supera o inserir ou substituir os conflitos. Esse resultado ocorre não importa o que é o valor do caminho de resolução de conflito.

  > [!NOTE]
  > O Last Writer Wins é o modo de resolução de conflitos padrão. Ele está disponível para as seguintes APIs: SQL, MongoDB, Cassandra, Gremlin e tabela.

  Para saber mais, confira [exemplos que usam políticas de resolução de conflitos LWW](how-to-manage-conflicts.md).

- **Personalizado**: Esta política de resolução é projetada para semântica definida pelo aplicativo para reconciliação de conflitos. Quando você definir essa política em seu contêiner do Azure Cosmos, você também precisará registrar um *mesclar o procedimento armazenado*. Esse procedimento é invocado automaticamente quando os conflitos são detectados em uma transação de banco de dados no servidor. O sistema fornece exatamente uma garantia para a execução de um procedimento de mesclagem como parte do protocolo de confirmação.  

  Se você configurar seu contêiner com a opção de resolução personalizada e você não conseguir registrar um procedimento de mesclagem no contêiner ou o procedimento de mesclagem gera uma exceção em tempo de execução, os conflitos são gravados para o *conflitos feed*. Seu aplicativo precisará resolver manualmente os conflitos no feed de conflitos. Para saber mais, confira [exemplos de como usar uma política de resolução personalizada e de como usar o feed de conflitos](how-to-manage-conflicts.md).

  > [!NOTE]
  > A política de resolução de conflitos personalizada só está disponível para contas da API do SQL.

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar políticas de resolução de conflitos:

* [Como configurar vários mestres em seus aplicativos](how-to-multi-master.md)
* [Como gerenciar políticas de resolução de conflitos](how-to-manage-conflicts.md)
* [Como ler de conflitos de feed](how-to-manage-conflicts.md#read-from-conflict-feed)
