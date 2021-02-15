---
title: Logs de diagnóstico dos hubs de notificação do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral de todos os logs operacionais e de diagnóstico que estão disponíveis para os hubs de notificação do Azure.
author: brannon
ms.author: brjones
ms.service: notification-hubs
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: b532dca6ceba44a32132bf64b322e1b4764fd5fa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416979"
---
# <a name="enable-diagnostics-logs-for-notification-hubs"></a>Habilitar logs de diagnóstico para hubs de notificação

Ao começar a usar o namespace de hubs de notificação do Azure, convém monitorar como e quando o namespace é criado, excluído ou acessado. Este artigo fornece uma visão geral de todos os logs operacionais e de diagnóstico disponíveis.

Atualmente, os hubs de notificação do Azure dão suporte a atividades e logs operacionais, que capturam *operações de gerenciamento* executadas no namespace de hubs de notificação do Azure.

## <a name="diagnostic-logs-schema"></a>Esquema de logs de diagnóstico

Todos os logs são armazenados no formato JavaScript Object Notation (JSON) nos dois locais a seguir:

- **AzureActivity**: exibe logs de operações e ações que são realizadas em relação ao seu namespace no portal do Azure ou por meio de implantações de modelo Azure Resource Manager.
- **AzureDiagnostics**: exibe logs de operações e ações que são realizadas em seu namespace usando a API ou por meio de clientes de gerenciamento no SDK do idioma.

As cadeias de caracteres JSON do log de diagnóstico incluem os elementos listados na tabela a seguir:

| Nome | Descrição |
| ------- | ------- |
| time | Carimbo de data/hora UTC do log |
| resourceId | Caminho relativo para o recurso do Azure |
| operationName | Nome da operação de gerenciamento |
| category | Categoria do log. Valores válidos: `OperationalLogs` |
| callerIdentity | Identidade do chamador que iniciou a operação de gerenciamento |
| resultType | Status da operação de gerenciamento. Valores válidos: `Succeeded` ou `Failed` |
| resultDescription | Descrição da operação de gerenciamento |
| correlationId | ID de correlação da operação de gerenciamento (se especificado) |
| callerIpAddress | O endereço IP do chamador. Vazio para chamadas originadas do portal do Azure |

Este é um exemplo de uma cadeia de caracteres JSON do log operacional:

```json
{
    "operationName": "Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action",
    "resourceId": "/SUBSCRIPTIONS/2CAC2A14-BA6B-46A6-BCE8-2D9781A41BA2/RESOURCEGROUPS/SAMPLES/PROVIDERS/MICROSOFT.NOTIFICATIONHUBS/NAMESPACES/SAMPLE-NS",
    "time": "1/1/2021 5:16:32 AM +00:00",
    "category": "OperationalLogs",
    "resultType": "Succeeded",
    "resultDescription": "Gets Hub Authorization Rules",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "callerIdentity": "{ \"identityType\": \"Portal\", \"identity\": \"\" }"
}
```

O `callerIdentity` campo pode estar vazio ou uma cadeia de caracteres JSON com um dos formatos a seguir.

Para chamadas provenientes da portal do Azure o `identity` campo está vazio. O log pode ser correlacionado aos logs de atividade para determinar o usuário conectado.
```json
{
    "identityType": "Portal",
    "identity": ""
}
```

Para chamadas feitas por meio de Azure Resource Manager o `identity` campo conterá o nome de usuário do conectado.
```json
{
   "identityType": "Username",
   "identity": "test@foo.com"
}
```

Para chamadas para a API REST dos hubs de notificação, o campo conterá `identity` o nome da política de acesso usada para gerar o token SharedAccessSignature.
```json
{
   "identityType": "KeyName",
   "identity": "SharedAccessRootKey2"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Eventos e operações capturados em logs operacionais

Os logs operacionais capturam todas as operações de gerenciamento executadas no namespace dos hubs de notificação do Azure. As operações de dados não são capturadas devido ao alto volume de operações de dados que são realizadas em hubs de notificação do Azure.

As seguintes operações de gerenciamento são capturadas em logs operacionais: 

| Escopo | Nome de operação | Descrição da operação |
| :-- | :-- | :-- |
| Namespace | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Listar regras de autorização |
| Namespace | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Excluir regra de autorização |
| Namespace | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Listar chaves |
| Namespace | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Obter regra de autorização |
| Namespace | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Regenerar chaves |
| Namespace | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Criar ou atualizar regra de autorização |
| Namespace | Microsoft. NotificationHubs/namespaces/excluir | Excluir namespace |
| Namespace | Microsoft.NotificationHubs/Namespaces/read | Obter namespace |
| Namespace | Microsoft.NotificationHubs/Namespaces/write | Criar ou atualizar namespace |
| Hub de notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Listar regras de autorização |
| Hub de notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Excluir regra de autorização |
| Hub de notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Listar chaves |
| Hub de notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Ler regra de autorização |
| Hub de notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Regenerar chaves |
| Hub de notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Criar ou atualizar regra de autorização |
| Hub de notificação | Microsoft. NotificationHubs/namespaces/NotificationHubs/Delete | Excluir Hub de notificação |
| Hub de notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Criar, atualizar ou obter credenciais do PNS |
| Hub de notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Obter Hub de notificação |
| Hub de notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Criar ou atualizar o Hub de notificação |

## <a name="enable-operational-logs"></a>Habilitar logs operacionais

Os logs operacionais são desabilitados por padrão. Para habilitar os logs, faça o seguinte:

1. Na [portal do Azure](https://portal.azure.com), acesse o namespace de hubs de notificação do Azure e, em **monitoramento**, selecione  **configurações de diagnóstico**.

   ![O link "configurações de diagnóstico"](./media/notification-hubs-diagnostic-logs/image-1.png)

1. No painel **configurações de diagnóstico** , selecione **Adicionar configuração de diagnóstico**.  

   ![O link "Adicionar configuração de diagnóstico"](./media/notification-hubs-diagnostic-logs/image-2.png)

1. Defina as configurações de diagnóstico fazendo o seguinte:

   a. Na caixa **Nome**, insira um nome para as configurações de diagnóstico.  

   b. Selecione um dos seguintes três destinos para seus logs de diagnóstico:  
   - Se você selecionar **Enviar para log Analytics espaço de trabalho**, será necessário especificar a qual instância do log Analytics o diagnóstico será enviado.  
   - Se você selecionar **arquivar em uma conta de armazenamento**, precisará configurar a conta de armazenamento onde os logs de diagnóstico serão armazenados.  
   - Se você selecionar **fluxo para um hub de eventos**, precisará configurar o Hub de eventos para o qual deseja transmitir os logs de diagnóstico.

   c. Marque a caixa de seleção **OperationalLogs** .

    ![O painel "configurações de diagnóstico"](./media/notification-hubs-diagnostic-logs/image-3.png)

1. Selecione **Salvar**.

As novas configurações terão efeito em aproximadamente dez minutos. Os logs aparecerão no destino de arquivamento configurado, no painel **Logs de diagnóstico**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como definir as configurações de diagnóstico, consulte:
* [Visão geral dos logs de diagnóstico do Azure](../azure-monitor/platform/platform-logs-overview.md).

Para saber mais sobre os hubs de notificação do Azure, consulte:
* [O que são Hubs de Notificação do Azure?](notification-hubs-push-notification-overview.md)

