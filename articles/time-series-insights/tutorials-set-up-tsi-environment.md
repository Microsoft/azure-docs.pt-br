---
title: 'Tutorial: Configurar um ambiente do Gen2 – Azure Time Series Insights Gen2 | Microsoft Docs'
description: 'Tutorial: Saiba como configurar um ambiente no Azure Time Series Insights Gen2.'
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 01/19/2021
ms.custom: seodec18
ms.openlocfilehash: 912dbf48254a8d68b9bf76db04734fd791a3edb9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677046"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>Tutorial: Configurar um ambiente do Azure Time Series Insights Gen2

Este tutorial orienta você no processo de criação de um ambiente PAYG (*pago conforme o uso*) do Azure Time Series Insights Gen2.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Criar um ambiente do Azure Time Series Insights Gen2.
> * Conectar o ambiente do Azure Time Series Insights Gen2 a um Hub IoT.
> * Executar uma amostra do acelerador de solução para transmitir dados para o ambiente do Azure Time Series Insights Gen2.
> * Executar uma análise básica nos dados.
> * Definir uma hierarquia e um tipo de Modelo de Série Temporal e associá-los às suas instâncias.

>[!TIP]
> Os [aceleradores de solução de IoT](https://www.azureiotsolutions.com/Accelerators) fornecem soluções de nível empresarial pré-configuradas que você pode usar para acelerar o desenvolvimento de soluções personalizadas de IoT.

Inscreva-se em uma [assinatura do Azure gratuita](https://azure.microsoft.com/free/) se já não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* No mínimo, você deve ter a função de **Colaborador** para a assinatura do Azure. Para obter mais informações, leia [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivo

Nesta seção, você criará três dispositivos simulados que enviam dados para uma instância do Hub IoT do Azure.

1. Acesse a [página de aceleradores de solução do IoT do Azure](https://www.azureiotsolutions.com/Accelerators). A página exibe vários exemplos pré-criados. Entre usando sua conta do Azure. Em seguida, selecione **Simulação de Dispositivo**.

   [![Página de aceleradores de solução do IoT do Azure.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. Insira a assinatura na qual deseja criar a simulação do dispositivo.

   Parâmetro|Descrição
   ---|---
   **Nome da implantação** | Esse valor exclusivo é usado para criar um grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos.
   **Assinatura do Azure** | Especifique a mesma assinatura usada para criar seu ambiente do Azure Time Series Insights Gen2 na seção anterior.
   **Opções de implantação** | Selecione **Provisionar novo Hub IoT** de modo a criar um novo hub IoT específico para este tutorial.
   **Localização do Azure** | Especifique a mesma região usada para criar o ambiente do Azure Time Series Insights Gen2 na seção anterior.

   Quando terminar, selecione **Criar** para provisionar os recursos do Azure da solução. Esse processo pode levar até 20 minutos para ser concluído.

   [![Provisione a solução de simulação de dispositivo.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. Após a conclusão do provisionamento, duas notificações serão exibidas anunciando que o estado da implantação mudou de **Provisionamento** para **Pronto**.

   >[!IMPORTANT]
   > Não insira seu acelerador de solução ainda. Mantenha essa página da Web aberta porque você retornará a ela mais tarde.

   [![Provisionamentos da solução de simulação de dispositivo concluídos.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Agora, inspecione os recursos recentemente criados no portal do Azure. Na página **Grupos de recursos**, observe que um grupo de recursos foi criado usando o **Nome da solução** fornecido na última etapa. Anote os recursos que foram criados para a simulação do dispositivo.

   [![Recursos de simulação do dispositivo.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-azure-time-series-insights-gen2-environment"></a>Criar um ambiente do Azure Time Series Insights Gen2

Esta seção descreve como criar um ambiente do Azure Time Series Insights Gen2 e conectá-lo ao hub IoT criado pelo Acelerador de Solução de IoT usando o [portal do Azure](https://portal.azure.com/).

1. Entre no [portal do Azure](https://portal.azure.com) usando sua conta da assinatura do Azure.
1. Selecione **+ Criar um recurso** no canto superior esquerdo.
1. Selecione a categoria **Internet das Coisas** e depois selecione **Time Series Insights**.

   [![Selecione o recurso de ambiente Time Series Insights.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. No painel **Criar ambiente do Time Series Insights**, na guia **Básico**, defina os seguintes parâmetros:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome do ambiente** | Insira um nome exclusivo para o ambiente do Azure Time Series Insights Gen2. |
    | **Assinatura** | Insira a assinatura na qual deseja criar o ambiente do Azure Time Series Insights Gen2. É uma melhor prática usar a mesma assinatura que o restante de seus recursos de IoT criados pelo simulador de dispositivo. |
    | **Grupo de recursos** | Selecione um grupo de recursos existente ou crie um para o recurso do ambiente do Azure Time Series Insights Gen2. Um grupo de recursos é um contêiner para os recursos do Azure. É uma melhor prática usar o mesmo grupo de recursos que os outros recursos IoT criados pelo simulador de dispositivo. |
    | **Localidade** | Selecione uma região de data center para o ambiente do Azure Time Series Insights Gen2. Para evitar latência adicional, é melhor criar o ambiente do Azure Time Series Insights Gen2 na mesma região que o Hub IoT criado pelo simulador de dispositivo. |
    | **Camada** |  Selecione **Gen2(L1)** . Essa é a SKU do produto Azure Time Series Insights Gen2. |
    | **Nome da propriedade da ID da série temporal** | Insira o nome de uma propriedade que contenha valores que identifiquem exclusivamente suas instâncias de série temporal. O valor inserido na caixa **Nome da propriedade** como a ID da série temporal não poderá ser alterado posteriormente. Para este tutorial, insira **_iothub-connection-device-id_* _. Para saber mais sobre a ID da série temporal, incluindo a ID da série temporal composta, leia [Melhores práticas para a escolha de uma ID da série temporal](./how-to-select-tsid.md). |
    | _ *Nome da conta de armazenamento** | Insira um nome global exclusivo para uma nova conta de armazenamento.|
    | **Tipo de conta de armazenamento** | Selecione o tipo de armazenamento para uma nova conta de armazenamento. Recomendamos StorageV2|
    | **Replicação da conta de armazenamento** | Selecione o tipo de armazenamento para uma nova conta de armazenamento. Com base na seleção de localização, você pode escolher entre LRS, GRS e ZRS. Para este tutorial, selecione LRS|
    | **Namespace hierárquico** |Essa opção pode ser selecionada após você selecionar o tipo de armazenamento como StorageV2. Por padrão, ela fica desabilitada. Para este tutorial, você pode deixá-la no estado padrão *desabilitado*|
    |**Habilitar o armazenamento warm**|Selecione **Sim** para habilitar o armazenamento warm. Essa configuração também pode ser desabilitada e habilitada novamente após o ambiente ser criado. |
    |**Retenção de dados (em dias)**|Escolha a opção padrão de sete dias. |

    [![Configuração de um novo ambiente do Time Series Insights.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

1. Selecione **Avançar: Origem do Evento**.

   [![Configure a ID da Série Temporal para o ambiente.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. Na guia **Origem do Evento**, defina os seguintes parâmetros:

   | Parâmetro | Ação |
   | --- | --- |
   | **Criar uma origem do evento?** | Selecione **Sim** na barra superior.|
   | **Nome** | Insira um valor exclusivo para o nome de origem do evento. |
   | **Tipo de fonte** | Selecione **Hub IoT**. |
   | **Selecione um hub** | Escolha **Selecionar existente**. |
   | **Assinatura** | Selecione a assinatura usada para o simulador de dispositivo. |
   | **Nome do Hub IoT** | Selecione o nome do hub IoT criado para o simulador de dispositivo. |
   | **Política de acesso do Hub IoT** | Selecione **iothubowner**. |
   | **Grupo de consumidores do Hub IoT** | Selecione **Novo**, insira um nome exclusivo e, em seguida, selecione **+ Adicionar**. O grupo de consumidores deve ser um valor exclusivo no Azure Time Series Insights Gen2. |
   | **Propriedade de carimbo de data/hora** | Esse valor é usado para identificar a propriedade **Carimbo de data/hora** em seus dados de telemetria de entrada. Para este tutorial, deixe essa caixa em branco. Esse simulador usa o carimbo de data/hora de entrada do Hub IoT, que é usado por padrão pelo Azure Time Series Insights Gen2. |

1. Selecione **Examinar + criar**.

   [![Configure o Hub IoT criado como uma origem do evento.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Selecione **Criar**.

    [![Página Examinar + Criar com o botão Criar.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    Você pode examinar o status da implantação:

    [![Notificação de que a implantação foi concluída.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. Caso seja um proprietário da assinatura do Azure, você terá acesso ao ambiente do Azure Time Series Insights Gen2 por padrão. Verifique se você tem acesso:

   1. Pesquise o grupo de recursos e selecione o ambiente do Azure Time Series Insights Gen2 recém-criado.

      [![Selecione e exiba seu ambiente.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. Na página do Azure Time Series Insights Gen2, selecione **Políticas de Acesso a Dados**:

      [![Verifique as políticas de acesso a dados.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Verifique se suas credenciais estão listadas:

      Se as credenciais não estiverem listadas, será necessário conceder permissão a você mesmo para acessar o ambiente selecionando Adicionar e pesquisando pelas suas credenciais. Para saber mais sobre como definir permissões, leia [Conceder o acesso a dados](./concepts-access-policies.md).

## <a name="stream-data"></a>Transmitir dados

Agora que você implantou seu ambiente do Azure Time Series Insights Gen2, comece a transmitir dados para fins de análise.

1. Volte para o [Painel de aceleradores da solução](https://www.azureiotsolutions.com/Accelerators#dashboard). Entre novamente, se necessário, usando a mesma conta do Azure que você usou neste tutorial. Selecione a "Solução de Dispositivo" e, em seguida, **Acessar o acelerador de solução** para iniciar a solução implantada.

   [![Painel de aceleradores de solução.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. O aplicativo Web da simulação de dispositivo começa solicitando que você conceda ao aplicativo Web a permissão **Conectá-lo e ler seu perfil**. Com essa permissão, o aplicativo pode recuperar as informações de perfil do usuário necessárias para dar suporte ao funcionamento do aplicativo.

   [![Consentimento do aplicativo Web da simulação de dispositivo.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Selecione **+ Nova simulação**.

    1. Depois que a página **Instalação da simulação** for carregada, insira os parâmetros necessários.

        | Parâmetro | Ação |
        | --- | --- |
        | **Nome** | Insira um nome exclusivo para um simulador. |
        | **Descrição** | Insira uma definição. |
        | **Duração da simulação** | Defina essa opção como **Executar indefinidamente**. |
        | **Modelo do dispositivo** | Clique em + **Adicionar um tipo de dispositivo** <br />**Name**: Insira **Elevador**. <br />**Valor**: Insira **3**. <br /> Mantenha os valores padrão restantes |
        | **Hub IoT de destino** | Defina essa opção como **Usar o Hub IoT pré-provisionado**. |

        [![Configure parâmetros e inicie.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    1. Selecione **Iniciar simulação**. No painel de simulação do dispositivo, **Dispositivos ativos** e **Mensagens totais** serão exibidos.

        [![Painel de simulação de IoT do Azure.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analisar dados

Nesta seção, você executa uma análise básica em seus dados de série temporal usando o [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).

1. Acesse o Azure Time Series Insights Gen2 Explorer selecionando a URL na página do recurso no [portal do Azure](https://portal.azure.com/).

    [![A URL do Azure Time Series Insights Gen2 Explorer.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. No Azure Time Series Insights Gen2 Explorer, uma barra abrangendo a parte superior da tela será exibida. Este é seu seletor de disponibilidade. Verifique se você tem pelo menos dois minutos selecionados e, se necessário, expanda o período selecionando e arrastando os identificadores do seletor para a esquerda e para a direita.

1. As **Instâncias de Série Temporal** serão exibidas à esquerda.

    [![Lista de instâncias órfãs.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Selecione a primeira instância de série temporal. Em seguida, selecione **Mostrar temperatura**.

    [![Instância de série temporal selecionada com o comando de menu para mostrar a temperatura média.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Um gráfico da série temporal é exibido. Altere o **Intervalo** para **30s**.

1. Repita a etapa anterior com as outras duas instâncias de série temporal de modo que você esteja vendo todas as três, conforme mostrado neste gráfico:

    [![Gráfico de todas as séries temporais.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Use o seletor de intervalo no canto superior direito. Aqui você pode selecionar horários de início e término específicos com precisão de milissegundo ou escolher entre as opções pré-configuradas, como **Últimos 30 minutos**. Você também poderá alterar o fuso horário padrão.

    [![Definir o intervalo de tempo para os últimos 30 minutos.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    O progresso do acelerador de solução nos **Últimos 30 minutos** é exibido no Azure Time Series Insights Gen2 Explorer.

## <a name="define-and-apply-a-model"></a>Definir e aplicar um modelo

Nesta seção, você aplicará um modelo para estruturar seus dados. Para concluir o modelo, você definirá tipos, hierarquias e instâncias. Para saber mais sobre modelagem de dados, leia [Modelo de Série Temporal](./concepts-model-overview.md).

1. No Explorer, selecione a guia **Modelo**:

   [![Exibir a guia Modelo no Explorer.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   Na guia **Tipos**, selecione **+ Adicionar**.

1. Insira os parâmetros s seguir:

    | Parâmetro | Ação |
    | --- | ---|
    | **Nome** | Insira **Elevador** |
    | **Descrição** | Insira **Esta é uma definição de tipo do Elevador** |

1. Em seguida, selecione a guia **Variáveis**.

    1. Selecione **+ Adicionar Variável** e preencha os valores a seguir para a primeira variável do tipo de Elevador. Você criará três variáveis no total.

        | Parâmetro | Ação |
        | --- | --- |
        | **Nome** | Insira **Temperatura Média**. |
        | **Tipo** | Selecione **Numérico** |
        | **Valor** | Selecione da predefinição: Selecione **temperatura (Duplo)** . <br /> Observação: talvez sejam necessários alguns minutos até que o **Valor** seja preenchido automaticamente após o Azure Time Series Insights Gen2 começar a receber eventos.|
        | **Operação de Agregação** | Expanda **Opções Avançadas**. <br /> Selecione **AVG**. |

    1. Escolha **Aplicar**. Em seguida, **+ Adicionar Variável** novamente e defina os seguintes valores:

        | Parâmetro | Ação |
        | --- | --- |
        | **Nome** | Insira **Vibração Média**. |
        | **Tipo** | Selecione **Numérico** |
        | **Valor** | Selecione da predefinição: Selecione **vibração (Double)** . <br /> Observação: talvez sejam necessários alguns minutos até que o **Valor** seja preenchido automaticamente após o Azure Time Series Insights Gen2 começar a receber eventos.|
        | **Operação de Agregação** | Expanda **Opções Avançadas**. <br /> Selecione **AVG**. |

    1. Escolha **Aplicar**. Em seguida, **+ Adicionar Variável** novamente e defina os seguintes valores para a terceira e última variável:

        | Parâmetro | Ação |
        | --- | --- |
        | **Nome** | Insira **Andar**. |
        | **Tipo** | Selecione **Categórica** |
        | **Valor** | Selecione da predefinição: Selecione **Andar (Double)** . <br /> Observação: talvez sejam necessários alguns minutos até que o **Valor** seja preenchido automaticamente após o Azure Time Series Insights Gen2 começar a receber eventos.|
        | **Categorias** | <span style="text-decoration: underline">Rótulos </span>  - <span style="text-decoration: underline">Valores</span> <br /> Inferiores: 1,2,3,4 <br /> Intermediários: 5,6,7,8,9 <br /> Superiores: 10,11,12,13,14,15 |
        | **Categoria padrão** | Insira **Desconhecido** |

        [![Adicionar variáveis de tipo.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    1. Escolha **Aplicar**. 
    1. Clique em **Salvar**. Três variáveis são criadas e exibidas.

        [![Após adicionar o tipo, examine-o na exibição Modelo.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Selecione a guia **Hierarquias**. Em seguida, selecione **+ Adicionar**.

   1. No painel **Editar Hierarquia**, defina os seguintes parâmetros:

        | Parâmetro | Ação |
        | --- | ---|
        | **Nome** | Insira **Hierarquia de Localizações**. |
        |**Níveis**| Insira **País** como o nome do primeiro nível <br /> Selecione **+ Adicionar Nível** <br /> Insira **Cidade** para o segundo nível e, em seguida, selecione **+ Adicionar Nível** <br /> Insira **Prédio** como o nome do terceiro e último nível |

   1. Clique em **Salvar**.

        [![Exiba sua nova hierarquia na exibição de Modelo.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Navegue até **Instâncias**.

    1. Em **Ações**, na extrema direita, selecione o ícone de lápis para editar a primeira instância com os seguintes valores:

        | Parâmetro | Ação |
        | --- | --- |
        | **Tipo** | Selecione **Elevador**. |
        | **Nome** | Insira **Elevador 1**|
        | **Descrição** | Insira **Instância do Elevador 1** |

    1. Navegue até **Campos de Instância** e insira os seguintes valores:

        | Parâmetro | Ação |
        | --- | --- |
        | **Hierarquias** | Selecione **Hierarquia de Localizações** |
        | **País** | Insira **EUA** |
        | **Cidade** | Insira **Seattle** |
        | **Prédio** | Insira **Space Needle** |

    1. Clique em **Salvar**.

1. Repita a etapa anterior com as outras duas instâncias ao usar os seguintes valores:

    **Para o Elevador 2:**

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **Elevador**. |
    | **Nome** | Insira **Elevador 2**|
    | **Descrição** | Insira **Instância do Elevador 2** |
    | **Hierarquias** | Selecione **Hierarquia de Localizações** |
    | **País** | Insira **EUA** |
    | **Cidade** | Insira **Seattle** |
    | **Prédio** | Insira **Centro de Ciência do Pacífico** |

    **Para o Elevador 3:**

    | Parâmetro | Ação |
    | --- | --- |
    | **Tipo** | Selecione **Elevador**. |
    | **Nome** | Insira **Elevador 3**|
    | **Descrição** | Insira **Instância do Elevador 3** |
    | **Hierarquias** | Selecione **Hierarquia de Localizações** |
    | **País** | Insira **EUA** |
    | **Cidade** | Insira **Nova York** |
    | **Prédio** | Insira **Empire State Building** |

    [![Exiba as instâncias atualizadas.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Navegue de volta para a guia **Analisar** para exibir o painel de gráfico. Sob **Hierarquia local**, expanda todos os níveis de hierarquia para exibir as instâncias da série temporal:

    [![Exiba todas as hierarquias na exibição de gráfico.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. Em **Centro de Ciência do Pacífico**, selecione a instância de série temporal **Elevador 2** e selecione **Mostrar Temperatura Média**.

1. Para a mesma instância, **Elevador 2**, selecione **Mostrar Andar**.

    Com sua variável categórica, você pode determinar quanto tempo o elevador gastou nos andares superiores, inferiores e intermediários.

    [![Visualize o Elevador 2 com a hierarquia e os dados.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Limpar os recursos

Agora que concluiu o tutorial, limpe os recursos que você criou:

1. No menu à esquerda no [portal do Azure](https://portal.azure.com), selecione **Todos os recursos** e localize o grupo de recursos do Azure Time Series Insights Gen2.
1. Exclua todo o grupo de recursos (e todos os recursos contidos nele) selecionando **Excluir** ou remova cada recurso individualmente.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:  

* Criar e usar um acelerador de simulação de dispositivo.
* Criar um ambiente do Azure Time Series Insights Gen2 com PAYG.
* Conectar o ambiente do Azure Time Series Insights Gen2 a um hub IoT.
* Executar uma amostra do acelerador de solução para transmitir dados para o ambiente do Azure Time Series Insights Gen2.
* Execute uma análise básica dos dados.
* Definir uma hierarquia e um tipo de Modelo de Série Temporal e associá-los às suas instâncias.

Agora que você sabe criar seu ambiente do Azure Time Series Insights Gen2, saiba mais sobre os principais conceitos do Azure Time Series Insights Gen2.

Leia sobre a ingestão no Azure Time Series Insights Gen2:

> [!div class="nextstepaction"]
> [Visão geral da ingestão de dados do Azure Time Series Insights Gen2](./concepts-ingestion-overview.md)

Leia sobre o armazenamento do Azure Time Series Insights Gen2:

> [!div class="nextstepaction"]
> [Armazenamento de dados do Azure Time Series Insights Gen2](./concepts-storage.md)

Saiba mais sobre Modelos do Time Series:

> [!div class="nextstepaction"]
> [Modelagem de dados no Azure Time Series Insights Gen2](./concepts-model-overview.md)

Saiba mais sobre como conectar seu ambiente ao Power BI:

> [!div class="nextstepaction"]
> [Visualizar dados do Azure Time Series Insights Gen2 no Power BI](./how-to-connect-power-bi.md)
