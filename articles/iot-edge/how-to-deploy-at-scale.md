---
title: Implantar módulos em escala em portal do Azure Azure IoT Edge
description: Usar o portal do Azure para criar dispositivos de implantações automáticas para grupos do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9d03b6f4a512c22564480405ec0f0e0c0e62a958
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048416"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implantar módulos IoT Edge em escala usando o portal do Azure

Crie um **IOT Edge implantação automática** no portal do Azure para gerenciar implantações em andamento para vários dispositivos de uma vez. As implantações automáticas para IoT Edge fazem parte do recurso [Gerenciamento automático de dispositivo](../iot-hub/iot-hub-automatic-device-management.md) do Hub IoT. Implantações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos, acompanhar o status e a integridade dos módulos e fazer alterações, quando necessário.

Para saber mais, veja [Noções básicas sobre implantações automáticas do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando marcações

Antes de criar uma implantação, você precisa conseguir especificar quais dispositivos você deseja afetar. O Azure IoT Edge identifica os dispositivos usando **marcações** no dispositivo gêmeo. Cada dispositivo pode ter várias marcas que você define de qualquer modo que faça sentido para sua solução.

Por exemplo, se você gerenciar um campus de prédios inteligentes, poderá adicionar localização, tipo de sala e marcas de ambiente a um dispositivo:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Para obter mais informações sobre tags e gêmeos de dispositivos, consulte [Entender e usar gêmeos de dispositivos no Hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Criar uma implantação

O IoT Edge fornece dois tipos diferentes de implantações automáticas que você pode usar para personalizar seu cenário. Você pode criar uma *implantação*padrão, que inclui os módulos de tempo de execução do sistema e todos os módulos e rotas adicionais. Cada dispositivo só pode aplicar uma implantação. Ou você pode criar uma *implantação em camadas*, que inclui apenas módulos e rotas personalizadas, não o tempo de execução do sistema. Muitas implantações em camadas podem ser combinadas em um dispositivo, além de uma implantação padrão. Para obter mais informações sobre como os dois tipos de implantações automáticas funcionam em conjunto, consulte [entender IOT Edge implantações automáticas para dispositivos únicos ou em escala](module-deployment-monitoring.md).

As etapas para criar uma implantação e uma implantação em camadas são muito semelhantes. Todas as diferenças são chamadas nas etapas a seguir.

1. Na [portal do Azure](https://portal.azure.com), vá para o Hub IOT.
1. No menu no painel esquerdo, selecione **IOT Edge** em gerenciamento de **dispositivo automático**.
1. Na barra superior, selecione **criar implantação** ou **criar implantação em camadas**.

Há cinco etapas para criar uma implantação. As seções a seguir explicam cada uma delas.

>[!NOTE]
>As etapas neste artigo refletem a versão de esquema mais recente do IoT Edge Agent e Hub. A versão do esquema 1,1 foi lançada junto com IoT Edge versão 1.0.10 e habilita os recursos de ordem de inicialização do módulo e priorização de rota.
>
>Se você estiver implantando em um dispositivo que executa a versão 1.0.9 ou anterior, edite as **configurações de tempo de execução** na etapa **módulos** do assistente para usar a versão de esquema 1,0.

### <a name="step-1-name-and-label"></a>Etapa 1: nome e rótulo

1. Dê à sua implantação um nome exclusivo com até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.
1. Você pode adicionar rótulos como pares chave-valor para ajudar a acompanhar suas implantações. Por exemplo, **HostPlatform** e **Linux** ou **Versão** e **3.0.1**.
1. Selecione **Avançar: módulos** para mover para a etapa dois.

### <a name="step-2-modules"></a>Etapa 2: módulos

Você pode adicionar até 50 módulos a uma implantação. Se você criar uma implantação sem módulos, ela removerá os módulos atuais dos dispositivos de destino.

Em implantações, você pode gerenciar as configurações para os módulos agente de IoT Edge e IoT Edge Hub. Selecione **configurações de tempo de execução** para configurar os dois módulos de tempo de execução. Em implantação em camadas, os módulos de tempo de execução não são incluídos, portanto não podem ser configurados.

Para adicionar um código personalizado como um módulo ou para adicionar um módulo de serviço do Azure manualmente, siga estas etapas:

1. Na seção **configurações de registro de contêiner** da página, forneça as credenciais para acessar qualquer registro de contêiner privado que contenha suas imagens de módulo.
1. Na seção **módulos IOT Edge** da página, selecione **Adicionar**.
1. Escolha um dos três tipos de módulos no menu suspenso:

   * **Módulo IOT Edge** -você fornece o nome do módulo e o URI da imagem de contêiner. Por exemplo, o URI da imagem para o módulo SimulatedTemperatureSensor de exemplo é `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` . Se a imagem do módulo estiver armazenada em um registro de contêiner privado, adicione as credenciais nesta página para acessar a imagem.
   * **Módulo do Marketplace** -módulos hospedados no Azure Marketplace. Alguns módulos do Marketplace exigem configuração adicional, portanto, examine os detalhes do módulo na lista de [módulos de IOT Edge do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .
   * **Módulo Azure Stream Analytics** -módulos gerados de uma carga de trabalho de Azure Stream Analytics.

1. Se necessário, repita as etapas 2 e 3 para adicionar mais módulos à sua implantação.

Depois de adicionar um módulo a uma implantação, você pode selecionar seu nome para abrir a página **atualizar IOT Edge módulo** . Nessa página, você pode editar as configurações do módulo, variáveis de ambiente, criar opções, ordem de inicialização e módulo. Se você adicionou um módulo do Marketplace, talvez ele já tenha alguns desses parâmetros preenchidos. Para obter mais informações sobre as configurações de módulo disponíveis, consulte [configuração e gerenciamento de módulo](module-composition.md#module-configuration-and-management).

Se você estiver criando uma implantação em camadas, poderá estar Configurando um módulo que existe em outras implantações direcionando os mesmos dispositivos. Para atualizar o módulo de texto sem substituir outras versões, abra a guia **configurações de configuração do módulo** . Crie uma nova **Propriedade MyModule** com um nome exclusivo para uma subseção dentro das propriedades desejadas do módulo r, por exemplo `properties.desired.settings` . Se você definir propriedades somente dentro do `properties.desired` campo, ele substituirá as propriedades desejadas para o módulo definido em qualquer implantação de prioridade mais baixa.

![Definir a propriedade addmodule para implantação em camadas](./media/how-to-deploy-monitor/module-twin-property.png)

Para obter mais informações sobre a configuração do módulo "em implantações em camadas", consulte [implantação em camadas](module-deployment-monitoring.md#layered-deployment).

Depois de ter todos os módulos de uma implantação configurada, selecione **Avançar: rotas** para mover para a etapa três.

### <a name="step-3-routes"></a>Etapa 3: rotas

Na guia **Rotas**, defina como as mensagens são transmitidas entre os módulos e o Hub IoT. As mensagens são construídas com pares nome/valor.

Por exemplo, uma rota com uma **rota** de nome e um valor **de/messages/ \* para $upstream** levaria qualquer saída de mensagem por qualquer módulo e as enviaria para o Hub IOT.  

Os parâmetros **Priority** e **time to Live** são parâmetros opcionais que você pode incluir em uma definição de rota. O parâmetro Priority permite que você escolha quais rotas devem ter suas mensagens processadas primeiro ou quais rotas devem ser processadas por último. A prioridade é determinada pela definição de um número 0-9, em que 0 é a prioridade mais alta. O parâmetro vida útil permite que você declare por quanto tempo as mensagens nessa rota devem ser mantidas até que sejam processadas ou removidas da fila.

Para obter mais informações sobre como criar rotas, consulte [declarar rotas](module-composition.md#declare-routes).

Selecione **Avançar: métricas**.

### <a name="step-4-metrics"></a>Etapa 4: métricas

As métricas fornecem contagens de resumos de vários estados em que um dispositivo pode relatar como resultado da aplicação de conteúdo da configuração.

1. Insira um nome para **nome da métrica**.

1. Insira uma consulta para **Critérios da métrica**. A consulta é baseada nas [propriedades relatadas](module-edgeagent-edgehub.md#edgehub-reported-properties) do módulo gêmeo do hub do IoT Edge. A métrica representa o número de linhas retornadas pela consulta.

   Por exemplo:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Selecione **Avançar: dispositivos de destino**.

### <a name="step-5-target-devices"></a>Etapa 5: dispositivos de destino

Use a propriedade tags dos dispositivos para direcionar os dispositivos específicos que devem receber essa implantação.

Como várias implantações podem direcionar o mesmo dispositivo, você deve atribuir a cada implantação um número de prioridade. Se houver algum conflito, a implantação com a prioridade mais alta (valores maiores indicam prioridade mais alta) vence. Se duas implantações tiverem o mesmo número de prioridade, aquela que foi criada mais recentemente ganhará.

Se várias implantações tiverem como destino o mesmo dispositivo, somente aquela com a prioridade mais alta será aplicada. Se várias implantações em camadas tiverem como destino o mesmo dispositivo, elas serão todas aplicadas. No entanto, se alguma das propriedades for duplicada, como se houver duas rotas com o mesmo nome, a da implantação em camadas de prioridade mais alta substituirá o restante.

Qualquer implantação em camadas direcionada a um dispositivo deve ter uma prioridade mais alta do que a implantação base para ser aplicada.

1. Insira um inteiro positivo para a **Prioridade** da implantação.
1. Insira uma **Condição de destino** para determinar quais dispositivos serão direcionados com essa implantação. A condição se baseia nas marcas do dispositivo gêmeo ou propriedades reportadas do dispositivo gêmeo e deve corresponder ao formato da expressão.Por exemplo, `tags.environment='test'` ou `properties.reported.devicemodel='4000x'`.

Selecione **Avançar: revisar + criar** para passar para a etapa final.

### <a name="step-6-review-and-create"></a>Etapa 6: examinar e criar

Examine as informações de implantação e, em seguida, selecione **criar**.

Para monitorar a implantação, consulte [monitorar implantações de IOT Edge](how-to-monitor-iot-edge-deployments.md).

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando você modifica uma implantação, as alterações são replicadas imediatamente para todos os dispositivos direcionados. Você pode modificar as seguintes configurações e recursos para uma implantação existente:

* Condições de destino
* Métricas personalizadas
* Rótulos
* Marcações
* Propriedades desejadas

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>Modificar condições de destino, métricas personalizadas e rótulos

1. No Hub IoT, selecione **IOT Edge** no menu do painel esquerdo.
1. Selecione a guia **implantações IOT Edge** e, em seguida, selecione a implantação que deseja configurar.
1. Selecione a guia **condição de destino** . Altere a **condição de destino** para direcionar os dispositivos pretendidos. Você também pode ajustar a **prioridade**.  Clique em **Salvar**.

    Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

    * Caso um dispositivo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa implantação tiver a prioridade mais alta para o dispositivo, essa implantação será aplicada ao dispositivo.
    * Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino, ele desinstalará essa implantação e usará a próxima implantação com a prioridade mais alta.
    * Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino e não atenda à condição de destino de todas as outras implantações, nenhuma alteração ocorrerá no dispositivo. O dispositivo continua executando seus módulos atuais em seu estado atual, mas não é mais gerenciado como parte dessa implantação. Depois que ele atende à condição de destino de qualquer outra implantação, ele desinstala essa implantação e usa a nova.

1. Selecione a guia **métricas** e clique no botão **Editar métricas** . Adicione ou modifique métricas personalizadas usando a sintaxe de exemplo como um guia. Clique em **Salvar**.

    ![Editar métricas personalizadas em uma implantação](./media/how-to-deploy-monitor/metric-list.png)

1. Selecione a guia **Rótulos** e faça as alterações desejadas e selecione **salvar**.

## <a name="delete-a-deployment"></a>Excluir uma implantação

Quando você exclui uma implantação, todos os dispositivos implantados assumem a próxima implantação de prioridade mais alta. Se os dispositivos não atenderem à condição de destino de qualquer outra implantação, os módulos não serão removidos quando a implantação for excluída.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.
1. Selecione **IoT Edge**.
1. Selecione a guia **Implantações IOT Edge** .

   ![Exibir implantações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Use a caixa de seleção para selecionar a implantação que deseja excluir.
1. Selecione **Excluir**.
1. Um prompt informará que essa ação excluirá essa implantação e reverterá para o estado anterior de todos os dispositivos.Uma implantação com prioridade mais baixa será aplicada.Se nenhuma outra implantação for direcionada, nenhum módulo será removido. Se desejar remover todos os módulos do seu dispositivo, crie uma implantação com zero módulos e implante-a nos mesmos dispositivos.Clique em **Sim** para continuar.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [Implantação de módulos em dispositivos do IoT Edge](module-deployment-monitoring.md).