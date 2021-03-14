---
title: Validação de alerta na central de segurança do Azure | Microsoft Docs
description: Saiba como validar se os alertas de segurança estão configurados corretamente na central de segurança do Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 74323c63ab8985dee2391a546d82258dcb8d0114
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099515"
---
# <a name="alert-validation-in-azure-security-center"></a>Validação de alerta na central de segurança do Azure
Este documento ensina você a verificar se o sistema está configurado corretamente para os alertas da Central de Segurança do Azure.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
Alertas são as notificações que a Central de Segurança gera quando detecta ameaças em seus recursos. Ele prioriza e lista os alertas junto com as informações necessárias para investigar rapidamente o problema. A Central de Segurança também fornece recomendações sobre como corrigir um ataque.
Para obter mais informações, consulte [alertas de segurança na central de segurança](security-center-alerts-overview.md) e [Gerenciando e respondendo a alertas de segurança](security-center-managing-and-responding-alerts.md)


## <a name="generate-sample-azure-defender-alerts"></a>Gerar alertas de exemplo do Azure Defender

Se você estiver usando a nova experiência de alertas de visualização, conforme descrito em [gerenciar e responder a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md), poderá criar alertas de exemplo com alguns cliques na página alertas de segurança no portal do Azure.

Use alertas de exemplo para:

- avaliar o valor e os recursos do Azure defender
- Valide todas as configurações feitas para seus alertas de segurança (como integrações do SIEM, automação de fluxo de trabalho e notificações por email)

Para criar alertas de exemplo:

1. Na barra de ferramentas da página alertas, selecione **criar alertas de exemplo**. 
1. Selecione a assinatura.
1. Selecione o plano/s do Azure defender relevante para o qual você deseja ver os alertas. 
1. Selecione **criar alertas de exemplo**.

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="Etapas para criar alertas de exemplo na central de segurança do Azure":::
    
    É exibida uma notificação informando que os alertas de exemplo estão sendo criados:

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="Notificação de que os alertas de exemplo estão sendo gerados.":::

    Depois de alguns minutos, os alertas são exibidos na página alertas de segurança. Eles também aparecerão em qualquer outro lugar que você tenha configurado para receber seus alertas de segurança da central de segurança do Azure (SIEMs conectados, notificações por email e assim por diante).

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="Alertas de exemplo na lista de alertas de segurança":::

    > [!TIP]
    > Os alertas são para recursos simulados.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Simular alertas em suas VMs do Azure (Windows) <a name="validate-windows"></a>

Depois que o agente da central de segurança estiver instalado no computador, siga estas etapas do computador no qual você deseja ser o recurso atacado do alerta:

1. Copie um executável (por exemplo **calc.exe**) na área de trabalho do computador ou em outro diretório de sua conveniência e renomeie-o como **ASC_AlertTest_662jfi039N.exe**.
1. Abra o prompt de comando e execute esse arquivo com um argumento (apenas um nome de argumento falso), como: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Aguarde 5 a 10 minutos e abra Alertas da Central de Segurança. Um alerta deve aparecer.

> [!NOTE]
> Ao revisar este alerta de teste para o Windows, verifique se a auditoria de argumentos de campo **habilitada** é **verdadeira**. Se for **false**, você precisará habilitar a auditoria de argumentos de linha de comando. Para habilitar, use o seguinte comando: 
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Simular alertas em suas VMs do Azure (Linux) <a name="validate-linux"></a>

Depois que o agente da central de segurança estiver instalado no computador, siga estas etapas do computador no qual você deseja ser o recurso atacado do alerta:
1. Copie um executável para um local conveniente e renomeie-o para **./asc_alerttest_662jfi039n**, por exemplo:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Abra o prompt de comando e execute este arquivo:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Aguarde 5 a 10 minutos e abra Alertas da Central de Segurança. Um alerta deve aparecer.


## <a name="simulate-alerts-on-kubernetes"></a>Simular alertas no kubernetes <a name="validate-kubernetes"></a>

Se você integrou o serviço kubernetes do Azure com a central de segurança, poderá testar se os alertas estão funcionando com o seguinte comando kubectl:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Para obter mais informações sobre como defender seus nós e clusters do kubernetes, consulte [introdução ao Azure defender para kubernetes](defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Próximas etapas
Este artigo apresentou a você o processo de validação de alertas. Agora que você está familiarizado com esse tipo de validação, experimente os seguintes artigos:

* [Validação da detecção de ameaças do Azure Key Vault na Central de Segurança do Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Gerenciando e respondendo a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md) -saiba como gerenciar alertas e responder a incidentes de segurança na central de segurança.
* [Noções básicas sobre alertas de segurança na central de segurança do Azure](./security-center-alerts-overview.md) -saiba mais sobre os diferentes tipos de alertas de segurança.