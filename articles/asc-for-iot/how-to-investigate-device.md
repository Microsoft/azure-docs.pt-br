---
title: Central de segurança do Azure para guia básico de investigação de dispositivo IoT Preview | Microsoft Docs
description: Este guia de instruções explica como usar a Central de segurança do Azure para IoT para investigar um dispositivo IoT suspeito usando o Log Analytics.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: mlottner
ms.openlocfilehash: 6182662cb0da7fa5bcd3f329ada9ca5851490724
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357545"
---
# <a name="investigate-a-suspicious-iot-device"></a>Investigar um dispositivo IoT suspeito

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Esta versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Central de segurança do Azure (ASC) para a evidência e os alertas do serviço IoT fornecem indicações claras quando dispositivos IoT são suspeitos de envolvimento com atividades suspeitas ou quando indicações existem que um dispositivo está comprometido. 

Neste guia, use as sugestões de investigação fornecidas para ajudar a determinar os riscos potenciais para sua organização, decida como corrigir e descobrir as melhores maneiras de evitar ataques semelhantes no futuro.  

> [!div class="checklist"]
> * Encontrar dados do dispositivo
> * Investigar usando consultas kql


## <a name="how-can-i-access-my-data"></a>Como acessar meus dados?

Por padrão, a ASC para IoT armazena seus alertas de segurança e recomendações no espaço de trabalho do Log Analytics. Você também pode optar por armazenar seus dados brutos de segurança.

Para localizar o seu espaço de trabalho do Log Analytics para o armazenamento de dados:

1. Abra seu Hub IoT, 
1. Sob **segurança**, clique em **visão geral**e, em seguida, selecione **configurações**.
1. Altere seus detalhes de configuração do espaço de trabalho do Log Analytics. 
1. Clique em **Salvar**. 

Após a configuração, faça o seguinte para acessar dados armazenados no espaço de trabalho do Log Analytics:

1. Selecione e clique em um alerta da ASC para IoT em seu Hub IoT. 
1. Clique em **Investigação posterior**. 
1. Selecione **Para ver quais dispositivos têm esse alerta, clique aqui e veja a coluna DeviceId**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Etapas de investigação para dispositivos de IoT suspeitas

Para acessar informações e dados brutos sobre os dispositivos IoT, vá para seu espaço de trabalho do Log Analytics [para acessar seus dados](#how-can-i-access-my-data).

Verifique e investigue os dados do dispositivo para os seguintes detalhes e atividades usando as seguintes consultas kql.

### <a name="related-alerts"></a>Alertas relacionados

Para saber se outros alertas foram disparados em torno do mesmo tempo, use a seguinte consulta kql:

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>Usuários com acesso

Para descobrir quais usuários têm acesso a esse dispositivo, use a seguinte consulta kql: 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
  ~~~
Use esses dados para descobrir: 
  1. Quais usuários têm acesso ao dispositivo?
  2. Os usuários com acesso possuem os níveis de permissão conforme o esperado? 

### <a name="open-ports"></a>Abrir portas

Para descobrir quais portas no dispositivo estão em uso no momento ou que foram usadas, use a seguinte consulta de kql: 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
  ~~~

    Use this data to discover:
  1. Quais soquetes de escuta estão ativos no dispositivo no momento?
  2. Os soquetes de escutando que estão ativos no momento devem ser permitidos?
  3. Existem quaisquer endereços remotos suspeitos conectados ao dispositivo?

### <a name="user-logins"></a>Logons de usuário

Para descobrir os usuários conectados ao dispositivo usam a seguinte consulta kql: 
 
  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
  ~~~

    Use the query results to discover:
  1. Quais usuários se conectaram ao dispositivo?
  2. São os usuários que fez logon, deve fazer logon?
  3. Os usuários que fizeram logon se conectaram com endereços de IP esperados ou inesperados?
  
### <a name="process-list"></a>Lista de processos

Para descobrir se a lista de processos é conforme o esperado, use a seguinte consulta de kql: 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
  ~~~

    Use the query results to discover:

  1. Houve qualquer processo em execução suspeito no dispositivo?
  2. Os processos foram executados por usuários apropriados?
  3. Execuções de qualquer linha de comando continha os argumentos corretos e esperados?

## <a name="next-steps"></a>Próximas etapas

Após investigar um dispositivo e obter uma melhor compreensão sobre os riscos, você talvez queira considerar [Configurar alertas personalizados](quickstart-create-custom-alerts.md) para melhorar sua postura de segurança da solução de IoT. Se você ainda não tiver um agente de dispositivo, considere [Implantar um agente de segurança](how-to-deploy-agent.md) ou [alterar a configuração de um agente de dispositivo existente](how-to-agent-configuration.md) para melhorar os resultados. 