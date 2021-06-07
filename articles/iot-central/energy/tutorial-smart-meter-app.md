---
title: 'Tutorial: Criar um aplicativo de análise de medidor inteligente com o IoT Central'
description: 'Tutorial: Saiba como criar um aplicativo monitoramento de medidor inteligente usando os modelos de aplicativo do Azure IoT Central.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d9b5af45ab9a1003cc25e8b1ea2059b83bc715c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833296"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Tutorial: Criar e percorrer o modelo de aplicativo de monitoramento do medidor inteligente 

Este tutorial orienta você pelo processo de criação do aplicativo de monitoramento de medidor inteligente, que inclui um modelo de dispositivo de exemplo cujos dados são simulados. Neste tutorial, você aprenderá:

> [!div class="checklist"]
> * Criar o aplicativo de medidor inteligente gratuitamente
> * Passo a passo do aplicativo
> * Limpar os recursos


Caso não tenha uma assinatura, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Pré-requisitos
- Nenhum
- A assinatura do Azure é recomendada, mas não é obrigatória para experimentar

## <a name="create-a-smart-meter-monitoring-app"></a>Criar um aplicativo de monitoramento de medidor inteligente 

Você pode criar esse aplicativo em três etapas simples:

1. Abra a [home page do Azure IoT Central](https://apps.azureiotcentral.com) e clique em **Criar** para criar um novo aplicativo. 
1. Selecione a guia **Energia** e clique em **Criar aplicativo** no bloco de aplicativo **Monitoramento de medidor inteligente**.

    > [!div class="mx-imgBorder"]
    > ![Compilar o aplicativo](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

1. **Criar aplicativo** abrirá o formulário **Novo aplicativo**. Preencha os detalhes solicitados, conforme mostrado na figura abaixo:
    * **Nome do aplicativo**: escolha o nome para seu aplicativo do IoT Central. 
    * **URL**: escolha uma URL do IoT Central, a plataforma verificará sua exclusividade.
    * **Avaliação gratuita de 7 dias**: se você já tiver uma assinatura do Azure, a configuração padrão será recomendada. Caso não tenha uma assinatura do Azure, comece com uma avaliação gratuita.
    * **Informações de Cobrança**: o aplicativo é gratuito. é necessário informar os detalhes em Diretório, Assinatura do Azure e Região para provisionar os recursos para seu aplicativo.
    * Clique no botão **Criar** na parte inferior da página e seu aplicativo será criado em cerca de um minuto.

        ![Formulário de novo aplicativo](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![Informações de cobrança do formulário de novo aplicativo](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)

### <a name="verify-the-application-and-simulated-data"></a>Verifique o aplicativo e os dados simulados

O aplicativo de medidor inteligente recém-criado é seu e você pode modificá-lo a qualquer momento. Vamos garantir que o aplicativo esteja implantado e que funcione conforme o esperado antes de modificá-lo.

Para verificar a criação do aplicativo e a simulação de dados, acesse o **Painel**. Se você puder ver os blocos com alguns dados, a implantação do aplicativo terá sido bem-sucedida. A simulação de dados pode levar algum tempo para gerar os dados, portanto, aguarde 1 ou 2 minutos. 

## <a name="application-walk-through"></a>Passo a passo do aplicativo
Após você implantar com êxito o modelo de aplicativo, ele vem com o dispositivo de medidor inteligente de exemplo, o modelo de dispositivo e um painel. 

Adatum é uma empresa de energia fictícia, que monitora e gerencia medidores inteligentes. No painel de monitoramento de medidor inteligente, você vê as propriedades do medidor inteligente, os dados e os comandos de exemplo. Ele permite que os operadores e as equipes de suporte executem proativamente as seguintes atividades antes que ocorram incidentes de suporte: 
* Examinar as informações mais recentes do medidor e sua localização de instalação no mapa
* Verificar proativamente a rede do medidor e o status da conexão 
* Monitorar leituras de tensão mínima e máxima para manter a integridade da rede 
* Examinar tendências de energia, potência e tensão para capturar padrões anormais 
* Acompanhar o consumo de energia total para fins de planejamento e cobrança
* Comandar e controlar operações, como reconectar o medidor e atualizar a versão do firmware. No modelo, os botões de comando mostram as funcionalidades possíveis e não enviam comandos reais. 

> [!div class="mx-imgBorder"]
> ![Painel de monitoramento do medidor inteligente](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Dispositivos
O aplicativo vem com um dispositivo de medidor inteligente de exemplo. Você pode ver os detalhes do dispositivo clicando na guia **Dispositivos**.

> [!div class="mx-imgBorder"]
> ![Dispositivos de medidor inteligente](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Clique no link do dispositivo de exemplo **SM0123456789** para ver os detalhes. Na página **Atualizar Propriedades**, você pode atualizar as propriedades graváveis do dispositivo e visualizar os valores atualizados no painel.

> [!div class="mx-imgBorder"]
> ![Propriedades do medidor inteligente](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Modelo de dispositivo
Clique na guia **Modelos de dispositivo** para ver o modelo de dispositivo do medidor inteligente. O modelo tem uma interface predefinida para Dados, Propriedade, Comandos e Exibições.

> [!div class="mx-imgBorder"]
> ![Modelos de dispositivo de medidor inteligente](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Limpar os recursos
Se optar por não continuar usando este aplicativo, exclua-o seguindo estas etapas:

1. No painel esquerdo, abra a guia Administração
1. Selecione Configurações do aplicativo e clique no botão Excluir na parte inferior da página. 

    > [!div class="mx-imgBorder"]
    > ![Excluir aplicativo](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a arquitetura do aplicativo de medidor inteligente, confira:

> [!div class="nextstepaction"]
> [Arquitetura de aplicativo de medidor inteligente](./concept-iot-central-smart-meter-app.md)
