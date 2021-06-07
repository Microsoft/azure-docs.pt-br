---
title: Gerenciamento automático de dispositivo em escala com o Hub IoT do Azure | Microsoft Docs
description: Usar configurações automáticas do Hub IoT do Azure para gerenciar vários dispositivos e módulos IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 0e017f4df413d6db528bb99756646859d9a74aea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92545388"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Gerenciamento automático de dispositivos e módulos IoT usando o portal do Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

O gerenciamento automático de dispositivo no Hub IoT do Azure automatiza muitas das tarefas repetitivas e complexas de gerenciamento de grande frotas de dispositivos. Com o gerenciamento automático de dispositivo, você pode direcionar um conjunto de dispositivos com base em suas propriedades, definir uma configuração desejada e permitir que o Hub IoT atualize dispositivos sempre que entrem no escopo. Essa atualização é feita usando uma _configuração automática de dispositivo_ ou _configuração automática de módulo_, que permite resumir a conclusão e a conformidade, tratar a mesclagem e os conflitos e implementar configurações em uma abordagem em fases.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

O gerenciamento automático de dispositivo trabalha atualizando um conjunto de dispositivos gêmeos ou módulos gêmeos com as propriedades desejadas e informando um resumo com base em propriedades reportadas por gêmeos.  Ele apresenta uma nova classe e um documento JSON denominado *Configuração* que tem três partes:

* A **condição de destino** define o escopo de dispositivos gêmeos ou módulos gêmeos a serem atualizados. A condição de destino é especificada como uma consulta em marcas gêmeas e/ou propriedades reportadas.

* O **conteúdo de destino** define as propriedades desejadas para serem adicionadas ou atualizadas nos dispositivos gêmeos ou módulos gêmeos de destino. O conteúdo inclui um caminho para a seção de propriedades desejadas a ser alterado.

* As **métricas** definem as contagens de resumos de vários estados de configuração como **êxito**, **em andamento**, e **erro**. Métricas personalizadas são especificadas como consultas nas propriedades reportadas do gêmeo.  As métricas do sistema são métricas padrão que medem o status de atualização do gêmeo, como o número de gêmeos definidos como destino e o número de gêmeos que foram atualizados com êxito.

As configurações automáticas são executadas pela primeira vez logo após a configuração ser criada e, em seguida, em intervalos de cinco minutos. As consultas de métricas são executadas cada vez que a configuração automática é executada.

## <a name="implement-twins"></a>Implementar gêmeos

As configurações automáticas de dispositivo requerem o uso de dispositivos gêmeos para sincronizar o estado entre a nuvem e os dispositivos.  Para saber mais, veja [Noções básicas e uso de dispositivos gêmeos no Hub IoT](iot-hub-devguide-device-twins.md).

As configurações automáticas de módulo requerem o uso de módulos gêmeos para sincronizar o estado entre a nuvem e os módulos. Para obter mais informações, confira [Noções básicas e uso de módulos gêmeos no Hub IoT](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Usar marcas para direcionar gêmeos

Antes de criar uma configuração, especifique quais dispositivos ou módulos você deseja afetar. O Hub IoT do Azure identifica dispositivos e por meio do uso de marcas no dispositivo gêmeo e identifica os módulos que usam marcas no módulo gêmeo. Cada dispositivo ou módulo pode ter várias marcas e você pode defini-las de qualquer modo que faça sentido para sua solução. Por exemplo, se você gerenciar dispositivos em locais diferentes, adicione as seguintes marcas a um dispositivo gêmeo:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Criar uma configuração

1. No [portal do Azure](https://portal.azure.com), vá para o hub IoT. 

2. Selecione **Configuração do dispositivo IoT**.

3. Selecione **Adicionar configuração de dispositivo** ou **Adicionar configuração de módulo**.

   ![Adicionar configuração de dispositivo ou configuração de módulo](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Há cinco etapas para criar uma configuração. As seções a seguir explicam cada uma delas. 

### <a name="name-and-label"></a>Nome e rótulo

1. Dê um nome exclusivo à sua configuração de até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.

2. Adicione rótulos para ajudar a acompanhar as configurações. Os rótulos são pares de **Nome** e **Valor** que descrevem a configuração. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`.

3. Selecione **Próximo** para ir para o próximo passo. 

### <a name="specify-settings"></a>Especifique as configurações

Esta seção define o conteúdo a ser definido nos dispositivos ou módulos gêmeos de destino. Há duas entradas para cada conjunto de configurações. A primeira é o caminho do gêmeo, que é o caminho para a seção JSON dentro das propriedades desejadas do gêmeo que será definido.  O segundo é o conteúdo do JSON a ser inserido nessa seção. 

Por exemplo, seria possível definir o caminho gêmeo como `properties.desired.chiller-water` e fornecer o seguinte conteúdo JSON: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Definir o caminho gêmeo e o conteúdo](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Também é possível definir configurações individuais especificando o caminho gêmeo inteiro e fornecendo o valor sem colchetes. Por exemplo, com o caminho gêmeo `properties.desired.chiller-water.temperature`, defina o conteúdo como `66`. Em seguida, crie uma configuração gêmea para a propriedade de pressão. 

Se duas ou mais configurações direcionarem o mesmo caminho gêmeo, o conteúdo da configuração de prioridade mais alta será aplicado (a prioridade é definida na etapa 4).

Se você desejar remover uma propriedade existente, especifique o valor da propriedade como `null`.

É possível adicionar mais configurações selecionando **Adicionar Configuração do Dispositivo Gêmeo** ou **Adicionar Configuração do Módulo Gêmeo**.

### <a name="specify-metrics-optional"></a>Especificar as métricas (opcionais)

As métricas fornecem contagens de resumo dos vários estados que um dispositivo ou módulo pode relatar após aplicar o conteúdo da configuração. Por exemplo, você pode criar uma métrica para alterações de configurações pendentes, uma métrica de erros e uma métrica para alterações de configurações bem-sucedidas.

Cada configuração pode ter até cinco métricas personalizadas. 

1. Insira um nome para **nome da métrica**.

2. Insira uma consulta para **Critérios da métrica**.  A consulta é baseada nas propriedades reportadas de dispositivo gêmeo.  A métrica representa o número de linhas retornadas pela consulta.

Por exemplo:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Você pode incluir uma cláusula na qual a configuração foi aplicada, por exemplo: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Se você estiver criando uma métrica para relatar os módulos configurados, selecione `moduleId` em `devices.modules`. Por exemplo:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Dispositivos de destino

Use a propriedade marcas dos gêmeos para direcionar os dispositivos ou módulos específicos que devem receber essa configuração. Também é possível direcionar propriedades reportadas de gêmeo.

As configurações automáticas de dispositivo só podem direcionar marcas do dispositivo gêmeo de destino e as configurações automáticas de módulo só podem ser direcionadas a marcas do módulo gêmeo. 

Como várias configurações podem direcionar o mesmo dispositivo ou módulo, cada uma delas precisa de um número de prioridade. Se houver um conflito, a configuração com a prioridade mais alta ganhará. 

1. Insira um inteiro positivo para a **Prioridade** da configuração. O maior valor numérico é considerado a prioridade mais alta. Se duas configurações tiverem o mesmo número de prioridade, aquela que foi criada mais recentemente ganhará. 

2. Insira uma **Condição de destino** para determinar quais dispositivos ou módulos serão direcionados com essa configuração. A condição se baseia nas marcas do gêmeo ou propriedades reportadas do gêmeo e deve corresponder ao formato da expressão. 

   Para configuração automática do dispositivo, é possível especificar apenas a marca ou a propriedade relatada a ser direcionada. Por exemplo, `tags.environment='test'` ou `properties.reported.chillerProperties.model='4000x'`. É possível especificar `*` para direcionar todos os dispositivos. 
   
   Para configuração automática de módulo, use uma consulta para especificar marcas ou propriedades reportadas dos módulos registrados para o hub IoT. Por exemplo, `from devices.modules where tags.environment='test'` ou `from devices.modules where properties.reported.chillerProperties.model='4000x'`. O curinga não pode ser usado para direcionar todos os módulos. 

3. Selecione **Avançar** para ir para a etapa final.

### <a name="review-configuration"></a>Configuração de revisão

Revise as informações da configuração e, em seguida, selecione **Enviar**.

## <a name="monitor-a-configuration"></a>Monitorar uma configuração

Para exibir os detalhes de uma configuração e monitorar os dispositivos que a executam, use as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), vá para o hub IoT. 

2. Selecione **Configuração do dispositivo IoT**.

3. Inspecione a lista de configuração. Para cada configuração, você pode exibir os seguintes detalhes:

   * **ID** - o nome da configuração.

   * **Condição de destino** – a consulta usada para definir os dispositivos ou módulos direcionados.

   * **Prioridade** - o número de prioridade atribuído à configuração.

   * **Hora de criação** - o carimbo de data/hora de quando a configuração foi criada. Esse carimbo de data/hora é usado para desempate quando duas configurações têm a mesma prioridade. 

   * **As métricas do sistema** - métricas que são calculadas pelo Hub IoT e não podem ser personalizadas pelos desenvolvedores. O destino especifica o número de dispositivos gêmeos que correspondem à condição de destino. Aplica-se o número especificado de dispositivos gêmeos que foram modificados pela configuração, que pode incluir modificações parciais no evento que uma configuração separada de prioridade mais alta também fez alterações. 

   * **Métricas personalizadas** -métricas que foram especificadas pelo desenvolvedor como consultas em propriedades reportadas do gêmeo.  Até cinco métricas personalizadas podem ser definidas por configuração. 
   
4. Selecione a configuração que deseja monitorar.  

5. Inspecione os detalhes da configuração. Você pode usar guias para exibir detalhes específicos sobre os dispositivos que receberam a configuração.

   * **Condição de Destino** – os dispositivos ou módulos que correspondem à condição de destino. 

   * **Métricas** - uma lista de métricas do sistema e métricas personalizadas.  É possível exibir uma lista de dispositivos ou módulos que são contadas para cada métrica selecionando a métrica na lista suspensa e, em seguida, selecionando **Exibir Dispositivos** ou **Exibir Módulos**.

   * **Configurações do Dispositivo Gêmeo** ou **Configurações do Módulo Gêmeo** – as configurações do gêmeo definidas pela configuração. 

   * **Rótulos de configuração** - pares de chave-valor usados para descrever uma configuração.  Rótulos não têm impacto sobre a funcionalidade. 

## <a name="modify-a-configuration"></a>Modificar uma configuração

Quando você modifica uma configuração, as alterações são replicadas imediatamente para todos os dispositivos ou módulos direcionados. 

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

* Caso um gêmeo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa configuração tenha a prioridade mais alta para o gêmeo, essa configuração será aplicada. 

* Se um gêmeo que executa essa configuração no momento não atender mais à condição de destino, as definições da configuração serão removidas e o gêmeo será modificado pela próxima configuração de prioridade mais alta. 

* Se um gêmeo que executa essa configuração no momento não atende à condição de destino e não atende a condição destino de outras configurações, as definições da configuração serão removidas e nenhuma outra alteração será feita no gêmeo. 

Para modificar uma configuração, use as seguintes etapas: 

1. No [portal do Azure](https://portal.azure.com), vá para o hub IoT. 

2. Selecione **Configuração do dispositivo IoT**. 

3. Selecione a configuração que deseja modificar. 

4. Faça atualizações nos seguintes campos: 

   * Condição de destino 
   * Rótulos 
   * Prioridade 
   * Métricas

4. Clique em **Salvar**.

5. Siga as etapas em [Monitorar uma configuração](#monitor-a-configuration) para observar as mudanças. 

## <a name="delete-a-configuration"></a>Excluir uma configuração

Quando você exclui uma configuração, qualquer dispositivo gêmeo assume sua próxima configuração de prioridade mais alta. Se os dispositivos gêmeos não atendem a condição destino de qualquer outra configuração, não serão aplicadas outras configurações. 

1. No [portal do Azure](https://portal.azure.com), vá para o hub IoT. 

2. Selecione **Configuração do dispositivo IoT**. 

3. Use a caixa de seleção para selecionar a configuração que deseja excluir. 

4. Selecione **Excluir**.

5. Uma mensagem solicitará que você confirme.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a configurar e monitorar dispositivos IoT em escala. Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerencie suas identidades de dispositivo do Hub IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Monitorar o Hub IoT](monitor-iot-hub.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)
* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/quickstart-linux.md)

Para explorar usando o Serviço de Provisionamento de Dispositivos do Hub IoT para habilitar o provisionamento sem toque e Just-In-Time, consulte: 

* [Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](../iot-dps/index.yml)