---
title: Amostra de script – Desabilitar a exclusão temporária de um compartilhamento de arquivo
description: Saiba como usar um script para desabilitar a exclusão temporária de compartilhamentos de arquivo em uma conta de armazenamento.
ms.topic: sample
ms.date: 02/02/2020
ms.openlocfilehash: 172a7bd19ffd07ca7900a457c447362343e5f185
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84121278"
---
# <a name="disable-soft-delete-for-file-shares-in-a-storage-account"></a>Desabilitar a exclusão temporária de compartilhamentos de arquivo em uma conta de armazenamento

Este documento explica o processo usado para desabilitar a exclusão temporária de compartilhamentos de arquivo em uma conta de armazenamento.

Siga estas etapas:

1. Instale o armclient. Para saber como instalá-lo, acesse [este link](https://github.com/projectkudu/ARMClient).

2. Salve os dois arquivos de corpo da solicitação a seguir em uma pasta no computador.

    ```json
    rqbody-enableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":true,
        "days": 14
        }
    },
    "cors": {
        "corsRules": []
    }
    }

    rqbody-disableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":false,
        "days": 0
        }
    },
    "cors": {
        "corsRules": []
    }
    }
    ```

3. Mantenha a ID do ARM (Azure Resource Manager) da sua conta de armazenamento em um lugar de fácil acesso. Por exemplo: `/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare/providers/Microsoft.Storage/storageAccounts/inquirytest`

4. Entre usando suas credenciais executando o **logon do armclient**.

5. Obtenha as propriedades atuais da exclusão temporária de compartilhamentos de arquivo na conta de armazenamento.

    A seguinte operação GET efetua fetch das propriedades de exclusão temporária de compartilhamentos de arquivo na conta do *inquirytest*:

    ```cmd
    armclient get /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01
    ```

    ```output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "cors": {
        "corsRules": []
        },
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```

6. Desabilite a exclusão temporária de compartilhamentos de arquivo na conta de armazenamento.

    A seguinte operação PUT desabilita as propriedades de exclusão temporária de compartilhamentos de arquivo na conta do *inquirytest*:

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-disableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": false,
        "days": 0
        }
    }
    }
    ```

7. Caso deseje habilitar novamente a exclusão temporária, use a amostra a seguir.

    A operação PUT a seguir habilita as propriedades de exclusão temporária de compartilhamentos de arquivo na conta “inquirytest”.

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-EnableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```
