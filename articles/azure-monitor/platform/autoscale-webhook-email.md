---
title: Usar dimensionamento automático para enviar notificações de alerta por email e webhook
description: 'Consulte como usar ações de escala automática para chamar URLs da web ou enviar notificações por email no Azure Monitor. '
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: c1386f4058f9490bad0161b680005db6031bace1
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491522"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Use ações de dimensionamento automático para enviar notificações de alerta por email e webhook no Azure Monitor
Este artigo mostra como configurar gatilhos para que você possa chamar URLs da web específicas ou enviar emails com base em ações de escala automática no Azure.  

## <a name="webhooks"></a>webhooks
Webhooks permitem rotear as notificações de alerta do Azure para outros sistemas para pós-processamento ou notificações personalizadas. Por exemplo, rotear o alerta para serviços que podem lidar com uma solicitação da Web de entrada para enviar SMS, registrar bugs, notificar a equipe por meio de serviços de chat/mensagens etc. O URI do webhook deve ser um ponto de extremidade HTTP ou HTTPS válido.

## <a name="email"></a>Email
O email pode ser enviado para qualquer endereço de email válido. Os administradores e administradores da assinatura em que a regra está em execução também serão notificados.

## <a name="cloud-services-and-web-apps"></a>Serviços de nuvem e aplicativos Web
Você pode aderir no portal do Azure para Serviços de Nuvem e Farms de Servidores (aplicativos Web).

* Escolha a métrica **escalar por** .

![escalar por](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de escala de Máquina Virtual
Para ver as Máquinas Virtuais mais novas criadas com o Gerenciador de Recursos (conjuntos de escala da Máquina Virtual), você pode configurar isso usando a API REST, modelos do Gerenciador de Recursos, PowerShell e CLI. Uma interface de portal ainda não está disponível.
Ao usar o modelo da API REST ou do Gerenciador de Recursos, inclua o elemento de notificações com as seguintes opções.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```

| Campo | Obrigatório? | DESCRIÇÃO |
| --- | --- | --- |
| operation |Sim |o valor deve ser "Scale" |
| sendToSubscriptionAdministrator |Sim |o valor deve ser "true" ou "false" |
| sendToSubscriptionCoAdministrators |Sim |o valor deve ser "true" ou "false" |
| customEmails |Sim |o valor pode ser null [] ou uma matriz da cadeia de caracteres de emails |
| webhooks |Sim |o valor pode ser um Uri válido ou nulo |
| serviceUri |Sim |um Uri de https válido |
| propriedades |Sim |o valor deve ser vazio {} ou pode conter pares chave-valor |

## <a name="authentication-in-webhooks"></a>Autenticação em webhooks
O webhook pode autenticar usando autenticação baseada em token, em que você salva o URI do webhook com uma ID de token como um parâmetro de consulta. Por exemplo, https: \/ /mysamplealert/webcallback? tokenid = sometokenid & someparameter = algumvalor

## <a name="autoscale-notification-webhook-payload-schema"></a>Escala automática do esquema de carga útil do webhook de notificação
Quando a notificação de escala automática é gerada, os metadados a seguir são incluídos na carga útil do webhook:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Campo | Obrigatório? | DESCRIÇÃO |
| --- | --- | --- |
| status |Sim |O status que indica que uma ação de escala automática foi gerada |
| operation |Sim |Para um aumento de instâncias, será "Escalar Horizontalmente" e para uma diminuição de instâncias, será "Reduzir Horizontalmente" |
| context |Sim |O contexto de ação de escala automática |
| timestamp |Sim |Carimbo de data/hora de quando a ação de escala automática foi disparada |
| id |Sim |ID do Gerenciador de Recursos da configuração de autoescala |
| name |Sim |O nome da configuração de escala automática |
| details |Sim |Explicação da ação que o serviço de escala automática realizada a alteração na contagem da instância |
| subscriptionId |Sim |ID da assinatura do recurso de destino que está sendo escalado |
| resourceGroupName |Sim |Nome do Grupo de Recursos do recurso de destino que está sendo escalado |
| resourceName |Sim |Nome do recurso de destino que está sendo escalado |
| resourceType |Sim |Os três valores com suporte: "microsoft.classiccompute/domainnames/slots/roles" - funções de Serviço de Nuvem, "microsoft.compute/virtualmachinescalesets" - Conjuntos de Escala de Máquina Virtual e "Microsoft.Web/serverfarms" - Aplicativo Web |
| ResourceId |Sim |ID do Gerenciador de Recursos do recurso de destino que está sendo dimensionado |
| portalLink |Sim |Link do portal do Azure para a página de resumo do recurso de destino |
| oldCapacity |Sim |A atual (antiga) contagem de instância quando Escala Automática adotou uma ação de escala |
| newCapacity |Sim |A nova contagem de instância para a qual a Escala Automática escalou o recurso |
| propriedades |Não |Opcional. Conjunto de pares de <Chave, Valor> (por exemplo, Dicionário <Cadeia de caracteres, Cadeia de caracteres>). O campo de propriedades é opcional. Em uma interface do usuário personalizada ou fluxo de trabalho de aplicativo Lógico, você pode inserir as chaves e valores que podem ser passados usando a carga útil. Uma maneira alternativa de passar as propriedades personalizadas de volta para a chamada de saída do webhook é usar o URI do webhook em si (como parâmetros de consulta) |

