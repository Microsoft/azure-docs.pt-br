---
title: 'Tutorial: Configurar um ambiente da Versão Prévia do Azure Time Series Insights | Microsoft Docs'
description: Saiba como configurar seu ambiente na Versão Prévia do Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: 824d24b97f192583a42192b3bb90eb1818e1aa18
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273005"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Tutorial: Configurar um ambiente de Versão Prévia do Azure Time Series Insights

Este tutorial orienta você pelo processo de criação de um ambiente PAYG (Pago Conforme o Uso) da Versão Prévia do Azure Time Series Insights. 

Neste tutorial, você aprenderá como:

* Criar um ambiente de Versão Prévia do Azure Time Series Insights.
* Conectar o ambiente de Versão Prévia do Azure Time Series Insights a um hub de eventos nos Hubs de Eventos do Azure.
* Executar uma amostra do acelerador de solução para transmitir dados para o ambiente da Versão Prévia do Azure Time Series Insights.
* Executar uma análise básica nos dados.
* Definir uma hierarquia e um tipo de Modelo de Série Temporal e associá-los às suas instâncias.

>[!TIP]
> Os [aceleradores de solução de IoT](https://www.azureiotsolutions.com/Accelerators) fornecem soluções de nível empresarial pré-configuradas que você pode usar para acelerar o desenvolvimento de soluções personalizadas de IoT.

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivo

Nesta seção, você criará três dispositivos simulados que enviam dados para uma instância do Hub IoT do Azure.

1. Acesse a [página de aceleradores de solução do IoT do Azure](https://www.azureiotsolutions.com/Accelerators). A página exibe vários exemplos pré-criados. Entre usando sua conta do Azure. Em seguida, selecione **Simulação de Dispositivo**.

   [![Página de aceleradores de solução do IoT do Azure](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Selecione **Experimentar Agora**.

1. Defina os parâmetros a seguir na página **Criar solução de Simulação de Dispositivo**:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome da implantação** | Insira um valor exclusivo para a criação de um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos. |
    | **Assinatura do Azure** | Selecione a assinatura que você usou para criar seu ambiente do Time Series Insights. |
    | **Localização do Azure** | Selecione a região que você usou para criar seu ambiente do Time Series Insights. |
    | **Opções de implantação** | Selecione **Provisionar um novo Hub IoT**. |
 
    Selecione **Criar solução**. Pode demorar até 20 minutos para concluir a implantação da solução.

    [![Página da solução Criar Simulação de Dispositivo](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Criar um ambiente PAYG de Versão Prévia do Time Series Insights

Esta seção descreve como criar um ambiente de visualização do Azure Time Series Insights e conectá-lo ao hub IoT criado pelo Acelerador de solução de IoT usando o [portal do Azure](https://portal.azure.com/).

1. Entre no portal do Azure usando sua conta da assinatura.

1. Selecione **Criar um recurso** > **Internet das Coisas** > **Time Series Insights**.

   [![Selecione Internet das Coisas e, em seguida, Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. No painel **Criar ambiente do Time Series Insights**, na guia **Básico**, defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome do ambiente** | Insira um nome exclusivo para o ambiente de Versão Prévia do Azure Time Series Insights. |
    | **Assinatura** | Insira a assinatura em que você deseja criar o ambiente de Versão Prévia do Azure Time Series Insights. É uma melhor prática usar a mesma assinatura que o restante de seus recursos de IoT criados pelo simulador de dispositivo. |
    | **Grupo de recursos** | Selecione um grupo de recursos existente ou crie um novo para o recurso de ambiente de Versão Prévia do Azure Time Series Insights. Um grupo de recursos é um contêiner para os recursos do Azure. É uma melhor prática usar o mesmo grupo de recursos que os outros recursos IoT criados pelo simulador de dispositivo. |
    | **Localidade** | Selecione uma região do datacenter para o ambiente da Versão Prévia do Azure Time Series Insights. Para evitar a latência extra, é melhor criar seu ambiente de Versão Prévia do Azure Time Series Insights na mesma região que outros recursos de IoT. |
    | **Camada** |  Selecione **PAYG** (*pagamento conforme o uso*). Essa é a SKU para a Versão Prévia do Azure Time Series Insights. |
    | **ID da Propriedade** | Insira um valor que identifique exclusivamente sua instância de série temporal. O valor inserido na caixa **ID da Propriedade** é imutável. Não é possível alterá-lo posteriormente. Para este tutorial, insira **iothub-connection-device-id**. Para saber mais sobre a ID do Time Series, confira [Melhores práticas para a escolha de uma ID do Time Series](./time-series-insights-update-how-to-id.md). |
    | **Nome da conta de armazenamento** | Insira um nome global exclusivo para uma nova conta de armazenamento a ser criada. |
   
   Selecione **Avançar: Origem do Evento**.

   [![Painel para criação de um ambiente do Time Series Insights](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Na guia **Origem do Evento**, defina os seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | --- |
   | **Criar uma origem do evento?** | Selecione **Sim**.|
   | **Nome** | Insira um valor exclusivo para o nome de origem do evento. |
   | **Tipo de fonte** | Selecione **Hub IoT**. |
   | **Selecione um hub** | Escolha **Selecionar existente**. |
   | **Assinatura** | Selecione a assinatura usada para o simulador de dispositivo. |
   | **Nome do Hub IoT** | Selecione o nome do hub IoT criado para o simulador de dispositivo. |
   | **Política de acesso do Hub IoT** | Selecione **iothubowner**. |
   | **Grupo de consumidores do Hub IoT** | Selecione **Novo**, insira um nome exclusivo e, em seguida, selecione **Adicionar**. O grupo de consumidores deve ser um valor exclusivo na Versão Prévia do Azure Time Series Insights. |
   | **Propriedade de carimbo de data/hora** | Esse valor é usado para identificar a propriedade **Carimbo de data/hora** em seus dados de telemetria de entrada. Para este tutorial, deixe essa caixa em branco. Esse simulador usa o carimbo de data/hora de entrada do Hub IoT, que é usado por padrão pelo Time Series Insights. |

   Selecione **Examinar + criar**.

   [![Configurar uma Fonte de Evento](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Na guia **Revisar + Criar** revise suas seleções e, em seguida, selecione **Criar**.

    [![Página Revisar + Criar com o botão Criar](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Você pode ver o status da implantação:

    [![Notificação de que a implantação foi concluída](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Você terá acesso ao ambiente da Versão Prévia do Azure Time Series Insights se você for o proprietário do locatário. Para verificar se você tem acesso:

   1. Pesquise o grupo de recursos e selecione o ambiente da Versão Prévia do Azure Time Series Insights:

      [![Ambiente selecionado](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Na página da Versão Prévia do Azure Time Series Insights, selecione **Políticas de Acesso a Dados**:

      [![Políticas de acesso de dados](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Verifique se suas credenciais estão listadas:

      [![Credenciais listadas](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Se as credenciais não estiverem listadas, será necessário conceder permissão a você mesmo para acessar o ambiente. Para saber mais sobre como definir permissões, leia [Conceder o acesso a dados](./time-series-insights-data-access.md).

## <a name="stream-data-into-your-environment"></a>Streaming de dados em seu ambiente

1. Vá até a [página de aceleradores de solução de IoT do Azure](https://www.azureiotsolutions.com/Accelerators). Encontre sua solução no painel do acelerador de solução. Em seguida, selecione **Iniciar**:

    [![Inicializar a solução de simulação de dispositivo](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Você será redirecionado para a página **Simulação de Dispositivo IoT do Microsoft Azure**. Selecione **Nova simulação** no canto superior direito da página.

    [![Página de simulação de IoT do Azure](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. No painel **Configuração de simulação**, defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | --- |
    | **Nome** | Insira um nome exclusivo para um simulador. |
    | **Descrição** | Insira uma definição. |
    | **Duração da simulação** | Defina essa opção como **Executar indefinidamente**. |
    | **Modelo do dispositivo** | **Nome**: Insira **Resfriador**. <br />**Valor**: Insira **3**. |
    | **Hub IoT de destino** | Defina essa opção como **Usar o Hub IoT pré-provisionado**. |

    [![Parâmetros para especificar](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Selecione **Iniciar simulação**.

    No painel de simulação do dispositivo, anote as informações exibidas para **Dispositivos ativos** e **Mensagens por segundo**.

    [![Painel de simulação de IoT do Azure](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data-in-your-environment"></a>Analisar dados em seu ambiente

Nesta seção, você executa análise básica em seus dados de série temporal usando o [gerenciador de Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Acesse o gerenciador da Versão Prévia do Azure Time Series Insights selecionando a URL na página do recurso no [portal do Azure](https://portal.azure.com/).

    [![A URL do gerenciador da Versão Prévia do Time Series Insights](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. No gerenciador, selecione o nó **Time Series Insights** para ver todas as instâncias da Versão Prévia do Azure Time Series Insights no ambiente.

    [![Lista de instâncias órfãs](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Selecione a primeira instância de série temporal. Em seguida, selecione **Mostrar pressão**.

    [![Instância de série temporal selecionada com o comando de menu para mostrar a pressão média](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Um gráfico da série temporal é exibido. Atualize o **intervalo** para **15s**.

    [![Gráfico de série temporal](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Repita a etapa 3 com as outras duas instâncias de série temporal. Você poderá exibir todas as séries temporais, conforme mostra neste gráfico:

    [![Gráfico de todas as séries temporais](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Na caixa de opção **Período de Tempo**, modifique o intervalo de tempo para ver as tendências de séries temporais da última hora:

    [![Definir o intervalo de tempo como uma hora](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definir e aplicar um modelo

Nesta seção, você aplicará um modelo para estruturar seus dados. Para concluir o modelo, você definirá tipos, hierarquias e instâncias. Para saber mais sobre modelagem de dados, confira [Modelo de Série Temporal](./time-series-insights-update-tsm.md).

1. No gerenciador, selecione a guia **Modelo**:

   [![Guia Modelo no gerenciador](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Selecione **Adicionar** para adicionar um tipo:

   [![O botão Adicionar para tipos](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Em seguida, defina três variáveis para o tipo: *pressão*, *temperatura* e *umidade*. No painel **Adicionar um Tipo**, defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome** | Insira **Resfriador**. |
    | **Descrição** | Insira **Esta é uma definição de tipo do Resfriador**. |

   * Para definir *pressão*, em **Variáveis**, defina os seguintes parâmetros:

     | Parâmetro | Ação |
     | --- | ---|
     | **Nome** | Insira **Pressão Média**. |
     | **Valor** | Selecione **pressão (Duplo)** . Talvez demore alguns minutos até que **Valor** seja preenchido automaticamente depois que a Versão Prévia do Azure Time Series Insights começar a receber eventos. |
     | **Operação de Agregação** | Selecione **AVG**. |

      [![Seleções para definição da pressão](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Para adicionar a próxima variável, selecione **Adicionar Variável**.

   * Defina a *temperatura*:

     | Parâmetro | Ação |
     | --- | ---|
     | **Nome** | Insira **Temperatura Média**. |
     | **Valor** | Selecione **temperatura (Duplo)** . Talvez demore alguns minutos até que **Valor** seja preenchido automaticamente depois que a Versão Prévia do Azure Time Series Insights começar a receber eventos. |
     | **Operação de Agregação** | Selecione **AVG**.|

      [![Seleções para definição da temperatura](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Para adicionar a próxima variável, selecione **Adicionar Variável**.

   * Defina a *umidade*:

      | | |
      | --- | ---|
      | **Nome** | Insira **Umidade Máxima** |
      | **Valor** | Selecione **umidade (Duplo)** . Talvez demore alguns minutos até que **Valor** seja preenchido automaticamente depois que a Versão Prévia do Azure Time Series Insights começar a receber eventos. |
      | **Operação de Agregação** | Selecione **MAX**.|

      [![Seleções para definição da temperatura](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Selecione **Criar**.

    Você pode ver o tipo que você adicionou:

    [![Informações sobre o tipo adicionado](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. A próxima etapa é adicionar uma hierarquia. Sob **Hierarquias**, selecione **Adicionar**:

    [![Guia Hierarquias com o botão Adicionar](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. No painel **Editar Hierarquia**, defina os seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | ---|
   | **Nome** | Insira **Hierarquia de Localizações**. |
   | **Nível 1** | Insira **País**. |
   | **Nível 2** | Insira **Cidade**. |
   | **Nível 3** | Insira **Edifício**. |

   Clique em **Salvar**.

    [![Campos de hierarquia com o botão Criar](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Você poderá ver a hierarquia criada:

    [![Informações sobre a hierarquia](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Selecione **Instâncias**. Selecione a primeira instância e, em seguida, selecione **Editar**:

    [![Selecionando o botão Editar de uma instância](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. No painel **Editar instâncias**, defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **Resfriador**. |
    | **Descrição** | Insira **Instância do Resfriador-01.1**. |
    | **Hierarquias** | Selecione **Hierarquia de Localizações**. |
    | **País** | Insira **EUA**. |
    | **Cidade** | Insira **Seattle**. |
    | **Prédio** | Insira **Space Needle**. |

    [![Campos de instância com o botão Salvar](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Clique em **Salvar**.

1. Repita a etapa anterior para os outros sensores. Atualize os seguintes valores:

   * Para o Resfriador 01.2:

     | Parâmetro | Ação |
     | --- | --- |
     | **Tipo** | Selecione **Resfriador**. |
     | **Descrição** | Insira **Instância do Resfriador-01.2**. |
     | **Hierarquias** | Selecione **Hierarquia de Localizações**. |
     | **País** | Insira **EUA**. |
     | **Cidade** | Insira **Seattle**. |
     | **Prédio** | Insira **Centro de Ciência do Pacífico**. |

   * Para o Resfriador 01.3:

     | Parâmetro | Ação |
     | --- | --- |
     | **Tipo** | Selecione **Resfriador**. |
     | **Descrição** | Insira **Instância do Resfriador-01.3**. |
     | **Hierarquias** | Selecione **Hierarquia de Localizações**. |
     | **País** | Insira **EUA**. |
     | **Cidade** | Insira **Nova York**. |
     | **Prédio** | Insira **Empire State Building**. |

1. Selecione a guia **Analisar** e atualize a página. Sob **Hierarquia local**, expanda todos os níveis de hierarquia para exibir as instâncias da série temporal:

   [![A guia Analisar](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Para explorar as instâncias de série temporal da última hora, altere **Tempos Rápidos** para **Última Hora**:

    [![A caixa Tempos Rápidos, com a Última Hora selecionada](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. Em **Centro de Ciência do Pacífico**, selecione a instância de série temporal e selecione **Mostrar Umidade Máxima**.

    [![Instância de série temporal selecionada com a seleção de menu Mostrar Umidade Máxima](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. A série temporal de **Umidade Máxima** com um tamanho de intervalo igual a **1 minuto** será aberta. Selecione uma região para filtrar um intervalo. Para analisar os eventos do período, clique com o botão direito do mouse no gráfico e selecione **Zoom**:

   [![Intervalo selecionado com o comando Aplicar Zoom em um menu de atalho](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Para ver os detalhes do evento, selecione uma região e, em seguida, clique com o botão direito no gráfico:

   [![Lista detalhada de eventos](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:  

> [!div class="checklist"]
> * Criar e usar um acelerador de simulação de dispositivo.
> * Crie um ambiente PAYG de Versão Prévia do Azure Time Series Insights.
> * Conecte o ambiente de Versão Prévia do Azure Time Series Insights a um hub de eventos.
> * Executar uma amostra do acelerador de solução para transmitir dados para o ambiente da Versão Prévia do Azure Time Series Insights.
> * Execute uma análise básica dos dados.
> * Definir uma hierarquia e um tipo de Modelo de Série Temporal e associá-los às suas instâncias.

Agora que você sabe como criar seu próprio ambiente de Versão Prévia do Azure Time Series Insights, saiba mais sobre os principais conceitos no Azure Time Series Insights.

Leia mais sobre a configuração de armazenamento do Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Armazenamento e entrada da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-storage-ingress.md)

Saiba mais sobre Modelos do Time Series:

> [!div class="nextstepaction"]
> [Modelagem de dados da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-tsm.md)
