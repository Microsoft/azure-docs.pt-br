---
title: Criar implantações automáticas do portal do Azure - Azure IoT Edge | Microsoft Docs
description: Usar o portal do Azure para criar dispositivos de implantações automáticas para grupos do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 83e2490821f59adeb37958c6c31403121a40274e
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540894"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implante e monitore módulos de IoT Edge em escala usando o portal do Azure

Criar uma **implantação automática do IoT Edge** no portal do Azure para gerenciar implantações contínuas para vários dispositivos ao mesmo tempo. Implantações automáticas para o IoT Edge fazem parte do [gerenciamento de dispositivo automático](/azure/iot-hub/iot-hub-automatic-device-management) recurso do IoT Hub. As implantações são processos dinâmicos que permitem que você implantar vários módulos em vários dispositivos, acompanhar o status e a integridade dos módulos e fazer alterações quando necessário. 

Para obter mais informações, consulte [implantações automáticas de entender o IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando marcações

Antes de criar uma implantação, você precisa conseguir especificar quais dispositivos você deseja afetar. O Azure IoT Edge identifica os dispositivos usando **marcações** no dispositivo gêmeo. Cada dispositivo pode ter várias marcas que você define em qualquer forma que faça sentido para sua solução. Por exemplo, se você gerencia um campus de edifícios inteligentes, poderá adicionar as seguintes marcações a um dispositivo:

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

1. No [portal do Azure](https://portal.azure.com), vá para o hub IoT. 
1. Selecione **IoT Edge**.
1. Selecione **Adicionar implantação do IoT Edge**.

Há cinco etapas para criar uma implantação. As seções a seguir explicam cada uma delas. 

### <a name="step-1-name-and-label"></a>Etapa 1: Nome e rótulo

1. Dê à sua implantação um nome exclusivo com até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.
1. Você pode adicionar rótulos como pares chave-valor para ajudar a acompanhar suas implantações. Por exemplo, **HostPlatform** e **Linux** ou **Versão** e **3.0.1**.
1. Selecione **Avançar** para ir para a etapa 2. 

### <a name="step-2-add-modules-optional"></a>Etapa 2: Adicionar módulos (opcional)

Você pode adicionar até 20 módulos para uma implantação. 

Se você criar uma implantação sem módulos, ele remove todos os módulos atuais dos dispositivos de destino. 

Para adicionar um módulo do Azure Stream Analytics, siga estas etapas:

1. Na seção **Módulos de Implantação** da página, clique em **Adicionar**.
1. Selecione **módulo do Azure Stream Analytics**.
1. Escolha uma **Assinatura** no menu de lista suspensa.
1. Escolha seu IoT **trabalho do Edge** no menu suspenso.
1. Selecione **Salvar** para adicionar o módulo à implantação. 

Para adicionar um código personalizado como um módulo ou para adicionar um módulo de serviço do Azure manualmente, siga estas etapas:

1. Na seção **Configurações de Registro de Contêiner** da página, forneça os nomes e as credenciais para os registros de contêiner privados que contêm as imagens de módulo dessa implantação. O agente do Edge IoT relatará o erro 500 se ele não é possível localizar a credencial do registro de contêiner para uma imagem do Docker.
1. Na seção **Módulos de Implantação** da página, clique em **Adicionar**.
1. Selecione **Módulo IoT Edge**.
1. Dê um **Nome** ao módulo.
1. No campo **URI da Imagem**, insira a imagem de contêiner para o módulo. 
1. Especifique qualquer **Opção de criação de contêiner** que deverá ser passada para o contêiner. Para obter mais informações, consulte [criar docker](https://docs.docker.com/engine/reference/commandline/create/).
1. Use o menu suspenso para selecionar uma **Política de reinicialização**. Escolha uma das seguintes opções: 
   * **Sempre** – o módulo sempre é reiniciado se é desligado por qualquer motivo.
   * **Nunca** -o módulo nunca é reiniciado se é desligado por qualquer motivo.
   * **em caso de falha** -o módulo é reiniciado se ele falha, mas não se ele é desligado corretamente. 
   * **Em Íntegro** -o módulo é reiniciado se ele falhar ou retorna um status não íntegro. Cabe a cada módulo implementar a função de status da integridade. 
1. Use o menu suspenso para selecionar o **Status** desejado do módulo. Escolha uma das seguintes opções:
   * **executando** -em execução é a opção padrão. O módulo será iniciado imediatamente depois de ser implantado.
   * **Parado** -depois de ser implantado, o módulo permanecerá ocioso até que seja chamado para ser iniciado por você ou outro módulo.
1. Selecione **Definir propriedades desejadas do módulo gêmeo** se você quiser adicionar marcas ou outras propriedades ao módulo gêmeo.
1. Insira **Variáveis de Ambiente** para este módulo. Variáveis de ambiente fornecem informações de configuração para um módulo.
1. Selecione **Salvar** para adicionar o módulo à implantação. 

Depois de configurar todos os módulos para uma implantação, selecione **Avançar** para ir para a etapa 3.

### <a name="step-3-specify-routes-optional"></a>Etapa 3: Especificar rotas (opcional)

As rotas definem como os módulos se comunicam entre si em uma implantação. Por padrão, o assistente fornece uma rota chamada **rota** e definida como **FROM /\* INTO $upstream**, que significa que quaisquer mensagens de saída por quaisquer módulos são enviadas para o seu Hub IoT.  

Adicionar ou atualizar as rotas com informações de [declarar rotas](module-composition.md#declare-routes), em seguida, selecione **próximo** para continuar para a seção de revisão.

### <a name="step-4-specify-metrics-optional"></a>Etapa 4: Especificar as métricas (opcionais)

As métricas fornecem contagens de resumos de vários estados em que um dispositivo pode relatar como resultado da aplicação de conteúdo da configuração.

1. Insira um nome para **nome da métrica**.

1. Insira uma consulta para **Critérios da métrica**. A consulta é baseada nas [propriedades relatadas](module-edgeagent-edgehub.md#edgehub-reported-properties) do módulo gêmeo do hub do IoT Edge. A métrica representa o número de linhas retornadas pela consulta.

   Por exemplo:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>Etapa 5: Dispositivos de destino

Use a propriedade tags dos dispositivos para direcionar os dispositivos específicos que devem receber essa implantação. 

Como várias implantações podem direcionar o mesmo dispositivo, você deve atribuir a cada implantação um número de prioridade. Se já houver um conflito, a implantação com a prioridade mais alta (valores maiores indicam maior prioridade) ganha. Se duas implantações tiverem o mesmo número de prioridade, aquela que foi criada mais recentemente ganhará. 

1. Insira um inteiro positivo para a **Prioridade** da implantação.
1. Insira uma **Condição de destino** para determinar quais dispositivos serão direcionados com essa implantação. A condição se baseia nas marcas do dispositivo gêmeo ou propriedades reportadas do dispositivo gêmeo e deve corresponder ao formato da expressão. Por exemplo, `tags.environment='test'` ou `properties.reported.devicemodel='4000x'`. 
1. Selecione **Avançar** para ir para a etapa final.

### <a name="step-6-review-deployment"></a>Etapa 6: Examinar implantação

Examine as informações da implantação e, em seguida, selecione **Enviar**.

## <a name="deploy-modules-from-azure-marketplace"></a>Implantar módulos do Azure Marketplace

O Azure Marketplace é um marketplace online de aplicativos e serviços onde você pode procurar por uma ampla variedade de aplicativos e soluções corporativas que são certificadas e otimizadas para execução no Azure, incluindo [módulos do IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). O Azure Marketplace também pode ser acessado pelo portal do Azure em **Criar um Recurso**.

Você pode implantar um módulo do IoT Edge do Azure Marketplace ou no portal do Azure:

1. Localize um módulo e inicie o processo de implantação.

   * Portal do Azure: Encontre um módulo e selecione **Criar**.

   * Azure Marketplace:

     1. Encontre um módulo e selecione **Obter agora**.
     1. Confirme os termos de uso e a política de privacidade do provedor selecionando **Continuar**.

1. Escolha sua assinatura e o Hub IoT ao qual o dispositivo de destino está anexado.

1. Escolha **Implantar em Escala**.

1. Escolha se deseja adicionar o módulo a uma nova implantação ou a um clone de uma implantação existente; se for usar o clone, selecione a implantação existente na lista.

1. Selecione **Criar** para continuar o processo de criação de uma implantação em escala. Você poderá especificar os mesmos detalhes como faria em qualquer implantação.

## <a name="monitor-a-deployment"></a>Monitorar uma implantação

Para exibir os detalhes de uma implantação e monitorar os dispositivos que a executam, use as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT. 
1. Selecione **IoT Edge**.
1. Selecione **Implantações do IoT Edge**. 

   ![Exibir implantações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Inspecione a lista de implantação. Para cada implantação, você pode exibir os seguintes detalhes:
   * **ID** – o nome da implantação.
   * **Condição de destino** – a marcação usada para definir os dispositivos direcionados.
   * **Prioridade** – o número de prioridade atribuído à implantação.
   * **Métricas de sistema** - **Desejados** especifica o número de dispositivos gêmeos em Hub IoT que batem com a condição desejada, e **Aplicados** especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado aos seus módulos gêmeos no Hub IoT. 
   * **Métricas do dispositivo** -o número de dispositivos do IoT Edge no relatório de êxito ou erros de tempo de execução do IoT Edge cliente de implantação.
   * **Métricas personalizadas** -o número de dispositivos do IoT Edge na implantação de dados para qualquer métricas que você definiu para a implantação de relatórios.
   * **Hora de criação** – o carimbo de data/hora de quando a implantação foi criada. Esse carimbo de data/hora é usado para desempate quando duas implantações têm a mesma prioridade. 
1. Selecione a implantação que deseja monitorar.  
1. Inspecione os detalhes da implantação. Você pode usar guias para revisar os detalhes da implantação.

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando você modifica uma implantação, as alterações são replicadas imediatamente para todos os dispositivos direcionados. 

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

* Caso um dispositivo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa implantação tiver a prioridade mais alta para o dispositivo, essa implantação será aplicada ao dispositivo. 
* Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino, ele desinstalará essa implantação e usará a próxima implantação com a prioridade mais alta. 
* Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino e não atenda à condição de destino de todas as outras implantações, nenhuma alteração ocorrerá no dispositivo. O dispositivo continua executando seus módulos atuais em seu estado atual, mas não é mais gerenciado como parte dessa implantação. Depois que ele atende à condição de destino de qualquer outra implantação, ele desinstala essa implantação e usa a nova. 

Para modificar uma implantação, use as seguintes etapas: 

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT. 
1. Selecione **IoT Edge**.
1. Selecione **Implantações do IoT Edge**. 

   ![Exibir implantações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Selecione a implantação que deseja modificar. 
1. Faça atualizações nos seguintes campos: 
   * Condição de destino
   * Métricas – você pode modificar ou excluir as métricas que definiu ou adicionar novas métricas.
   * Rótulos
   * Prioridade
1. Clique em **Salvar**.
1. Siga as etapas em [monitorar uma implantação](#monitor-a-deployment) para observar as alterações a distribuir. 

## <a name="delete-a-deployment"></a>Excluir uma implantação

Quando você exclui uma implantação, todos os dispositivos usam sua próxima implantação com a prioridade mais alta. Se os dispositivos não atenderem à condição de destino de qualquer outra implantação, os módulos não serão removidos quando a implantação for excluída. 

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT. 
1. Selecione **IoT Edge**.
1. Selecione **Implantações do IoT Edge**. 

   ![Exibir implantações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Use a caixa de seleção para selecionar a implantação que deseja excluir. 
1. Selecione **Excluir**.
1. Um prompt informará que essa ação excluirá essa implantação e reverterá para o estado anterior de todos os dispositivos.  Isso significa que uma implantação com uma prioridade mais baixa será aplicada.  Se nenhuma outra implantação for direcionada, nenhum módulo será removido. Se desejar remover todos os módulos do seu dispositivo, crie uma implantação com zero módulos e implante-a nos mesmos dispositivos. Escolha **Sim** para continuar. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [implantar módulos em dispositivos IoT Edge](module-deployment-monitoring.md).
