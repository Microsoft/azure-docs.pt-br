---
title: incluir arquivo
description: incluir arquivo
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 4ef7bc7ed1ab255bffdfec10c07bc040019d9f1e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121565"
---
| Nomes de domínios                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Usada pelo runtime de integração auto-hospedada para se conectar à conta de armazenamento do Azure ao usar o recurso cópia em etapas. |
| `*.database.windows.net`      | 1433           | Obrigatório somente quando você copia do Banco de Dados SQL do Azure ou do Azure Synapse Analytics ou para ambos; caso contrário, opcional. Use o recurso de cópia em etapas para copiar dados para o Banco de Dados SQL ou para o Azure Synapse Analytics sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Obrigatório somente quando você copia do Azure Data Lake Storage ou para ele; caso contrário, opcional. |
