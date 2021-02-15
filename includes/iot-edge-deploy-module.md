---
title: incluir arquivo
description: incluir arquivo
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 76c8eb7acf20d8cf68d3573defd947efbc6c3c43
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "85801663"
---
Um dos principais recursos do Azure IoT Edge é a possibilidade de implantar código em seus dispositivos IoT Edge na nuvem. *Módulos do IoT Edge* são pacotes executáveis implementados como contêineres. Nesta seção, você implanta um módulo pré-criado na [seção Módulos do IoT Edge do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) diretamente de seu Hub IoT do Azure.

O módulo que você implanta nesta seção simula um sensor e envia os dados gerados. Esse módulo é uma informação útil de código quando você estiver começando a usar o IoT Edge porque você pode usar os dados simulados para desenvolvimento e teste. Se quiser ver exatamente o que esse módulo faz, você poderá exibir o [código-fonte do sensor de temperatura simulado](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Para implantar seu primeiro módulo do Azure Marketplace, use as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.

1. No menu no painel esquerdo, em **Gerenciamento Automático de Dispositivo**, selecione **IoT Edge**.

1. Clique na ID do dispositivo alvo da lista de dispositivos.

1. Na barra superior, selecione **Definir Módulos**.

   ![Selecionar Definir Módulos na página de detalhes do dispositivo](./media/iot-edge-deploy-module/select-set-modules.png)

1. Na seção **Módulos do IoT Edge** da página, clique em **Adicionar** e selecione **Módulo do Marketplace** no menu suspenso.

   ![Adicionar módulo do Marketplace](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. Em **Marketplace do Módulo do IoT Edge**, pesquise "Sensor de Temperatura Simulado" e selecione esse módulo.

1. Observe que o módulo SimulatedTemperatureSensor é adicionado à seção Módulos do IoT Edge, com o status desejado **Em execução**.

   Selecione **Avançar: Rotas** para prosseguir para a próxima etapa do assistente.

   ![Vá para a próxima etapa depois que o módulo do sensor de temperatura estiver listado](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. Na guia **Rotas** do assistente, você pode definir como as mensagens são transmitidas entre os módulos e o Hub IoT. As rotas são construídas usando pares nome/valor. Você verá duas rotas nessa página. A rota padrão chamada **rota** envia todas as mensagens ao Hub IoT (que é chamado `$upstream`). Uma segunda rota chamada **SimulatedTemperatureSensorToIoTHub** foi criada automaticamente quando você adicionou o módulo do Marketplace. Essa rota envia todas as mensagens especificamente do módulo de temperatura simulada para o Hub IoT. Você pode excluir a rota padrão porque ela é redundante nesse caso.

   Selecione **Avançar: Examinar + criar** para continuar e ir para a próxima etapa do assistente.

   ![Exclua a rota padrão e vá para a próxima etapa](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. Na guia **Examinar + criar** do assistente, você pode visualizar o arquivo JSON que define todos os módulos implantados no dispositivo IoT Edge. Observe que o módulo **SimulatedTemperatureSensor** está incluído, bem como os dois módulos de runtime, **edgeAgent** e **edgeHub**. Selecione **Criar** quando terminar de examinar.

   Quando você envia uma nova implantação para um dispositivo IoT Edge, nada é enviado por push para o dispositivo. Em vez disso, o dispositivo consulta o Hub IoT regularmente para verificar se há novas instruções. Se o dispositivo encontrar um manifesto de implantação atualizada, ele usará as informações sobre a nova implantação para efetuar pull das imagens de módulo da nuvem e, em seguida, começa a executar os módulos localmente. Esse processo pode levar alguns minutos.

1. Depois que você criar os detalhes de implantação do módulo, o assistente direcionará você à página de detalhes do dispositivo. Na página de detalhes do dispositivo, veja o status da implantação na guia **Módulos**. Três módulos deverão estar listados: $edgeAgent, $edgeHub e SimulatedTemperatureSensor. Se um ou mais módulos estão listados conforme especificado na implantação, mas não são relatados pelo dispositivo, o dispositivo IoT Edge ainda está iniciando os módulos. Aguarde alguns instantes e selecione **Atualizar** na parte superior da página.

   ![Exibir SimulatedTemperatureSensor na lista de módulos implantados](./media/iot-edge-deploy-module/view-deployed-modules.png)
