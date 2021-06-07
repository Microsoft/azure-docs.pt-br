---
title: 'Tutorial: Criar um ambiente – Azure Time Series Insights | Microsoft Docs'
description: Saiba como criar um ambiente do Azure Time Series Insights populado com os dados de dispositivos simulados.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: ea57a623546df99408d488da4522411f55ab0dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101094362"
---
# <a name="tutorial-create-an-azure-time-series-insights-gen1-environment"></a>Tutorial: Criar um ambiente do Azure Time Series Insights Gen1

> [!CAUTION]
> Esse é um artigo do Gen1.

Este tutorial orientará você pelo processo de criação de um ambiente do Azure Time Series Insights populado com os dados de dispositivos simulados. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Criar um ambiente do Azure Time Series Insights.
> * Criar uma solução de simulação de dispositivo que contém um hub IoT.
> * Conectar o ambiente do Azure Time Series Insights ao hub IoT.
> * Executar uma simulação de dispositivo para transmitir dados para o ambiente do Azure Time Series Insights.
> * Verificar os dados telemétricos simulados.

> [!IMPORTANT]
> Inscreva-se em uma [assinatura do Azure gratuita](https://azure.microsoft.com/free/) se já não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* Sua conta de entrada do Azure também precisa ser um membro da função **Proprietário** da assinatura. Para obter mais informações, leia [Atribuir funções do Azure usando o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Ver um vídeo

Saiba como usar um acelerador de solução de IoT do Azure para gerar dados e começar a usar o Azure Time Series Insights.

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Visão geral

O ambiente do Azure Time Series Insights é o local em que os dados do dispositivo são coletados e armazenados. Uma vez armazenados, o [Gerenciador do Azure Time Series Insights](time-series-quickstart.md) e a [API de Consulta do Azure Time Series Insights](/rest/api/time-series-insights/gen1-query-api) poderão ser usados para consultar e analisar os dados.

O Hub IoT do Azure é a origem do evento usada por todos os dispositivos (simulados ou físicos) no tutorial para conectar e transmitir com segurança os dados à sua nuvem do Azure.

Este tutorial também usa um [acelerador de solução de IoT](https://www.azureiotsolutions.com) para gerar e transmitir dados telemétricos de exemplo ao Hub IoT.

>[!TIP]
> Os [aceleradores de solução de IoT](https://www.azureiotsolutions.com) fornecem soluções de nível empresarial pré-configuradas que você pode usar para acelerar o desenvolvimento de soluções personalizadas de IoT.

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivo

Primeiramente, crie a solução de simulação de dispositivo que gera dados de teste para popular o ambiente do Azure Time Series Insights.

1. Em uma janela ou uma guia separada, acesse [azureiotsolutions.com](https://www.azureiotsolutions.com). Entre usando a mesma conta de assinatura do Azure e selecione o acelerador de **Simulação de Dispositivo**.

   [![Executar o acelerador de Simulação de Dispositivo](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. Selecione **Experimentar Agora**. Em seguida, insira os parâmetros necessários na página da **solução Criar Simulação de Dispositivo**.

   Parâmetro|Descrição
   ---|---
   **Nome da implantação** | Esse valor exclusivo é usado para criar um grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos.
   **Assinatura do Azure** | Especifique a mesma assinatura usada para criar o ambiente do Azure Time Series Insights na seção anterior.
   **Opções de implantação** | Selecione **Provisionar novo Hub IoT** de modo a criar um novo hub IoT específico para este tutorial.
   **Localização do Azure** | Especifique a mesma região usada para criar o ambiente do Azure Time Series Insights na seção anterior.

   Quando terminar, selecione **Criar** para provisionar os recursos do Azure da solução. Esse processo pode levar até 20 minutos para ser concluído.

   [![Provisionar a solução de simulação de dispositivo](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. Após a conclusão do provisionamento, duas atualizações serão exibidas, notificando que o estado da implantação foi movido de **Provisionamento** para **Pronto**.

   >[!IMPORTANT]
   > Não insira seu acelerador de solução ainda. Mantenha essa página da Web aberta porque você retornará a ela mais tarde.

   [![Provisionamento da solução de simulação de dispositivo concluído](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Agora, inspecione os recursos recentemente criados no portal do Azure. Na página **Grupos de recursos**, observe que um grupo de recursos foi criado usando o **Nome da solução** fornecido na última etapa. Anote os recursos que foram criados para a simulação do dispositivo.

   [![Recursos de simulação do dispositivo](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Criar um ambiente

Em segundo lugar, crie um ambiente do Azure Time Series Insights na sua assinatura do Azure.

1. Entre no [portal do Azure](https://portal.azure.com) usando sua conta da assinatura do Azure.
1. Selecione **+ Criar um recurso** no canto superior esquerdo.
1. Selecione a categoria **Internet das Coisas** e depois selecione **Time Series Insights**.

   [![Selecione o recurso de ambiente do Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. Na página **Ambiente do Time Series Insights**, preencha os parâmetros necessários.

   Parâmetro|Descrição
   ---|---
   **Nome do ambiente** | Escolha um nome exclusivo para o ambiente do Azure Time Series Insights. Os nomes são usados pelo Gerenciador do Azure Time Series Insights e pelas [APIs de Consulta](/rest/api/time-series-insights/gen1-query).
   **Assinatura** | As assinaturas são contêineres para recursos do Azure. Escolha uma assinatura para criar o ambiente do Azure Time Series Insights.
   **Grupo de recursos** | Um grupo de recursos é um contêiner para os recursos do Azure. Escolha um grupo de recursos existente ou crie um para o recurso de ambiente do Azure Time Series Insights.
   **Localidade** | Escolha uma região do data center para o seu ambiente do Azure Time Series Insights. Para evitar latência adicional, crie o ambiente do Azure Time Series Insights na mesma região que outros recursos de IoT.
   **Camada** | Escolha a taxa de transferência necessária. Selecione **S1**.
   **Capacidade** | A capacidade é o multiplicador aplicado à taxa de entrada e à capacidade de armazenamento associada à SKU selecionada. Você pode alterar a capacidade após a criação. Selecione uma capacidade de **1**.

   Quando terminar, selecione **Avançar: Origem do evento** para prosseguir para a próxima etapa.

   [![Criar um recurso do ambiente do Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Agora, conecte o ambiente do Azure Time Series Insights ao hub IoT criado pelo Acelerador de Solução. Defina **Selecionar um hub** como `Select existing`. Em seguida, escolha o hub IoT criado pelo Acelerador de Solução ao definir **Nome do Hub IoT**.

   [![Conectar o ambiente do Azure Time Series Insights ao hub IoT criado](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Por fim, selecione **Examinar + criar**.

1. Verifique o painel **Notificações** para monitorar a conclusão da implantação.

   [![Implantação do ambiente do Azure Time Series Insights bem-sucedida](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Executar simulação de dispositivo

Agora que a implantação e a configuração inicial foram concluídas, popule o ambiente do Azure Time Series Insights com os dados de exemplo dos [dispositivos simulados criados pelo acelerador](#create-a-device-simulation).

Com o hub IoT, um aplicativo Web do Serviço de Aplicativo do Azure foi gerado para criar e transmitir a telemetria do dispositivo simulado.

1. Volte para o [Painel de aceleradores da solução](https://www.azureiotsolutions.com/Accelerators#dashboard). Entre novamente, se necessário, usando a mesma conta do Azure que você usou neste tutorial. Selecione a "Solução de Dispositivo" e, em seguida, **Acessar o acelerador de solução** para iniciar a solução implantada.

   [![Painel de aceleradores de solução](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. O aplicativo Web da simulação de dispositivo começa solicitando que você conceda ao aplicativo Web a permissão **Conectá-lo e ler seu perfil**. Com essa permissão, o aplicativo pode recuperar as informações de perfil do usuário necessárias para dar suporte ao funcionamento do aplicativo.

   [![Consentimento do aplicativo Web da simulação de dispositivo](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Selecione **+ Nova simulação**. Depois que a página **Instalação da simulação** for carregada, insira os parâmetros necessários.

   Parâmetro|Descrição
   ---|---
   **Hub IoT de destino** | Selecione **Usar o Hub IoT pré-provisionado**.
   **Modelo do dispositivo** | Selecione **Resfriador**.
   **Número de dispositivos**  | Insira `10` em **Quantidade**.
   **Frequência de telemetria** | Insira `10` segundos.
   **Duração da simulação** | Selecione **Terminar em:** e insira `5` minutos.

   Quando terminar, selecione **Iniciar Simulação**. A simulação é executada por um total de 5 minutos. Ela gera dados de 1.000 dispositivos simulados a cada 10 segundos.

   [![Instalação da simulação de dispositivo](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Enquanto a simulação é executada, observe que os campos **Total de mensagens** e **Mensagens por segundo** são atualizados, em intervalos de aproximadamente 10 segundos. A simulação é concluída após cerca de 5 minutos, levando você de volta à **Instalação da simulação**.

   [![Simulação de dispositivo em execução](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Verificar os dados telemétricos

Nesta seção final, verifique se os dados telemétricos foram gerados e armazenados no ambiente do Azure Time Series Insights. Para verificar os dados, use o Gerenciador do Azure Time Series Insights, que é usado para consultar e analisar dados telemétricos.

1. Retorne à página **Visão geral** do grupo de recursos do ambiente do Azure Time Series Insights. Selecione o ambiente do Azure Time Series Insights.

   [![Ambiente e grupo de recursos do ambiente do Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Na página **Visão geral** do ambiente do Azure Time Series Insights, selecione a **URL do Gerenciador do Time Series Insights** para abrir o Gerenciador do Azure Time Series Insights.

   [![Gerenciador do Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. O Gerenciador do Azure Time Series Insights é carregado e autenticado usando sua conta do portal do Azure. Inicialmente, será exibida a área do gráfico com a qual o ambiente do Azure Time Series Insights estava populado juntamente com os dados telemétricos simulados. Para filtrar um intervalo de tempo mais estreito, selecione a lista suspensa no canto superior esquerdo. Insira um intervalo de tempo grande o suficiente para abranger a duração da simulação de dispositivo. Em seguida, selecione a lupa de pesquisa.

   [![Filtro de intervalo de tempo do Gerenciador do Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. A restrição do intervalo de tempo permite que o gráfico amplie as intermitências distintas da transferência de dados para o hub IoT e o ambiente do Azure Time Series Insights. Observe também o texto **Streaming concluído** no canto superior direito, que mostra o número total de eventos encontrados. Arraste também o controle deslizante **Tamanho do intervalo** para controlar a granularidade do gráfico.

   [![Exibição filtrada do intervalo de tempo do Gerenciador do Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Por fim, clique também em uma região para filtrar um intervalo. Em seguida, clique com o botão direito do mouse e use **Explorar eventos** para mostrar os detalhes do evento na exibição **Eventos** de tabela.

   [![Eventos e exibição filtrada do intervalo de tempo do Gerenciador do Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Limpar os recursos

Este tutorial cria vários serviços que executam o Azure para dar suporte ao ambiente do Azure Time Series Insights e à solução de simulação de dispositivo. Para removê-los, volte para o portal do Azure.

No menu à esquerda no portal do Azure:

1. Selecione o ícone **Grupos de recursos**. Em seguida, selecione o grupo de recursos criado para o ambiente do Azure Time Series Insights. Na parte superior da página, selecione **Excluir grupo de recursos**, insira o nome do grupo de recursos e selecione **Excluir**.

1. Selecione o ícone **Grupos de recursos**. Em seguida, selecione o grupo de recursos criado pelo acelerador de solução de simulação de dispositivo. Na parte superior da página, selecione **Excluir grupo de recursos**, insira o nome do grupo de recursos e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
>
> * Criar um ambiente do Azure Time Series Insights.
> * Criar uma solução de simulação de dispositivo que contém um hub IoT.
> * Conectar o ambiente do Azure Time Series Insights ao hub IoT.
> * Executar uma simulação de dispositivo para transmitir dados para o ambiente do Azure Time Series Insights.
> * Verificar os dados telemétricos simulados.

Agora que você sabe como criar o próprio ambiente do Azure Time Series Insights, saiba como criar um aplicativo Web que consome dados de um ambiente do Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Ler exemplos de visualização do SDK do cliente hospedado](https://tsiclientsample.azurewebsites.net/)