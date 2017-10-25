---
title: "Usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook. | Microsoft Docs"
description: "Consulte como usar ações de escala automática para chamar URLs da web ou enviar notificações por email no Azure Monitor. "
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: eb9a4c98-0894-488c-8ee8-5df0065d094f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: ancav
ms.openlocfilehash: 16caf14028494800e9259f0296c292b606d0210a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Use ações de dimensionamento automático para enviar notificações de alerta por email e webhook no Azure Monitor
Este artigo mostra como configurar gatilhos para que você possa chamar URLs da web específicas ou enviar emails com base em ações de escala automática no Azure.  

## <a name="webhooks"></a>Webhooks
Webhooks permitem rotear as notificações de alerta do Azure para outros sistemas para pós-processamento ou notificações personalizadas. Por exemplo, rotear o alerta para serviços que podem lidar com uma solicitação da Web de entrada para enviar SMS, registrar bugs, notificar a equipe por meio de serviços de chat/mensagens etc. O URI do webhook deve ser um ponto de extremidade HTTP ou HTTPS válido.

## <a name="email"></a>Email
O email pode ser enviado para qualquer endereço de email válido. Os administradores e administradores da assinatura em que a regra está em execução também serão notificados.

## <a name="cloud-services-and-web-apps"></a>Serviços de nuvem e aplicativos Web
Você pode aderir no portal do Azure para Serviços de Nuvem e Farms de Servidores (aplicativos Web).

* Escolha a métrica **escalar por** .

![escalar por](./media/insights-autoscale-to-webhook-email/insights-autoscale-notify.png)

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
| Campo | Obrigatório? | Descrição |
| --- | --- | --- |
| operation |sim |o valor deve ser "Scale" |
| sendToSubscriptionAdministrator |sim |o valor deve ser "true" ou "false" |
| sendToSubscriptionCoAdministrators |sim |o valor deve ser "true" ou "false" |
| customEmails |sim |o valor pode ser null [] ou uma matriz da cadeia de caracteres de emails |
| Webhooks |sim |o valor pode ser um Uri válido ou nulo |
| serviceUri |sim |um Uri de https válido |
| propriedades |sim |o valor deve ser vazio {} ou pode conter pares de chave-valor |

## <a name="authentication-in-webhooks"></a>Autenticação em webhooks
O webhook pode autenticar usando autenticação baseada em token, em que você salva o URI do webhook com uma ID de token como um parâmetro de consulta. Por exemplo, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue

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


| Campo | Obrigatório? | Descrição |
| --- | --- | --- |
| status |sim |O status que indica que uma ação de escala automática foi gerada |
| operation |sim |Para um aumento de instâncias, será "Escalar Horizontalmente" e para uma diminuição de instâncias, será "Reduzir Horizontalmente" |
| context |sim |O contexto de ação de escala automática |
| timestamp |sim |Carimbo de data/hora de quando a ação de escala automática foi disparada |
| ID |sim |ID do Gerenciador de Recursos da configuração de autoescala |
| name |sim |O nome da configuração de escala automática |
| detalhes |sim |Explicação da ação que o serviço de escala automática realizada a alteração na contagem da instância |
| subscriptionId |sim |ID da assinatura do recurso de destino que está sendo escalado |
| resourceGroupName |sim |Nome do Grupo de Recursos do recurso de destino que está sendo escalado |
| resourceName |sim |Nome do recurso de destino que está sendo escalado |
| resourceType |Sim |Os três valores com suporte: "microsoft.classiccompute/domainnames/slots/roles" - funções de Serviço de Nuvem, "microsoft.compute/virtualmachinescalesets" - Conjuntos de Escala de Máquina Virtual e "Microsoft.Web/serverfarms" - Aplicativo Web |
| resourceId |sim |ID do Gerenciador de Recursos do recurso de destino que está sendo dimensionado |
| portalLink |sim |Link do portal do Azure para a página de resumo do recurso de destino |
| oldCapacity |sim |A atual (antiga) contagem de instância quando Escala Automática adotou uma ação de escala |
| newCapacity |sim |A nova contagem de instância para a qual a Escala Automática escalou o recurso |
| propriedades |Não |Opcional. Conjunto de pares de <Chave, Valor> (por exemplo, Dicionário <Cadeia de caracteres, Cadeia de caracteres>). O campo de propriedades é opcional. Em uma interface do usuário personalizada ou fluxo de trabalho de aplicativo Lógico, você pode inserir as chaves e valores que podem ser passados usando a carga útil. Uma maneira alternativa de passar as propriedades personalizadas de volta para a chamada de saída do webhook é usar o URI do webhook em si (como parâmetros de consulta) |
