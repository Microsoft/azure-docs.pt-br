---
title: Atualização de dispositivo para o manifesto de atualização do Hub IoT | Microsoft Docs
description: Saiba como as propriedades são enviadas do serviço de atualização de dispositivo para o dispositivo durante uma atualização
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: bbd39f7752a1a482350a7231a0bb5a3710591b1d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030674"
---
# <a name="device-update-for-iot-hub-update-manifest"></a>Atualização de dispositivo para o manifesto de atualização do Hub IoT

## <a name="overview"></a>Visão geral

A atualização de dispositivo para o Hub IoT usa um _manifesto de atualização_ para comunicar ações e também metadados que dão suporte a essas ações por meio das propriedades de interface [AzureDeviceUpdateCore. OrchestratorMetadata: 4](./device-update-plug-and-play.md).
Este documento descreve os conceitos básicos de como a `updateManifest` propriedade, na `AzureDeviceUpdateCore.OrchestratorMetadata:4` interface, é usada pelo agente de atualização de dispositivo. As `AzureDeviceUpdateCore.OrchestratorMetadata:4` Propriedades da interface são enviadas da atualização do dispositivo para o serviço Hub IOT para o agente de atualização de dispositivo. O `updateManifest` é um objeto JSON serializado que é analisado pelo agente de atualização de dispositivo.

### <a name="an-example-update-manifest"></a>Um exemplo de manifesto de atualização

```JSON
{
    "manifestVersion": "1",
    "updateId": {
        "provider": "DuTest",
        "name": "DuTestUser",
        "version": "2020.611.534.16"
    },
    "updateType": "microsoft/swupdate:1",
    "installedCriteria": "1.0",
    "files": {
        "00000": {
            "fileName": "image.swu",
            "sizeInBytes": 256000,
            "hashes": {
                "sha256": "IhIIxBJpLfazQOk/PVi6SzR7BM0jf4HDqw+6gdZ3vp8="
            }
        }
    },
    "createdDateTime": "2020-06-12T00:38:13.9350278"
}
```

A finalidade do manifesto de atualização é descrever o conteúdo de uma atualização, ou seja, sua identidade, tipo, critério instalado e atualizar os metadados do arquivo. Além disso, o manifesto de atualização é assinado criptograficamente para permitir que o agente de atualização de dispositivo Verifique sua autenticidade. Consulte a documentação de [segurança de atualização do dispositivo](./device-update-security.md) para obter mais informações sobre como o manifesto de atualização é usado para importar conteúdo com segurança.

## <a name="import-manifest-vs-update-manifest"></a>Importar manifesto vs. manifesto de atualização

É importante entender as diferenças entre o manifesto de importação e os conceitos de manifesto de atualização na atualização do dispositivo para o Hub IoT. 
* O [manifesto de importação](./import-concepts.md) é criado por quem cria a atualização correspondente. Ele descreve o conteúdo da atualização que será importada para a atualização do dispositivo para o Hub IoT. 
* O manifesto de atualização é gerado automaticamente pela atualização do dispositivo para o serviço do Hub IoT, usando algumas das propriedades que foram definidas no manifesto de importação. Ele é usado para comunicar informações relevantes para o agente de atualização de dispositivo durante o processo de atualização. 

Cada tipo de manifesto tem sua própria versão de esquema e esquema.

## <a name="update-manifest-properties"></a>Atualizar propriedades do manifesto

As definições de alto nível das propriedades do manifesto de atualização podem ser encontradas nas definições de interface encontradas [aqui](./device-update-plug-and-play.md). Para fornecer um contexto mais profundo, vamos examinar mais de perto as propriedades e como elas são usadas no sistema.

### <a name="updateid"></a>updateId

Contém o `provider` , `name` , e `version` , que representa a atualização exata do dispositivo para a identidade de atualização do Hub IOT usada para determinar os dispositivos compatíveis para a atualização.

### <a name="updatetype"></a>updateType

Representa o tipo de atualização que é manipulado por um tipo específico de manipulador de atualização. Ele segue a forma de `microsoft/swupdate:1` uma atualização baseada em imagem e `microsoft/apt:1` uma atualização baseada em pacote (consulte a `Update Handler Types` seção abaixo).

### <a name="installedcriteria"></a>installedCriteria

Uma cadeia de caracteres que contém as informações necessárias para o manipulador de atualização do agente de atualização de dispositivo para determinar se a atualização está instalada no dispositivo. A `Update Handler Types` seção documenta o formato do `installedCriteria` , para cada tipo de atualização com suporte da atualização do dispositivo para o Hub IOT.

### <a name="files"></a>files

Informa ao agente de atualização de dispositivo quais arquivos baixar e o hash que será usado para verificar se os arquivos foram baixados corretamente.
Veja uma análise mais detalhada do `files` conteúdo da propriedade:

```json
"files":{
        <FILE_ID_STRING>:{
            "fileName":<STRING>,
            "sizeInBytes":<INTEGER>,
            "hashes":{
                <HASH-TYPE>:<HASH-STRING>
            }
        }
    }
```

Fora do `updateManifest` é a `fileUrls` matriz do objeto JSON.

```json
"fileUrls":{
      <FILE_ID_STRING>: <URL-in-String-Format>
 }
```

O `FILE_ID_STRING` , o dentro do e o `fileUrls` `files` são os mesmos (por exemplo, "0000" no `files` tem a URL em "0000" em `fileUrls` ).

### <a name="manifestversion"></a>manifestVersion

Uma cadeia de caracteres que representa a versão do esquema.

## <a name="update-handler-types"></a>Atualizar tipos de manipulador

|Método Update|Tipo de manipulador de atualização|Tipo de atualização|Critérios instalados|Arquivos esperados para publicação|
|-------------|-------------------|----------|-----------------|--------------|
|Baseado em imagem|SWUpdate|"Microsoft/swupdate: Version"|A imagem de referência salva a dica de sua versão no arquivo/etc/Adu-Version.  |arquivo. SWU que contém a imagem SWUpdate|
|Baseado em pacote|APT|"Microsoft/apt: Version"|`<name>` + "-" + `<version>` (propriedades definidas no arquivo de manifesto apt|`<APT Update Manifest>`. JSON que contém a configuração de APT e a lista de pacotes|

