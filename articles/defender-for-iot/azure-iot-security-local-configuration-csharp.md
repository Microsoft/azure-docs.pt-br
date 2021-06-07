---
title: Configuração local do agente de segurança do defender para IoT (C#)
description: Saiba mais sobre o serviço de segurança do defender for IoT, o arquivo de configuração local do agente de segurança para C#.
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 42cf7f129005f057a9d51ce2e09db735e825f476
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784774"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Noções básicas sobre o arquivo de configuração local (agente C#)

O agente de segurança do defender para IoT usa configurações de um arquivo de configuração local.

O agente de segurança lê o arquivo de configuração uma vez quando o agente inicia a execução. As configurações encontradas no arquivo de configuração local contêm a configuração de autenticação e outras configurações relacionadas ao agente.

O agente de segurança do C# usa vários arquivos de configuração:

- Configurações relacionadas ao **General.config** Agent.
- **Authentication.config** -configuração relacionada à autenticação (incluindo detalhes de autenticação).
- Configurações relacionadas a **SecurityIotInterface.config** -IOT.

Os arquivos de configuração contêm a configuração padrão. A configuração de autenticação é populada durante a instalação do agente e as alterações no arquivo de configuração são feitas quando o agente é reiniciado.

## <a name="configuration-file-location"></a>Local do arquivo de configuração

Para Linux:

- Os arquivos de configuração do sistema operacional estão localizados em `/var/ASCIoTAgent` .

Para Windows:

- Os arquivos de configuração do sistema operacional estão localizados no diretório do agente de segurança.

### <a name="generalconfig-configurations"></a>Configurações de General.config

| Nome da configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| agentId | GUID | Identificador exclusivo do agente |
| readRemoteConfigurationTimeout | TimeSpan | Período de tempo para buscar a configuração remota do Hub IoT. Se o agente não puder buscar a configuração dentro do tempo especificado, a operação atingirá o tempo limite.|
| schedulerInterval | TimeSpan | Intervalo do Agendador interno. |
| producerInterval | TimeSpan | Intervalo do trabalhador do produtor de eventos. |
| consumerInterval | TimeSpan | Intervalo de trabalho do consumidor de eventos. |
| highPriorityQueueSizePercentage | 0 < número < 1 | A parte do cache total dedicado para mensagens de alta prioridade. |
| logLevel | "Off", "fatal", "erro", "aviso", "informações", "depurar"  | Mensagens de log iguais e superiores a essa gravidade são registradas no console de depuração (syslog no Linux). |
| fileLogLevel |  "Off", "fatal", "erro", "aviso", "informações", "depurar"| As mensagens de log são iguais e superiores a essa gravidade são registradas em arquivo (syslog no Linux). |
| diagnosticVerbosityLevel | "None", "Some", "All", | Nível de detalhamento dos eventos de diagnóstico. Nenhum – eventos de diagnóstico não são enviados. Somente alguns eventos de diagnóstico com alta importância são enviados. Todos-todos os logs também são enviados como eventos de diagnóstico. |
| logFilePath | Caminho para o arquivo | Se fileLogLevel > desativado, os logs serão gravados nesse arquivo. |
| defaultEventPriority | "Alta", "baixa", "desativado" | Prioridade de evento padrão. |

### <a name="generalconfig-example"></a>Exemplo de General.config

```xml
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| Nome da configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| moduleName | Cadeia de caracteres | Nome da identidade do defender-IoT-micro-Agent. Esse nome deve corresponder ao nome de identidade do módulo no dispositivo. |
| deviceId | Cadeia de caracteres | ID do dispositivo (como registrado no Hub IoT do Azure). |
| schedulerInterval | Cadeia de TimeSpan | Intervalo do Agendador interno. |
| gatewayHostname | Cadeia de caracteres | Nome do host do Hub IOT do Azure. Geralmente <meu Hub>. azure-devices.net |
| filePath | Cadeia de caracteres-caminho para o arquivo | Caminho para o arquivo que contém o segredo de autenticação.|
| type | "SymmetricKey", "SelfSignedCertificate" | O segredo do usuário para autenticação. Escolha *SymmetricKey* se o segredo do usuário for uma chave simétrica, escolha *certificado autoassinado* se o segredo for um certificado autoassinado. |
| identidade | "DPS", "módulo", "dispositivo" | Identidade de autenticação – DPS se a autenticação for feita por meio do DPS, módulo se a autenticação for feita usando credenciais de módulo ou dispositivo se a autenticação for feita usando as credenciais do dispositivo.
| certificateLocationKind |  "LocalFile", "Store" | LocalFile se o certificado estiver armazenado em um arquivo, armazenará se o certificado estiver localizado em um repositório de certificados. |
| idScope | Cadeia de caracteres | Escopo da ID do DPS |
| registrationId | Cadeia de caracteres  | ID de registro do dispositivo DPS. |
|

### <a name="authenticationconfig-example"></a>Exemplo de Authentication.config

```xml
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Nome da configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| transportType | "Ampq" "MQTT" | Tipo de transporte do Hub IoT. |
|

### <a name="securityiotinterfaceconfig-example"></a>Exemplo de SecurityIotInterface.config

```xml
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Próximas etapas

- Leia a [visão geral](overview.md) do serviço defender para IOT
- Saiba mais sobre a [arquitetura](architecture.md) do defender para IOT
- Habilitar o [serviço](quickstart-onboard-iot-hub.md) defender para IOT
- Leia as [perguntas frequentes](resources-frequently-asked-questions.md) do serviço defender para IOT
- Aprenda a acessar [dados brutos de segurança](how-to-security-data-access.md)
- Entender as [recomendações](concept-recommendations.md)
- Entender os [alertas](concept-security-alerts.md) de segurança
