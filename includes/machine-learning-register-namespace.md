---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: e92d3ac9ed4330cc1680428a426e881538cb0e78
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102623276"
---
* Ao criar um novo espaço de trabalho, você pode criar automaticamente os serviços necessários para o espaço de trabalho ou usar os serviços existentes. Se você quiser usar os __serviços existentes de uma assinatura do Azure diferente__ do espaço de trabalho, deverá registrar o namespace Azure Machine Learning na assinatura que contém esses serviços. Por exemplo, ao criar um espaço de trabalho na assinatura A que usa uma conta de armazenamento da assinatura B, o namespace Azure Machine Learning deve ser registrado na assinatura B antes que você possa usar a conta de armazenamento com o espaço de trabalho.

    O provedor de recursos para Azure Machine Learning é __Microsoft. MachineLearningServices__. Para obter informações sobre como ver se ele está registrado e como registrá-lo, consulte o artigo [provedores de recursos e tipos do Azure](../articles/azure-resource-manager/management/resource-providers-and-types.md)  .

    > [!IMPORTANT]
    > Isso se aplica somente aos recursos fornecidos durante a criação do espaço de trabalho; Contas de armazenamento do Azure, registro de contêiner do Azure, Azure Key Vault e Application Insights.
