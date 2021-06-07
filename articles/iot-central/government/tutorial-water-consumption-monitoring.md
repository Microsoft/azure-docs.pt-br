---
title: 'Tutorial: Criar um aplicativo de monitoramento do consumo de água com o Azure IoT Central'
description: 'Tutorial: Saiba como criar um aplicativo de monitoramento do consumo de água usando modelos de aplicativo do Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 476a0c2d75dcbee5c1ed4d758e0cbc9d4726ff95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587189"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Tutorial: Criar um aplicativo de monitoramento do consumo de água com o Azure IoT Central

Este tutorial mostra como criar um aplicativo de monitoramento do consumo de água do Azure IoT Central usando o modelo de aplicativo de monitoramento do consumo de água do Azure IoT Central.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Usar o modelo de monitoramento do consumo de água do Azure IoT Central para criar seu aplicativo de monitoramento do consumo de água.
> * Explorar e personalizar o painel do operador.
> * Explorar modelos de dispositivo.
> * Explorar dispositivos simulados.
> * Explorar e configurar regras.
> * Configurar trabalhos.
> * Personalizar a identidade visual do seu aplicativo usando white labeling.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, poderá criar uma na [página de entrada do Azure](https://aka.ms/createazuresubscription).

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Criar um aplicativo de monitoramento do consumo de água com o Azure IoT Central

Nesta seção, você usará o modelo de monitoramento do consumo de água do Azure IoT Central para criar seu aplicativo de monitoramento do consumo de água no Azure IoT Central.

Para criar um novo aplicativo de monitoramento de consumo de água do Azure IoT Central:

1. Acesse o site da [home page do Azure IoT Central](https://aka.ms/iotcentral).

    Se você tiver uma assinatura do Azure, entre com as credenciais usadas para acessá-la. Caso contrário, conecte-se com uma conta Microsoft.

    ![Insira a conta da sua organização](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. Selecione **Criar** no painel esquerdo e a guia **Governo**. A página **Governo** exibe vários modelos de aplicativos governamentais.

   ![Criar modelos de aplicativos governamentais](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Selecione o  modelo de aplicativo de **monitoramento de consumo de água**.
Este modelo inclui um exemplo de modelo de dispositivo de consumo de água, um dispositivo simulado, um painel do operador e regras de monitoramento pré-configuradas.

1. Selecione **Criar aplicativo** para abrir o formulário de criação de **Novo aplicativo** com os seguintes campos:
    * **Nome do aplicativo**: por padrão, o aplicativo usa *Monitoramento do consumo da água* seguido por uma cadeia de caracteres de ID exclusiva gerada pelo Azure IoT Central. Opcionalmente, escolha um Nome de aplicativo amigável. Você também pode alterar o nome do aplicativo mais tarde.
    * **URL**: o Azure IoT Central gera automaticamente uma URL com base no nome do aplicativo. Você pode optar por atualizar a URL de sua preferência. Você também pode alterar a URL mais tarde.
    * Caso você tenha uma assinatura do Azure, insira suas informações de **Diretório**, **Assinatura do Azure** e **Localização**. Caso não tenha uma assinatura, selecione a opção de **avaliação gratuita de 7 dias** e preencha as informações de contato necessárias.

    Para obter mais informações sobre diretórios e assinaturas, confira o [início rápido Criar um aplicativo](../core/quick-deploy-iot-central.md).

1. Selecione **Criar** na parte inferior da página.

    ![A página Novo aplicativo do Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Página Informações de cobrança do Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

Você acabou de criar um aplicativo de monitoramento do consumo de água usando o modelo de monitoramento do consumo de água do Azure IoT Central.

O aplicativo de monitoramento do consumo de água é fornecido com os seguintes itens pré-configurados:

* Exemplos de painéis do operador.
* Exemplos de modelos de dispositivo de válvula e fluxo de água predefinidos.
* Dispositivos de válvula inteligente e fluxo de água simulados.
* Regras e trabalhos.
* Exemplo de identidade visual com white labeling.

O aplicativo é seu, e você pode modificá-lo a qualquer momento. Agora vamos explorar o aplicativo e fazer algumas personalizações.

## <a name="explore-and-customize-the-operator-dashboard"></a>Explorar e personalizar o painel do operador

Depois que você criar o aplicativo, a amostra **Painel de consumo de água da Wide World** será aberta.

   ![Painel de monitoramento do consumo de água](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Como construtor, você pode criar e personalizar modos de exibição no painel para uso dos operadores. Vamos explorar o painel antes de você tentar personalizá-lo.

> [!NOTE]
> Todos os dados exibidos no painel são baseados em dados do dispositivo simulado, que serão explorados na próxima seção.
  
O painel é composto por diferentes tipos de blocos:

* **Bloco de imagem da concessionária Wide World Water**: o primeiro bloco do painel é um bloco de imagem da concessionária de abastecimento de água fictícia Wide World Water. Personalize o bloco inserindo a própria imagem ou removendo-a.
* **Bloco de KPI do fluxo de água médio**: o bloco de KPI está configurado para ser exibido como um exemplo *a média nos últimos 30 minutos*. Personalize o bloco de KPI e defina-o com outro tipo e outro intervalo de tempo.
* **Blocos de comando do dispositivo**: esses blocos incluem os blocos **Fechar válvula**, **Abrir válvula** e **Definir posição da válvula**. A seleção dos comandos leva você para a página de comando do dispositivo simulado. No Azure IoT Central, um *comando* é um tipo de *funcionalidade do dispositivo*. Exploraremos esse conceito mais adiante na seção "Modelo de dispositivo" deste tutorial.
* **Mapa da área de distribuição de água**: o mapa usa os Azure Mapas, que podem ser configurados diretamente no Azure IoT Central. A peça de mapa exibe a localização do dispositivo. Posicione o cursor sobre o mapa e experimente os controles no mapa, como *ampliar*, *reduzir* ou *expandir*.

    ![Mapa do painel de monitoramento do consumo de água](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Gráfico de linhas do fluxo de água médio** e **gráfico de linhas da condição ambiental**: visualize uma ou várias telemetrias de dispositivo plotadas como um gráfico de linhas em um intervalo de tempo desejado.
* **Gráfico do mapa de calor da pressão da válvula média**: escolha o tipo de visualização do mapa de calor dos dados telemétricos do dispositivo que você deseja ver distribuídos em um intervalo de tempo com um índice de cores.
* **Bloco de conteúdo de redefinição dos limites de alerta**: inclua blocos de conteúdo de chamada para ação e insira um link para uma página de ação. Nesse caso, a redefinição dos limites de alerta leva você ao aplicativo **Trabalhos**, em que você pode executar atualizações nas propriedades do dispositivo. Exploraremos essa opção mais adiante na seção "Configurar trabalhos" deste tutorial.
* **Blocos de propriedades**: o painel exibe **Informações operacionais da válvula**, **Limites de alerta de fluxo** e **Informações de manutenção**.

### <a name="customize-the-dashboard"></a>Personalizar o painel

Como construtor, você pode personalizar as exibições no painel dos operadores.

1. Selecione **Editar** para personalizar o **painel de consumo da água da Wide World**. Personalize o painel selecionando o menu **Editar**. Depois que o painel estiver no modo de **edição**, você poderá adicionar novos blocos ou configurá-lo.

     ![Editar o painel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Selecione **+ Novo** para criar um painel e configurá-lo do zero. Você pode ter vários painéis e navegar entre eles no menu do painel.

## <a name="explore-the-device-template"></a>Explorar o modelo de dispositivo

Um modelo de dispositivo no Azure IoT Central define a funcionalidade de um dispositivo, que pode ser de telemetria, propriedades ou comando. Como construtor, você pode definir um ou mais modelos de dispositivo no Azure IoT Central que representam a funcionalidade dos dispositivos que você conectará.

O aplicativo de monitoramento do consumo de água é fornecido com dois modelos de dispositivo de referência que representam um *medidor de fluxo* e um dispositivo de *válvula inteligente*.

Para exibir do modelo de dispositivo:

1. Selecione **Modelos de dispositivo** no painel esquerdo do seu aplicativo no Azure IoT Central. Na lista **Modelos de dispositivo**, você verá dois modelos de dispositivo, **Válvula Inteligente** e **Medidor de fluxo**.

   ![Modelo de dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Selecione o modelo de dispositivo **Medidor de fluxo** e familiarize-se com as funcionalidades do dispositivo.

     ![Medidor de fluxo do modelo de dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Personalizar o modelo de dispositivo

Para personalizar o modelo de dispositivo:

1. Acesse **Personalizar** no menu **Modelos de dispositivo**.
1. Encontre o tipo de telemetria `Temperature`.
1. Atualize o **Nome de Exibição** de `Temperature` para `Reported temperature`.
1. Atualize a unidade de medida ou defina o **Valor mín.** e o **Valor máx.** .
1. Selecione **Salvar** para salvar as alterações.

### <a name="add-a-cloud-property"></a>Adicionar uma propriedade de nuvem

1. Acesse **Propriedades de Nuvem** no menu **Modelos de dispositivo**.
1. Adicione uma nova propriedade de nuvem selecionando **+ Adicionar Propriedade de Nuvem**.
    No Azure IoT Central, você pode adicionar uma propriedade que seja relevante para o dispositivo. Por exemplo, uma propriedade de nuvem pode ser um limite de alerta específico de uma área de instalação, informações de ativos ou outras informações de manutenção.
1. Selecione **Salvar** para salvar as alterações.

### <a name="views"></a>Exibições

O modelo de dispositivo do monitor de consumo de água é fornecido com exibições predefinidas. Explore as exibições e faça atualizações. As exibições definem como os operadores veem os dados do dispositivo, mas também inserem propriedades de nuvem.

  ![Exibições do modelo de dispositivo](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publicar

Caso tenha feito alguma alteração, lembre-se de **Publicar** o modelo de dispositivo.

### <a name="create-a-new-device-template"></a>Criar um novo modelo de dispositivo

Selecione **+ Novo** para criar um modelo de dispositivo e siga o processo de criação.
Você pode criar um modelo de dispositivo personalizado do zero ou escolher um modelo de dispositivo no Catálogo de Dispositivos do Azure.

## <a name="explore-simulated-devices"></a>Explorar dispositivos simulados

No Azure IoT Central, você também pode criar dispositivos simulados para testar o aplicativo e o modelo de dispositivo. O aplicativo de monitoramento do consumo de água tem dois dispositivos simulados mapeados para os modelos de dispositivo **Medidor de fluxo** e **Válvula Inteligente**.

### <a name="view-the-devices"></a>Exibir os dispositivos

1. Selecione **Dispositivos** > **Todos os dispositivos** no painel esquerdo.

   ![Painel de Todos os dispositivos](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Selecione **Válvula Inteligente 1**.

    ![Válvula Inteligente 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. Na guia **Comandos**, você pode ver os três comandos de dispositivo (**Fechar válvula**, **Abrir válvula** e **Definir posição da válvula**) que são as funcionalidades definidas no modelo de dispositivo **Válvula Inteligente**.

1. Explore a guia **Propriedades do Dispositivo** e a guia **Painel do Dispositivo**.

> [!NOTE]
> Observe que todas as guias foram configuradas por meio das exibições do modelo de dispositivo.

### <a name="add-new-devices"></a>Adicionar novos dispositivos

Adicione novos dispositivos selecionando **+ Novo** na guia **Dispositivos**.

## <a name="explore-and-configure-rules"></a>Explorar e configurar regras

No Azure IoT Central, você poderá criar regras para monitorar automaticamente a telemetria do dispositivo e disparar ações quando uma ou mais condições forem atendidas. As ações podem incluir o envio de notificações por email, o gatilho de uma ação do Microsoft Power Automate ou uma ação de webhook para enviar dados a outros serviços.

O aplicativo de monitoramento do consumo de água criado tem três regras pré-configuradas.

### <a name="view-rules"></a>Exibir regras

1. Selecione **Regras** no painel esquerdo.

   ![Painel Regras](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Selecione **Alerta de pH alto**, que é uma das regras pré-configuradas no aplicativo.

     ![Alerta de pH alto](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    A regra `High flow alert` está configurada para verificar se a condição `Acidity (pH)` é `greater than``Max flow threshold`. O limite máximo de fluxo é uma propriedade de nuvem definida no modelo de dispositivo **Válvula Inteligente**. O valor de `Max flow threshold` é definido por instância de dispositivo.

Agora, vamos criar uma ação de email.

Para adicionar uma ação à regra:

1. Selecione **+ Email**.
1. Insira **Alerta de pH alto** como o **Nome de exibição** amigável para a ação.
1. Insira o endereço de email associado à sua conta do Azure IoT Central em **Para**.
1. Opcionalmente, insira uma observação a ser incluída no texto do email.
1. Selecione **Concluído** para concluir a ação.
1. Selecione **Salvar** para salvar e ativar a nova regra.

Em alguns minutos, você deverá receber um email depois que a condição configurada for atendida.

> [!NOTE]
> O aplicativo envia um email toda vez que uma condição é atendida. Selecione **Desabilitar** para desabilitar a regra para parar de receber emails da regra automatizada.
  
Para criar uma nova regra:

* Selecione **+ Novo** na guia **Regras** no painel esquerdo.

## <a name="configure-jobs"></a>Configurar trabalhos

No Azure IoT Central, os trabalhos permitem que você dispare atualizações de propriedade de dispositivo ou de nuvem em vários dispositivos. Além das propriedades, você também pode usar trabalhos para disparar comandos de dispositivo em vários dispositivos. O Azure IoT Central automatiza o fluxo de trabalho para você.

1. Selecione **Trabalhos** no painel esquerdo.
1. Selecione **+ Novo** e configure um ou mais trabalhos.

## <a name="customize-your-application"></a>Personalizar seu aplicativo

Como um criador, você pode alterar várias configurações para personalizar a experiência do usuário em seu aplicativo.

1. Selecione **Administração** > **Personalizar seu aplicativo**.
1. Para escolher uma imagem a ser carregada como o **Logotipo do aplicativo**, selecione o botão **Alterar**.
1. Para escolher uma imagem de **Ícone do navegador** que será exibida nas guias do navegador, selecione o botão **Alterar**.
1. Você também pode substituir as **Cores do navegador** padrão adicionando códigos de cores hexadecimais HTML.

   ![Seleções de logotipo do aplicativo, ícone do navegador e cores do navegador](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. Altere também as imagens do aplicativo selecionando **Administração** > **Configurações de aplicativo**. Para escolher uma imagem a ser carregada como a imagem do aplicativo, selecione o botão **Selecionar imagem**.
1. Por fim, altere também o **Tema** selecionando o ícone de **Configurações** no canto superior direito do aplicativo.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não pretende continuar usando este aplicativo, exclua-o.

1. Selecione **Administração** no painel esquerdo do seu aplicativo do Azure IoT Central.
1. Selecione **Configurações de aplicativo** e, em seguida, selecione **Excluir** na parte inferior da página.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre 

> [!div class="nextstepaction"]
> [Conceitos de monitoramento do consumo de água](./concepts-waterconsumptionmonitoring-architecture.md).
