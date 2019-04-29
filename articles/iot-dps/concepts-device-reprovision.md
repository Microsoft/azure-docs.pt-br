---
title: Conceitos de dispositivo para reprovisionamento para o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure | Microsoft Docs
description: Descreve os conceitos de reprovisionamento de dispositivo para o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: fa8cb29f145c7658227f93d08a990c98563a0cfc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729934"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Conceitos de reprovisionamento de dispositivos no Hub IoT

Durante o ciclo de vida de uma solução IoT, é comum mover dispositivos entre os Hubs IoT. Os motivos para essa mudança podem incluir os seguintes cenários:

* **Localização geográfica / GeoLatency**: Enquanto um dispositivo se movem entre locais, a latência de rede é aprimorada fazendo com que o dispositivo migrado para um hub IoT mais próximo.

* **Multilocação**: Um dispositivo pode ser usado dentro da mesma solução de IoT e reatribuído a um novo cliente ou site do cliente. Esse novo cliente pode ser atendido usando um Hub IoT diferente.

* **Alteração de solução**: Um dispositivo pode ser movido para uma solução de IoT nova ou atualizada. Essa reatribuição pode exigir que o dispositivo se comunique com um novo Hub IoT que está conectado a outros componentes de back-end.

* **Quarentena**: Semelhante a uma alteração de solução. Um dispositivo que está com defeito, comprometido ou desatualizado pode ser reatribuído a um Hub IoT em que tudo o que ele pode fazer é atualizar e voltar em conformidade. Depois que o dispositivo estiver funcionando corretamente, ele é migrado de volta a seu hub principal.

O reprovisionamento oferece suporte dentro dos endereços de Serviço de Provisionamento de Dispositivo a essas necessidades. Os dispositivos podem ser reatribuídos automaticamente para novos hubs IoT com base na política de reprovisionamento configurada na entrada de registro do dispositivo.

## <a name="device-state-data"></a>Dados de estado do dispositivo

Os dados de estado do dispositivo são compostos pelo [dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md) e recursos do dispositivo. Esses dados são armazenados na instância do Serviço de Provisionamento de Dispositivos e Hub IoT atribuídos a um dispositivo.

![Provisionar com o Serviço de Provisionamento de Dispositivos](./media/concepts-device-reprovisioning/dps-provisioning.png)

Quando um dispositivo for inicialmente provisionado com uma instância do Serviço de Provisionamento de Dispositivos, as etapas a seguir são executadas:

1. O dispositivo envia uma solicitação de provisionamento para a instância do Serviço de Provisionamento de Dispositivos. A instância de serviço autentica a identidade do dispositivo com base em uma entrada de registro e cria a configuração inicial dos dados de estado do dispositivo. A instância de serviço atribui o dispositivo a um hub IoT com base na configuração do registro e retorna essa atribuição de hub IoT para o dispositivo.

2. A instância do serviço de provisionamento dá uma cópia de quaisquer dados de estado inicial do dispositivo ao hub IoT atribuído. O dispositivo se conecta ao hub IoT atribuído e inicia as operações.

Ao longo do tempo, os dados de estado do dispositivo no hub IoT podem ser atualizados pelas [operações de dispositivo](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) e [operações de back-end](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). As informações de estado inicial do dispositivo armazenadas na instância do Serviço de Provisionamento de Dispositivo ficam inalteradas. Esses dados de estado do dispositivo inalterados são a configuração inicial.

![Provisionar com o Serviço de Provisionamento de Dispositivos](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

Dependendo do cenário, uma vez que um dispositivo se move entre hubs IoT, também pode ser necessário migrar o estado do dispositivo atualizado no hub IoT anterior para o novo hub IoT. Essa migração é suportada pelas políticas de reprovisionamento no Serviço de Provisionamento de Dispositivos.

## <a name="reprovisioning-policies"></a>Políticas de reprovisionamento

Dependendo do cenário, um dispositivo geralmente envia uma solicitação para uma instância de serviço de provisionamento na reinicialização. Ele também dá suporte a um método para disparar manualmente o provisionamento sob demanda. A política de reprovisionamento em uma entrada de registro determina como a instância de serviço de provisionamento de dispositivos lida com essas solicitações de provisionamento. A política também determina se os dados de estado do dispositivo devem ser migrados durante o reprovisionamento. As mesmas políticas estão disponíveis para os registros individuais e grupos de registro:

* **Provisionar novamente e migrar dados**: Essa política é o padrão para novas entradas de registro. Essa política entra em ação quando os dispositivos associados com a entrada de registro enviam uma nova solicitação (1). Dependendo da configuração de entrada de registro, o dispositivo pode ser reatribuído a outro hub IoT. Se o dispositivo está mudando entre hubs IoT, o registro de dispositivo com o hub IoT inicial será removido. As informações atualizadas de estado do dispositivo do hub IoT inicial serão migradas para o novo hub IoT (2). Durante a migração, o status do dispositivo será relatado como **atribuindo**.

    ![Provisionar com o Serviço de Provisionamento de Dispositivos](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Provisionar novamente e redefinir a configuração inicial**: Essa política entra em ação quando os dispositivos associados com a entrada de registro enviam uma nova solicitação de provisionamento (1). Dependendo da configuração de entrada de registro, o dispositivo pode ser reatribuído a outro hub IoT. Se o dispositivo está mudando entre hubs IoT, o registro de dispositivo com o hub IoT inicial será removido. Os dados de configuração inicial que a instância de serviço de provisionamento recebeu quando o dispositivo foi provisionado são fornecidos ao novo hub IoT (2). Durante a migração, o status do dispositivo será relatado como **atribuindo**.

    Essa política é geralmente usada para uma redefinição de fábrica sem alterar os hubs IoT.

    ![Provisionar com o Serviço de Provisionamento de Dispositivos](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nunca provisionar novamente**: O dispositivo nunca é reatribuído a um hub diferente. Essa política é fornecida para gerenciar compatibilidade com versões anteriores.

### <a name="managing-backwards-compatibility"></a>Gerenciar compatibilidade com versões anteriores

Antes de setembro de 2018, as atribuições de dispositivo para os hubs IoT tinham um comportamento adesivo. Quando um dispositivo passava novamente pelo processo de provisionamento, ele só seria atribuído de volta ao mesmo hub IoT.

Para soluções que obtiveram uma dependência nesse comportamento, o serviço de provisionamento inclui compatibilidade com versões anteriores. Atualmente, esse comportamento é mantido para dispositivos de acordo com os seguintes critérios:

1. Os dispositivos se conectam com uma versão de API antes da disponibilidade de suporte nativo de reprovisionamento no Serviço de Provisionamento de Dispositivos. Consulte a tabela de API abaixo.

2. A entrada de registro para os dispositivos não tem uma política de reprovisionamento definida neles.

Essa compatibilidade garante que os dispositivos implantados anteriormente, experimentem o mesmo comportamento que estava presente durante o teste inicial. Para preservar o comportamento anterior, não salve uma política de reprovisionamento para esses registros. Se uma política de reprovisionamento for definida, a política de reprovisionamento terá precedência sobre o comportamento. Ao permitir que a política de reprovisionamento tenha precedência, os clientes poderão atualizar o comportamento do dispositivo sem precisar refazer a imagem de dispositivo.

O fluxograma a seguir ajuda a mostrar quando o comportamento está presente:

![fluxograma de compatibilidade com versões anteriores](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

A tabela a seguir mostra as versões de API antes da disponibilidade de suporte nativo de reprovisionamento no Serviço de Provisionamento de Dispositivos:

| API REST | SDK C | SDK do Python |  SDK do Node | Java SDK | SDK .NET |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 e anterior](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 e anterior](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 e anterior](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 ou anterior](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 ou anterior](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 ou anterior](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Esses valores e links provavelmente mudarão. Isso é apenas uma tentativa de marcação para determinar onde as versões podem ser determinadas por um cliente e quais serão as versões esperadas.

## <a name="next-steps"></a>Próximas etapas

* [Como reprovisionar dispositivos](how-to-reprovision.md)
