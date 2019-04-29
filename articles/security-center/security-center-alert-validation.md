---
title: Validação de alertas na Central de Segurança do Azure | Microsoft Docs
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
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: 009f5fe7243b8ce597c2be9f9c6874cdb56d103c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706033"
---
# <a name="alerts-validation-in-azure-security-center"></a>Validação de alertas na Central de Segurança do Azure
Este documento ensina você a verificar se o sistema está configurado corretamente para os alertas da Central de Segurança do Azure.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
A Central de Segurança coleta, analisa e integra automaticamente os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros conectados, como firewall e soluções de proteção de ponto de extremidade, a fim de detectar e alertar sobre ameaças. Leia [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) para saber mais sobre alertas de segurança e [Noções básicas sobre alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) para saber mais sobre os diferentes tipos de alertas.

## <a name="alert-validation"></a>Validação de alerta
Depois que o agente da Central de Segurança for instalado no seu computador, siga as etapas abaixo no computador alvo do alerta:

1. Copie um arquivo executável (por exemplo, calc.exe) para a área de trabalho do computador ou outro diretório desejado.
2. Renomeie o arquivo como **ASC_AlertTest_662jfi039N.exe**.
3. Abra o prompt de comando e execute o arquivo com um argumento (apenas um nome de argumento falso), como: *ASC_AlertTest_662jfi039N.exe -foo*
4. Aguarde 5 a 10 minutos e abra Alertas da Central de Segurança. Lá você deve encontrar um alerta semelhante ao seguinte:

    ![Validação de alerta](./media/security-center-alert-validation/security-center-alert-validation-fig2.png)

Ao revisar o alerta, verifique se o campo Auditoria de Argumentos Habilitada aparece como true. Se ele mostra false, você precisa habilitar a auditoria de argumentos na linha de comando. Você pode habilitar essa opção usando a seguinte linha de comando:

*reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"*


> [!NOTE]
> Assista ao vídeo [Validação de alerta na Central de Segurança do Azure](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Alert-Validation-in-Azure-Security-Center) para ver uma demonstração desse recurso.

## <a name="see-also"></a>Consulte também
Este artigo apresentou a você o processo de validação de alertas. Agora que você está familiarizado com esse tipo de validação, experimente os seguintes artigos:

* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerenciar alertas e responder a incidentes de segurança na Central de Segurança.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de solução de problemas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como solucionar problemas comuns na Central de Segurança.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.
