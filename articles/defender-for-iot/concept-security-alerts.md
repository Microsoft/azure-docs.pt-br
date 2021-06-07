---
title: Lista interna de alertas & personalizados
description: Saiba mais sobre alertas de segurança e correção recomendada usando o defender para recursos e serviço do Hub IoT.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: 73b3a1ca3e52f571ab7b531235650b6bda870691
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784553"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Alertas de segurança do defender para Hub IoT

O defender para IoT analisa continuamente sua solução de IoT usando a análise avançada e a inteligência contra ameaças para alertá-lo sobre atividades mal-intencionadas.
Além disso, você pode criar alertas personalizados com base em seu conhecimento do comportamento esperado do dispositivo.
Um alerta atua como um indicador de possível comprometimento e deve ser investigado e corrigido.

Neste artigo, você encontrará uma lista de alertas internos, que podem ser disparados em seu hub IoT.
Além dos alertas internos, o defender para IoT permite que você defina alertas personalizados com base no comportamento esperado do Hub IoT e/ou do dispositivo.
Para obter mais informações, consulte [alertas personalizáveis](concept-customizable-security-alerts.md).

## <a name="built-in-alerts-for-iot-hub"></a>Alertas internos do Hub IoT

| Severidade | Nome | Descrição | Correção sugerida |
|--|--|--|--|
| Severidade **média** |  |  |  |
| Novo certificado adicionado a um hub IoT | Médio | Um certificado denominado \' % {DescCertificateName} \' foi adicionado ao Hub IOT \' % {DescIoTHubName} \' . Se essa ação foi feita por uma parte não autorizada, ela pode indicar atividade mal-intencionada. | 1. Verifique se o certificado foi adicionado por uma parte autorizada. <br> 2. se não tiver sido adicionado por uma parte autorizada, remova o certificado e encaminhe o alerta para a equipe de segurança organizacional. |
| Certificado excluído de um hub IoT | Médio | Um certificado denominado \' % {DescCertificateName} \' foi excluído do Hub IOT \' % {DescIoTHubName} \' . Se essa ação foi feita por uma parte não autorizada, ela pode indicar uma atividade mal-intencionada. | 1. Verifique se o certificado foi removido por uma parte autorizada. <br> 2. se o certificado não tiver sido removido por uma parte autorizada, adicione o certificado de volta e escale o alerta para a equipe de segurança organizacional. |
| Tentativa malsucedida de adicionar um certificado a um hub IoT | Médio | Houve uma tentativa malsucedida de adicionar o certificado \' % {DescCertificateName} \' ao Hub IOT \' % {DescIoTHubName} \' . Se essa ação foi feita por uma parte não autorizada, ela pode indicar atividade mal-intencionada. | Certifique-se de que as permissões para alterar certificados sejam concedidas somente a partes autorizadas. |
| Tentativa malsucedida de excluir um certificado de um hub IoT | Médio | Houve uma tentativa malsucedida de excluir o certificado \' % {DescCertificateName} \' do Hub IOT \' % {DescIoTHubName} \' . Se essa ação foi feita por uma parte não autorizada, ela pode indicar atividade mal-intencionada. | Certifique-se de que as permissões para alterar certificados sejam concedidas somente a uma parte autorizada. |
| incompatibilidade de impressão digital do certificado de dispositivo x. 509 | Médio | a impressão digital do certificado de dispositivo x. 509 não correspondeu à configuração. | Examine os alertas nos dispositivos. Nenhuma ação adicional é necessária. |
| o certificado x. 509 expirou | Médio | O certificado de dispositivo X. 509 expirou. | Isso pode ser um dispositivo legítimo com um certificado expirado ou uma tentativa de representar um dispositivo legítimo. Se o dispositivo legítimo estiver atualmente se comunicando corretamente, isso provavelmente é uma tentativa de representação. |
| Severidade **baixa** |  |  |  |
| Tentativa de adicionar ou editar uma configuração de diagnóstico de um hub IoT detectada | Baixo | Foi detectada uma tentativa de adicionar ou editar as configurações de diagnóstico de um hub IoT. As configurações de diagnóstico permitem recriar trilhas de atividade para fins de investigação quando ocorre um incidente de segurança ou sua rede é comprometida. Se essa ação não foi feita por uma parte autorizada, ela pode indicar atividade mal-intencionada. | 1. Verifique se o certificado foi removido por uma parte autorizada.<br> 2. se o certificado não tiver sido removido por uma parte autorizada, adicione o certificado de volta e escale o alerta para sua equipe de segurança de informações. |
| Tentativa de excluir uma configuração de diagnóstico de um hub IoT detectada | Baixo | Houve% {DescAttemptStatusMessage} \' tentativa de adicionar ou editar a configuração \' de diagnóstico% {DescDiagnosticSettingName} \' do Hub IOT \' % {DescIoTHubName} \' . A configuração de diagnóstico permite recriar trilhas de atividade para fins de investigação quando ocorre um incidente de segurança ou sua rede é comprometida. Se essa ação não foi feita por uma parte autorizada, ela poderá indicar uma atividade mal-intencionada. | Verifique se as permissões para alterar as configurações de diagnóstico são concedidas somente a uma parte autorizada. |
| Token SAS expirado | Baixo | Token SAS expirado usado por um dispositivo | Pode ser um dispositivo legítimo com um token expirado ou uma tentativa de representar um dispositivo legítimo. Se o dispositivo legítimo estiver atualmente se comunicando corretamente, isso provavelmente é uma tentativa de representação. |
| Assinatura de token SAS inválida | Baixo | Um token SAS usado por um dispositivo tem uma assinatura inválida. A assinatura não corresponde à chave primária ou secundária. | Examine os alertas nos dispositivos. Nenhuma ação adicional é necessária. |

## <a name="next-steps"></a>Próximas etapas

- [Visão geral](overview.md) do serviço defender para IOT
- Saiba como [acessar seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)
