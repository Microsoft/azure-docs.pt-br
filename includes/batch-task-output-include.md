---
title: incluir arquivo
description: incluir arquivo
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 019e8db54c1cfd9f436f880b8ddbb9bfa31c50bc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026459"
---
Uma tarefa em execução em lote do Azure pode produzir dados de saída quando ele é executado. Com frequência, os dados de saída precisam ser armazenados para a recuperação por outras tarefas no trabalho, o aplicativo cliente que executou o trabalho, ou ambos. As tarefas gravam dados de saída no sistema de arquivos de um nó de computação do Lote, mas todos os dados no nó serão perdidos quando ele for reimaginado ou quando o nó deixar o pool. As tarefas também podem ter um período de retenção de arquivo, após o qual os arquivos criados pela tarefa são excluídos. Por esses motivos, é importante manter a saída da tarefa que você vai precisar posteriormente para um repositório de dados, como o [Armazenamento do Azure](../articles/storage/index.yml).

Para opções de conta de armazenamento do Lote, confira [Contas do Lote e contas do Armazenamento do Azure](../articles/batch/accounts.md#azure-storage-accounts).