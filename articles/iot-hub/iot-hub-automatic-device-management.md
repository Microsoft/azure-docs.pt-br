---
title: Gerenciamento automático de dispositivos em escala com o IoT Hub do Azure | Microsoft Docs
description: Use o gerenciamento automático de dispositivo de IoT Hub do Azure para atribuir uma configuração para vários dispositivos de IoT
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: ff4e236569cc728b7011ffa26554277f281397fd
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485851"
---
# <a name="automatic-iot-device-management-at-scale-using-the-azure-portal"></a>Gerenciamento automático de dispositivo do IoT em escala usando o portal do Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Gerenciamento automático de dispositivos no IoT Hub do Azure automatiza muitas das tarefas complexas e repetitivas de gerenciamento de frotas de dispositivo grandes. Com o gerenciamento automático de dispositivo, um conjunto de dispositivos com base em suas propriedades de destino, definir uma configuração desejada e, em seguida, permitir que o IoT Hub atualizar dispositivos quando eles entrarem no escopo. Esta atualização é feita usando um _configuração de dispositivo automático_, que permite que você resumir conclusão e conformidade, mescla de identificador e conflitos e distribuir as configurações em uma abordagem em fases.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Propriedades relatadas do dispositivo automático gerenciamento funciona atualizando um conjunto de dispositivos gêmeos com as propriedades desejadas e um resumo com base no dispositivo gêmeo de emissão de relatórios.  Ele introduz uma nova classe e um documento JSON chamado um *configuração* que tem três partes:

* A **condição de destino** define o escopo de dispositivos gêmeos a serem atualizados. A condição de destino é especificada como uma consulta em marcas de dispositivos gêmeos e/ou propriedades reportadas.

* O **conteúdo de destino** define as propriedades desejadas para serem adicionadas ou atualizadas nos dispositivos gêmeos de destino. O conteúdo inclui um caminho para a seção de propriedades desejadas a ser alterado.

* As **métricas** definem as contagens de resumos de vários estados de configuração como **êxito**, **em andamento**, e **erro**. Métricas personalizadas são especificadas como consultas nas propriedades reportados do dispositivo gêmeo.  As métricas do sistema são as métricas padrão que medem o status de atualização de gêmeos, como o número de dispositivos gêmeos que são o destino e o número de gêmeos foram atualizados com êxito.

Configurações de dispositivo automático executam pela primeira vez logo após a configuração é criada e, em seguida, em intervalos de cinco minutos. Consultas de métricas executada cada vez que a configuração de dispositivo automático é executado.

## <a name="implement-device-twins-to-configure-devices"></a>Implementar dispositivos gêmeos para configurar dispositivos

As configurações automáticas de dispositivo requerem o uso de dispositivos gêmeos para sincronizar o estado entre a nuvem e os dispositivos.  Consulte [Compreender e usar gêmeos de dispositivos no Hub IoT](iot-hub-devguide-device-twins.md) para obter orientações sobre o uso de gêmeos de dispositivos.

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando marcações

Antes de criar uma configuração, você deve especificar quais dispositivos você deseja afetar. O Azure IoT Hub identifica os dispositivos usando marcas no dispositivo gêmeo. Cada dispositivo pode ter várias marcações e você pode defini-las que qualquer modo que faça sentido para sua solução. Por exemplo, se você gerenciar dispositivos em locais diferentes, adicione as seguintes marcas para um dispositivo gêmeo:

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

3. Selecione **Adicionar configuração**.

Há cinco etapas para criar uma configuração. As seções a seguir explicam cada uma delas. 

### <a name="name-and-label"></a>Nome e rótulo

1. Dê um nome exclusivo à sua configuração de até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.

2. Adicione rótulos para ajudar a acompanhar as configurações. Os rótulos são pares de **Nome** e **Valor** que descrevem a configuração. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`.

3. Selecione **Próximo** para ir para o próximo passo. 

### <a name="specify-settings"></a>Especifique as configurações

Esta seção especifica o conteúdo de destino a ser definido nos dispositivos gêmeos de destino. Há duas entradas para cada conjunto de configurações. A primeira é o caminho do dispositivo gêmeo, que é o caminho para a seção JSON dentro das propriedades desejadas do dispositivo gêmeo que será definido.  O segundo é o conteúdo do JSON a ser inserido nessa seção. Por exemplo, defina o caminho do dispositivo gêmeo e o conteúdo para o seguinte:

![Definir o caminho do dispositivo gêmeo e conteúdo](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

Você também pode definir configurações individuais, especificando o caminho inteiro no caminho do dispositivo gêmeo e o valor do conteúdo sem colchetes. Por exemplo, defina o Caminho Twin do Dispositivo como `properties.desired.chiller-water.temperature` e defina o Conteúdo como `66`.

Se duas ou mais configurações direcionam o mesmo caminho do dispositivo gêmeo, o conteúdo da configuração de prioridade mais alta será aplicado (a prioridade é definida na etapa 4).

Se você quiser remover uma propriedade, especifique o valor da propriedade como `null`.

Você pode adicionar configurações adicionais, selecionando **Adicionar configuração do dispositivo gêmeo**.

### <a name="specify-metrics-optional"></a>Especificar as métricas (opcionais)

As métricas fornecem contagens resumidas de vários estados em que um dispositivo pode relatar novamente após a aplicação de conteúdo da configuração. Por exemplo, você pode criar uma métrica para alterações de configurações pendentes, uma métrica de erros e uma métrica para alterações de configurações bem-sucedidas.

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

### <a name="target-devices"></a>Dispositivos de destino

Use a propriedade marcas dos dispositivos gêmeos para direcionar os dispositivos específicos que devem receber essa configuração.  Você também pode direcionar dispositivos por propriedades reportadas de dispositivo gêmeo.

Como várias configurações podem direcionar o mesmo dispositivo, você deve atribuir a cada configuração um número de prioridade. Se houver um conflito, a configuração com a prioridade mais alta ganhará. 

1. Insira um inteiro positivo para a **Prioridade** da configuração. O maior valor numérico é considerado a prioridade mais alta. Se duas configurações tiverem o mesmo número de prioridade, aquela que foi criada mais recentemente ganhará. 

2. Insira uma **Condição de destino** para determinar quais dispositivos serão direcionados com essa configuração. A condição se baseia nas marcas do dispositivo gêmeo ou propriedades reportadas do dispositivo gêmeo e deve corresponder ao formato da expressão. Por exemplo, `tags.environment='test'` ou `properties.reported.chillerProperties.model='4000x'`. É possível especificar `*` para direcionar todos os dispositivos.

3. Selecione **Avançar** para ir para a etapa final.

### <a name="review-configuration"></a>Configuração de revisão

Revise as informações da configuração e, em seguida, selecione **Enviar**.

## <a name="monitor-a-configuration"></a>Monitorar uma configuração

Para exibir os detalhes de uma configuração e monitorar os dispositivos que a executam, use as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), vá para o hub IoT. 

2. Selecione **Configuração do dispositivo IoT**.

3. Inspecione a lista de configuração. Para cada configuração, você pode exibir os seguintes detalhes:

   * **ID** - o nome da configuração.

   * **Condição de destino** - a consulta usada para definir os dispositivos direcionados.

   * **Prioridade** - o número de prioridade atribuído à configuração.

   * **Hora de criação** - o carimbo de data/hora de quando a configuração foi criada. Esse carimbo de data/hora é usado para desempate quando duas configurações têm a mesma prioridade. 

   * **As métricas do sistema** - métricas que são calculadas pelo Hub IoT e não podem ser personalizadas pelos desenvolvedores. O destino especifica o número de dispositivos gêmeos que correspondem à condição de destino. Aplica-se o número especificado de dispositivos gêmeos que foram modificados pela configuração, que pode incluir modificações parciais no evento que uma configuração separada de prioridade mais alta também fez alterações. 

   * **Métricas personalizadas** -métricas que foram especificadas pelo desenvolvedor como consultas em propriedades reportadas de dispositivo gêmeo.  Até cinco métricas personalizadas podem ser definidas por configuração. 
   
4. Selecione a configuração que deseja monitorar.  

5. Inspecione os detalhes da configuração. Você pode usar guias para exibir detalhes específicos sobre os dispositivos que receberam a configuração.

   * **Condição de destino** - os dispositivos que correspondem à condição de destino. 

   * **Métricas** - uma lista de métricas do sistema e métricas personalizadas.  Você pode exibir uma lista de dispositivos que são contadas para cada métrica selecionando a métrica na lista suspensa e, em seguida, selecionando **Exibir dispositivos**.

   * **Configurações do dispositivo gêmeo** - as configurações de dispositivo gêmeo que são definidas pela configuração. 

   * **Rótulos de configuração** - pares de chave-valor usados para descrever uma configuração.  Rótulos não têm impacto sobre a funcionalidade. 

## <a name="modify-a-configuration"></a>Modificar uma configuração

Quando você modifica uma configuração, as alterações são replicadas imediatamente para todos os dispositivos direcionados. 

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

* Caso um dispositivo gêmeo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa configuração tiver a prioridade mais alta para o dispositivo gêmeo, essa configuração será aplicada ao dispositivo gêmeo. 

* Se um dispositivo gêmeo não atende mais à condição de destino, as definições da configuração serão removidas e o dispositivo gêmeo será modificada pela próxima configuração de prioridade mais alta. 

* Se um dispositivo gêmeo que executa essa configuração no momento não atende à condição de destino e não atende a condição destino de quaisquer outras configurações, as definições da configuração serão removidas e nenhuma outra alteração será feita no dispositivo gêmeo. 

Para modificar uma configuração, use as seguintes etapas: 

1. No [portal do Azure](https://portal.azure.com), vá para o hub IoT. 

2. Selecione **Configuração do dispositivo IoT**. 

3. Selecione a configuração que deseja modificar. 

4. Faça atualizações nos seguintes campos: 

   * Condição de destino 
   * Rótulos 
   * Prioridade 
   * metrics

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

Neste artigo, você aprendeu a configurar e monitorar dispositivos de IoT em escala. Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerencie suas identidades de dispositivo do Hub IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Métricas do IoT Hub](iot-hub-metrics.md)
* [Monitoramento de operações](iot-hub-operations-monitoring.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)
* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Para explorar usando o Serviço de Provisionamento de Dispositivos do Hub IoT para habilitar o provisionamento sem toque e Just-In-Time, consulte: 

* [Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](/azure/iot-dps)
