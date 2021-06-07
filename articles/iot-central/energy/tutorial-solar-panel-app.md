---
title: 'Tutorial: Criar um aplicativo de monitoramento de painel solar com o Azure IoT Central'
description: 'Tutorial: Saiba como criar um aplicativo de painel solar usando os modelos de aplicativo do Azure IoT Central.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 509e31919dd974da253cd0478a70f889cc060fae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831783"
---
# <a name="tutorial-create-and-explore-the-solar-panel-monitoring-app-template"></a>Tutorial: Criar e explorar o modelo de aplicativo de monitoramento do painel solar 

Este tutorial orienta você pelo processo de criação de um aplicativo de monitoramento de painel solar, que inclui um modelo de dispositivo de exemplo cujos dados são simulados. Neste tutorial, você aprenderá como:


> [!div class="checklist"]
> * Criar um aplicativo de painel solar gratuitamente
> * Explorar o aplicativo
> * Limpar os recursos


Caso não tenha uma assinatura, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

Não há pré-requisitos para a conclusão deste tutorial. A assinatura do Azure é recomendada, mas não é obrigatória.


## <a name="create-a-solar-panel-monitoring-app"></a>Criar um aplicativo de monitoramento de painel solar 

Você pode criar esse aplicativo em três etapas simples:

1. Acesse o [Azure IoT Central](https://apps.azureiotcentral.com). Para criar um aplicativo, selecione **Criar**. 

1. Selecione a guia **Energia**. Em **Monitoramento do painel solar**, selecione **Criar aplicativo**. 

    > [!div class="mx-imgBorder"]
    > ![Captura de tela das opções de criação do Azure IoT Central.](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. Na caixa de diálogo **Novo aplicativo**, preencha os detalhes solicitados e, em seguida, selecione **Criar**:
    * **Nome do aplicativo**: Escolha o nome para seu aplicativo do Azure IoT Central. 
    * **URL**: Escolha uma URL do Azure IoT Central. A plataforma verifica a própria exclusividade.
    * **Plano de preços**: se você já tem uma assinatura do Azure, a configuração padrão é recomendada. Caso não tenha uma assinatura do Azure, comece com a avaliação gratuita.
    * **Informações de cobrança**: o aplicativo é gratuito. é necessário informar os detalhes de diretório, assinatura do Azure e região para provisionar os recursos para seu aplicativo.
        ![Captura de tela do Novo aplicativo.](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Captura de tela das Informações de cobrança.](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Verifique o aplicativo e os dados simulados

Você pode modificar seu novo aplicativo de painel solar a qualquer momento. Por hora, vamos garantir que o aplicativo esteja implantado e que funcione conforme o esperado antes de modificá-lo.

Para verificar a criação do aplicativo e a simulação de dados, acesse o **Painel**. Se você puder ver os blocos com alguns dados, a implantação do aplicativo terá sido bem-sucedida. A simulação de dados pode levar algum tempo para gerar os dados. 

## <a name="application-walk-through"></a>Passo a passo do aplicativo
Depois de implantar com êxito o modelo de aplicativo, você desejará explorar o aplicativo um pouco mais. Observe que ele vem com o dispositivo de medidor inteligente de exemplo, o modelo de dispositivo e o painel.

A Adatum é uma empresa de energia fictícia, que monitora e gerencia painéis solares. No painel de monitoramento de painel solar, você vê as propriedades do painel solar, os dados e os comandos de exemplo. Este painel permite que você ou sua equipe de suporte execute as seguintes atividades proativamente, antes que os problemas exijam suporte adicional:
* examinar as informações mais recentes do painel e a localização de instalação dele no mapa;
* verificar o status do painel e o status da conexão;
* examinar tendências de geração de energia e temperatura para capturar padrões anormais.
* acompanhar a geração de energia total para fins de planejamento e cobrança;
* ativar um painel e atualizar a versão do firmware, se necessário. No modelo, os botões de comando mostram as funcionalidades possíveis e não enviam comandos reais.

> [!div class="mx-imgBorder"]
> ![Captura de tela do Painel de Modelo de Monitoramento de Painel Solar.](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Dispositivos
O aplicativo vem com um dispositivo de painel solar de exemplo. Em detalhes do dispositivo, selecione **Dispositivos**.

> [!div class="mx-imgBorder"]
> ![Captura de tela dos Dispositivos de Modelo de Monitoramento de Painel Solar.](media/tutorial-iot-central-solar-panel/solar-panel-device.png)

Selecione o dispositivo de exemplo, **SP0123456789**. Na guia **Atualizar Propriedades**, você pode atualizar as propriedades graváveis do dispositivo e visualizar os valores atualizados no painel. 

> [!div class="mx-imgBorder"]
> ![Captura de tela da guia Atualizar Propriedades dos Dispositivos de Modelo de Monitoramento de Painel Solar.](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Modelo de dispositivo
Selecione a guia **Modelos de dispositivo** para ver o modelo de dispositivo do painel solar. O modelo tem uma interface predefinida para dados, propriedades, comandos e exibições.

> [!div class="mx-imgBorder"]
> ![Captura de tela dos modelos de Dispositivos de Modelo de Monitoramento de Painel Solar.](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Limpar os recursos
Se optar por não continuar usando este aplicativo, exclua-o seguindo estas etapas:

1. No painel esquerdo, selecione **Administração**.
1. Selecione **Configurações do aplicativo** > **Excluir**. 

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da Administração de Modelo de Monitoramento de Painel Solar.](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>Próximas etapas
 
> [!div class="nextstepaction"]
> [Azure IoT Central – arquitetura de aplicativo do painel solar](./concept-iot-central-solar-panel-app.md)

