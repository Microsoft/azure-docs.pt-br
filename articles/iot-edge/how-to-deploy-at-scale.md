---
title: Implantar módulos em escala no portal do Azure – Azure IoT Edge
description: Usar o portal do Azure para criar dispositivos de implantações automáticas para grupos do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: db27a466ca5f1370e8b43ceb472f5deeaba509f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200326"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implantar módulos do IoT Edge em escala usando o portal do Azure

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Crie uma **implantação automática do IoT Edge** no portal do Azure para gerenciar implantações contínuas para vários dispositivos de uma vez. As implantações automáticas para IoT Edge fazem parte do recurso [Gerenciamento automático de dispositivo](../iot-hub/iot-hub-automatic-device-management.md) do Hub IoT. Implantações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos, acompanhar o status e a integridade dos módulos e fazer alterações, quando necessário.

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

O IoT Edge fornece dois tipos diferentes de implantações automáticas que você pode usar para personalizar seu cenário. Você pode criar uma *implantação* padrão, que inclui os módulos de runtime do sistema e todos os módulos e rotas adicionais. Cada dispositivo só pode aplicar uma implantação. Ou você pode criar uma *implantação em camadas*, que inclui apenas módulos e rotas personalizados, não o runtime do sistema. Muitas implantações em camadas podem ser combinadas em um dispositivo, sobre uma implantação padrão. Para obter mais informações sobre como os dois tipos de implantações automáticas funcionam em conjunto, confira [Noções básicas sobre implantações automáticas do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md).

As etapas para criar uma implantação e uma implantação em camadas são muito semelhantes. Todas as diferenças são descritas nas etapas a seguir.

1. No [portal do Azure](https://portal.azure.com), vá até o Hub IoT.
1. No menu no painel esquerdo, selecione **IoT Edge** em **Gerenciamento Automático de Dispositivo**.
1. Na barra superior, selecione **Criar Implantação** ou **Criar Implantação em Camadas**.

Há cinco etapas para criar uma implantação. As seções a seguir explicam cada uma delas.

>[!NOTE]
>As etapas neste artigo refletem a versão de esquema mais recente do agente e hub do IoT Edge. A versão 1.1 do esquema foi lançada junto com o IoT Edge versão 1.0.10 e habilita os recursos de ordem de inicialização do módulo e priorização de rota.
>
>Se você estiver implantando em um dispositivo que executa a versão 1.0.9 ou anterior, edite as **Configurações de Runtime** na etapa **Módulos** do assistente para usar a versão 1.0 do esquema.

### <a name="step-1-name-and-label"></a>Etapa 1: Nome e rótulo

1. Dê à sua implantação um nome exclusivo com até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.
1. Você pode adicionar rótulos como pares chave-valor para ajudar a acompanhar suas implantações. Por exemplo, **HostPlatform** e **Linux** ou **Versão** e **3.0.1**.
1. Selecione **Avançar: Módulos** para ir para a etapa 2.

### <a name="step-2-modules"></a>Etapa 2: Módulos

Você pode adicionar até 50 módulos a uma implantação. Se você criar uma implantação sem módulos, ela removerá todos os módulos atuais dos dispositivos de destino.

Nas implantações, você pode gerenciar as configurações dos módulos do agente e hub do IoT Edge. Selecione **Configurações de Runtime** para configurar os dois módulos de runtime. Na implantação em camadas, os módulos de runtime não são incluídos, portanto, não podem ser configurados.

Para adicionar um código personalizado como um módulo ou para adicionar um módulo de serviço do Azure manualmente, siga estas etapas:

1. Na seção **Configurações do Registro de Contêiner** da página, forneça as credenciais para acessar registros de contêiner privado que contêm as imagens do módulo.
1. Na seção **Módulos do IoT Edge** da página, selecione **Adicionar**.
1. Escolha um dos três tipos de módulos no menu suspenso:

   * **Módulo do IoT Edge** – você fornece o nome do módulo e o URI da imagem de contêiner. Por exemplo, o URI da imagem para o módulo SimulatedTemperatureSensor de exemplo é `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. Se a imagem do módulo estiver armazenada em um registro de contêiner privado, adicione as credenciais nesta página para acessar a imagem.
   * **Módulo do Marketplace** – módulos hospedados no Azure Marketplace. Alguns módulos do Marketplace exigem configuração adicional, portanto, examine os detalhes do módulo na lista de [Módulos do IoT Edge do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).
   * **Módulo do Azure Stream Analytics** – módulos gerados da carga de trabalho do Azure Stream Analytics.

1. Se necessário, repita as etapas 2 e 3 para adicionar mais módulos à sua implantação.

Depois de adicionar um módulo a uma implantação, você pode selecionar o nome dela para abrir a página **Atualizar módulo do IoT Edge**. Nessa página, você pode editar configurações do módulo, variáveis de ambiente, opções de criação, ordem de inicialização e módulo gêmeo. Se você adicionou um módulo do Marketplace, talvez ele já tenha alguns desses parâmetros preenchidos. Para obter mais informações sobre as configurações de módulo disponíveis, confira [Configuração e gerenciamento de módulo](module-composition.md#module-configuration-and-management).

Se você estiver criando uma implantação em camadas, poderá configurar um módulo que existe em outras implantações direcionadas aos mesmos dispositivos. Para atualizar o módulo gêmeo sem substituir outras versões, abra a guia **Configurações do Módulo Gêmeo**. Crie uma **Propriedade do Módulo Gêmeo** com um nome exclusivo para uma subseção dentro das propriedades desejadas do módulo gêmeo, por exemplo `properties.desired.settings`. Se você definir propriedades somente dentro do campo `properties.desired`, ele substituirá as propriedades desejadas para o módulo definido em implantações de prioridade mais baixa.

![Definir a propriedade do módulo gêmeo para implantação em camadas](./media/how-to-deploy-monitor/module-twin-property.png)

Para obter mais informações sobre a configuração do o módulo gêmeo nas implantações em camadas, confira [Implantação em camadas](module-deployment-monitoring.md#layered-deployment).

Depois de configurar todos os módulos para uma implantação, selecione **Avançar: Rotas** para ir para a etapa 3.

### <a name="step-3-routes"></a>Etapa 3: Rotas

Na guia **Rotas**, defina como as mensagens são transmitidas entre os módulos e o Hub IoT. As mensagens são construídas com pares nome/valor.

Por exemplo, uma rota com o nome **rota** e o valor **FROM /messages/\* INTO $upstream** levaria qualquer saída de mensagens dos módulos e as enviaria para o hub IoT.  

Os parâmetros **Priority** e **Time to live** são opcionais, e você pode incluí-los em uma definição de rota. O parâmetro priority permite que você escolha quais rotas devem ter suas mensagens processadas primeiro ou por último. A prioridade é determinada pela configuração de um número 0-9, em que 0 é a prioridade mais alta. O parâmetro time to live permite que você declare por quanto tempo as mensagens nessa rota devem ser mantidas até que sejam processadas ou removidas da fila.

Para obter mais informações sobre como criar rotas, confira [Declarar rotas](module-composition.md#declare-routes).

Selecione **Avançar: Métricas**.

### <a name="step-4-metrics"></a>Etapa 4: Métricas

As métricas fornecem contagens de resumos de vários estados em que um dispositivo pode relatar como resultado da aplicação de conteúdo da configuração.

1. Insira um nome para **nome da métrica**.

1. Insira uma consulta para **Critérios da métrica**. A consulta é baseada nas [propriedades relatadas](module-edgeagent-edgehub.md#edgehub-reported-properties) do módulo gêmeo do hub do IoT Edge. A métrica representa o número de linhas retornadas pela consulta.

   Por exemplo:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Selecione **Avançar: Direcionar dispositivos**.

### <a name="step-5-target-devices"></a>Etapa 5: Direcionar dispositivos

Use a propriedade tags dos dispositivos para direcionar os dispositivos específicos que devem receber essa implantação.

Como várias implantações podem direcionar o mesmo dispositivo, você deve atribuir a cada implantação um número de prioridade. Se houver um conflito, a implantação com a prioridade mais alta ganhará (valores maiores indicam prioridades mais altas). Se duas implantações tiverem o mesmo número de prioridade, aquela que foi criada mais recentemente ganhará.

Se várias implantações tiverem como destino o mesmo dispositivo, somente aquela com a prioridade mais alta será aplicada. Se várias implantações em camadas tiverem como destino o mesmo dispositivo, elas serão todas aplicadas. No entanto, se alguma das propriedades for duplicada, como se houver duas rotas com o mesmo nome, a da implantação em camadas de prioridade mais alta substituirá o restante.

Qualquer implantação em camadas direcionada a um dispositivo precisa ter uma prioridade mais alta do que a implantação base para ser aplicada.

1. Insira um inteiro positivo para a **Prioridade** da implantação.
1. Insira uma **Condição de destino** para determinar quais dispositivos serão direcionados com essa implantação.  A condição se baseia nas marcas do dispositivo gêmeo ou propriedades reportadas do dispositivo gêmeo e deve corresponder ao formato da expressão. Por exemplo, `tags.environment='test'` ou `properties.reported.devicemodel='4000x'`.

Selecione **Avançar: Examinar + Criar** para ir para a etapa final.

### <a name="step-6-review-and-create"></a>Etapa 6: Examinar e criar

Examine as informações da implantação e selecione **Criar**.

Para monitorar a implantação, confira [Monitorar implantações do IoT Edge](how-to-monitor-iot-edge-deployments.md).

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando você modifica uma implantação, as alterações são replicadas imediatamente para todos os dispositivos direcionados. Você pode modificar as seguintes configurações e recursos para uma implantação existente:

* Condições de destino
* Métricas personalizadas
* Rótulos
* Marcações
* Propriedades desejadas

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>Modificar condições de destino, métricas personalizadas e rótulos

1. No hub IoT, selecione **IoT Edge** no menu do painel esquerdo.
1. Selecione a guia **Implantações do IoT Edge** e a implantação que deseja configurar.
1. Selecione a guia **Condição de Destino**. Altere a **Condição de Destino** para direcionar os dispositivos pretendidos. Você também pode ajustar a **Prioridade**.  Clique em **Salvar**.

    Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

    * Caso um dispositivo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa implantação tiver a prioridade mais alta para o dispositivo, essa implantação será aplicada ao dispositivo.
    * Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino, ele desinstalará essa implantação e usará a próxima implantação com a prioridade mais alta.
    * Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino e não atenda à condição de destino de todas as outras implantações, nenhuma alteração ocorrerá no dispositivo. O dispositivo continua executando seus módulos atuais em seu estado atual, mas não é mais gerenciado como parte dessa implantação. Depois que ele atende à condição de destino de qualquer outra implantação, ele desinstala essa implantação e usa a nova.

1. Selecione a guia **Métricas** e clique no botão **Editar Métricas**. Adicione ou modifique métricas personalizadas usando a sintaxe de exemplo como guia. Clique em **Salvar**.

    ![Editar métricas personalizadas em uma implantação](./media/how-to-deploy-monitor/metric-list.png)

1. Selecione a guia **Rótulos**, faça as alterações desejadas e selecione **Salvar**.

## <a name="delete-a-deployment"></a>Excluir uma implantação

Quando você exclui uma implantação, todos os dispositivos implantados usam a próxima implantação com a prioridade mais alta. Se os dispositivos não atenderem à condição de destino de qualquer outra implantação, os módulos não serão removidos quando a implantação for excluída.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.
1. Selecione **IoT Edge**.
1. Selecione a guia **Implantações do IoT Edge**.

   ![Exibir implantações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Use a caixa de seleção para selecionar a implantação que deseja excluir.
1. Selecione **Excluir**.
1. Um prompt informará que essa ação excluirá essa implantação e reverterá para o estado anterior de todos os dispositivos. Uma implantação com uma prioridade mais baixa será aplicada. Se nenhuma outra implantação for direcionada, nenhum módulo será removido. Se desejar remover todos os módulos do seu dispositivo, crie uma implantação com zero módulos e implante-a nos mesmos dispositivos. Clique em **Sim** para continuar.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [Implantação de módulos em dispositivos do IoT Edge](module-deployment-monitoring.md).