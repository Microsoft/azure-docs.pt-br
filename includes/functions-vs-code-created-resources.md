---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/31/2021
ms.author: glenga
ms.openlocfilehash: 9a5c143927f549124cb6e69a4c8eb4829709a9e9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493424"
---
+ Um [grupo de recursos](../articles/azure-resource-manager/management/overview.md), que é um contêiner lógico para recursos relacionados.
+ Uma [conta de armazenamento do Azure](../articles/storage/common/storage-account-create.md) padrão, que mantém o estado e outras informações sobre seus projetos.
+ Um plano de consumo, que define o host subjacente para o aplicativo de funções sem servidor. 
+ Um aplicativo de funções, que fornece o ambiente para a execução do código de função. Um aplicativo de funções lhe permite agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos dentro do mesmo plano de hospedagem.
+ Uma instância do Application Insights conectada ao aplicativo de funções, que controla o uso de sua função sem servidor.