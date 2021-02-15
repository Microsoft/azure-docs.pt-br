---
title: Trabalhar com o Defender para APIs de IoT
description: Use uma API REST externa para acessar os dados descobertos por sensores e consoles de gerenciamento e execute ações com esses dados.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: reference
ms.service: azure
ms.openlocfilehash: ae7965dd319f2ff885f4329262ae4772452afd62
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523236"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Sensor do defender para IoT e APIs do console de gerenciamento

Use uma API REST externa para acessar os dados descobertos por sensores e consoles de gerenciamento e execute ações com esses dados.

As conexões são protegidas por SSL.

## <a name="getting-started"></a>Introdução

Em geral, quando você estiver usando uma API externa no sensor do Azure defender para IoT ou no console de gerenciamento local, será necessário gerar um token de acesso. Os tokens não são necessários para APIs de autenticação que você usa no sensor e no console de gerenciamento local.

Para gerar um token:

1. Na janela **configurações do sistema** , selecione **tokens de acesso**.
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Captura de tela das janelas de configurações do sistema realçando o botão tokens de acesso.":::

2. Selecione **Gerar novo token**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Selecione o botão para gerar um novo token.":::

3. Descreva a finalidade do novo token e selecione **Avançar**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Gere um novo token e insira o nome da integração associada a ele.":::

4. O token de acesso é exibido. Copie-o, pois ele não será exibido novamente.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Copie seu token de acesso para sua integração.":::

5. Selecione **Concluir**. Os tokens que você cria aparecem na caixa de diálogo **tokens de acesso** .
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Captura de tela da caixa de diálogo tokens de dispositivo com tokens preenchidos":::

   **Usado** indica a última vez em que uma chamada externa com esse token foi recebida.

   Se **N/A** for exibido no campo **usado** para esse token, a conexão entre o sensor e o servidor conectado não estará funcionando.

6. Adicione um cabeçalho HTTP intitulado **Authorization** à sua solicitação e defina seu valor para o token que você gerou.

## <a name="sensor-api-specifications"></a>Especificações da API do sensor

Esta seção descreve as seguintes APIs de sensor:

- [Recuperar informações do dispositivo-/API/v1/Devices](#retrieve-device-information---apiv1devices)

- [Recuperar informações de conexão do dispositivo-/API/v1/Devices/Connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [Recuperar informações em CVEs-/API/v1/Devices/CVEs](#retrieve-information-on-cves---apiv1devicescves)

- [Recuperar informações de alerta-/API/v1/Alerts](#retrieve-alert-information---apiv1alerts)

- [Recuperar eventos de linha do tempo-/API/v1/Events](#retrieve-timeline-events---apiv1events)

- [Informações de recuperação de vulnerabilidade-/API/v1/Reports/Vulnerabilities/Devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [Recuperar vulnerabilidades de segurança-/API/v1/Reports/Vulnerabilities/Security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [Recuperar vulnerabilidades operacionais-/API/v1/Reports/Vulnerabilities/Operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

- [Validar credenciais do usuário-/API/external/Authentication/Validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [Alterar senha-/external/Authentication/set_password](#change-password---externalauthenticationset_password)

- [Atualização de senha do usuário por administrador do sistema-/external/Authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

### <a name="retrieve-device-information---apiv1devices"></a>Recuperar informações do dispositivo-/API/v1/Devices

Use essa API para solicitar uma lista de todos os dispositivos que um sensor do defender for IoT detectou.

#### <a name="method"></a>Método

**GET**

Solicita uma lista de todos os dispositivos que o sensor do defender for IoT detectou.

#### <a name="query-parameters"></a>Parâmetros de consulta

- **autorizado**: para filtrar somente dispositivos autorizados e não autorizados.

  **Exemplos**:

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Matriz de objetos JSON que representam dispositivos.

#### <a name="device-fields"></a>Campos de dispositivo

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **id** | Numérico | Não | - |
| **ipAddresses** | Matriz JSON | Sim | Endereços IP (podem ser mais de um endereço no caso de endereços da Internet ou um dispositivo com NICs duplas) |
| **name** | String | Não | - |
| **type** | String | Não | Desconhecido, estação de engenharia, PLC, HMI, Historian, controlador de domínio, servidor de BD, ponto de acesso sem fio, roteador, comutador, servidor, estação de trabalho, câmera IP, impressora, firewall, estação de terminal, gateway de VPN, Internet ou multicast e difusão |
| **macAddresses** | Matriz JSON | Sim | Endereços MAC (pode ser mais de um endereço no caso de um dispositivo com NICs duplas) |
| **operatingSystem** | String | Sim | - |
| **engineeringStation** | Boolean | Não | Verdadeiro ou falso |
| **detector** | Boolean | Não | Verdadeiro ou falso |
| **autorizado** | Boolean | Não | Verdadeiro ou falso |
| **fabricante** | String | Sim | - |
| **protocolos** | Matriz JSON | Sim | Objeto de protocolo |
| **firmware** | Matriz JSON | Sim | Objeto firmware |

#### <a name="protocol-fields"></a>Campos de protocolo

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **Nome** | String | Não |  |
| **Endereços** | Matriz JSON | Sim | Valores mestre ou numéricos |

#### <a name="firmware-fields"></a>Campos de firmware

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **série** | String | Não | N/A ou o valor real |
| **modelo** | String | Não | N/A ou o valor real |
| **firmwareVersion** | Double | Não | N/A ou o valor real |
| **additionalData** | String | Não | N/A ou o valor real |
| **moduleAddress** | String | Não | N/A ou o valor real |
| **montável** | String | Não | N/A ou o valor real |
| **soquete** | String | Não | N/A ou o valor real |
| **address** | String | Não | N/A ou o valor real |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

#### <a name="curl-command"></a>Comando de cURL

| Type | APIs | Exemplo |
|--|--|--|
| GET | enlaçada-k-H "autorização: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/v1/Devices | enlaçada-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https: <span> //127 <span> . 0.0.1/API/v1/Devices? Authorized = true |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>Recuperar informações de conexão do dispositivo-/API/v1/Devices/Connections

Use essa API para solicitar uma lista de todas as conexões por dispositivo.

#### <a name="method"></a>Método

**GET**

#### <a name="query-parameters"></a>Parâmetros de consulta

Se você não definir os parâmetros de consulta, todas as conexões de dispositivo serão retornadas.

**Exemplo**:

`/api/v1/devices/connections`

- **DeviceID**: filtre por uma ID de dispositivo específica para exibir suas conexões.

  **Exemplo**:

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes**: intervalo de tempo de agora para trás, por minuto, durante o qual as conexões estavam ativas.

  **Exemplo**:

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore**: filtrar somente as conexões que foram detectadas antes de uma hora específica (em milissegundos, UTC).

  **Exemplo**:

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter**: filtrar somente as conexões que foram detectadas após uma hora específica (em milissegundos, UTC).

  **Exemplo**:

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Matriz de objetos JSON que representam conexões de dispositivo.

#### <a name="fields"></a>Campos

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **firstDeviceId** | Numérico | Não | - |
| **secondDeviceId** | Numérico | Não | - |
| **lastSeen** | Numérico | Não | Época (UTC) |
| **descobrir** | Numérico | Não | Época (UTC) |
| **porta** | Matriz de números | Não | - |
| **protocolos** | Matriz JSON | Não | Campo de protocolo |

#### <a name="protocol-field"></a>Campo de protocolo

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **name** | String | Não | - |
| **comandos** | Matriz de cadeia de caracteres | Não | - |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

#### <a name="curl-command"></a>Comando de cURL

> [!div class="mx-tdBreakAll"]
> | Type | APIs | Exemplo |
> |--|--|--|
> | GET | enlaçada-k-H "autorização: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/v1/Devices/Connections | enlaçada-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/API/v1/Devices/Connections |
> | GET | enlaçada-k-H "autorização: <AUTH_TOKEN>" "https://<IP_ADDRESS>/API/v1/Devices/ <deviceId> /Connections? lastActiveInMinutes =&discoveredBefore =&discoveredAfter = ' | enlaçada-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/API/v1/Devices/2/Connections? lastActiveInMinutes = 20&discoveredBefore = 1594550986000&discoveredAfter = 1594550986000 ' |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>Recuperar informações em CVEs-/API/v1/Devices/CVEs

Use essa API para solicitar uma lista de todos os CVEs conhecidos descobertos nos dispositivos na rede.

#### <a name="method"></a>Método

**GET**

#### <a name="query-parameters"></a>Parâmetros de consulta

Por padrão, essa API fornece a lista de todos os IPs de dispositivo com CVEs, até 100 CVEs de pontuação superior para cada endereço IP.

**Exemplo**:

`/api/v1/devices/cves`

- **DeviceID**: filtrar por um endereço IP de dispositivo específico para obter até 100 CVEs de pontuação superior identificadas nesse dispositivo específico.

  **Exemplo**:

  `/api/v1/devices/<ipAddress>/cves`

- **Top**: Quantos CVEs de pontuação superior a serem recuperados para cada endereço IP do dispositivo.

  **Exemplo**:

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Matriz de objetos JSON que representam CVEs identificados em endereços IP.

#### <a name="fields"></a>Campos

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **cveId** | String | Não | - |
| **IP** | String | Não | Endereço IP |
| **placar** | String | Não | 0,0-10,0 |
| **attackVector** | String | Não | Rede, rede adjacente, local ou físico |
| **descrição** | String | Não | - |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

#### <a name="curl-command"></a>Comando de cURL

| Type | APIs | Exemplo |
|--|--|--|
| GET | enlaçada-k-H "autorização: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/v1/Devices/CVEs | enlaçada-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/API/v1/Devices/CVEs |
| GET | enlaçada-k-H "autorização: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/v1/Devices/ <deviceIpAddress> /CVEs? Top = | ondulação-k-H "autorização: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/API/v1/Devices/10.10.10.15/CVEs? Top = 50 |

### <a name="retrieve-alert-information---apiv1alerts"></a>Recuperar informações de alerta-/API/v1/Alerts

Use essa API para solicitar uma lista de todos os alertas detectados pelo sensor do defender para IoT.

#### <a name="method"></a>Método

**GET**

#### <a name="query-parameters"></a>Parâmetros de consulta

- **estado**: para filtrar somente os alertas manipulados ou não tratados.

  **Exemplo**:

  `/api/v1/alerts?state=handled`

- **detime**: para filtrar os alertas criados a partir de um tempo específico (em milissegundos, UTC).

  **Exemplo**:

  `/api/v1/alerts?fromTime=<epoch>`

- **ToTime**: para filtrar os alertas criados somente antes de um tempo específico (em milissegundos, UTC).

  **Exemplo**:

  `/api/v1/alerts?toTime=<epoch>`

- **tipo**: para filtrar alertas por um tipo específico. Tipos existentes para filtrar por: novos dispositivos inesperados, desconexões.

  **Exemplo**:

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Matriz de objetos JSON que representam alertas.

#### <a name="alert-fields"></a>Campos de alerta

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **ID** | Numérico | Não | - |
| **time** | Numérico | Não | Época (UTC) |
| **title** | String | Não | - |
| **message** | String | Não | - |
| **severity** | String | Não | Aviso, secundário, principal ou crítico |
| **motores** | String | Não | Violação de protocolo, violação de política, malware, anomalia ou operacional |
| **sourceDevice** | Numérico | Sim | ID do Dispositivo |
| **destinationDevice** | Numérico | Sim | ID do Dispositivo |
| **additionalInformation** | Objeto de informações adicionais | Sim | - |

#### <a name="additional-information-fields"></a>Campos de informações adicionais

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **descrição** | String | Não | - |
| **divulgação** | Matriz JSON | Não | String |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

#### <a name="curl-command"></a>Comando de cURL

> [!div class="mx-tdBreakAll"]
> | Type | APIs | Exemplo |
> |--|--|--|
> | GET | enlaçada-k-H "autorização: <AUTH_TOKEN>" "https://<IP_ADDRESS>/API/v1/Alerts? estado =&detime =&paratime =&Type = ' | ondulação-k-H "autorização: 1234b734a9244d54ab8d40aedddcabcd" "https:/ <span> /127.0.0.1/API/v1/Alerts? estado = sem tratamento&detime = 1594550986000&nahora = 1594550986001&tipo = desconexões ' |

### <a name="retrieve-timeline-events---apiv1events"></a>Recuperar eventos de linha do tempo-/API/v1/Events

Use essa API para solicitar uma lista de eventos relatados para a linha do tempo do evento.

#### <a name="method"></a>Método

**GET**

#### <a name="query-parameters"></a>Parâmetros de consulta

- **minutesTimeFrame**: o intervalo de tempo de agora para trás, por minuto, no qual os eventos foram relatados.

  **Exemplo**:

  `/api/v1/events?minutesTimeFrame=20`

- **tipo**: para filtrar a lista de eventos por um tipo específico.

  **Exemplos**:

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Matriz de objetos JSON que representam alertas.

#### <a name="event-fields"></a>Campos de evento

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|--|
| **timestamp** | Numérico | Não | Época (UTC) |
| **title** | String | Não | - |
| **severity** | String | Não | INFORMAÇÕES, aviso ou alerta |
| **owner** | String | Sim | Se o evento foi criado manualmente, esse campo incluirá o nome de usuário que criou o evento |
| **content** | String | Não | - |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

#### <a name="curl-command"></a>Comando de cURL

| Type | APIs | Exemplo |
|--|--|--|
| GET | enlaçada-k-H "autorização: <AUTH_TOKEN>" "https://<IP_ADDRESS>/API/v1/Events? minutesTimeFrame =&Type = ' | ondulação-k-H "autorização: 1234b734a9244d54ab8d40aedddcabcd" "https:/ <span> /127.0.0.1/API/v1/Events? minutesTimeFrame = 20&Type = DEVICE_CONNECTION_CREATED ' |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>Informações de recuperação de vulnerabilidade-/API/v1/Reports/Vulnerabilities/Devices

Use essa API para solicitar resultados de avaliação de vulnerabilidade para cada dispositivo.

#### <a name="method"></a>Método

**GET**

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Matriz de objetos JSON que representam dispositivos avaliados.

O objeto de dispositivo contém:

- Dados gerais

- Pontuação de avaliação

- Vulnerabilidades

#### <a name="device-fields"></a>Campos de dispositivo

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **name** | String | Não | - |
| **ipAddresses** | Matriz JSON | Não | - |
| **securityScore** | Numérico | Não | - |
| **fabricante** | String | Sim |  |
| **firmwareVersion** | String | Sim | - |
| **modelo** | String | Sim | - |
| **isWirelessAccessPoint** | Boolean | Não | Verdadeiro ou falso |
| **operatingSystem** | Objeto do sistema operacional | Sim | - |
| **vulnerabilidades** | Objeto de vulnerabilidades | Sim | - |

#### <a name="operating-system-fields"></a>Campos do sistema operacional

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **Nome** | String | Sim | - |
| **Tipo** | String | Sim | - |
| **Versão** | String | Sim | - |
| **latestVersion** | String | Sim | - |

#### <a name="vulnerabilities-fields"></a>Campos de vulnerabilidades
 
| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **antivírus** | Matriz JSON | Sim | Nomes de antivírus |
| **plainTextPasswords** | Matriz JSON | Sim | Objetos de senha |
| **remoteAccess** | Matriz JSON | Sim | Objetos de acesso remoto |
| **isBackupServer** | Boolean | Não | Verdadeiro ou falso |
| **openedPorts** | Matriz JSON | Sim | Objetos de porta abertos |
| **isEngineeringStation** | Boolean | Não | Verdadeiro ou falso |
| **isKnownScanner** | Boolean | Não | Verdadeiro ou falso |
| **cves** | Matriz JSON | Sim | Objetos de CVE |
| **isUnauthorized** | Boolean | Não | Verdadeiro ou falso |
| **malwareIndicationsDetected** | Boolean | Não | Verdadeiro ou falso |
| **weakAuthentication** | Matriz JSON | Sim | Aplicativos detectados que estão usando autenticação fraca |

#### <a name="password-fields"></a>Campos de senha

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **password** | String | Não | - |
| **protocol** | String | Não | - |
| **segurança** | String | Não | Muito fraco, fraco, médio ou forte |

#### <a name="remote-access-fields"></a>Campos de acesso remoto

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **port** | Numérico | Não | - |
| **porta** | String | Não | TCP ou UDP |
| **cliente** | String | Não | Endereço IP |
| **clientSoftware** | String | Não | SSH, VNC, área de trabalho remota ou Visualizador de equipe |

#### <a name="open-port-fields"></a>Abrir campos de porta

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **port** | Numérico | Não | - |
| **porta** | String | Não | TCP ou UDP |
| **protocol** | String | Sim | - |
| **isConflictingWithFirewall** | Boolean | Não | Verdadeiro ou falso |

#### <a name="cve-fields"></a>Campos de CVE

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **ID** | String | Não | - |
| **placar** | Numérico | Não | Double |
| **descrição** | String | Não | - |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]

```

#### <a name="curl-command"></a>Comando de cURL

| Type | APIs | Exemplo |
|--|--|--|
| GET | enlaçada-k-H "autorização: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/v1/Reports/Vulnerabilities/Devices | enlaçada-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/API/v1/Reports/Vulnerabilities/Devices |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>Recuperar vulnerabilidades de segurança-/API/v1/Reports/Vulnerabilities/Security

Use essa API para solicitar resultados de uma avaliação de vulnerabilidade geral. Essa avaliação fornece informações sobre o nível de segurança do seu sistema.

Essa avaliação é baseada em informações gerais de rede e de sistema, e não em uma avaliação de dispositivo específica.

#### <a name="method"></a>Método

**GET**

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Objeto JSON que representa resultados avaliados. Cada chave pode ser anulável. Caso contrário, ele conterá um objeto JSON com chaves não anuláveis.

### <a name="result-fields"></a>Campos de resultado

**Chaves**

**unauthorizedDevices**

| Nome do campo | Tipo | Lista de valores |
| ---------- | ---- | -------------- |
| **address** | String | Endereço IP |
| **name** | String | - |
| **firstDetectionTime** | Numérico | Época (UTC) |
| lastSeen | Numérico | Época (UTC) |

**illegalTrafficByFirewallRules**

| Nome do campo | Tipo | Lista de valores |
| ---------- | ---- | -------------- |
| **server** | String | Endereço IP |
| **cliente** | String | Endereço IP |
| **port** | Numérico | - |
| **porta** | String | TCP, UDP ou ICMP |

**weakFirewallRules**

| Nome do campo | Tipo | Lista de valores |
| ---------- | ---- | -------------- |
| **sources** | Matriz JSON de fontes. Cada fonte pode estar em qualquer um dos quatro formatos. | "Qualquer", "endereço IP (host)", "de IP para IP (intervalo)", "endereço IP, máscara de sub-rede (rede)" |
| **destinos** | Matriz JSON de destinos. Cada destino pode estar em qualquer um dos quatro formatos. | "Qualquer", "endereço IP (host)", "de IP para IP (intervalo)", "endereço IP, máscara de sub-rede (rede)" |
| **porta** | Matriz JSON de portas em qualquer um dos três formatos | "Qualquer", "porta (protocolo, se detectada)", de porta para porta (protocolo, se detectado) " |

**Acesso**

| Nome do campo | Tipo | Lista de valores |
| ---------- | ---- | -------------- |
| **macAddress** | String | Endereço MAC |
| **fabricante** | String | Nome do fornecedor |
| **IP** | String | Endereço IP ou N/A |
| **name** | String | Nome do dispositivo ou N/A |
| **sem fio** | String | Não, suspeito ou Sim |

**connectionsBetweenSubnets**

| Nome do campo | Tipo | Lista de valores |
| ---------- | ---- | -------------- |
| **server** | String | Endereço IP |
| **cliente** | String | Endereço IP |

**industrialMalwareIndicators**

| Nome do campo | Tipo | Lista de valores |
| ---------- | ---- | -------------- |
| **detecçãotime** | Numérico | Época (UTC) |
| **alertMessage** | String | - |
| **descrição** | String | - |
| **pseudodispositivos** | Matriz JSON | Nomes de dispositivo | 

**internetConnections**

| Nome do campo | Tipo | Lista de valores |
| ---------- | ---- | -------------- |
| **internalAddress** | String | Endereço IP |
| **autorizado** | Boolean | Sim ou não | 
| **externalAddresses** | Matriz JSON | Endereço IP |

#### <a name="response-example"></a>Exemplo de resposta

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Comando de cURL

| Type | APIs | Exemplo |
|--|--|--|
| GET | enlaçada-k-H "autorização: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/v1/Reports/Vulnerabilities/Security | enlaçada-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/API/v1/Reports/Vulnerabilities/Security |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>Recuperar vulnerabilidades operacionais-/API/v1/Reports/Vulnerabilities/Operational

Use essa API para solicitar resultados de uma avaliação de vulnerabilidade geral. Essa avaliação fornece informações sobre o status operacional da sua rede. Ele é baseado em informações gerais de rede e do sistema, e não em uma avaliação de dispositivo específica.

#### <a name="method"></a>Método

**GET**

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Objeto JSON que representa resultados avaliados. Cada chave contém uma matriz JSON de resultados.

#### <a name="result-fields"></a>Campos de resultado

**Chaves**

**backupServer**

| Nome do campo | Tipo | Lista de valores |
|--|--|--|
| **source** | String | Endereço IP |
| **destination** | String | Endereço IP |
| **port** | Numérico | - |
| **porta** | String | TCP ou UDP |
| **backupMaximalInterval** | String | - |
| **lastSeenBackup** | Numérico | Época (UTC) |

**ipNetworks**

| Nome do campo | Tipo | Lista de valores |
|--|--|--|
| **endereçar** s | Numérico | - |
| **rede** | String | Endereço IP |
| **mascara** | String | Máscara de sub-rede |

**protocolProblems**

| Nome do campo | Tipo | Lista de valores |
|--|--|--|
| **protocol** | String | - |
| **atende** | Matriz JSON | Endereços IP |
| **alerta** | String | - |
| **relatórios** | Numérico | Época (UTC) |

**protocolDataVolumes**

| Nome do campo | Tipo | Lista de valores |
|--|--|--|
| protocolo | String | - |
| volume | String | "número de volume MB" |

**desconexões**

| Nome do campo | Tipo | Lista de valores |
|--|--|--|
| **assetAddress** | String | Endereço IP |
| **assetName** | String | - |
| **lastDetectionTime** | Numérico | Época (UTC) |
| **backToNormalTime** | Numérico | Época (UTC) |     

#### <a name="response-example"></a>Exemplo de resposta

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Comando de cURL

| Type | APIs | Exemplo |
|--|--|--|
| GET | enlaçada-k-H "autorização: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/v1/Reports/Vulnerabilities/Operational | enlaçada-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/API/v1/Reports/Vulnerabilities/Operational |

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>Validar credenciais do usuário-/API/external/Authentication/Validation

Use essa API para validar um nome de usuário e senha do defender for IoT. Todas as funções de usuário do defender para IoT podem trabalhar com a API.

Você não precisa de um token de acesso do defender para IoT para usar essa API.

#### <a name="method"></a>Método

**POST**

#### <a name="request-type"></a>Tipo de solicitação

**JSON**

#### <a name="query-parameters"></a>Parâmetros de consulta

| **Nome** | **Tipo** | **Permite valor nulo** |
|--|--|--|
| **username** | String | Não |
| **password** | String | Não |

#### <a name="request-example"></a>Exemplo de solicitação

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Cadeia de caracteres de mensagem com os detalhes de status da operação:

- **Êxito-mensagem**: autenticação bem-sucedida

- **Falha-erro**: falha na validação de credenciais

#### <a name="response-example"></a>Exemplo de resposta

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

#### <a name="curl-command"></a>Comando de cURL

| Type | APIs | Exemplo |
|--|--|--|
| GET | enlaçada-k-H "autorização: <AUTH_TOKEN>" https://<IP_ADDRESS>/API/external/Authentication/Validation | enlaçada-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/API/external/Authentication/Validation |

### <a name="change-password---externalauthenticationset_password"></a>Alterar senha-/external/Authentication/set_password

Use essa API para permitir que os usuários alterem suas próprias senhas. Todas as funções de usuário do defender para IoT podem trabalhar com a API. Você não precisa de um token de acesso do defender para IoT para usar essa API.

#### <a name="method"></a>Método

**POST**

#### <a name="request-type"></a>Tipo de solicitação

**JSON**

#### <a name="request-example"></a>Exemplo de solicitação

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Cadeia de caracteres de mensagem com os detalhes de status da operação:

- **Êxito – msg: a** senha foi substituída

- **Falha – erro**: falha na autenticação do usuário

- **Falha – erro: a** senha não corresponde à política de segurança

#### <a name="response-example"></a>Exemplo de resposta

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Campos de dispositivo

| **Nome** | **Tipo** | **Permite valor nulo** |
|--|--|--|
| **username** | String | Não |
| **password** | String | Não |
| **new_password** | String | Não |

#### <a name="curl-command"></a>Comando de cURL

| Type | APIs | Exemplo |
|--|--|--|
| POST | enlaçada-k-d ' {"username": "<USER_NAME>", "password": "<CURRENT_PASSWORD>", "new_password": "<NEW_PASSWORD>"} '-H ' Content-Type: Application/JSON ' https://<IP_ADDRESS>/API/external/Authentication/set_password | enlaçada-k-d ' {"username": "myuser", "senha": " 1234@abcd ", "new_password": " abcd@1234 "} '-H ' Content-Type: Application/JSON ' https:/ <span> /127.0.0.1/API/external/Authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Atualização de senha do usuário por administrador do sistema-/external/Authentication/set_password_by_admin

Use essa API para permitir que os administradores de sistema alterem senhas para usuários especificados. As funções de usuário do defender para administrador de IoT podem trabalhar com a API. Você não precisa de um token de acesso do defender para IoT para usar essa API.

#### <a name="method"></a>Método

**POST**

#### <a name="request-type"></a>Tipo de solicitação

**JSON**

#### <a name="request-example"></a>Exemplo de solicitação

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Cadeia de caracteres de mensagem com os detalhes de status da operação:

- **Êxito – msg: a** senha foi substituída

- **Falha – erro**: falha na autenticação do usuário

- **Falha – erro**: o usuário não existe

- **Falha – erro: a** senha não corresponde à política de segurança

- **Falha – erro**: o usuário não tem as permissões para alterar a senha

#### <a name="response-example"></a>Exemplo de resposta

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Campos de dispositivo

| **Nome** | **Tipo** | **Permite valor nulo** |
|--|--|--|
| **admin_username** | String | Não |
| **admin_password** | String | Não |
| **username** | String | Não |
| **new_password** | String | Não |

#### <a name="curl-command"></a>Comando de cURL

> [!div class="mx-tdBreakAll"]
> | Type | APIs | Exemplo |
> |--|--|--|
> | POST | enlaçada-k-d ' {"admin_username": "<ADMIN_USERNAME>", "admin_password": "<ADMIN_PASSWORD>", "username": "<USER_NAME>", "new_password": "<NEW_PASSWORD>"} '-H ' Content-Type: Application/JSON ' https://<IP_ADDRESS>/API/external/Authentication/set_password_by_admin | enlaçada-k-d ' {"admin_user": "adminUser", "admin_password": " 1234@abcd ", "username": "myuser", "new_password": " abcd@1234 "} '-H ' Content-Type: Application/JSON ' https:/ <span> /127.0.0.1/API/external/Authentication/set_password_by_admin |

## <a name="on-premises-management-console-api-specifications"></a>Especificações da API do console de gerenciamento local

Esta seção descreve as seguintes APIs do console de gerenciamento local:

- **/external/v1/alerts/<UUID>**

- **Exclusões de alerta (janela de manutenção)**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="A janela de exclusão de alertas, mostrando regras ativas.":::

Defina as condições sob as quais os alertas não serão enviados. Por exemplo, defina e atualize as horas de parada e de início, os dispositivos ou sub-redes que devem ser excluídos ao disparar alertas ou defender para mecanismos de IoT que devem ser excluídos. Por exemplo, durante uma janela de manutenção, talvez você queira interromper a entrega de todos os alertas, exceto alertas de malware em dispositivos críticos.

As APIs que você define aqui aparecem na janela de **exclusões de alerta** do console de gerenciamento local como uma regra de exclusão somente leitura.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **Exemplo de resposta**

- **responde**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password---externalauthenticationset_password"></a>Alterar senha-/external/Authentication/set_password

Use essa API para permitir que os usuários alterem suas próprias senhas. Todas as funções de usuário do defender para IoT podem trabalhar com a API. Você não precisa de um token de acesso do defender para IoT para usar essa API.

#### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Atualização de senha do usuário por administrador do sistema-/external/Authentication/set_password_by_admin

Use essa API para permitir que os administradores de sistema alterem senhas para usuários específicos. As funções de usuário do administrador do defender para IoT podem trabalhar com a API. Você não precisa de um token de acesso do defender para IoT para usar essa API.

### <a name="retrieve-device-information---externalv1devices"></a>Recuperar informações do dispositivo-/external/v1/Devices

Essa API solicita uma lista de todos os dispositivos detectados pelo defender para sensores de IoT que estão conectados a um console de gerenciamento local.

#### <a name="method"></a>Método

**GET**

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Matriz de objetos JSON que representam dispositivos.

#### <a name="query-parameters"></a>Parâmetros de consulta

- **autorizado**: para filtrar somente dispositivos autorizados e não autorizados.

- **SiteId**: para filtrar somente os dispositivos relacionados a sites específicos.

- **Identificação_da_Zona**: para filtrar somente os dispositivos relacionados a zonas específicas. [1](#1)

- **sensorid**: para filtrar somente os dispositivos detectados por sensores específicos. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *talvez você não tenha o site e a ID da zona. Se esse for o caso, consulte todos os dispositivos para recuperar o site e a ID da zona.*

#### <a name="query-parameters-example"></a>Exemplo de parâmetros de consulta

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Campos de dispositivo

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **sensorId** | Numérico | Não | - |
| **zonaid** | Numérico | Sim | - |
| **siteId** | Numérico | Sim | - |
| **ipAddresses** | Matriz JSON | Sim | Endereços IP (podem ser mais de um endereço no caso de endereços da Internet ou um dispositivo com NICs duplas) |
| **name** | String | Não | - |
| **type** | String | Não | Desconhecido, estação de engenharia, PLC, HMI, Historian, controlador de domínio, servidor de BD, ponto de acesso sem fio, roteador, comutador, servidor, estação de trabalho, câmera IP, impressora, firewall, estação de terminal, gateway de VPN, Internet ou multicast e difusão |
| **macAddresses** | Matriz JSON | Sim | Endereços MAC (pode ser mais de um endereço no caso de um dispositivo com NICs duplas) |
| **operatingSystem** | String | Sim | - |
| **engineeringStation** | Boolean | Não | Verdadeiro ou falso |
| **detector** | Boolean | Não | Verdadeiro ou falso |
| **autorizado** | Boolean | Não | Verdadeiro ou falso |
| **fabricante** | String | Sim | - |
| **Protocolos** | Matriz JSON | Sim | Objeto de protocolo |
| **firmware** | Matriz JSON | Sim | Objeto firmware |

#### <a name="protocol-fields"></a>Campos de protocolo

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| Nome | String | Não | - |
| Endereços | Matriz JSON | Sim | Valores mestre ou numéricos |

#### <a name="firmware-fields"></a>Campos de firmware

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **série** | String | Não | N/A ou o valor real |
| **modelo** | String | Não | N/A ou o valor real |
| **firmwareVersion** | Double | Não | N/A ou o valor real |
| **additionalData** | String | Não | N/A ou o valor real |
| **moduleAddress** | String | Não | N/A ou o valor real |
| **montável** | String | Não | N/A ou o valor real |
| **soquete** | String | Não | N/A ou o valor real |
| **address** | String | Não | N/A ou o valor real |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

#### <a name="curl-command"></a>Comando de cURL

| Type | APIs | Exemplo |
|--|--|--|
| GET | ondulação-k-H "autorização: <AUTH_TOKEN>" "https://<>IP_ADDRESS>/external/v1/Devices? SiteId =&Identificação_da_Zona =&sensorid =&autorizado = ' | enlaçada-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/external/v1/Devices? SiteId = 1&Identificação_da_Zona = 2&sensorid = 5&Authorized = true ' |

### <a name="retrieve-alert-information---externalv1alerts"></a>Recuperar informações de alerta-/external/v1/Alerts

Use essa API para recuperar todos os alertas ou filtros filtrados de um console de gerenciamento local.

#### <a name="method"></a>Método

**GET**

#### <a name="query-parameters"></a>Parâmetros de consulta

- **estado**: para filtrar somente os alertas manipulados e não tratados.

  **Exemplo**:

  `/api/v1/alerts?state=handled`

- **detime**: para filtrar os alertas criados a partir de um tempo específico (em milissegundos, UTC).

  **Exemplo**:

  `/api/v1/alerts?fromTime=<epoch>`

- **ToTime**: para filtrar os alertas criados somente antes de um tempo específico (em milissegundos, UTC).

  **Exemplo**:

  `/api/v1/alerts?toTime=<epoch>`

- **SiteId**: o site em que o alerta foi descoberto. [2](#2)

- **Identificação_da_Zona**: a zona na qual o alerta foi descoberto. [2](#2)

- **sensor**: o sensor no qual o alerta foi descoberto.

##### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="2">2</a> *talvez você não tenha o site e a ID da zona. Se esse for o caso, consulte todos os dispositivos para recuperar o site e a ID da zona.*

#### <a name="alert-fields"></a>Campos de alerta

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **ID** | Numérico | Não | - |
| **time** | Numérico | Não | Época (UTC) |
| **title** | String | Não | - |
| **message** | String | Não | - |
| **severity** | String | Não | Aviso, secundário, principal ou crítico |
| **motores** | String | Não | Violação de protocolo, violação de política, malware, anomalia ou operacional |
| **sourceDevice** | Numérico | Sim | ID do Dispositivo |
| **destinationDevice** | Numérico | Sim | ID do Dispositivo |
| **additionalInformation** | Objeto de informações adicionais | Sim | - |

#### <a name="additional-information-fields"></a>Campos de informações adicionais

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **descrição** | String | Não | - |
| **divulgação** | Matriz JSON | Não | String |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Traffic Detected on sensor Interface",
        
        "additionalInformation": null,
        
        "sourceDevice": 0,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808245000,
        
        "sensorId": 1,
        
        "message": "The sensor resumed detecting network traffic on ens224.",
        
        "destinationDevice": 0,
        
        "id": 1,
        
        "severity": "Warning"
    
    },
    
    {
    
        "engine": "Anomaly",
        
        "handled": false,
        
        "title": "Address Scan Detected",
        
        "additionalInformation": null,
        
        "sourceDevice": 4,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808260000,
        
        "sensorId": 1,
        
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        
        "destinationDevice": 0,
        
        "id": 2,
        
        "severity": "Critical"
    
    },
    
    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Suspicion of Unresponsive MODBUS Device",
        
        "additionalInformation": null,
        
        "sourceDevice": 194,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808285000,
        
        "sensorId": 1,
        
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        
        "destinationDevice": 0,
        
        "id": 3,
        
        "severity": "Minor"
    
    }
  
]
```

#### <a name="curl-command"></a>Comando de cURL

> [!div class="mx-tdBreakAll"]
> | Type | APIs | Exemplo |
> |--|--|--|
> | GET | enlaçada-k-H "autorização: <AUTH_TOKEN>" "https://<>IP_ADDRESS>/external/v1/Alerts? estado =&Identificação_da_Zona =&detime =&natime =&SiteId =&sensor = ' | ondulação-k-H "autorização: 1234b734a9244d54ab8d40aedddcabcd" "https:/ <span> /127.0.0.1/external/v1/Alerts? estado = sem tratamento&ZoneID = 1&detime = 0&natime = 1594551777000&SiteId = 1&sensor = 1 ' |

### <a name="qradar-alerts"></a>Alertas do QRadar

A integração do QRadar com o defender para IoT ajuda a identificar os alertas gerados pelo defender para IoT e executar ações com esses alertas. O QRadar recebe os dados do defender para IoT e, em seguida, contata o componente de console de gerenciamento local da API pública.

Para enviar os dados descobertos pelo defender for IoT para o QRadar, defina uma regra de encaminhamento no sistema do defender para IoT e selecione a opção de **tratamento de alertas de suporte remoto** .

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Edite as regras de encaminhamento para atender às suas necessidades.":::

Quando você seleciona essa opção durante o processo de configuração de regras de encaminhamento, os seguintes campos adicionais aparecem no QRadar:

- **UUID**: identificador de alerta exclusivo, como 1-1555245116250.

- **Site**: o site em que o alerta foi descoberto.

- **Zona**: a zona onde o alerta foi descoberto.

Exemplo da carga enviada para QRadar:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>UUID de/external/v1/Alerts/ &lt;&gt;

#### <a name="method"></a>Método

**PUT**

#### <a name="request-type"></a>Tipo de solicitação

**JSON**

#### <a name="request-content"></a>Conteúdo da solicitação

Objeto JSON que representa a ação a ser executada no alerta que contém o UUID.

#### <a name="action-fields"></a>Campos de ação

| Nome | Type | Nullable | Lista de valores |
|--|--|--|--|
| **action** | String | Não | identificador ou handleAndLearn |

#### <a name="request-example"></a>Exemplo de solicitação

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Matriz de objetos JSON que representam dispositivos.

#### <a name="response-fields"></a>Campos de resposta


| Nome | Type | Nullable | Descrição |
|--|--|--|--|
| **conteúdo/erro** | String | Não | Se a solicitação for bem-sucedida, a propriedade Content será exibida. Caso contrário, a Propriedade Error será exibida. |

#### <a name="possible-content-values"></a>Possíveis valores de conteúdo

| Código de status | Valor do conteúdo | Descrição |
|--|--|--|
| 200 | Solicitação de atualização de alerta concluída com êxito. | A solicitação de atualização foi concluída com êxito. Nenhum comentário. |
| 200 | O alerta já foi tratado (**identificador**). | O alerta já foi tratado quando uma solicitação de identificador para o alerta foi recebida.<br />O alerta permanece **Tratado**. |
| 200 | O alerta já foi tratado e aprendido (**handleAndLearn**). | O alerta já foi tratado e aprendido quando uma solicitação para **handleAndLearn** foi recebida.<br />O alerta permanece no status **handledAndLearn** . |
| 200 | O alerta já foi tratado (**manipulado**).<br />O identificador e saiba (**handleAndLearn**) foi executado no alerta. | O alerta já foi tratado quando uma solicitação para **handleAndLearn** foi recebida.<br />O alerta se torna **handleAndLearn**. |
| 200 | O alerta já foi tratado e aprendido (**handleAndLearn**). Solicitação de identificador ignorada. | O alerta já foi **handleAndLearn** quando uma solicitação para tratar o alerta foi recebida. O alerta permanece **handleAndLearn**. |
| 500 | Ação inválida. | A ação enviada não é uma ação válida a ser executada no alerta. |
| 500 | Ocorreu um erro inesperado. | Erro inesperado. Para resolver o problema, entre em contato com o suporte técnico. |
| 500 | Não foi possível executar a solicitação porque nenhum alerta foi encontrado para este UUID. | O UUID de alerta especificado não foi encontrado no sistema. |

#### <a name="response-example"></a>Exemplo de resposta

**Bem-sucedida**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**Sem êxito**

```rest
{
    "error": "Invalid action"
}
```

#### <a name="curl-command"></a>Comando de cURL

| Type | APIs | Exemplo |
|--|--|--|
| PUT | enlaçada-k-X PUT-d ' {"ação": " <ACTION> "} '-H "autorização: <AUTH_TOKEN>" https://<IP_ADDRESS>/external/v1/Alerts/<UUID> | enlaçada-k-X PUT-d ' {"ação": "identificador"} '-H "autorização: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/external/v1/Alerts/1-1594550943000 |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>Exclusões de alerta (janela de manutenção)-/external/v1/maintenanceWindow

Defina as condições sob as quais os alertas não serão enviados. Por exemplo, defina e atualize as horas de parada e de início, os dispositivos ou sub-redes que devem ser excluídos ao disparar alertas ou defender para mecanismos de IoT que devem ser excluídos. Por exemplo, durante uma janela de manutenção, talvez você queira parar a entrega de alerta de todos os alertas, exceto os alertas de malware em dispositivos críticos.

As APIs que você define aqui aparecem na janela de **exclusões de alerta** do console de gerenciamento local como uma regra de exclusão somente leitura.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="A janela exclusões de alerta, mostrando uma lista de todas as regras de exclusão. ":::

#### <a name="method---post"></a>Método – POST

#### <a name="query-parameters"></a>Parâmetros de consulta

- **TicketID**: define a ID do tíquete de manutenção nos sistemas do usuário.

- **TTL**: define o TTL (vida útil), que é a duração da janela de manutenção em minutos. Após o período de tempo que esse parâmetro define, o sistema inicia automaticamente o envio de alertas.

- **mecanismos**: define de qual mecanismo de segurança suprimir alertas durante o processo de manutenção:

   - ANOMALIAS

   - ATAQUE

   - EFICIÊNCIA

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds**: define de qual sensor do defender para IOT suprimirá alertas durante o processo de manutenção. É a mesma ID recuperada de/API/v1/Appliances (GET).

- **sub-redes**: define de qual sub-rede suprimir alertas durante o processo de manutenção. A sub-rede é enviada no seguinte formato: 192.168.0.0/16.

#### <a name="error-codes"></a>Códigos do Erro

- **201 (criado)**: a ação foi concluída com êxito.

- **400 (solicitação inadequada)**: aparece nos seguintes casos:

   - O parâmetro **TTL** não é numérico ou não é positivo.

   - O parâmetro **sub-redes** foi definido usando um formato incorreto.

   - O parâmetro **TicketID** está ausente.

   - O parâmetro do **mecanismo** não corresponde aos mecanismos de segurança existentes.

- **404 (não encontrado)**: um dos sensores não existe.

- **409 (conflito)**: a ID do tíquete está vinculada a outra janela de manutenção aberta.

- **500 (erro interno do servidor)**: qualquer outro erro inesperado.

> [!NOTE]
> Verifique se a ID do tíquete não está vinculada a uma janela aberta existente. A seguinte regra de exclusão é gerada: manutenção-{nome do token}-{ID do tíquete}.

#### <a name="method---put"></a>Método-PUT

Permite atualizar a duração da janela de manutenção depois de iniciar o processo de manutenção alterando o parâmetro **TTL** . A nova definição de duração substitui a anterior.

Esse método é útil quando você deseja definir uma duração maior do que a duração configurada atualmente.

#### <a name="query-parameters"></a>Parâmetros de consulta

- **TicketID**: define a ID do tíquete de manutenção nos sistemas do usuário.

- **TTL**: define a duração da janela em minutos.

#### <a name="error-code"></a>Código do erro

- **200 (OK)**: a ação foi concluída com êxito.

- **400 (solicitação inadequada)**: aparece nos seguintes casos:

   - O parâmetro **TTL** não é numérico ou não é positivo.

   - O parâmetro **TicketID** está ausente.

   - O parâmetro **TTL** está ausente.

- **404 (não encontrado)**: a ID do tíquete não está vinculada a uma janela de manutenção aberta.

- **500 (erro interno do servidor)**: qualquer outro erro inesperado.

> [!NOTE]
> Verifique se a ID do tíquete está vinculada a uma janela aberta existente.

#### <a name="method---delete"></a>Método-excluir

Fecha uma janela de manutenção existente.

#### <a name="query-parameters"></a>Parâmetros de consulta

- **TicketID**: registra a ID do tíquete de manutenção nos sistemas do usuário.

#### <a name="error-code"></a>Código do erro

- **200 (OK)**: a ação foi concluída com êxito.

- **400 (solicitação inadequada)**: o parâmetro **TicketID** está ausente.

- **404 (não encontrado)**: a ID do tíquete não está vinculada a uma janela de manutenção aberta.

- **500 (erro interno do servidor)**: qualquer outro erro inesperado.

> [!NOTE]
> Verifique se a ID do tíquete está vinculada a uma janela aberta existente.

#### <a name="method---get"></a>Método-obter

Recuperar um log de todas as ações de abrir, fechar e atualizar que foram executadas no sistema durante a manutenção. Você pode recuperar um log somente para janelas de manutenção que estavam ativas no passado e que foram fechadas.

#### <a name="query-parameters"></a>Parâmetros de consulta

- **fromDate**: filtra os logs da data predefinida e mais tarde. O formato é 2019-12-30.

- **acumulado: filtra** os logs até a data predefinida. O formato é 2019-12-30.

- **TicketID**: filtra os logs relacionados a uma ID de tíquete específica.

- **tokenname**: filtra os logs relacionados a um nome de token específico.

#### <a name="error-code"></a>Código do erro

- **200 (OK)**: a ação foi concluída com êxito.

- **400 (solicitação incorreta)**: o formato de data está incorreto.

- **204 (sem conteúdo)**: não há dados a serem mostrados.

- **500 (erro interno do servidor)**: qualquer outro erro inesperado.

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Matriz de objetos JSON que representam operações de janela de manutenção.

#### <a name="response-structure"></a>Estrutura de resposta

| Nome | Type | Comentário | Nullable |
|--|--|--|--|
| **dateTime** | String | Exemplo: "2012-04-23T18:25:43.511 Z" | não |
| **TicketID** | String | Exemplo: "9a5fe99c-D914-4bda-9332-307384fe40bf" | não |
| **tokenname** | String | - | não |
| **mecanismo** | Matriz de cadeia de caracteres | - | sim |
| **sensorIds** | Matriz de cadeia de caracteres | - | sim |
| **sub-redes** | Matriz de cadeia de caracteres | - | sim |
| **ttl** | Numérico | - | sim |
| **operationType** | String | Os valores são "OPEN", "UPDATE" e "CLOSE" | não |

#### <a name="curl-command"></a>Comando de cURL

| Type | APIs | Exemplo |
|--|--|--|
| POST | enlaçada-k-X POST-d ' {"TicketID": "<TICKET_ID>", TTL ": <TIME_TO_LIVE>," Engines ": [<ENGINE1, ENGINE2... ENGINEn>], "sensorIds": [<SENSOR_ID1, SENSOR_ID2... SENSOR_IDn>], "sub-redes": [<SUBNET1, SUBNET2.... SUB-rede>]} '-H "autorização: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | enlaçada-k-X POST-d ' {"TicketID": "a5fe99c-D914-4bda-9332-307384fe40bf", "TTL": "20", "Engines": ["ANOMALIAy"], "sensorIds": ["5", "3"], "sub-redes": ["10.0.0.3"]} '-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| PUT | ondulação-k-X PUT-d ' {"bilheteid": "<TICKET_ID>", TTL ":" <TIME_TO_LIVE> "} '-H" autorização: <AUTH_TOKEN> "https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | enlaçada-k-X PUT-d ' {"bilheteid": "a5fe99c-D914-4bda-9332-307384fe40bf", "TTL": "20"} '-H "autorização: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| Delete (excluir) | ondulação-k-X DELETE-d ' {"bilheteid": "<TICKET_ID>"} '-H "autorização: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | ondulação-k-X DELETE-d ' {"tgtid": "a5fe99c-D914-4bda-9332-307384fe40bf"} '-H "autorização: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| GET | enlaçada-k-H "autorização: <AUTH_TOKEN>" "https://<IP_ADDRESS>/external/v1/maintenanceWindow? fromDate =&=&tgtid =&tokenname = ' | ondulação-k-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/external/v1/maintenanceWindow? fromDate = 2020-01-01&-out = 2020-07-14&TicketID = a5fe99c-D914-4bda-9332-307384fe40bf&tokenname = a ' |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>Autenticar credenciais de usuário-/external/Authentication/Validation

Use esta API para validar as credenciais do usuário. Todas as funções de usuário do defender para IoT podem trabalhar com a API. Você não precisa de um token de acesso do defender para IoT para usar essa API.

#### <a name="method"></a>Método

**POST**

#### <a name="request-type"></a>Tipo de solicitação

**JSON**

#### <a name="request-example"></a>Exemplo de solicitação

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Cadeia de caracteres de mensagem com os detalhes de status da operação:

- **Êxito – msg**: autenticação bem-sucedida

- **Falha – erro**: falha na validação de credenciais

#### <a name="device-fields"></a>Campos de dispositivo

| **Nome** | **Tipo** | **Permite valor nulo** |
|--|--|--|
| **username** | String | Não |
| **password** | String | Não |

#### <a name="response-example"></a>Exemplo de resposta

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Comando de cURL

| Type | APIs | Exemplo |
|--|--|--|
| POST | enlaçada-k-d ' {"username": "<USER_NAME>", "password": "PASSWORD"} ' ' https://<IP_ADDRESS>/external/Authentication/Validation ' | enlaçada-k-d ' {"username": "myuser", "password": " 1234@abcd "} ' ' https:/ <span> /127.0.0.1/external/Authentication/Validation ' |

### <a name="change-password---externalauthenticationset_password"></a>Alterar senha-/external/Authentication/set_password

Use essa API para permitir que os usuários alterem suas próprias senhas. Todas as funções de usuário do defender para IoT podem trabalhar com a API. Você não precisa de um token de acesso do defender para IoT para usar essa API.

#### <a name="method"></a>Método

**POST**

#### <a name="request-type"></a>Tipo de solicitação

**JSON**

#### <a name="request-example"></a>Exemplo de solicitação

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Cadeia de caracteres de mensagem com os detalhes de status da operação:

- **Êxito – msg: a** senha foi substituída

- **Falha – erro**: falha na autenticação do usuário

- **Falha – erro: a** senha não corresponde à política de segurança

#### <a name="response-example"></a>Exemplo de resposta

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Campos de dispositivo

| **Nome** | **Tipo** | **Permite valor nulo** |
|--|--|--|
| **username** | String | Não |
| **password** | String | Não |
| **new_password** | String | Não |

#### <a name="curl-command"></a>Comando de cURL

| Type | APIs | Exemplo |
|--|--|--|
| POST | enlaçada-k-d ' {"username": "<USER_NAME>", "password": "<CURRENT_PASSWORD>", "new_password": "<NEW_PASSWORD>"} '-H ' Content-Type: Application/JSON ' https://<IP_ADDRESS>/external/Authentication/set_password | enlaçada-k-d ' {"username": "myuser", "senha": " 1234@abcd ", "new_password": " abcd@1234 "} '-H ' Content-Type: Application/JSON ' https:/ <span> /127.0.0.1/external/Authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Atualização de senha do usuário por administrador do sistema-/external/Authentication/set_password_by_admin

Use essa API para permitir que os administradores de sistema alterem senhas para usuários especificados. As funções de usuário do administrador do defender para IoT podem trabalhar com a API. Você não precisa de um token de acesso do defender para IoT para usar essa API.

#### <a name="method"></a>Método

**POST**

#### <a name="request-type"></a>Tipo de solicitação

**JSON**

#### <a name="request-example"></a>Exemplo de solicitação

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo da resposta

Cadeia de caracteres de mensagem com os detalhes de status da operação:

- **Êxito – msg: a** senha foi substituída

- **Falha – erro**: falha na autenticação do usuário

- **Falha – erro**: o usuário não existe

- **Falha – erro: a** senha não corresponde à política de segurança

- **Falha – erro**: o usuário não tem as permissões para alterar a senha

#### <a name="response-example"></a>Exemplo de resposta

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Campos de dispositivo

| **Nome** | **Tipo** | **Permite valor nulo** |
|--|--|--|
| **admin_username** | String | Não |
| **admin_password** | String | Não |
| **username** | String | Não |
| **new_password** | String | Não |

#### <a name="curl-command"></a>Comando de cURL

> [!div class="mx-tdBreakAll"]
> | Type | APIs | Exemplo |
> |--|--|--|
> | POST | enlaçada-k-d ' {"admin_username": "<ADMIN_USERNAME>", "admin_password": "<ADMIN_PASSWORD>", "username": "<USER_NAME>", "new_password": "<NEW_PASSWORD>"} '-H ' Content-Type: Application/JSON ' https://<IP_ADDRESS>/external/Authentication/set_password_by_admin | enlaçada-k-d ' {"admin_user": "adminUser", "admin_password": " 1234@abcd ", "username": "myuser", "new_password": " abcd@1234 "} '-H ' Content-Type: Application/JSON ' https:/ <span> /127.0.0.1/external/Authentication/set_password_by_admin |

## <a name="next-steps"></a>Próximas etapas

[Investigar as detecções do sensor em um inventário de dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md)

[Investigar todas as detecções do sensor corporativo em um inventário de dispositivos](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
