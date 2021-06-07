---
title: Recuperar logs de IoT Edge-Azure IoT Edge
description: IoT Edge a recuperação de log do módulo e o upload para o armazenamento de BLOBs do Azure.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/12/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: c06120d1a2e8aa6aa0c006c6f40fed6fab44c5b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200706"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>Recuperar logs de implantações IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Recupere logs de suas implantações IoT Edge sem precisar de acesso físico ou SSH ao dispositivo usando os métodos diretos incluídos no módulo IoT Edge Agent. Os métodos diretos são implementados no dispositivo e, em seguida, podem ser invocados na nuvem. O agente de IoT Edge inclui métodos diretos que ajudam a monitorar e gerenciar seus dispositivos de IoT Edge remotamente. Os métodos diretos discutidos neste artigo estão geralmente disponíveis com a versão 1.0.10.

Para obter mais informações sobre métodos diretos, como usá-los e como implementá-los em seus próprios módulos, consulte [entender e invocar métodos diretos do Hub IOT](../iot-hub/iot-hub-devguide-direct-methods.md).

Os nomes desses métodos diretos são tratados com diferenciação de maiúsculas e minúsculas.

## <a name="recommended-logging-format"></a>Formato de log recomendado

Embora não seja necessário, para obter a melhor compatibilidade com esse recurso, o formato de log recomendado é:

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Timestamp}` deve ser formatado como `yyyy-MM-dd hh:mm:ss.fff zzz` e `{Log Level}` deve seguir a tabela abaixo, que deriva seus níveis de severidade do código de [severidade no padrão de syslog](https://wikipedia.org/wiki/Syslog#Severity_level).

| Valor | Severidade |
|-|-|
| 0 | Emergência |
| 1 | Alerta |
| 2 | Crítico |
| 3 | Erro |
| 4 | Aviso |
| 5 | Informativo |
| 6 | Informativo |
| 7 | Depurar |

A [classe de agente no IOT Edge](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs) serve como uma implementação canônica.

## <a name="retrieve-module-logs"></a>Recuperar logs de módulo

Use o método direto **GetModuleLogs** para recuperar os logs de um módulo IOT Edge.

Esse método aceita uma carga JSON com o esquema a seguir:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Nome | Type | Descrição |
|-|-|-|
| schemaVersion | string | Definida como `1.0` |
| itens | Matriz JSON | Uma matriz com `id` e `filter` tuplas. |
| ID | string | Uma expressão regular que fornece o nome do módulo. Ele pode corresponder a vários módulos em um dispositivo de borda. O formato de [expressões regulares do .net](/dotnet/standard/base-types/regular-expressions) é esperado. |
| filter | Seção JSON | Filtros de log a serem aplicados aos módulos que correspondem à `id` expressão regular na tupla. |
| engloba | inteiro | Número de linhas de log no passado para recuperar a partir da versão mais recente. OPCIONAL. |
| since | string | Apenas retorne logs desde o momento, como uma duração (1 d, 90 m, 2 dias 3 horas 2 minutos), carimbo de data/hora do rfc3339 ou carimbo de data/hora do UNIX.  Se ambos `tail` e `since` forem especificados, os logs serão recuperados usando o `since` valor primeiro. Em seguida, o `tail` valor é aplicado ao resultado e o resultado final é retornado. OPCIONAL. |
| until | string | Só retorna logs antes da hora especificada, como um carimbo de data/hora rfc3339, um carimbo de data/hora do UNIX ou duração (1 d, 90 m, 2 dias 3 horas 2 minutos). OPCIONAL. |
| nível de log | inteiro | Filtrar linhas de log menores ou iguais ao nível de log especificado. As linhas de log devem seguir o formato de log recomendado e usar o padrão de [nível de severidade de syslog](https://en.wikipedia.org/wiki/Syslog#Severity_level) . OPCIONAL. |
| regex | string | Filtre as linhas de log que têm conteúdo que corresponde à expressão regular especificada usando o formato de [expressões regulares .net](/dotnet/standard/base-types/regular-expressions) . OPCIONAL. |
| codificando | string | `gzip` ou `none`. O padrão é `none`. |
| contentType | string | `json` ou `text`. O padrão é `text`. |

> [!NOTE]
> Se o conteúdo dos logs exceder o limite de tamanho de resposta de métodos diretos, que é, no momento, 128 KB, a resposta retornará um erro.

Uma recuperação bem-sucedida de logs retorna um **"status": 200** seguido por uma carga que contém os logs recuperados do módulo, filtrados pelas configurações que você especificar em sua solicitação.

Por exemplo:

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

Na portal do Azure, invoque o método com o nome do método `GetModuleLogs` e o seguinte conteúdo JSON:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Invocar o método direto ' GetModuleLogs ' no portal do Azure](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

Você também pode canalizar a saída da CLI para utilitários do Linux, como [gzip](https://en.wikipedia.org/wiki/Gzip), para processar uma resposta compactada. Por exemplo:

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>Carregar logs de módulo

Use o método direto **UploadModuleLogs** para enviar os logs solicitados para um contêiner de armazenamento de BLOBs do Azure especificado.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Se você quiser carregar logs de um dispositivo por trás de um dispositivo de gateway, será necessário ter os [módulos proxy de API e armazenamento de BLOBs](how-to-configure-api-proxy-module.md) configurados no dispositivo de camada superior. Esses módulos roteiam os logs do dispositivo de camada inferior por meio do dispositivo de gateway para o armazenamento na nuvem.

::: moniker-end

Esse método aceita um conteúdo JSON semelhante a **GetModuleLogs**, com a adição da chave "sasUrl":

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Nome | Type | Descrição |
|-|-|-|
| sasURL | Cadeia de caracteres (URI) | [URL de assinatura de acesso compartilhado com acesso de gravação ao contêiner de armazenamento de BLOBs do Azure](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer). |

Uma solicitação bem-sucedida para carregar logs retorna um **"status": 200** seguido por uma carga com o esquema a seguir:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Nome | Type | Descrição |
|-|-|-|
| status | string | Um dos `NotStarted` , `Running` , `Completed` , `Failed` ou `Unknown` . |
| message | string | Mensagem se houver erro, Cadeia de caracteres vazia, caso contrário. |
| correlationId | string   | ID para consultar o status da solicitação de upload. |

Por exemplo:

A seguinte invocação carrega as últimas 100 linhas de log de todos os módulos, em formato JSON compactado:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

A seguinte invocação carrega as últimas 100 linhas de log de edgeAgent e edgeHub com as últimas 1000 linhas de log do módulo tempSensor no formato de texto descompactado:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

Na portal do Azure, invoque o método com o nome do método `UploadModuleLogs` e o seguinte conteúdo JSON depois de preencher o sasURL com suas informações:

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Invocar o método direto ' UploadModuleLogs ' no portal do Azure](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>Carregar o diagnóstico do pacote de suporte

Use o método direto **UploadSupportBundle** para agrupar e carregar um arquivo zip de logs de módulo IOT Edge para um contêiner de armazenamento de BLOBs do Azure disponível. Esse método direto executa o [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) comando em seu dispositivo IOT Edge para obter os logs.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Se você quiser carregar logs de um dispositivo por trás de um dispositivo de gateway, será necessário ter os [módulos proxy de API e armazenamento de BLOBs](how-to-configure-api-proxy-module.md) configurados no dispositivo de camada superior. Esses módulos roteiam os logs do dispositivo de camada inferior por meio do dispositivo de gateway para o armazenamento na nuvem.

::: moniker-end

Esse método aceita uma carga JSON com o esquema a seguir:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| Nome | Type | Descrição |
|-|-|-|
| schemaVersion | string | Definida como `1.0` |
| sasURL | Cadeia de caracteres (URI) | [URL de assinatura de acesso compartilhado com acesso de gravação ao contêiner de armazenamento de BLOBs do Azure](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |
| since | string | Apenas retorne logs desde o momento, como uma duração (1 d, 90 m, 2 dias 3 horas 2 minutos), carimbo de data/hora do rfc3339 ou carimbo de data/hora do UNIX. OPCIONAL. |
| until | string | Só retorna logs antes da hora especificada, como um carimbo de data/hora rfc3339, um carimbo de data/hora do UNIX ou duração (1 d, 90 m, 2 dias 3 horas 2 minutos). OPCIONAL. |
| edgeRuntimeOnly | booleano | Se verdadeiro, apenas os logs de retorno do agente do Edge, Hub do Edge e o daemon de segurança do Edge. Padrão: falso.  OPCIONAL. |

> [!IMPORTANT]
> IoT Edge grupo de suporte pode conter informações de identificação pessoal.

Uma solicitação bem-sucedida para carregar logs retorna um **"status": 200** seguido por uma carga com o mesmo esquema que a resposta **UploadModuleLogs** :

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Nome | Type | Descrição |
|-|-|-|
| status | string | Um dos `NotStarted` , `Running` , `Completed` , `Failed` ou `Unknown` . |
| message | string | Mensagem se houver erro, Cadeia de caracteres vazia, caso contrário. |
| correlationId | string   | ID para consultar o status da solicitação de upload. |

Por exemplo:

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

Na portal do Azure, invoque o método com o nome do método `UploadSupportBundle` e o seguinte conteúdo JSON depois de preencher o sasURL com suas informações:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![Invocar o método direto ' UploadSupportBundle ' no portal do Azure](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>Obter status da solicitação de carregamento

Use o método direto **GetTaskStatus** para consultar o status de uma solicitação de logs de upload. A carga de solicitação **GetTaskStatus** usa o `correlationId` da solicitação carregar logs para obter o status da tarefa. O `correlationId` é retornado em resposta à chamada de método direto **UploadModuleLogs** .

Esse método aceita uma carga JSON com o esquema a seguir:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

Uma solicitação bem-sucedida para carregar logs retorna um **"status": 200** seguido por uma carga com o mesmo esquema que a resposta **UploadModuleLogs** :

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Nome | Type | Descrição |
|-|-|-|
| status | string | Um dos `NotStarted` , `Running` , `Completed` , `Failed` ou `Unknown` . |
| message | string | Mensagem se houver erro, Cadeia de caracteres vazia, caso contrário. |
| correlationId | string   | ID para consultar o status da solicitação de upload. |

Por exemplo:

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

Na portal do Azure, invoque o método com o nome do método `GetTaskStatus` e o seguinte conteúdo JSON depois de preencher o GUID com suas informações:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![Invocar o método direto ' GetTaskStatus ' no portal do Azure](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>Próximas etapas

[Propriedades do agente do IoT Edge e dos gêmeos de módulo do hub do IoT Edge](module-edgeagent-edgehub.md)
