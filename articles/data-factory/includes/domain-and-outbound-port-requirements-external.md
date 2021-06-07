---
title: incluir arquivo
description: incluir arquivo
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: d24e8957dc63024a46495e8a66bad7dbb3790f38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389477"
---
| Nomes de domínios                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Usada pelo runtime de integração auto-hospedada para se conectar à conta de armazenamento do Azure ao usar o recurso cópia em etapas. |
| `*.database.windows.net`      | 1433           | Obrigatório somente quando você copia do Banco de Dados SQL do Azure ou do Azure Synapse Analytics ou para ambos; caso contrário, opcional. Use o recurso de cópia em etapas para copiar dados para o Banco de Dados SQL ou para o Azure Synapse Analytics sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Obrigatório somente quando você copia do Azure Data Lake Storage ou para ele; caso contrário, opcional. |
