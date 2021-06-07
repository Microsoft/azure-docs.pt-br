---
title: Defender-IoT-micro-Agent para RTOS do Azure interno & alertas e recomendações personalizáveis
description: Saiba mais sobre alertas de segurança e correção recomendada usando o Azure IoT defender-IoT-micro-Agent-RTOS.
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: cfbd411617a0b80f4857e08f9803b34b80b873d4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784672"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Defender-IoT-micro-Agent para alertas e recomendações de segurança dos RTOS do Azure (versão prévia)

O defender-IoT-micro-Agent para os RTOS do Azure analisa continuamente sua solução de IoT usando análise avançada e inteligência contra ameaças para alertá-lo sobre possíveis atividades mal-intencionadas e modificações suspeitas do sistema. Você também pode criar alertas personalizados com base em seu conhecimento sobre o comportamento esperado do dispositivo e linhas de base.

Um alerta do defender-IoT-micro-Agent para RTOS do Azure atua como um indicador de possível comprometimento e deve ser investigado e corrigido. Uma recomendação do defender-IoT-micro-Agent para RTOS do Azure identifica a postura de segurança fraca a ser corrigida e atualizada. 

Neste artigo, você encontrará uma lista de alertas internos e recomendações disparadas com base nos intervalos padrão e personalizáveis com seus próprios valores, com base no comportamento esperado ou de linha de base. 

Para obter mais informações sobre como a personalização de alertas funciona no serviço do defender para IoT, consulte [alertas personalizáveis](concept-customizable-security-alerts.md). Os alertas e recomendações específicos disponíveis para personalização ao usar o defender-IoT-micro-Agent para os RTOS do Azure são detalhados nas tabelas a seguir. 

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-security-alerts"></a>Defender-IoT-micro-Agent para os RTOS do Azure com suporte alertas de segurança

### <a name="device-related-security-alerts"></a>Alertas de segurança relacionados ao dispositivo

|Atividade de alerta de segurança relacionada ao dispositivo  |Nome do alerta  |
|---------|---------|
|Endereço IP| Comunicação com um endereço IP suspeito detectado|
|Impressão digital do certificado de dispositivo X. 509|Incompatibilidade de impressão digital do certificado de dispositivo X. 509|
|Certificado X.509| O certificado X. 509 expirou|
|Token SAS| Token SAS expirado|
|Token SAS| Assinatura de token SAS inválida|

### <a name="iot-hub-related-security-alerts"></a>Alertas de segurança relacionados ao Hub IoT

|Atividade de alerta de segurança do Hub IoT  |Nome do alerta  |
|---------|---------|
|Adicionar um certificado    |  Foi detectada uma tentativa malsucedida de adicionar um certificado a um hub IoT       |
|Adição ou edição de uma configuração de diagnóstico    | Detectada uma tentativa de adicionar ou editar uma configuração de diagnóstico de um hub IoT      |
|Excluir um certificado    |  Tentativa malsucedida de excluir um certificado de um hub IoT não detectada       |
|Excluir uma configuração de diagnóstico    |  Detectada tentativa de excluir uma configuração de diagnóstico de um hub IoT      |
|Certificado excluído    | Foi detectada a exclusão de um certificado de um hub IoT        |
|Novo certificado     |  Foi detectada a adição do novo certificado a um hub IoT       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-customizable-alerts"></a>O defender-IoT-micro-Agent para os RTOS do Azure com suporte para alertas personalizáveis

### <a name="device-related-customizable-alerts"></a>Alertas personalizáveis relacionados ao dispositivo

|Atividade relacionada ao dispositivo |Nome do alerta  |
|---------|---------|
|Conexões ativas|O número de conexões ativas não está no intervalo permitido|
|Mensagens de nuvem para dispositivo no protocolo **MQTT**|O número de mensagens de nuvem para o dispositivo no protocolo **MQTT** não está no intervalo permitido|
|Conexão de saída| Conexão de saída para um IP que não é permitido|

### <a name="hub-related-customizable-alerts"></a>Alertas personalizáveis relacionados ao Hub 

|Atividade relacionada ao Hub  |Nome do alerta  |
|---------|---------|
|Limpezas de fila de comando     |  Número de limpezas de fila de comando fora do intervalo permitido       |
|Mensagens de nuvem para dispositivo no protocolo **MQTT**    |  Número de mensagens de nuvem para dispositivo no protocolo **MQTT** fora do intervalo permitido       |
|Mensagens do dispositivo para a nuvem no protocolo **MQTT**    | Número de mensagens de dispositivo para nuvem no protocolo **MQTT** fora do intervalo permitido        |
|Invocações de método direto     |  Número de invocações de método direto fora do intervalo permitido       |
|Mensagens rejeitadas da nuvem para o dispositivo no protocolo **MQTT**     |   Número de mensagens de nuvem rejeitadas para dispositivo no protocolo **MQTT** fora do intervalo permitido      |
|Atualizações para módulos de atualização     |  Número de atualizações para módulos de n º fora do intervalo permitido       |
|Operações não autorizadas    |  Número de operações não autorizadas fora do intervalo permitido       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-recommendations"></a>Defender-IoT-micro-Agent para recomendações com suporte do Azure RTOS

### <a name="device-related-recommendations"></a>Recomendações relacionadas ao dispositivo

|Atividade relacionada ao dispositivo  |Nome da recomendação |
|---------|---------|
|Credenciais de autenticação    |  Credenciais de autenticação idênticas usadas por vários dispositivos       |

### <a name="hub-related-recommendations"></a>Recomendações relacionadas ao Hub

|Atividade relacionada ao Hub IoT  |Nome da recomendação |
|---------|---------|
|Política de filtro IP   |  A política de filtro IP padrão deve ser definida como **negar**  |
|Regra de filtro IP| A regra de filtro IP inclui um intervalo de IP grande|
|Logs de diagnóstico|Sugestão para habilitar os logs de diagnóstico no Hub IoT|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>Todos os alertas e recomendações do defender for IoT

Para obter uma lista completa de todos os alertas e recomendações relacionados ao serviço do defender para IoT, consulte [alertas de segurança](concept-security-alerts.md)de IOT, [recomendações](concept-recommendations.md)de segurança de IOT.

## <a name="next-steps"></a>Próximas etapas

- [Início rápido: defender-IoT-micro-Agent para RTOS do Azure](quickstart-azure-rtos-security-module.md)
- [Configurar e personalizar o defender-IoT-micro-Agent para RTOS do Azure](how-to-azure-rtos-security-module.md)
- Consulte o [defender-IOT-micro-Agent para a API de RTOs do Azure](azure-rtos-security-module-api.md)