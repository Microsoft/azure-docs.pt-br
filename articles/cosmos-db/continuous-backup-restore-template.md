---
title: Use o modelo ARM para configurar o backup contínuo e a restauração pontual no Azure Cosmos DB.
description: Saiba como provisionar uma conta com backup contínuo e restaurar dados usando modelos de Azure Resource Manager.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 4abfdd0209bd9f13fb7bd902b27a53f65156da2e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381810"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-resource-manager-templates"></a>Configurar e gerenciar o backup contínuo e a restauração pontual (versão prévia)-usando modelos de Azure Resource Manager
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> O recurso de restauração pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O recurso de restauração pontual do Azure Cosmos DB (versão prévia) ajuda você a se recuperar de uma alteração acidental dentro de um contêiner para restaurar uma conta excluída, um banco de dados ou um contêiner ou para restaurar em qualquer região (onde os backups existiam). O modo de backup contínuo permite que você faça a restauração para qualquer ponto de tempo nos últimos 30 dias.

Este artigo descreve como provisionar uma conta com backup contínuo e restaurar dados usando modelos de Azure Resource Manager.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Provisionar uma conta com backup contínuo

Você pode usar modelos de Azure Resource Manager para implantar uma conta de Azure Cosmos DB com o modo contínuo. Ao definir o modelo para provisionar uma conta, inclua o `backupPolicy` parâmetro, conforme mostrado no exemplo a seguir:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

Em seguida, implante o modelo usando Azure PowerShell ou CLI. O exemplo a seguir mostra como implantar o modelo com um comando da CLI:

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>Restaurar usando o modelo do Resource Manager

Você também pode restaurar uma conta usando o modelo do Resource Manager. Ao definir o modelo, inclua os seguintes parâmetros:

* Definir o `createMode` parâmetro a ser *restaurado*
* Defina o `restoreParameters` , observe que o `restoreSource` valor é extraído da saída do `az cosmosdb restorable-database-account list` comando para sua conta de origem. O atributo de ID de instância para o nome de sua conta é usado para fazer a restauração.
* Defina o `restoreMode` parâmetro como *PointInTime* e configure o `restoreTimestampInUtc` valor.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

Em seguida, implante o modelo usando Azure PowerShell ou CLI. O exemplo a seguir mostra como implantar o modelo com um comando da CLI:  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>Próximas etapas

* Configure e gerencie o backup contínuo usando [CLI do Azure](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-command-line.md)ou [portal do Azure](continuous-backup-restore-portal.md).
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)
* [Gerencie as permissões](continuous-backup-restore-permissions.md) necessárias para restaurar dados com o modo de backup contínuo.