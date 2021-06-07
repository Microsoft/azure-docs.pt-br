---
title: Dimensionamento do Hub IoT do Azure | Microsoft Docs
description: Como dimensionar o Hub IoT para dar suporte à taxa de transferência de mensagem antecipada e os recursos desejados. Inclui um resumo das taxas de transferência com suporte para cada camada e opções de fragmentação.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: Operations'
ms.openlocfilehash: cdb792198e6912fb0e68b2abec052c2b05166f17
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310114"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Escolher a camada certa do Hub IoT para a solução

Cada solução IoT é diferente, portanto, o Hub IoT oferece várias opções com base em preço e escala. Este artigo destina-se a ajudá-lo a avaliar suas necessidades de Hub IoT. Para obter informações sobre os preços das camadas do Hub IoT, consulte [Preços do Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub).

Para decidir qual camada do Hub IoT é ideal para a solução, faça duas perguntas:

**Quais recursos planejo usar?**

O Hub IoT oferece duas camadas, Básica e Standard, que diferem no número de recursos que podem dar suporte. Se a solução de IoT baseia-se na coleção de dados de dispositivos e na análise central, a camada básica provavelmente é a certa para você. Se você quiser usar configurações mais avançadas para controlar dispositivos IoT remotamente ou distribuir algumas de suas cargas de trabalho nos próprios dispositivos, considere a camada Standard. Para uma análise detalhada de quais recursos estão incluídos em cada camada, continue em [Camadas Básica e Standard](#basic-and-standard-tiers).

**Quantos dados planejo mover diariamente?**

Cada camada do Hub IoT está disponível em três tamanhos, com base na quantidade de taxa de transferência que pode ser manipulada em um determinado dia. Esses tamanhos são identificados numericamente como 1, 2 e 3. Por exemplo, cada unidade de um Hub IoT de nível 1 pode manipular 400 mil mensagens por dia, enquanto uma unidade de nível 3 pode lidar com 300 milhões. Para mais detalhes sobre as diretrizes de dados, continue com [Taxa de transferência da mensagem](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Camadas Básica e Standard

A camada Standard do Hub IoT permite todos os recursos e é necessária para qualquer solução de IoT que pretende usar os recursos de comunicação bidirecional. A camada Básica permite um subconjunto dos recursos e destina-se a soluções de IoT que precisam apenas de comunicação unidirecional dos dispositivos para a nuvem. Ambas as camadas oferecem os mesmos recursos de segurança e autenticação.

Somente um tipo de [edição](https://azure.microsoft.com/pricing/details/iot-hub/) em uma camada pode ser escolhido por Hub IoT. Por exemplo, é possível criar um Hub IoT com várias unidades do S1, mas não com uma combinação de unidades de edições diferentes, como S1 e S2.

| Recurso | Camada básica | Camada Gratuita/Standard |
| ---------- | ---------- | ------------- |
| [Telemetria de dispositivo para nuvem](iot-hub-devguide-messaging.md) | Sim | Sim |
| [Identidade por dispositivo](iot-hub-devguide-identity-registry.md) | Sim | Sim |
| [Roteamento de mensagens](iot-hub-devguide-messages-read-custom.md), [aprimoramentos de mensagens](iot-hub-message-enrichments-overview.md) e [integração da Grade de Eventos](iot-hub-event-grid.md) | Sim | Sim |
| [Protocolos HTTP, AMQP e MQTT](iot-hub-devguide-protocols.md) | Sim | Sim |
| [Serviço de provisionamento de dispositivos](../iot-dps/about-iot-dps.md) | Sim | Sim |
| [Monitoramento e diagnósticos](monitor-iot-hub.md) | Sim | Sim |
| [Mensagens de nuvem para dispositivo](iot-hub-devguide-c2d-guidance.md) |   | Sim |
| [Dispositivos gêmeos](iot-hub-devguide-device-twins.md), [Módulos gêmeos](iot-hub-devguide-module-twins.md) e [Gerenciamento de dispositivo](iot-hub-device-management-overview.md) |   | Sim |
| [Fluxos de dispositivo (versão prévia)](iot-hub-device-streams-overview.md) |   | Sim |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Sim |
| [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) |   | Sim |

O Hub IoT também oferece uma camada gratuita destinado a testes e avaliação. Ela tem todos os recursos da camada Standard, mas provisões limitadas de mensagens. Não é possível fazer upgrade da camada gratuita para Básica ou Standard.

## <a name="partitions"></a>Partições

Os Hubs IoT do Azure contêm muitos componentes principais de [Hubs de Eventos do Azure](../event-hubs/event-hubs-features.md), incluindo [Partições](../event-hubs/event-hubs-features.md#partitions). Os fluxos de eventos para Hubs IoT geralmente são preenchidos com dados de telemetria recebidos, relatados por vários dispositivos de IoT. O particionamento do fluxo de eventos é usado para reduzir as contenções que ocorrem ao ler e gravar simultaneamente em fluxos de eventos.

O limite de partição é escolhido quando o Hub IoT é criado e não pode ser alterado. O limite máximo de partição para o Hub IoT de camada básica e o Hub IoT de camada padrão é 32. A maioria dos Hubs IoT precisa apenas de 4 partições. Para obter mais informações sobre como determinar as partições, consulte as Perguntas frequentes sobre Hubs de Eventos [De quantas partições eu preciso?](../event-hubs/event-hubs-faq.yml#how-many-partitions-do-i-need-)

## <a name="tier-upgrade"></a>Upgrade de camada

Após criar o hub IoT, você poderá fazer upgrade da camada básica para a camada padrão sem interromper as operações existentes. Para obter mais informações, consulte [Como fazer upgrade do Hub IoT](iot-hub-upgrade.md).

A configuração da partição permanecerá inalterada quando você migrar da camada básica para a camada padrão.

> [!NOTE]
> A camada gratuita não dá suporte à atualização para a camada básica ou padrão.

## <a name="iot-hub-rest-apis"></a>APIs REST do Hub IoT

A diferença nos recursos com suporte entre as camadas Básica e Standard do Hub IoT significa que algumas chamadas de API não funcionam com hubs de camada Básica. A tabela a seguir mostra quais APIs estão disponíveis:

| API | Camada básica | Camada Gratuita/Standard |
| --- | ---------- | ------------- |
| [Excluir dispositivo](/javascript/api/azure-iot-digitaltwins-service/registrymanager#deletedevice-string--models-registrymanagerdeletedeviceoptionalparams-) | Sim | Sim |
| [Obter dispositivo](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-getdevice) | Sim | Sim |
| [Excluir módulo](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletemodule) | Sim | Sim |
| [Obter módulo](/java/api/com.microsoft.azure.sdk.iot.service.registrymanager.getmodule) | Sim | Sim |
| [Obter estatísticas de registro](/javascript/api/azure-iot-digitaltwins-service/registrymanager#getdevicestatistics-msrest-requestoptionsbase-) | Sim | Sim |
| [Obter estatísticas de serviços](/javascript/api/azure-iot-digitaltwins-service/registrymanager#getservicestatistics-msrest-requestoptionsbase-) | Sim | Sim |
| [Criar ou atualizar o dispositivo](/javascript/api/azure-iot-digitaltwins-service/registrymanager#createorupdatedevice-string--device--servicecallback-device--) | Sim | Sim |
| [Criar ou atualizar o módulo](/javascript/api/azure-iot-digitaltwins-service/registrymanager#createorupdatemodule-string--string--module--models-registrymanagercreateorupdatemoduleoptionalparams-) | Sim | Sim |
| [Consulta Hub IoT](/dotnet/api/microsoft.azure.devices.registrymanager) | Sim | Sim |
| [Criar URI de SAS de upload de arquivo](/rest/api/iothub/device/createfileuploadsasuri) | Sim | Sim |
| [Receber notificação de limite de dispositivo](/rest/api/iothub/device/receivedeviceboundnotification) | Sim | Sim |
| [Enviar evento de dispositivo](/rest/api/iothub/device/senddeviceevent) | Sim | Sim |
| Enviar eventos de módulo | AMQP e MQTT apenas | AMQP e MQTT apenas |
| [Atualizar o status de upload de arquivo](/rest/api/iothub/device/updatefileuploadstatus) | Sim | Sim |
| [Operação de dispositivo em massa](/javascript/api/azure-iot-digitaltwins-service/registrymanager#bulkdevicecrud-exportimportdevice----msrest-requestoptionsbase-) | Sim, exceto pelos recursos do IoT Edge | Sim |
| [Cancelar trabalho de importação exportação](/rest/api/iothub/service/jobs/cancelimportexportjob) | Sim | Sim |
| [Criar trabalho de importação/exportação](/rest/api/iothub/service/jobs/createimportexportjob) | Sim | Sim |
| [Obter trabalho de importação/exportação](/rest/api/iothub/service/jobs/getimportexportjob) | Sim | Sim |
| [Obter trabalhos de importação/exportação](/rest/api/iothub/service/jobs/getimportexportjobs) | Sim | Sim |
| [Limpar fila de comandos](/javascript/api/azure-iot-digitaltwins-service/registrymanager#purgecommandqueue-string--msrest-requestoptionsbase-) |   | Sim |
| [Obter dispositivo gêmeo](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin) |   | Sim |
| [Obter módulo gêmeo](/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-getmoduletwin) |   | Sim |
| [Invocar um método de dispositivo](./iot-hub-devguide-direct-methods.md) |   | Sim |
| [Atualizar dispositivo gêmeo](./iot-hub-devguide-device-twins.md) |   | Sim |
| [Atualizar módulo gêmeo](/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-updatemoduletwin) |   | Sim |
| [Abandonar notificação de limite de dispositivo](/rest/api/iothub/device/abandondeviceboundnotification) |   | Sim |
| [Completar notificação de limite de dispositivo](/rest/api/iothub/device/completedeviceboundnotification) |   | Sim |
| [Cancelar trabalho](/rest/api/media/jobs/canceljob) |   | Sim |
| [Criar trabalho](/rest/api/media/jobs/create) |   | Sim |
| [Obter Trabalho](/java/api/com.microsoft.azure.sdk.iot.service.jobs.jobclient.getjob) |   | Sim |
| [Trabalhos de consulta](/javascript/api/azure-iot-digitaltwins-service/jobclient#queryjobs-jobclientqueryjobsoptionalparams--servicecallback-queryresult--) |   | Sim |

## <a name="message-throughput"></a>Taxa de transferência da mensagem

A melhor maneira de dimensionar uma solução do Hub IoT é avaliar o tráfego de acordo com a unidade. Em particular, considere a taxa de transferência de pico necessária para as seguintes categorias de operações:

* Mensagens do dispositivo para a nuvem
* Mensagens da nuvem para o dispositivo
* Operações de registro de identidade

O tráfego é medido para o seu hub IoT por unidade. Ao criar um hub IoT, você escolhe a camada e a edição dele e define o número de unidades disponíveis. É possível comprar até 200 unidades para a edição B1, B2, S1 ou S2 ou até 10 unidades para a edição B3 ou S3. Depois que o Hub IoT for criado, será possível alterar o número de unidades disponíveis na edição, atualizar ou fazer downgrade entre as edições dentro da camada (B1 para B2) ou atualizar da camada básica para a padrão (B1 para S1) sem interromper as operações existentes. Para obter mais informações, consulte [Como fazer upgrade do Hub IoT](iot-hub-upgrade.md).  

Como um exemplo dos recursos de tráfego de cada camada, as mensagens de dispositivo para nuvem seguem estas diretrizes de taxa de transferência sustentada:

| Edição da camada | Taxa de transferência sustentada | Taxa de envio sustentada |
| --- | --- | --- |
| B1, S1 |Até 1111 KB/minuto por unidade<br/>(1,5 GB/dia/unidade) |Média de 278 mensagens/minuto por unidade<br/>(400.000 mensagens/dia por unidade) |
| B2, S2 |Até 16 MB/minuto por unidade<br/>(22,8 GB/dia/unidade) |Média de 4.167 mensagens/minuto por unidade<br/>(6 milhões de mensagens/dia por unidade) |
| B3, S3 |Até 814 MB/minuto por unidade<br/>(1144,4 GB/dia/unidade) |Média de 208,333 mensagens/minuto por unidade<br/>(300 milhões de mensagens/dia por unidade) |

A taxa de transferência de dispositivo para nuvem é apenas uma das métricas que precisam ser consideradas quando se cria uma solução de IoT. Para obter informações mais abrangentes, confira [Cotas e limitações do Hub IoT](iot-hub-devguide-quotas-throttling.md).

### <a name="identity-registry-operation-throughput"></a>Taxa de transferência de operações de registro de identidade

As operações de Registro de identidade do Hub IoT não devem ser operações em tempo de execução, pois estão relacionadas principalmente com provisionamento do dispositivo.

Consulte [Cotas e limites do Hub IoT](iot-hub-devguide-quotas-throttling.md)para obter números específicos de desempenho de intermitência.

## <a name="auto-scale"></a>Dimensionamento automático

Se você estiver se aproximando do limite permitido de mensagens no Hub IoT, poderá usar essas [etapas para dimensionar automaticamente](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) a fim de incrementar uma unidade de Hub IoT na mesma camada do Hub IoT.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre detalhes de desempenho e funcionalidades do Hub IoT, confira [Preços do Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub) ou [Cotas e limitações do Hub IoT](iot-hub-devguide-quotas-throttling.md).

* Para alterar a camada do Hub IoT, siga as etapas em [Fazer upgrade do Hub IoT](iot-hub-upgrade.md).