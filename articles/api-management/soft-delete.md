---
title: Exclusão reversível do gerenciamento de API do Azure (visualização) | Microsoft Docs
description: A exclusão reversível permite que você recupere instâncias de gerenciamento de API excluídas.
ms.service: api-management
ms.topic: conceptual
author: vladvino
ms.author: apimpm
ms.date: 11/27/2020
ms.openlocfilehash: e2842f3e428abb4f0eb628dbb8e446f2714d5d89
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652378"
---
# <a name="api-management-soft-delete-preview"></a>Exclusão reversível do gerenciamento de API (versão prévia)

Com a exclusão reversível do gerenciamento de API (versão prévia), você pode recuperar e restaurar instâncias de APIM (gerenciamento de API) excluídas recentemente.

> [!IMPORTANT]
> Somente instâncias de gerenciamento de API excluídas usando `2020-06-01-preview` o e versões de API posteriores serão excluídas por software e recuperáveis usando as etapas descritas neste artigo. Instâncias APIM excluídas usando versões anteriores da API continuarão a ser excluídas. Azure PowerShell e CLI do Azure atualmente não usam a `2020-06-01-preview` versão e também resultarão em um comportamento de exclusão de hardware.

## <a name="supporting-interfaces"></a>Interfaces de suporte

O recurso de exclusão reversível está disponível por meio da [API REST](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore).

> [!TIP]
> Consulte a [referência da API REST do Azure](/rest/api/azure/) para obter dicas e ferramentas para chamar as APIs REST do Azure.

| Operação | Descrição | Namespace de gerenciamento de API | Versão mínima da API |
|--|--|--|--|
| [Criar ou atualizar](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | Cria ou atualiza um serviço de gerenciamento de API.  | Serviço de gerenciamento de API | Qualquer |
| [Criar ou atualizar](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) com `restore` a propriedade definida como **true** | O não excluirá o serviço de gerenciamento de API se ele tiver sido excluído anteriormente por software. Se `restore` for especificado e definido como `true` todas as outras propriedades será ignorado.  | Serviço de gerenciamento de API |  2020-06-01-visualização |
| [Delete (excluir)](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | Exclui um serviço de gerenciamento de API existente. | Serviço de gerenciamento de API | 2020-06-01-visualização|
| [Obter por nome](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) | Obtenha o serviço de gerenciamento de API com exclusão reversível por nome. | Serviços excluídos | 2020-06-01-visualização |
| [Listar por assinatura](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) | Lista todos os serviços excluídos de forma reversível disponíveis para a exclusão da assinatura especificada. | Serviços excluídos | 2020-06-01-visualização
| [Limpar](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | Limpa o serviço de gerenciamento de API (exclui-o sem a opção de restaurar). | Serviços excluídos | 2020-06-01-visualização

## <a name="soft-delete-behavior"></a>Comportamento de exclusão reversível

Você pode usar qualquer versão de API para criar sua instância de gerenciamento de API, no entanto, você precisará usar o `2020-06-01-preview` ou versões posteriores para excluir a instância do APIM (e ter a opção de recuperá-la).

Após a exclusão de uma instância de gerenciamento de API, o serviço existirá em um estado excluído, tornando-o inacessível para qualquer operação APIM. Enquanto estiver nesse estado, a instância de APIM só poderá ser listada, recuperada ou descartada (permanentemente excluída).

Ao mesmo tempo, o Azure agendará a exclusão dos dados subjacentes correspondentes à instância APIM para execução após o intervalo de retenção predeterminado (48 hora). O registro DNS correspondente à instância também é retido durante o intervalo de retenção. Não é possível reutilizar o nome de uma instância de gerenciamento de API que foi excluída de forma reversível até que o período de retenção tenha passado.

Se sua instância do APIM não for recuperada dentro de 48 horas, ela será excluída de forma impressa (irrecuperável). Você também pode optar por [limpar](#purge-a-soft-deleted-apim-instance) (excluir permanentemente) sua instância de APIM, adotar o período de retenção de exclusão reversível.

## <a name="list-deleted-apim-instances"></a>Listar instâncias de APIM excluídas

Você pode verificar se uma instância de APIM excluída de forma reversível está disponível para restauração (sem exclusão) usando os serviços excluídos [obter por nome](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) ou [lista por operações de assinatura](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) .

### <a name="get-a-soft-deleted-instance-by-name"></a>Obter uma instância excluída de maneira reversível por nome

Use a operação de obtenção de gerenciamento de API [por nome](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) , substituindo `{subscriptionId}` , `{location}` e `{serviceName}` por sua assinatura do Azure, local do recurso e nome da instância de gerenciamento de API:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Se disponível para a exclusão, o Azure retornará um registro da instância APIM mostrando seu `deletionDate` e `scheduledPurgeDate` , por exemplo:

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>Listar todas as instâncias de exclusão reversível de uma determinada assinatura

Use a lista de gerenciamento de API por operação de [assinatura](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) , substituindo `{subscriptionId}` pela sua ID de assinatura:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

Isso retornará uma lista de todos os serviços de exclusão reversível disponíveis para restauração na assinatura fornecida, mostrando o `deletionDate` e o `scheduledPurgeDate` para cada um.

## <a name="recover-a-deleted-apim-instance"></a>Recuperar uma instância de APIM excluída

Use a operação de [criação ou atualização](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) do gerenciamento de API, substituindo `{subscriptionId}` , `{resourceGroup}` e `{apimServiceName}` pela sua assinatura do Azure, nome do grupo de recursos e nome de gerenciamento de API:

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . e defina a `restore` propriedade como `true` no corpo da solicitação. (Quando esse sinalizador for especificado e definido como *true*, todas as outras propriedades serão ignoradas.) Por exemplo:

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>Limpar uma instância de APIM excluída de maneira reversível

Use a operação de [limpeza](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) de gerenciamento de API, substituindo `{subscriptionId}` , `{location}` e `{serviceName}` por sua assinatura do Azure, local do recurso e nome de gerenciamento de API:

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Isso excluirá permanentemente sua instância de gerenciamento de API do Azure.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as opções de backup e recuperação de gerenciamento de API de longo prazo:

- [Como implementar a recuperação de desastre usando o backup de serviço e restaurar no Gerenciamento de API no Azure](api-management-howto-disaster-recovery-backup-restore.md)