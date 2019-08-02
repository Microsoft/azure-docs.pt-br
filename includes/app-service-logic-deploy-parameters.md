---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385749"
---
Com o Azure Resource Manager, você define parâmetros para os valores que deseja especificar ao implantar o modelo. O modelo inclui uma seção `parameters`, que contém todos os valores de parâmetro. Cada valor de parâmetro é usado pelo modelo para definir os recursos que você deseja implantar.

> [!NOTE]
> Não defina parâmetros para valores que permanecem sempre os mesmos. Defina os parâmetros apenas para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre.

Quando definir parâmetros:

* Para especificar os valores permitidos que um usuário pode fornecer durante a implantação, use o campo **allowedValues**.

* Use o campo **defaultValue** para atribuir um valor a um parâmetro se nenhum valor for indicado durante a implantação. 
