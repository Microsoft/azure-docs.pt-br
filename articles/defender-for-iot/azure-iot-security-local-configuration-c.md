---
title: Configuração local do agente de segurança (C)
description: Saiba mais sobre o defender para configurações locais do agente para C.
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: dc4ea30080711633e52f966b89a16f7246a30673
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779402"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Noções básicas sobre o arquivo LocalConfiguration.json – agente do C

O agente de segurança do defender para IoT usa configurações de um arquivo de configuração local.
O agente de segurança lê a configuração uma vez, na inicialização do agente.
A configuração encontrada no arquivo de configuração local contém a configuração de autenticação e outras configurações relacionadas ao agente.
O arquivo contém configurações em pares de "chave-valor" na notação JSON e as configurações são preenchidas quando o agente é instalado.

Por padrão, o arquivo está localizado em:/var/ASCIoTAgent/LocalConfiguration.jsem

As alterações feitas no arquivo de configuração ocorrem quando o agente é reiniciado.

## <a name="security-agent-configurations-for-c"></a>Configurações do agente de segurança para C

| Nome da configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| AgentId | GUID | O identificador exclusivo do agente |
| TriggerdEventsInterval | Cadeia de caracteres ISO8601 | Intervalo do Agendador para coleta de eventos disparados |
| ConnectionTimeout | Cadeia de caracteres ISO8601 | Período de tempo antes que a conexão com o IoThub receba tempo limite |
| Autenticação | JsonObject | Configuração de autenticação. Este objeto contém todas as informações necessárias para a autenticação no IoTHub |
| Identidade | "DPS", "SecurityModule", "dispositivo" | Identidade de autenticação – DPS se a autenticação for feita por meio do DPS, SecurityModule se a autenticação for feita por meio do defender-IoT-micro-agentcredentials ou dispositivo se a autenticação for feita com as credenciais do dispositivo |
| AuthenticationMethod | "SasToken", "SelfSignedCertificate" | o segredo do usuário para autenticação-escolha SasToken se o segredo de uso for uma chave simétrica, escolha certificado autoassinado se o segredo for um certificado autoassinado  |
| FilePath | Caminho para o arquivo (cadeia de caracteres) | Caminho para o arquivo que contém o segredo de autenticação |
| HostName | Cadeia de caracteres | O nome do host do Hub IoT do Azure. geralmente <meu Hub>. azure-devices.net |
| DeviceId | Cadeia de caracteres | A ID do dispositivo (como registrado no Hub IoT do Azure) |
| DPS | JsonObject | Configurações relacionadas a DPS |
| IDScope | Cadeia de caracteres | Escopo da ID do DPS |
| RegistrationId | Cadeia de caracteres  | ID de registro do dispositivo DPS |
| Registro em log | JsonObject | Configurações relacionadas ao agente de log |
| SystemLoggerMinimumSeverity | 0 <= número <= 4 | mensagens de log iguais e superiores a essa gravidade serão registradas em/var/log/syslog (0 é a severidade mais baixa) |
| DiagnosticEventMinimumSeverity | 0 <= número <= 4 | as mensagens de log iguais e superiores a essa gravidade serão enviadas como eventos de diagnóstico (0 é a severidade mais baixa) |

## <a name="security-agent-configurations-code-example"></a>Exemplo de código de configurações do agente de segurança

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- Leia a [visão geral](overview.md) do serviço defender para IOT
- Saiba mais sobre a [arquitetura](architecture.md) do defender para IOT
- Habilitar o [serviço](quickstart-onboard-iot-hub.md) defender para IOT
- Leia as [perguntas frequentes](resources-frequently-asked-questions.md) do serviço defender para IOT
- Aprenda a acessar [dados brutos de segurança](how-to-security-data-access.md)
- Entender as [recomendações](concept-recommendations.md)
- Entender os [alertas](concept-security-alerts.md) de segurança
