---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204448"
---
O Azure permite que você coloque _bloqueios_ em recursos, para que eles não possam ser excluídos ou sejam somente leitura. __O bloqueio de um recurso pode levar a resultados inesperados.__ Algumas operações que não parecem modificar o recurso realmente exigem ações bloqueadas pelo bloqueio. 

Por exemplo, aplicar um bloqueio de exclusão ao grupo de recursos para seu espaço de trabalho impedirá operações de dimensionamento para clusters de computação do Azure ML.

Para obter mais informações sobre como bloquear recursos, consulte [Bloquear recursos para evitar alterações inesperadas](../articles/azure-resource-manager/management/lock-resources.md).