---
title: Como reprovisionar os dispositivos com o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure | Microsoft Docs
description: Como reprovisionar dispositivos com sua instância de serviço de provisionamento de dispositivos
author: wesmc7777
ms.author: v-yiso
origin.date: 04/04/2019
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 92680a453d93c8dc0189c6ae376449a8e7a22076
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627298"
---
# <a name="how-to-reprovision-devices"></a>Como reprovisionar dispositivos

Durante o ciclo de vida de uma solução IoT, é comum mover dispositivos entre os Hubs IoT. Os motivos para essa mudança podem incluir os seguintes cenários:

* **Localização geográfica**: Enquanto um dispositivo se movem entre locais, a latência de rede é aprimorada fazendo com que o dispositivo migrado para um hub IoT mais próximos a cada local.

* **Multilocação**: Um dispositivo pode ser usado dentro da mesma solução de IoT, mas, reatribuído ou concedido a um novo cliente ou site do cliente. Esse novo cliente pode ser atendido usando um Hub IoT diferente.

* **Alteração de solução**: Um dispositivo pode ser movido para uma solução de IoT nova ou atualizada. Essa reatribuição pode exigir que o dispositivo se comunique com um novo Hub IoT que está conectado a outros componentes de back-end. 

* **Quarentena**: Semelhante a uma alteração de solução. Um dispositivo que está com defeito, comprometido ou desatualizado pode ser reatribuído a um Hub IoT em que tudo o que ele pode fazer é atualizar e voltar em conformidade. Depois que o dispositivo estiver funcionando corretamente, ele é migrado de volta a seu hub principal.

Para mais uma visão mais detalhada de reprovisionamento, consulte [reprovisionamento conceitos de dispositivos no Hub IoT](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Configurar a política de alocação de registro

A política de alocação determina como os dispositivos associados com o registro serão alocados, ou atribuídos, a um Hub IoT após serem reprovisionados.

As seguintes etapas configuram a política de alocação para o registro do dispositivo:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até instância do Serviço de Provisionamento de Dispositivos.

2. Clique em **Gerenciar registros** e clique no grupo de registros ou no registro individual que você deseja configurar para o reprovisionamento. 

3. Em **Selecione como deseja atribuir dispositivos aos hubs**, selecione uma das seguintes políticas de alocação:

    * **Menor latência**: Essa política atribui dispositivos ao IoT Hub vinculado que resultará nas comunicações de latência mais baixas entre o dispositivo e IoT Hub. Essa opção permite que o dispositivo se comunique com o Hub IoT mais próximo no local. 
    
    * **Distribuição igualmente ponderada**: Essa política distribui os dispositivos em Hubs IoT vinculados com base no peso de alocação atribuído a cada hub IoT vinculado. Essa política permite que você faça o balanceamento de carga de dispositivos em um grupo de hubs vinculados com base nos pesos de alocação definidos nesses hubs. Se estiver provisionando dispositivos para apenas um Hub IoT, recomendamos essa configuração. Esta é a configuração padrão. 
    
    * **Configuração estática**: Essa política requer que um IoT Hub desejado estejam listados na entrada do registro para um dispositivo a ser provisionado. Essa política permite que você designe um único Hub IoT específico ao qual deseja atribuir dispositivos.

4. Em **Selecione os hubs IoT aos quais esse grupo pode ser atribuído**, selecione os Hubs IoT vinculados que você deseja incluir na sua política de alocação. Opcionalmente, adicione um novo Hub IoT vinculado usando o botão **Vincular um novo Hub IoT**.

    Com a política de alocação **Menor latência**, os hubs selecionados serão incluídos na avaliação de latência para determinar o hub mais próximo para a atribuição de dispositivo.

    Com a política de alocação **Distribuição uniformemente ponderada**, os dispositivos terão a carga balanceada entre os hubs selecionados com base nos pesos de alocação configurados deles e da carga do dispositivo atual.

    Com a política de alocação **Configuração estática**, selecione o Hub IoT ao qual deseja atribuir dispositivos.

4. Clique em **Salvar** ou vá para a próxima seção para definir a política de reprovisionamento.

    ![Selecionar a política de alocação de registro](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Definir a política de reprovisionamento

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até instância do Serviço de Provisionamento de Dispositivos.

2. Clique em **Gerenciar registros** e clique no grupo de registros ou no registro individual que você deseja configurar para o reprovisionamento.

3. Em **Selecione como quer que os dados do dispositivo sejam tratados no reprovisionamento para um Hub IoT diferente**, escolha uma das seguintes políticas de reprovisionamento:

    * **Provisionar novamente e migrar dados**: Essa política entra em ação quando os dispositivos associados com a entrada de registro enviar uma nova solicitação de provisionamento. Dependendo da configuração de entrada de registro, o dispositivo pode ser reatribuído a outro hub IoT. Se o dispositivo estiver mudando entre hubs IoT, o registro de dispositivo com o hub IoT inicial será removido. Todas as informações de estado do dispositivo do hub IoT inicial serão migradas para o novo Hub IoT. Durante a migração, o status do dispositivo será relatado como **Atribuindo**

    * **Provisionar novamente e redefinir a configuração inicial**: Essa política entra em ação quando os dispositivos associados com a entrada de registro enviar uma nova solicitação de provisionamento. Dependendo da configuração de entrada de registro, o dispositivo pode ser reatribuído a outro hub IoT. Se o dispositivo estiver mudando entre hubs IoT, o registro de dispositivo com o hub IoT inicial será removido. Os dados de configuração inicial que a instância de serviço de provisionamento recebeu quando o dispositivo foi provisionado são fornecidos ao novo Hub IoT. Durante a migração, o status do dispositivo será relatado como **Atribuindo**.

4. Clique em **Salvar** para habilitar o reprovisionamento do dispositivo com base em suas alterações.

    ![Selecionar a política de alocação de registro](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Enviar uma solicitação de provisionamento do dispositivo

Para que os dispositivos sejam reprovisionados com base nas alterações de configuração feitas nas seções anteriores, esses dispositivos devem solicitar o reprovisionamento. 

A frequência com que um dispositivo envia uma solicitação de provisionamento depende do cenário. No entanto, é recomendável programar os dispositivos para enviar uma solicitação de provisionamento para uma instância de serviço de provisionamento na reinicialização e dar suporte a um [método](../iot-hub/iot-hub-devguide-direct-methods.md) para acionar manualmente o provisionamento sob demanda. O provisionamento também pode ser disparado definindo uma [propriedade desejada](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

A política de reprovisionamento em uma entrada de registro determina como a instância de serviço de provisionamento de dispositivos lida com essas solicitações de provisionamento e se os dados de estado do dispositivo devem ser migrados durante o reprovisionamento. As mesmas políticas estão disponíveis para os registros individuais e grupos de registro:

Para encontrar um código de exemplo do envio de solicitações de provisionamento de um dispositivo durante uma sequência de reinicialização, confira [Provisionando automaticamente um dispositivo simulado](quick-create-simulated-device.md).


## <a name="next-steps"></a>Próximas etapas

- Para saber mais Reprovisioning, consulte [reprovisionamento conceitos de dispositivos no Hub IoT](concepts-device-reprovision.md) 
- Para saber mais desprovisionamento, consulte [como desprovisionar dispositivos que foram anteriormente autoprovisionado](how-to-unprovision-devices.md) 











