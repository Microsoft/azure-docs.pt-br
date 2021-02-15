---
title: Esquema de alerta comum para alertas de Azure Monitor
description: Compreendendo o esquema de alerta comum, por que você deve usá-lo e como habilitá-lo
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: aa619976c8fa03b925d66e884ad03fc4e385693e
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565595"
---
# <a name="common-alert-schema"></a>Esquema comum de alertas

Este artigo descreve o que é o esquema de alerta comum, os benefícios de usá-lo e como habilitá-lo.

## <a name="what-is-the-common-alert-schema"></a>O que é o esquema de alerta comum?

O esquema de alerta comum padroniza a experiência de consumo para notificações de alerta no Azure hoje. Historicamente, os três tipos de alertas no Azure hoje (métrica, log e log de atividades) tiveram seus próprios modelos de email, esquemas de webhook, etc. Com o esquema de alerta comum, agora você pode receber notificações de alerta com um esquema consistente.

Qualquer instância de alerta descreve **o recurso que foi afetado** e **a causa do alerta** , e essas instâncias são descritas no esquema comum nas seções a seguir:
* **Essentials** : um conjunto de **campos padronizados** , comuns em todos os tipos de alertas, que descrevem em **qual recurso** o alerta está junto com os metadados de alerta comuns adicionais (por exemplo, severidade ou descrição). 
* **Contexto de alerta** : um conjunto de campos que descrevem a **causa do alerta** , com campos que variam de acordo **com o tipo de alerta**. Por exemplo, um alerta de métrica teria campos como o nome da métrica e o valor da métrica no contexto do alerta, enquanto um alerta do log de atividades teria informações sobre o evento que gerou o alerta. 

Os cenários de integração típicos que ouvimos dos clientes envolvem o roteamento da instância de alerta para a equipe preocupada com base em alguma tabela dinâmica (por exemplo, grupo de recursos), após a qual a equipe responsável começa a trabalhar nela. Com o esquema de alerta comum, você pode ter uma lógica de roteamento padronizada em tipos de alertas aproveitando os campos essenciais, deixando os campos de contexto como estão para as equipes interessadas investigarem ainda mais.

Isso significa que você pode potencialmente ter menos integrações, tornando o processo de gerenciamento e manutenção de uma tarefa _muito_ mais simples. Além disso, futuros aprimoramentos de carga de alerta (por exemplo, personalização, enriquecimento de diagnóstico, etc.) só serão exibidos no esquema comum.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Quais aprimoramentos o esquema de alerta comum traz?

O esquema de alerta comum se manifestará principalmente em suas notificações de alerta. Os aprimoramentos que você verá serão listados abaixo:

| Ação | Aprimoramentos|
|:---|:---|
| sms | Um modelo de SMS consistente para todos os tipos de alerta. |
| Email | Um modelo de email consistente e detalhado, permitindo que você diagnostique facilmente os problemas rapidamente. Inseridos em profundidade – links para a instância de alerta no portal e o recurso afetado garantem que você possa ir rapidamente para o processo de correção. |
| Aplicativo webhook/lógico/Azure function/Automation runbook | Uma estrutura JSON consistente para todos os tipos de alertas, que permite que você crie facilmente integrações em diferentes tipos de alertas. |

O novo esquema também permitirá uma experiência de consumo de alerta mais rica em ambos os portal do Azure e no aplicativo móvel do Azure no futuro imediato. 

[Saiba mais sobre as definições de esquema para os Runbooks/aplicativos lógicos/Azure Functions/automação.](./alerts-common-schema-definitions.md)

> [!NOTE]
> As seguintes ações não dão suporte ao esquema de alerta comum: Conector ITSM.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Como fazer habilitar o esquema de alerta comum?

Você pode aceitar ou recusar o esquema de alerta comum por meio de grupos de ação, tanto no portal quanto na API REST. A alternância para alternar para o novo esquema existe em um nível de ação. Por exemplo, você precisa aceitar separadamente uma ação de email e uma ação de webhook.

> [!NOTE]
> 1. Os seguintes tipos de alerta dão suporte ao esquema comum por padrão (não é necessário aceitar):
>     * Alertas de detecção inteligente
> 1. Atualmente, os seguintes tipos de alertas não dão suporte ao esquema comum:
>     * Alertas gerados pelo [Azure monitor para VMs](../insights/vminsights-overview.md)
>     * Alertas gerados pelo [Gerenciamento de custos do Azure](../../cost-management-billing/manage/cost-management-budget-scenario.md)

### <a name="through-the-azure-portal"></a>Por meio do portal do Azure

![Aceitar o esquema de alerta comum](media/alerts-common-schema/portal-opt-in.png)

1. Abra qualquer ação existente ou nova em um grupo de ações. 
1. Selecione ' Sim ' para ativar/desativar o esquema de alerta comum, conforme mostrado.

### <a name="through-the-action-groups-rest-api"></a>Por meio da API REST dos grupos de ação

Você também pode usar a [API de grupos de ação](/rest/api/monitor/actiongroups) para aceitar o esquema de alerta comum. Ao fazer a chamada [criar ou atualizar](/rest/api/monitor/actiongroups/createorupdate) a API REST, você pode definir o sinalizador "useCommonAlertSchema" como "true" (para aceitar) ou "false" (para recusar) para qualquer um dos seguintes ações: email/webhook/aplicativo lógico/Azure function/Automation runbook.

Por exemplo, o corpo da solicitação a seguir feita para [criar ou atualizar](/rest/api/monitor/actiongroups/createorupdate) a API REST fará o seguinte:

* Habilite o esquema de alerta comum para a ação de email "email de João da Silva"
* Desabilite o esquema de alerta comum para a ação de email "email de Jane Smith"
* Habilitar o esquema de alerta comum para a ação de webhook "exemplo de webhook"

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Próximas etapas

- [Definições comuns de esquema de alerta para WebHooks/aplicativos lógicos/Azure Functions/Runbooks de automação.](./alerts-common-schema-definitions.md)
- [Saiba como criar um aplicativo lógico que aproveita o esquema de alerta comum para lidar com todos os seus alertas.](./alerts-common-schema-integrations.md)
