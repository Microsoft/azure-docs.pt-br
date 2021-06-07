---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: ffd053b98a54d3e23eec62427f5c3d82df58954d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800327"
---
<!-- List and set subscriptions -->

1. Obtenha a lista de assinaturas usando o comando [az account list](/cli/azure/account#az_account_list):

    ```
    az account list --output table
    ```

2. Use `az account set` com a ID da assinatura ou nome para o qual você deseja alterar.

    ```
    az account set --subscription "My Demos"
    ```
