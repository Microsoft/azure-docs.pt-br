---
title: Tutorial do centro de distribuição digital para IoT | Microsoft Docs
description: Tutorial do modelo de aplicativo do centro de distribuição digital para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 40b0d7eeb77f165c50c5470e1b5869e240d36534
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702684"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Tutorial: Implantar e usar um modelo de aplicativo do centro de distribuição digital

Este tutorial mostra como iniciar a implantação de um modelo de aplicativo do **centro de distribuição digital** para IoT Central. Veja como implantar o modelo, o conteúdo incluído pronto para uso e o que você poderá fazer em seguida.

Neste tutorial, você aprenderá a: 

> [!div class="checklist"]
> * Criar um aplicativo do centro de distribuição digital 
> * Explorar o aplicativo 

## <a name="prerequisites"></a>Pré-requisitos
* Não há pré-requisitos específicos para implantar este aplicativo
* É recomendável ter uma assinatura do Microsoft Azure, mas é possível também experimentá-lo sem ela

## <a name="create-digital-distribution-center-application-template"></a>Criar um modelo de aplicativo do centro de distribuição digital

Para criar um aplicativo, faça o seguinte:

1. Navegue até o site do Gerenciador de Aplicativos do Azure IoT Central. Selecione o **Build**, na barra de navegação à esquerda, e clique na guia **Varejo**.

    :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-homepage.png" alt-text="Modelo de aplicativo do centro de distribuição digital":::
1. Selecione a guia **Varejo** e, em seguida, **Criar aplicativo** sob **aplicativo do centro de distribuição digital**

1. A opção **Criar aplicativo** abrirá o formulário Novo aplicativo e preencherá os detalhes solicitados, conforme mostrado abaixo.
   **Nome do aplicativo**: use o nome sugerido padrão ou crie um nome de aplicativo amigável.
   **URL**: use a URL sugerida padrão ou crie uma URL amigável exclusiva de fácil memorização. Em seguida, recomendamos usar a configuração padrão, caso você já tenha uma assinatura do Azure. Você pode começar com o plano de preço de avaliação gratuita de 7 dias e optar por converter em um plano de preço padrão a qualquer momento antes que a avaliação gratuita expire.
   **Informações de Cobrança**: para provisionar os recursos, é necessário informar os detalhes em Diretório, Assinatura do Azure e Região.
   **Criar**: selecione Criar, no final da página, para implantar o aplicativo.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create.png" alt-text="Captura de tela mostrando como criar um aplicativo com base no modelo de aplicativo do centro de distribuição digital":::

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create-billinginfo.png" alt-text="Captura de tela mostrando as opções de cobrança quando você cria o aplicativo":::

## <a name="walk-through-the-application-dashboard"></a>Explorar o painel de aplicativo 

Depois de implantar com êxito o modelo de aplicativo, o painel padrão será um portal com foco no operador do Centro de Distribuição. A Northwind Trader é um provedor de soluções fictício do Centro de Distribuição que gerencia sistemas de transportadoras. 

Nesse painel, você verá um gateway e uma câmera atuando como dispositivo IoT. O gateway está fornecendo telemetria sobre os pacotes, como as propriedades de válido, inválido, não identificado e tamanho, juntamente com as propriedades do dispositivo gêmeo associado. Todos os comandos downstream são executados em dispositivos IoT, como uma câmera. Esse painel é pré-configurado para demonstrar a atividade crítica das operações do dispositivo do Centro de Distribuição.

O painel é organizado logicamente para mostrar as funcionalidades de gerenciamento de gateway do Azure IoT e do dispositivo IoT.  
   * Você pode executar tarefas de controle e comando do gateway.
   * Gerencie todas as câmeras que fazem parte da solução. 

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando o painel do centro de distribuição digital](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Modelo de dispositivo

Clique na guia Modelos de dispositivo para ver o modelo de funcionalidade do gateway. O modelo de funcionalidade é estruturado em duas interfaces diferentes: **Câmera** e **Gateway de Distribuição Digital**.

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando o modelo de dispositivo de gateway de distribuição digital no aplicativo](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Câmera** – essa interface organiza todas as funcionalidades de comando específicas de câmera. 

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando a interface da câmera no modelo de dispositivo do gateway de distribuição digital](./media/tutorial-iot-central-ddc/ddc-camera.png)

**centro de distribuição digital** – essa interface representa toda a telemetria proveniente de uma câmera, as propriedades do dispositivo gêmeo definidas na nuvem e as informações do gateway.

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando a interface de gateway de distribuição digital no modelo de dispositivo de gateway de distribuição digital](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Comandos de gateway
Esta interface organiza todas as funcionalidades de comando do gateway.

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando a interface de comandos do gateway no modelo de dispositivo de gateway de distribuição digital](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Regras
Selecione a guia Regras para ver duas regras diferentes neste modelo de aplicativo. Essas regras são configuradas para enviar notificações por email aos operadores para fins de investigações posteriores.

 **Excesso de alertas de pacotes inválidos** – essa regra é disparada quando a câmera detecta um grande número de pacotes inválidos que fluem pelo sistema da transportadora.
 
**Pacote grande** – essa regra é disparada quando a câmera detecta um grande pacote cuja qualidade não pode ser inspecionada. 

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando a lista de regras no aplicativo do centro de distribuição digital](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Trabalhos
Selecione a guia Trabalhos para exibir cinco trabalhos existentes como parte deste modelo de aplicativo: Você pode aproveitar o recurso Trabalhos para executar operações em toda a solução. Nesse caso, os trabalhos do centro de distribuição digital estão usando a funcionalidade de gêmeos e os comandos do dispositivo para executar tarefas, como:
   * Calibrar a câmera antes de iniciar a detecção do pacote. 
   * Atualizar periodicamente o firmware da câmera.
   * Modificar o intervalo de telemetria para gerenciar o carregamento de dados.

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando a lista de trabalhos no aplicativo do centro de distribuição digital](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Limpar os recursos
Caso não pretenda usar esse aplicativo, acesse **Administração** > **Configurações do Aplicativo** e clique em **Excluir** para excluir o modelo de aplicativo.

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando como excluir o aplicativo quando você terminar](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a arquitetura da solução do centro de distribuição digital:

> [!div class="nextstepaction"]
> [conceito de centro de distribuição digital](./architecture-digital-distribution-center.md)
