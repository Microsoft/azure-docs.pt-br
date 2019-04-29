---
title: Falha ao criar ou excluir um banco de dados ou uma tabela no Azure Data Explorer
description: Este artigo descreve etapas de solução de problemas para criar e excluir bancos de dados e tabelas no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0d221138914230d5455dc0addbe08cdaaed36a0b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826247"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Solucionar problemas: Falha ao criar ou excluir um banco de dados ou uma tabela no Azure Data Explorer

No Azure Data Explorer, você trabalha regularmente com bancos de dados e tabelas. Este artigo fornece as etapas de solução de problemas para os problemas que possam surgir.

## <a name="creating-a-database"></a>Criar um banco de dados

1. Certifique-se de ter as permissões adequadas. Para criar um cluster, você deve ser um membro da função *Colaborador* ou *Proprietário* da assinatura do Azure. Se necessário, trabalhe junto ao administrador da assinatura para que você possa ser adicionado à função apropriada.

1. Certifique-se de que não há nenhum erro de validação de nome para o nome do banco de dados. O nome deve ser alfanumérico, com um comprimento máximo de 260 caracteres.

1. Verifique se a retenção de banco de dados e os valores de armazenamento em cache estão dentro dos intervalos permitidos. A retenção deve ser entre 1 e 36500 dias (100 anos). O armazenamento em cache deve estar entre 1 o valor máximo definido para retenção.

## <a name="deleting-or-renaming-a-database"></a>Excluir ou renomear um banco de dados

Certifique-se de ter as permissões adequadas. Para criar ou renomear um banco de dados, você deve ser um membro da função *Colaborador* ou *Proprietário* da assinatura do Azure. Se necessário, trabalhe junto ao administrador da assinatura para que você possa ser adicionado à função apropriada.

## <a name="creating-a-table"></a>Criar uma tabela

1. Certifique-se de ter as permissões adequadas. Para criar uma tabela, você deve ser um membro da função *administrador de banco de dados* ou *usuário de banco de dados* no banco de dados ou da função *Colaborador* ou *Proprietário* da assinatura do Azure. Se necessário, trabalhe junto ao administrador da assinatura ou do cluster para que você possa ser adicionado à função apropriada.

    Para obter mais informações sobre permissões, consulte [Gerenciar permissões de banco de dados](manage-database-permissions.md).

1. Certifique-se de que já não exista uma tabela com o mesmo nome. Se ele existir, então, você pode: Criar uma tabela com um nome diferente. Renomeie a tabela existente (requer *tabela admin* função); ou remova a tabela existente (requer *administrador de banco de dados* função). Use os seguintes comandos.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Excluir ou renomear uma tabela

Certifique-se de ter as permissões adequadas. Para excluir ou renomear uma tabela, você deve ser um membro da função *administrador de banco de dados* ou *administrador de tabelas* no banco de dados. Se necessário, trabalhe junto ao administrador da assinatura ou do cluster para que você possa ser adicionado à função apropriada.

Para obter mais informações sobre permissões, consulte [Gerenciar permissões de banco de dados](manage-database-permissions.md).

## <a name="general-guidance"></a>Orientação geral

1. Verifique o [painel de integridade do serviço do Azure](https://azure.microsoft.com/status/). Procure o status do Azure Data Explorer na região em que você está tentando trabalhar com um banco de dados ou uma tabela.

    Se o status não for **Bom** (marca de seleção verde), tente novamente depois que o status melhorar.

1. Se você ainda precisar de ajuda para resolver seu problema, abra uma solicitação de suporte no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Próximas etapas

[Verificar integridade do cluster](check-cluster-health.md)