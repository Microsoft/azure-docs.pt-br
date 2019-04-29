---
title: Mapeamento de transformação de linha de alteração de fluxo de dados do Azure Data Factory
description: Como atualizar o destino de banco de dados usando o Azure Data Factory mapeamento fluxo Alter linha transformação de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: d842898ca700490ae99b46140be6609622a144df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627061"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Transformação de linha Alter do Azure Data Factory

Use a transformação de linha de alteração para definir políticas de upsert, delete, update e insert em linhas. Você pode adicionar condições de um-para-muitos como expressões. Cada uma dessas condições pode resultar em uma linha (ou linhas) que está sendo inserido, atualizado, excluído ou upsert. Linha de alteração pode gerar ações DDL e DML no banco de dados.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Alterar as configurações de linha](media/data-flow/alter-row1.png "Alter Settings de linha")

> [!NOTE]
> Transformações de linha de ALTER só funciona em Coletores de banco de dados em seu fluxo de dados. As ações que você atribui a linhas (insert, update, delete, upsert) não ocorrerá durante as sessões de depuração. Você deve adicionar uma tarefa de execução de fluxo de dados a um pipeline e usar a depuração de pipeline ou gatilhos para aplicar as diretivas de linha de alteração em suas tabelas de banco de dados.

## <a name="view-policies"></a>Exibir políticas

Alternar o modo de fluxo de dados de depuração como ativado e, em seguida, exibir os resultados de suas políticas de linha de alteração no painel de visualização de dados. Execução de uma linha de alteração no modo de fluxo de dados de depuração não produzirá ações DML ou DDL em relação a seu destino. Para que essas ações ocorrem, execute o fluxo de dados dentro de uma atividade de execução de fluxo de dados dentro de um pipeline.

![Alterar as políticas de linha](media/data-flow/alter-row3.png "alterar políticas de linha")

Isso permitirá que você possa verificar e exibir o estado de cada linha com base em suas condições. Ícone representa para cada instrução insert, update, delete e upsert ação que ocorrerá no fluxo de dados, que indica qual ação ocorrerá quando você executa o fluxo de dados dentro de um pipeline.

## <a name="sink-settings"></a>Configurações do coletor

Você deve ter um banco de dados de coletor do tipo de linha de Alter trabalhar. O coletor de configurações, você deve definir cada ação a ser permitido.

![Alterar o coletor de linha](media/data-flow/alter-row2.png "Alter coletor de linha")

É o comportamento padrão no fluxo de dados de ADF com Coletores de banco de dados inserir linhas. Se você quiser permitir que as atualizações, upserts e exclusões, bem, você também deve verificar essas caixas no coletor para permitir que as ações.

> [!NOTE]
> Se seu inserções, atualizações ou upserts modificar o esquema da tabela de destino no coletor, seu fluxo de dados falhará. Para modificar o esquema de destino no banco de dados, você deve escolher a opção "Recrie a tabela" no coletor. Isso será descarte e recrie a tabela com a nova definição de esquema.

## <a name="next-steps"></a>Próximas etapas

Após a transformação de linha de alteração, talvez você queira [seus dados do coletor no repositório de dados de destino](data-flow-sink.md).
