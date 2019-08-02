---
title: Alerta de validação (arquivo de teste EICAR) na Central de segurança do Azure | Microsoft Docs
description: Este documento ajuda a validar os alertas de segurança na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: rkarlin
ms.openlocfilehash: f65b4b74a1a91fa081bd9c0d8146d055cebb0de6
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626310"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Validação de alerta (arquivo de teste EICAR) na Central de segurança do Azure
Este documento ensina você a verificar se o sistema está configurado corretamente para os alertas da Central de Segurança do Azure.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
Os alertas são notificações que a Central de segurança gera quando ele detecta ameaças em seus recursos. Ele prioriza e lista os alertas, juntamente com as informações necessárias para que você possa investigar rapidamente o problema. A Central de segurança também fornece recomendações para como você pode corrigir um ataque.
Para obter mais informações, consulte [alertas de segurança na Central de segurança do Azure](security-center-alerts-overview.md) e [Gerenciando e respondendo a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Validação de alerta

* [Windows](#validate-windows)
* [Linux](#validate-linux)

## Validar o alerta na VM do Windows <a name="validate-windows"></a>

Após a Central de segurança do agente é instalado em seu computador, siga estas etapas no computador em que você deseja ser o recurso atacado do alerta:

1. Copie um arquivo executável (por exemplo **calc.exe**) para a área de trabalho do computador ou outro diretório de sua conveniência e renomeie-o como **ASC_AlertTest_662jfi039N.exe**.
1. Abra o prompt de comando e execute o arquivo com um argumento (apenas um nome de argumento FALSO), tais como: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Aguarde 5 a 10 minutos e abra Alertas da Central de Segurança. Um alerta semelhante para o [exemplo](#alert-validate) deve ser exibido abaixo:

> [!NOTE]
> Ao revisar o alerta de teste para Windows, verifique se o campo **auditoria de argumentos habilitada** é **verdadeiro**. Se ele estiver **falsos**, em seguida, você precisa habilitar a auditoria de argumentos de linha de comando. Para habilitá-lo, use a seguinte linha de comando:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Validar o alerta em VM do Linux <a name="validate-linux"></a>

Após a Central de segurança do agente é instalado em seu computador, siga estas etapas no computador em que você deseja ser o recurso atacado do alerta:
1. Copie um arquivo executável em um local conveniente e renomeie-o para **. / asc_alerttest_662jfi039n**, por exemplo:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Abra o prompt de comando e execute o arquivo:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Aguarde 5 a 10 minutos e abra Alertas da Central de Segurança. Um alerta semelhante para o [exemplo](#alert-validate) deve ser exibido abaixo:

### Exemplo de alerta <a name="alert-validate"></a>

![Exemplo de validação de alerta](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 

## <a name="see-also"></a>Consulte também
Este artigo apresentou a você o processo de validação de alertas. Agora que você está familiarizado com esse tipo de validação, experimente os seguintes artigos:

* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerenciar alertas e responder a incidentes de segurança na Central de Segurança.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de solução de problemas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como solucionar problemas comuns na Central de Segurança.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.
