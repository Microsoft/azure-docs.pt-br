---
title: Perguntas frequentes sobre Azure Cosmos DB recurso de restauração pontual.
description: Este artigo lista as perguntas frequentes sobre o Azure Cosmos DB recurso de restauração pontual que é obtido usando o modo de backup contínuo.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c0af1db12f3ade2945524f48e4539d2d2e9aa6b9
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539172"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Perguntas frequentes sobre o Azure Cosmos DB recurso de restauração pontual (versão prévia)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> O recurso de restauração pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo lista as perguntas frequentes sobre a Azure Cosmos DB funcionalidade de restauração pontual (versão prévia) que é obtida usando o modo de backup contínuo.

## <a name="how-much-time-does-it-takes-to-restore"></a>Quanto tempo leva para restaurar?
A duração da restauração depende do tamanho dos dados.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>Posso enviar o tempo de restauração na hora local?
A restauração pode não ocorrer dependendo se os recursos principais, como bancos de dados ou contêineres existiam naquele momento. Você pode verificar inserindo a hora e examinando o banco de dados ou contêiner selecionado por um determinado tempo. Se você não vir nenhum recurso para restaurar, o processo de restauração não funcionará.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>Como rastrear se uma conta está sendo restaurada?
Depois que você enviar o comando Restore e aguardar a mesma página, depois que a operação for concluída, a barra de status mostrará a mensagem de conta restaurada com êxito. Você também pode pesquisar a conta restaurada e [acompanhar o status da conta que está sendo restaurada](continuous-backup-restore-portal.md#track-restore-status). Enquanto a restauração estiver em andamento, o status da conta será "criando", após a conclusão da operação de restauração, o status da conta será alterado para "online".

Da mesma forma para o PowerShell e a CLI, você pode acompanhar o progresso da operação de restauração executando o `az cosmosdb show` comando da seguinte maneira:

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

O provisioningState mostra "êxito" quando a conta está online.

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>Como posso descobrir se uma conta foi restaurada de outra conta?
Execute o `az cosmosdb show` comando, na saída, você pode ver que o valor da `createMode` propriedade. Se o valor for definido como **Restore**. indica que a conta foi restaurada de outra conta. A `restoreParameters` propriedade tem mais detalhes, como `restoreSource` , que tem a ID da conta de origem. O último GUID no `restoreSource` parâmetro é a InstanceId da conta de origem.

Por exemplo, na saída a seguir, a ID da instância da conta de origem é "7b4bb-f6a0-430e-ade1-638d781830cc"

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>O que acontece quando eu restauro um banco de dados de taxa de transferência compartilhada ou um contêiner em um banco de dados de produtividade compartilhado?
O banco de dados de produtividade compartilhada inteiro é restaurado. Você não pode escolher um subconjunto de contêineres em um banco de dados de produtividade compartilhado para restauração.

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>Qual é o uso de InstanceID na definição de conta?
Em qualquer momento determinado, a propriedade "accountName" da conta Azure Cosmos DB é globalmente exclusiva enquanto está ativa. No entanto, depois que a conta é excluída, é possível criar outra conta com o mesmo nome e, portanto, o "accountName" não é mais suficiente para identificar uma instância de uma conta. 

ID ou "instanceId" é uma propriedade de uma instância de uma conta e é usada para fazer a ambiguidade entre várias contas (dinâmica e excluída) se elas tiverem o mesmo nome para restauração. Você pode obter a ID da instância executando os `Get-AzCosmosDBRestorableDatabaseAccount`  `az cosmosdb restorable-database-account` comandos ou. O valor do atributo Name denota "InstanceID".

## <a name="next-steps"></a>Próximas etapas

* O que é o modo de [backup contínuo](continuous-backup-restore-introduction.md) ?
* Configure e gerencie o backup contínuo usando [portal do Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gerencie as permissões](continuous-backup-restore-permissions.md) necessárias para restaurar dados com o modo de backup contínuo.
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)