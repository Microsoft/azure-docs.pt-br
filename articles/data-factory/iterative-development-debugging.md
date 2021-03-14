---
title: Desenvolvimento iterativo e depuração no Azure Data Factory
description: Saiba como desenvolver e depurar Data Factory pipelines iterativamente na UX do ADF
ms.date: 02/23/2021
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: kromerm
ms.author: makromer
ms.openlocfilehash: ef47d311f5f096db962ea27792e7871dbf0ef81a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712939"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Desenvolvimento iterativo e depuração com o Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory permite desenvolver e depurar de forma iterativa Data Factory pipelines à medida que você desenvolve suas soluções de integração de dados. Esses recursos permitem que você teste suas alterações antes de criar uma solicitação de pull ou publicá-las no serviço de data factory. 

Para ver uma introdução de oito minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>Depuração de um pipeline

Como você cria usando a tela de pipeline, você pode testar suas atividades usando a capacidade de **depuração** . Quando faz execuções de teste, você não precisa publicar suas alterações para o data factory antes de selecionar **Depurar**. Esse recurso é útil nos cenários em que você deseja garantir que as alterações funcionem conforme o esperado antes de atualizar o fluxo de trabalho de data factory.

![Recurso Depurar na tela de pipeline](media/iterative-development-debugging/iterative-development-1.png)

À medida que o pipeline estiver em execução, você poderá ver os resultados de cada atividade na guia **saída** da tela do pipeline.

Exiba os resultados das execuções de teste na janela **Saída** da tela de pipeline.

![Janela de saída da tela de pipeline](media/iterative-development-debugging/iterative-development-2.png)

Depois que uma execução de teste for bem-sucedida, adicione mais atividades ao seu pipeline e continue a depuração de maneira iterativa. Você também pode **Cancelar** uma execução de teste enquanto ela está em andamento.

> [!IMPORTANT]
> Selecionar **Depurar** realmente executa o pipeline. Por exemplo, se o pipeline contiver atividade de cópia, a execução de teste copiará dados da origem para o destino. Como resultado, é recomendável que você use pastas de teste em suas atividades de cópia e outras atividades durante a depuração. Depois que você tiver depurado pipeline, alterne para as pastas reais que você deseja usar em operações normais.

### <a name="setting-breakpoints"></a>Definindo pontos de interrupção

Azure Data Factory permite que você depure um pipeline até alcançar uma atividade específica na tela do pipeline. Coloque um ponto de interrupção na atividade até o qual você deseja testar e selecione **depurar**. O Data Factory garante que o teste seja executado somente até a atividade de ponto de interrupção na tela de pipeline. Esse recurso *Depurar Até* é útil quando você não quer testar o pipeline de inteiro, mas apenas um subconjunto das atividades dentro do pipeline.

![Pontos de interrupção na tela de pipeline](media/iterative-development-debugging/iterative-development-3.png)

Para definir um ponto de interrupção, selecione um elemento na tela do pipeline. Uma opção *Depurar Até* aparece como um círculo vermelho vazio no canto superior direito do elemento.

![Antes de configurar um ponto de interrupção no elemento selecionado](media/iterative-development-debugging/iterative-development-4.png)

Depois de selecionar a opção *Depurar Até*, ele é alterado para um círculo vermelho preenchido para indicar que o ponto de interrupção está habilitado.

![Depois de configurar um ponto de interrupção no elemento selecionado](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>Monitorar execuções de depuração

Quando você executa uma execução de depuração de pipeline, os resultados aparecerão na janela de **saída** da tela do pipeline. A guia saída conterá apenas a execução mais recente que ocorreu durante a sessão atual do navegador. 

![Janela de saída da tela de pipeline](media/iterative-development-debugging/iterative-development-2.png)

Para exibir uma exibição histórica das execuções de depuração ou ver uma lista de todas as execuções de depuração ativas, você pode entrar na experiência do **Monitor** . 

![Selecione o ícone Exibir execuções de depuração ativas](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> O serviço Azure Data Factory só persiste o histórico de execução de depuração por 15 dias. 

## <a name="debugging-mapping-data-flows"></a>Depuração de mapeamento de fluxos de dados

O mapeamento de fluxos de dados permite que você crie uma lógica de transformação de dados sem código que é executada em escala. Ao criar sua lógica, você pode ativar uma sessão de depuração para trabalhar interativamente com seus dados usando um cluster Spark ao vivo. Para saber mais, leia sobre [mapear o modo de depuração do fluxo de dados](concepts-data-flow-debug-mode.md).

Você pode monitorar sessões de depuração de fluxo de dados ativos em uma fábrica na experiência do **Monitor** .

![Exibir sessões de depuração do fluxo de dados](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

A visualização de dados no designer de fluxo de dados e a depuração de pipeline de fluxos de dados visam funcionar melhor com pequenos exemplos de dados. No entanto, se você precisar testar sua lógica em um pipeline ou fluxo de dados com base em grandes quantidades de dados, aumente o tamanho do Azure Integration Runtime que está sendo usado na sessão de depuração com mais núcleos e um mínimo de computação de uso geral.
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>Depurando um pipeline com uma atividade de fluxo de dados

Ao executar um pipeline de depuração executado com um fluxo de dados, você tem duas opções em que computação usar. Você pode usar um cluster de depuração existente ou criar um novo cluster just-in-time para seus fluxos de dados.

O uso de uma sessão de depuração existente reduzirá muito o tempo de inicialização do fluxo de dados, pois o cluster já está em execução, mas não é recomendado para cargas de trabalho complexas ou paralelas, uma vez que pode falhar quando vários trabalhos são executados ao mesmo tempo.

Usar o tempo de execução de atividade criará um novo cluster usando as configurações especificadas em cada tempo de execução de integração da atividade de fluxo de dados. Isso permite que cada trabalho seja isolado e deve ser usado para cargas de trabalhos complexas ou testes de desempenho. Você também pode controlar a TTL no Azure IR para que os recursos de cluster usados para depuração ainda estejam disponíveis para esse período de tempo para atender a solicitações de trabalho adicionais.

> [!NOTE]
> Se você tiver um pipeline com fluxos de dados em execução em paralelo ou em fluxos de dados que precisam ser testados com grandes DataSets, escolha "usar o tempo de execução de atividade" para que Data Factory possa usar o Integration Runtime que você selecionou em sua atividade de fluxo de dados. Isso permitirá que os fluxos de dados sejam executados em vários clusters e possam acomodar suas execuções de fluxo de dados paralelos.

![Executando um pipeline com um Dataflow](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>Próximas etapas

Depois de testar suas alterações, promova-as para ambientes mais altos usando a [integração e a implantação contínuas no Azure data Factory](continuous-integration-deployment.md).
