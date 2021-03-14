---
title: Importando atualizações na atualização do dispositivo para o Hub IoT-esquema e outras informações | Microsoft Docs
description: Esquema e outras informações relacionadas (incluindo objetos) que são usadas ao importar atualizações na atualização do dispositivo para o Hub IoT.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 13044b8f087b403f83516a32a490d2dee8db700f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054448"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>Importando atualizações na atualização do dispositivo para o Hub IoT-esquema e outras informações
Se você quiser importar uma atualização para a atualização de dispositivo para o Hub IoT, certifique-se de ter revisado os [conceitos](import-concepts.md) e o [Guia de instruções](import-update.md) primeiro. Se você estiver interessado nos detalhes do esquema usado ao construir um manifesto de importação, bem como informações sobre objetos relacionados, consulte abaixo.

## <a name="import-manifest-schema"></a>Importar esquema de manifesto

| Nome | Tipo | Descrição | Restrições |
| --------- | --------- | --------- | --------- |
| UpdateID | Objeto `UpdateId` | Atualizar identidade. |
| UpdateType | string | Tipo de atualização: <br/><br/> * Especifique `microsoft/apt:1` ao executar uma atualização baseada em pacote usando o agente de referência.<br/> * Especifique `microsoft/swupdate:1` ao executar uma atualização baseada em imagem usando o agente de referência.<br/> * Especifique `microsoft/simulator:1` ao usar o simulador de agente de exemplo.<br/> * Especifique um tipo personalizado se estiver desenvolvendo um agente personalizado. | Formato: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> Máximo de 32 caracteres no total |
| InstalledCriteria | string | Cadeia de caracteres interpretada pelo agente para determinar se a atualização foi aplicada com êxito:  <br/> * Especifique o **valor** de SWVersion para o tipo de atualização `microsoft/swupdate:1` .<br/> * Especifique `{name}-{version}` para o tipo de atualização `microsoft/apt:1` , de qual nome e versão são obtidos do arquivo apt.<br/> * Especifique o hash do arquivo de atualização para o tipo de atualização `microsoft/simulator:1` .<br/> * Especifique uma cadeia de caracteres personalizada se estiver desenvolvendo um agente personalizado.<br/> | Máximo de 64 caracteres |
| Compatibilidade | Matriz de `CompatibilityInfo` objetos | Informações de compatibilidade do dispositivo compatível com esta atualização. | Máximo de 10 itens |
| CreatedDateTime | data/hora | Data e hora em que a atualização foi criada. | Formato de data e hora ISO 8601 delimitado, em UTC |
| ManifestVersion | string | Importar versão do esquema de manifesto. Especifique `2.0` , que será compatível com `urn:azureiot:AzureDeviceUpdateCore:1` interface e `urn:azureiot:AzureDeviceUpdateCore:4` interface. | Precisa ser `2.0` |
| Arquivos | Matriz de `File` objetos | Atualizar arquivos de conteúdo | Máximo de 5 arquivos |

## <a name="updateid-object"></a>Objeto UpdateId

| Nome | Tipo | Descrição | Restrições |
| --------- | --------- | --------- | --------- |
| Provedor | string | Parte do provedor da identidade da atualização. | 1-64 caracteres, alfanuméricos, pontos e traço. |
| Nome | cadeia de caracteres | Nome parte da identidade da atualização. | 1-64 caracteres, alfanuméricos, pontos e traço. |
| Versão | version | Parte da versão da identidade da atualização. | 2 a 4, parte, número de versão separados por ponto entre 0 e 2147483647. Os zeros à esquerda serão descartados. |

## <a name="file-object"></a>Objeto de arquivo

| Nome | Tipo | Descrição | Restrições |
| --------- | --------- | --------- | --------- |
| Nome de arquivo | string | Nome do arquivo | Deve ser exclusivo em uma atualização |
| SizeInBytes | Int64 | Tamanho do arquivo em bytes. | Máximo de 800 MB por arquivo individual ou 800 MB coletivamente por atualização |
| Hashes | Objeto `Hashes` | Objeto JSON que contém hash (es) do arquivo |

## <a name="compatibilityinfo-object"></a>Objeto CompatibilityInfo

| Nome | Tipo | Descrição | Restrições |
| --- | --- | --- | --- |
| DeviceManufacturer | string | Fabricante do dispositivo ao qual a atualização é compatível. | 1-64 caracteres, alfanuméricos, pontos e traço. |
| DeviceModel | string | Modelo do dispositivo ao qual a atualização é compatível. | 1-64 caracteres, alfanuméricos, pontos e traço. |

## <a name="hashes-object"></a>Objeto hashes

| Name | Obrigatório | Type | Descrição |
| --------- | --------- | --------- | --------- |
| Sha256 | True | string | Hash codificado na base64 do arquivo usando o algoritmo SHA-256. |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [conceitos de importação](./import-concepts.md).

Se você estiver pronto, experimente o [Guia de importação How-To](./import-update.md), que o orientará durante o processo de importação passo a passo.
