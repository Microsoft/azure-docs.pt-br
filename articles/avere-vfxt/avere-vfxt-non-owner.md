---
title: Solução alternativa para não proprietários do Avere vFXT – Azure
description: Solução alternativa que permite que usuários sem a permissão de proprietário da assinatura implantem o Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 0d9b1060ee35af6cbc2e1b95b0f7813072c52d2e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85505368"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Autorizar não proprietários a implantar o Avere vFXT

Estas instruções são uma solução alternativa que permite que um usuário sem privilégios de proprietário da assinatura crie um sistema do Avere vFXT para Azure.

(A maneira recomendada de implantar o sistema do Avere vFXT é fazer com que um usuário com privilégios de proprietário realize as etapas de criação, conforme explicado em [Preparar para criar o Avere vFXT](avere-vfxt-prereqs.md).)  

A solução alternativa envolve a criação de uma função de acesso adicional que fornece aos usuários permissões suficientes para instalar o cluster. A função deve ser criada por um proprietário da assinatura e um proprietário deve atribuí-la aos usuários apropriados.

O proprietário da assinatura também deve [aceitar os termos de uso](avere-vfxt-prereqs.md) da imagem do marketplace do Avere vFXT.

> [!IMPORTANT]
> Todas essas etapas devem ser executadas por um usuário com privilégios de proprietário na assinatura que será usada para o cluster.

1. Copie estas linhas e salve-as em um arquivo (por exemplo, `averecreatecluster.json`). Use sua ID da assinatura na instrução `AssignableScopes`.

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",

           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. Execute este comando para criar a função:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Exemplo:

    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Atribua essa função ao usuário que criará o cluster:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Depois de concluir esse processo, a função concede a qualquer usuário as seguintes permissões para a assinatura:

* Criar e configurar a infraestrutura de rede
* Criar o controlador de cluster
* Executar scripts de criação de cluster do controlador de cluster para criar o cluster
