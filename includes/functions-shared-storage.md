---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76963641"
---
Use uma conta de armazenamento separada para cada aplicativo de funções para maximizar o desempenho. Isso será especialmente importante ao disparar as funções do Durable Functions ou do Hub de Eventos, que geram um alto volume de transações de armazenamento. Quando a lógica de aplicativo interagir com o Armazenamento do Azure, de modo direto (usando o SDK de Armazenamento) ou por meio de uma das associações de armazenamento, você deverá usar uma conta de armazenamento dedicada. Por exemplo, caso uma função disparada pelo Hub de Eventos esteja gravando dados no armazenamento de blobs, use duas contas de armazenamento&mdash;uma para o aplicativo de funções e outra para os blobs que estão sendo armazenados pela função.