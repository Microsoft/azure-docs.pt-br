---
title: Entenda como a atualização de dispositivo para o Hub IoT usa Plug and Play IoT | Microsoft Docs
description: A atualização de dispositivo para o Hub IoT usa o para descobrir e gerenciar dispositivos com capacidade de atualização por satélite.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0283a84650abaadd454b4f5bca83d1473e443fb8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561807"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>Atualização de dispositivo para o Hub IoT e Plug and Play de IoT

A atualização de dispositivo para o Hub IoT usa [plug and Play de IOT](../iot-pnp/index.yml) para descobrir e gerenciar dispositivos que têm capacidade de atualização por satélite. O serviço de atualização de dispositivo enviará e receberá Propriedades e mensagens de e para dispositivos usando interfaces PnP. A atualização de dispositivo para o Hub IoT requer que os dispositivos IoT implementem as interfaces e a ID de modelo a seguir, conforme descrito abaixo.

## <a name="adu-core-interface"></a>Interface principal do ADU

A interface ' ADUCoreInterface ' é usada para enviar ações de atualização e metadados para dispositivos e receber status de atualização de dispositivos. A interface ' ADU Core ' é dividida em duas propriedades de objeto.

O nome do componente esperado em seu modelo é **"azureDeviceUpdateAgent"** ao implementar essa interface. [Saiba mais sobre os componentes PnP do Azure IoT](../iot-pnp/concepts-components.md)

### <a name="agent-metadata"></a>Metadados do agente

Os metadados do agente contêm campos que o dispositivo ou o agente de atualização de dispositivo usa para enviar informações e status para os serviços de atualização de dispositivo.

|Name|Esquema|Direção|Descrição|Exemplo|
|----|------|---------|-----------|-----------|
|resultCode|inteiro|dispositivo para a nuvem|Um código que contém informações sobre o resultado da última ação de atualização. Pode ser preenchido para êxito ou falha e deve seguir a [especificação de código de status http](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).|500|
|extendedResultCode|inteiro|dispositivo para a nuvem|Um código que contém informações adicionais sobre o resultado. Pode ser preenchido para êxito ou falha.|0x80004005|
|state|inteiro|dispositivo para a nuvem|É um inteiro que indica o estado atual do agente de atualização de dispositivo. Confira abaixo para obter os detalhes |Ocioso|
|installedUpdateId|string|dispositivo para a nuvem|Uma ID da atualização instalada atualmente (por meio da atualização do dispositivo). Esse valor será NULL para um dispositivo que nunca tenha feito uma atualização por meio da atualização do dispositivo.|Nulo|
|`deviceProperties`|Map|dispositivo para a nuvem|O conjunto de propriedades que contêm o fabricante e o modelo.|Confira abaixo para obter os detalhes

#### <a name="state"></a>Estado

É o status relatado pelo agente de atualização de dispositivo depois de receber uma ação do serviço de atualização de dispositivo. `State` é relatado em resposta a um `Action` (veja `Actions` abaixo) enviado para o agente de atualização de dispositivo do serviço de atualização de dispositivo. Consulte o [fluxo de trabalho de visão geral](understand-device-update.md#device-update-agent) para solicitações que fluem entre o serviço de atualização de dispositivo e o agente de atualização de dispositivo.

|Name|Valor|Descrição|
|---------|-----|-----------|
|Ocioso|0|O dispositivo está pronto para receber uma ação do serviço de atualização de dispositivo. Após uma atualização bem-sucedida, o estado é retornado para o `Idle` estado.|
|DownloadSucceeded|2|Um download bem-sucedido.|
|InstallSucceeded|4|Uma instalação bem-sucedida.|
|Com falha|255|Ocorreu uma falha durante A atualização.|

#### <a name="device-properties"></a>Propriedades do Dispositivo

É o conjunto de propriedades que contêm o fabricante e o modelo.

|Name|Esquema|Direção|Description|
|----|------|---------|-----------|
|fabricante|string|dispositivo para a nuvem|O fabricante do dispositivo, relatado por meio do `deviceProperties` . Essa propriedade é lida de um dos dois locais-a interface ' AzureDeviceUpdateCore ' tentará primeiro ler o valor ' aduc_manufacturer ' do arquivo de [configuração](device-update-configuration-file.md) .  Se o valor não for preenchido no arquivo de configuração, o padrão será relatar a definição de tempo de compilação para ADUC_DEVICEPROPERTIES_MANUFACTURER. Essa propriedade só será relatada no momento da inicialização.|
|modelo|string|dispositivo para a nuvem|O modelo de dispositivo do dispositivo, relatado por meio de `deviceProperties` . Essa propriedade é lida de um dos dois locais-a interface AzureDeviceUpdateCore primeiro tentará ler o valor ' aduc_model ' do arquivo de [configuração](device-update-configuration-file.md) .  Se o valor não for preenchido no arquivo de configuração, o padrão será relatar a definição de tempo de compilação para ADUC_DEVICEPROPERTIES_MODEL. Essa propriedade só será relatada no momento da inicialização.|
|aduVer|string|dispositivo para a nuvem|Versão do agente de atualização de dispositivo em execução no dispositivo. Esse valor é lido somente no Build se durante a compilação ENABLE_ADU_TELEMETRY_REPORTING for definido como 1 (true). Os clientes podem optar por recusar a emissão de relatórios de versão definindo o valor como 0 (falso). [Como personalizar Propriedades do agente de atualização de dispositivo](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md).|
|doVer|string|dispositivo para a nuvem|Versão do agente de otimização de entrega em execução no dispositivo. O valor é lido somente do Build se, durante o tempo de compilação ENABLE_ADU_TELEMETRY_REPORTING for definido como 1 (true). Os clientes podem optar por recusar o relatório de versão definindo o valor como 0 (falso). [Como personalizar as propriedades do agente de otimização de entrega](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components).|

### <a name="service-metadata"></a>Metadados de serviço

Os metadados de serviço contêm campos que os serviços de atualização de dispositivo usam para comunicar ações e dados para o agente de atualização de dispositivo.

|Name|Esquema|Direção|Description|
|----|------|---------|-----------|
|ação|inteiro|nuvem para dispositivo|É um inteiro que corresponde a uma ação que o agente deve executar. Valores listados abaixo.|
|updateManifest|string|nuvem para dispositivo|Usado para descrever o conteúdo de uma atualização. Gerado a partir do [manifesto de importação](import-update.md#create-device-update-import-manifest)|
|updateManifestSignature|Objeto JSON|nuvem para dispositivo|Uma assinatura Web JSON (JWS) com chaves Web JSON usadas para verificação de origem.|
|fileUrls|Map|nuvem para dispositivo|Mapa de `FileHash` para `DownloadUri` . Informa ao agente, quais arquivos baixar e o hash a ser usado para verificar se os arquivos foram baixados corretamente.|

#### <a name="action"></a>Ação

`Actions` abaixo representa as ações executadas pelo agente de atualização de dispositivo, conforme instruído pelo serviço de atualização de dispositivo. O agente de atualização de dispositivo relatará um `State` (consulte `State` a seção acima) processando o `Action` recebido. Consulte o [fluxo de trabalho de visão geral](understand-device-update.md#device-update-agent) para solicitações que fluem entre o serviço de atualização de dispositivo e o agente de atualização de dispositivo.

|Name|Valor|Descrição|
|---------|-----|-----------|
|Baixar|0|Baixar conteúdo publicado ou atualizar e qualquer outro conteúdo necessário|
|Instalar|1|Instale o conteúdo ou a atualização. Normalmente, isso significa chamar o instalador para o conteúdo ou a atualização.|
|Aplicar|2|Finalize a atualização. Ele sinaliza o sistema para reinicializar, se necessário.|
|Cancelar|255|Pare de processar a ação atual e volte para `Idle` . Também será usado para informar o agente no `Failed` estado para voltar para `Idle` .|

## <a name="device-information-interface"></a>Interface de informações do dispositivo

A interface de informações do dispositivo é um conceito usado na [arquitetura de plug and Play de IOT](../iot-pnp/overview-iot-plug-and-play.md). Ele contém as propriedades de dispositivo para nuvem que fornecem informações sobre o hardware e o sistema operacional do dispositivo. A atualização de dispositivo para o Hub IoT usa as propriedades DeviceInformation. manufacturer e DeviceInformation. Model para telemetria e diagnóstico. Para saber mais sobre a interface de informações do dispositivo, consulte este [exemplo](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json).

O nome do componente esperado em seu modelo é **deviceInformation** ao implementar essa interface. [Saiba mais sobre os componentes PnP do Azure IoT](../iot-pnp/concepts-components.md)

|Nome|Tipo|Esquema|Direção|Descrição|Exemplo|
|----|----|------|---------|-----------|-----------|
|fabricante|Propriedade|string|dispositivo para a nuvem|Nome da empresa do fabricante do dispositivo. Isso pode ser igual ao nome do fabricante original do equipamento (OEM).|Contoso|
|modelo|Propriedade|string|dispositivo para a nuvem|Nome ou ID do modelo do dispositivo.|Dispositivo IoT Edge|
|swVersion|Propriedade|string|dispositivo para a nuvem|Versão do software em seu dispositivo. swVersion pode ser a versão do seu firmware.|4.15.0-122|
|osName|Propriedade|string|dispositivo para a nuvem|Nome do sistema operacional no dispositivo.|Ubuntu Server 18.04|
|processorArchitecture|Propriedade|string|dispositivo para a nuvem|Arquitetura do processador no dispositivo.|ARM64|
|processorManufacturer|Propriedade|string|dispositivo para a nuvem|Nome do fabricante do processador no dispositivo.|Microsoft|
|totalStorage|Propriedade|string|dispositivo para a nuvem|Total de armazenamento disponível no dispositivo, em kilobytes.|2.048|
|totalMemory|Propriedade|string|dispositivo para a nuvem|Memória total disponível no dispositivo, em kilobytes.|256|

## <a name="model-id"></a>ID do Modelo 

A ID do modelo é como os dispositivos inteligentes anunciam seus recursos para os aplicativos IoT do Azure com o plug-in de IoT e Play.To saiba mais sobre como criar dispositivos inteligentes para anunciar seus recursos aos aplicativos IoT do Azure visite o [Guia do desenvolvedor do dispositivo iot plug and Play](../iot-pnp/concepts-developer-guide-device.md).

A atualização de dispositivo para o Hub IoT exige que o dispositivo inteligente de IoT Plug and Play Anuncie uma ID de modelo com um valor de **"dtmi: AzureDeviceUpdate; 1"** como parte da conexão do dispositivo. [Saiba como anunciar uma ID de modelo](../iot-pnp/concepts-developer-guide-device.md#model-id-announcement).