---
title: Preparar seus ativos técnicos do módulo IoT Edge-Azure Marketplace
description: Saiba mais sobre os requisitos técnicos e de configuração que seus ativos técnicos do módulo de borda do Internet das Coisas (IoT) devem atender antes que você possa publicá-los no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/03/2020
ms.openlocfilehash: aca27b89a3b92b410fa560c8b4bd7eb3d4e0a935
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93346767"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Prepare seus recursos técnicos do módulo IoT Edge

Este artigo descreve os requisitos que seus ativos técnicos do módulo de borda do Internet das Coisas (IoT) devem atender antes de serem publicados no Azure Marketplace.

## <a name="get-started"></a>Introdução

Um módulo IoT Edge é um contêiner compatível com o Docker que é executado em um dispositivo IoT Edge.

- Para saber mais sobre os módulos do IoT Edge, confira [Entender os módulos do Azure IoT Edge](../../iot-edge/iot-edge-modules.md).
- Para começar a usar seu IoT Edge desenvolvimento de módulo, consulte [desenvolver seus próprios módulos de IOT Edge](../../iot-edge/module-development.md).

## <a name="technical-requirements"></a>Requisitos técnicos

Seu módulo de IoT Edge deve atender aos seguintes requisitos técnicos para ser certificado e publicado no Azure Marketplace.

### <a name="platform-support"></a>Suporte a plataforma

Seu módulo de IoT Edge deve oferecer suporte a uma das seguintes opções de plataforma:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Plataformas de nível 1 suportadas pelo IoT Edge

Seu módulo deve dar suporte a todas as plataformas de camada 1 com suporte pelo IoT Edge (conforme registrado em [suporte a Azure IOT Edge](../../iot-edge/support.md)). Recomendamos essa opção porque fornece uma melhor experiência do cliente. Os módulos que atendem a esses critérios serão demonstrados. Um módulo usando esta opção de plataforma deve:

- Forneça uma marca mais recente e uma marca de versão (por exemplo, 1.0.1) que são marcas de manifesto criadas com a [ferramenta de manifesto do GitHub](https://github.com/estesp/manifest-tool).

- Use a guia listagem de ofertas no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) para adicionar um link na seção **links úteis** para o [Catálogo de dispositivos Azure IOT Edge certificado](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Um subconjunto das plataformas de nível 1 com suporte do IoT Edge

Seu módulo deve dar suporte a um subconjunto (pelo menos um) de plataformas de camada 1 com suporte pelo IoT Edge (conforme registrado no [suporte a Azure IOT Edge](../../iot-edge/support.md)). Um módulo usando esta opção de plataforma deve:

- Forneça uma marca mais recente e uma marca de versão (por exemplo, 1.0.1) que são marcas de manifesto criadas com a [ferramenta de manifesto](https://github.com/estesp/manifest-tool) do GitHub se houver suporte para mais de uma plataforma. Tags de manifesto são opcionais somente quando uma plataforma é suportada
- Use a guia listagem de ofertas no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) para adicionar um link na seção **links úteis** para pelo menos um dispositivo IOT Edge do [Catálogo de dispositivos certificados Azure IOT Edge](https://catalog.azureiotsolutions.com/).

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Esta é uma imagem da seção de listagem de ofertas no Partner Center":::

### <a name="device-dimensions"></a>Device dimensions

IoT Edge dimensões de módulo (como CPU, RAM, armazenamento e GPU) nos dispositivos de IoT Edge de destino devem atender aos seguintes requisitos:

- O módulo deve trabalhar com pelo menos um dispositivo IoT Edge do [Catálogo de dispositivos certificados Azure IOT Edge](https://catalog.azureiotsolutions.com/).

- Os requisitos mínimos de hardware devem ser documentados como o último parágrafo na descrição da oferta (na guia listagem de ofertas no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace)). Opcionalmente, você também pode listar os requisitos de hardware recomendados se eles forem significativamente diferentes. Por exemplo, adicione a seguinte seção no final da descrição da sua oferta:

Copie esse texto HTML ou use as funções de Rich Text correspondentes na janela de edição.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Configuração

Seu módulo deve incluir definições de configuração padrão para tornar a implantação em um dispositivo IoT Edge o mais simples possível. Essas informações podem ser fornecidas na página de **configuração técnica** do plano no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace). O contêiner também pode incluir o SDK do módulo IoT Edge para habilitar a comunicação com o Hub do Edge e o Hub IoT.

#### <a name="default-configuration"></a>Configuração padrão

IoT Edge módulos devem ser capazes de iniciar com as configurações padrão fornecidas na página de **configuração técnica** do plano no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace). As seguintes configurações padrão estão disponíveis:

- **Rotas** padrão
- **Propriedades desejadas do módulo padrão de entrelaçamento**
- **Variáveis de ambiente** padrão
- **Opções de criação de contêiner** padrão

Em um cenário em que um parâmetro necessário para um valor padrão não faz sentido (por exemplo, o endereço IP do servidor de um cliente), adicione um parâmetro como o valor padrão. Esse valor é maiúscula e colocado entre colchetes. Para este exemplo, você configuraria a seguinte variável de ambiente padrão:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Documentação de configuração

Todas as definições de configuração de um módulo IoT Edge devem ser claramente documentadas. Por exemplo, você deve documentar como usar suas rotas, propriedades desejadas de entrelaçamento, variáveis de ambiente, criaroptions e assim por diante. Você deve fornecer um link para sua documentação ou torná-lo parte de sua oferta ou descrição do plano. Você pode fornecer essas informações na página **lista de ofertas** e **lista de planos** no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace).

#### <a name="tags-and-versioning"></a>Marcas e controle de versão

Os clientes devem ser capazes de implantar um módulo com facilidade e obter atualizações automaticamente do Marketplace (em um cenário de desenvolvedor). Eles também devem ser capazes de usar e congelar uma versão exata testada (em um cenário de produção).

Para atender a essas expectativas do cliente e publicá-las no Marketplace, os módulos do IoT Edge devem atender aos seguintes requisitos

- Inclua uma marca de manifesto mais recente que aponte para a versão mais recente em todas as plataformas com suporte.
- Faça marcas de versão no formato X. Y. Z, em que X, Y e Z são inteiros.
- Inclua uma marca "Version", como 1.0.1, que aponta para uma versão específica em todas as plataformas com suporte.
- Não atualize as marcas de "versão", como 1.0.1, porque elas não devem ser alteradas.

> [!NOTE]
> Opcionalmente, o controle de versão pode incluir marcas de "versão sem interrupção", como 2,0 e 1,0. Isso suporta a manutenção de várias versões principais em paralelo.

### <a name="telemetry"></a>Telemetria

Os módulos que usam o SDK do módulo IoT devem definir o identificador de módulo exclusivo para PublisherID. OfferId. SkuId para fins de telemetria. Um identificador exclusivo ajuda o Azure Marketplace a identificar o número de instâncias de módulo em execução.

Use um dos seguintes métodos dos SDKs do módulo IoT para definir o ProductInfo para esse identificador:

- [C#](/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](/java/api/com.microsoft.azure.sdk.iot.device.productinfo)

Para módulos que não usam o SDK do módulo IoT, as informações menos precisas estão disponíveis por meio do Partner Center, como o número de downloads.

### <a name="security"></a>Segurança

Os módulos de IoT Edge devem evitar [módulos com privilégios](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities). Em vez disso, solicite o acesso menos privilegiado ao host o mais possível.

### <a name="module-iot-sdk"></a>SDK de IoT de módulo

Incluir o SDK do Módulo IoT não é um pré-requisito para a certificação. No entanto, incluir o SDK do Módulo do IoT pode fornecer uma melhor experiência ao usuário. Por exemplo, para dar suporte ao roteamento ou ao enviar mensagens para a nuvem.

O SDK do módulo IoT é necessário para obter dados de telemetria sobre o número de instâncias de módulo em execução.

## <a name="recertification-process"></a>Processo recertificação

Os parceiros são notificados sempre que há uma alteração significativa que afeta seus módulos, como:

- Matriz de suporte de so/Arch da camada 1 com suporte pelo IoT Edge
- Módulo do IoT do SDK
- runtime do IoT Edge
- Diretrizes de certificação de módulo IoT Edge

Os parceiros devem atualizar e recertificar suas ofertas, publicando-as novamente no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace).

Sua oferta também será rescertificada se você atualizá-la, como adicionar novas marcas de imagem.

## <a name="host-module-in-azure-container-registry"></a>Módulo host no registro de contêiner do Azure

Para carregar o módulo IoT Edge no Azure Marketplace, primeiro você precisa hospedá-lo em um ACR ( [registro de contêiner do Azure](https://azure.microsoft.com/services/container-registry/) ). O módulo deve incluir todas as marcas que você deseja publicar, incluindo as marcas de imagem que são referenciadas por uma marca de manifesto. Para obter mais informações, consulte o tutorial [criar um registro de contêiner do Azure e enviar uma imagem de contêiner por push](../../container-instances/container-instances-tutorial-prepare-acr.md).

## <a name="next-steps"></a>Próximas etapas

- [Criar uma oferta de módulo do IoT Edge](azure-iot-edge-module-creation.md)