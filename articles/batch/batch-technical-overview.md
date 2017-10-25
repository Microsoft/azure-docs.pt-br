---
title: "O Lote do Azure executa soluções de computação paralela em larga escala na nuvem | Microsoft Docs"
description: "Saiba mais sobre o uso do serviço Lote do Azure para cargas de trabalho paralelas e HPC em larga escala."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a99f96db0c1e8bcd0cf29c564e5badf0eb728e56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-intrinsically-parallel-workloads-with-batch"></a>Executar cargas de trabalho intrinsecamente paralelas com o Lote

O Lote do Azure é um serviço de plataforma para execução de aplicativos paralelos em grande escala e aplicativos HPC (computação de alto desempenho) com eficiência na nuvem. O Lote do Azure agenda o trabalho de computação intensiva para executar em uma coleção gerenciada de máquinas virtuais e que pode dimensionar automaticamente os recursos de computação para atender às necessidades dos trabalhos.

Com o Lote do Azure, você pode definir facilmente os recursos de computação do Azure para executar os aplicativos em paralelo e em escala. Não é necessário criar, configurar e gerenciar manualmente um cluster HPC, máquinas virtuais, redes virtuais ou um trabalho e infraestrutura de agendamento de tarefas complexos. O Lote do Azure automatiza ou simplifica essas tarefas para você.

## <a name="use-cases-for-batch"></a>Casos de uso para o Lote
O Lote é um serviço do Azure gerenciado que é usado para *processamento em lotes* ou *computação em lotes*, executando grandes volumes de tarefas semelhantes para obter o resultado desejado. A computação em lote é mais comumente usada por organizações que processam, transformam e analisam grandes volumes de dados regularmente.

O Lote funciona bem com cargas de trabalho e aplicativos intrinsecamente paralelos (também conhecidos como "excessivamente paralelos"). Cargas de trabalho intrinsecamente paralelas são aquelas divididas com facilidade em várias tarefas que executam o trabalho simultaneamente em vários computadores.

![Tarefas paralelas][1]<br/>

Alguns exemplos de cargas de trabalho que normalmente são processadas usando essa técnica são:

* Modelagem de riscos financeiros
* Análise de dados de clima e hidrologia
* Processamento, análise e renderização de imagens
* Codificação e transcodificação de mídia
* Análise de sequência genética
* Análise de estresse de engenharia
* Teste de software

O lote também pode realizar cálculos paralelos com uma etapa de redução no final, além de executar cargas de trabalho HPC mais complexas, como aplicativos [MPI (nterface de Transmissão de Mensagens)](batch-mpi.md) .

Para obter uma comparação entre o Lote e outras opções de solução HPC no Azure, confira [Soluções do HPC, Lote e Big Compute](../virtual-machines/linux/high-performance-computing.md).

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="scenario-scale-out-a-parallel-workload"></a>Cenário: escalar horizontalmente uma carga de trabalho paralela
Uma solução comum que usa as APIs de Lote para interagir com o serviço de Lote envolve a expansão de trabalho intrinsecamente paralelo (como a renderização de imagens para cenas 3D) em um pool de nós de computação. Esse pool de nós de computação pode ser seu "farm de renderização" que oferece dezenas, centenas ou até milhares de núcleos para o trabalho de renderização, por exemplo.

O diagrama a seguir mostra um fluxo de trabalho do Lote comum, com um aplicativo cliente ou serviço hospedado usando o Lote para executar uma carga de trabalho paralela.

![Fluxo de trabalho da solução do Lote][2]

Nesse cenário comum, o aplicativo ou serviço processa uma carga de trabalho computacional no Lote do Azure, executando as seguintes etapas:

1. Carregue os **arquivos de entrada** e o **aplicativo** que processará esses arquivos em sua conta do Armazenamento do Azure. Os arquivos de entrada podem ser quaisquer dados que o aplicativo processará, como dados de modelagem financeira ou arquivos de vídeo a serem transcodificados. Os arquivos do aplicativo podem ser qualquer aplicativo que seja usado para processar os dados, como um aplicativo de renderização 3D ou um transcodificador de mídia.
2. Crie um **pool** do Lote de nós de computação em sua conta do Lote. Esses nós são as máquinas virtuais que executarão suas tarefas. Especifique propriedades, como o [tamanho de nó](../cloud-services/cloud-services-sizes-specs.md), o sistema operacional e o local no Armazenamento do Azure do aplicativo para instalação quando os nós ingressarem no pool (o aplicativo que você carregou na etapa 1). Você também pode configurar o pool para ser [dimensionado automaticamente](batch-automatic-scaling.md) em resposta à carga de trabalho gerada pelas tarefas. O dimensionamento automático ajusta dinamicamente o número de nós de computação no pool.
3. Crie um **trabalho** do Lote para executar a carga de trabalho no pool de nós de computação. Ao criar um trabalho, você pode associá-lo a um pool do Lote.
4. Adicione **Tarefas** ao trabalho. Quando você adiciona tarefas a um trabalho, o serviço de Lote agenda automaticamente as tarefas para execução em nós de computação no pool. Cada tarefa usa o aplicativo que você carregou para processar os arquivos de entrada.
   
   * 4a. Antes da execução de uma tarefa, ela pode baixar os dados (os arquivos de entrada) que deve processar para o nó de computação ao qual está atribuída. Se o aplicativo ainda não tiver sido instalado no nó (confira a etapa nº 2), ele poderá ser baixado aqui em vez disso. Quando os downloads forem concluídos, as tarefas serão executadas em seus nós atribuídos.
5. À medida que as tarefas são executadas, você pode consultar o Lote para monitorar o andamento do trabalho e suas tarefas. O aplicativo cliente ou serviço se comunica com o serviço de Lote por HTTPS. Como você pode monitorar milhares de tarefas em execução em milhares de nós de computação, não se esqueça de [consultar o serviço do Lote com eficiência](batch-efficient-list-queries.md).
6. À medida que as tarefas são concluídas,elas podem carregar seus dados de resultados para o Armazenamento do Azure. Você também pode recuperar arquivos diretamente do sistema de arquivos em um nó de computação.
7. Quando o monitoramento detecta que as tarefas em seu trabalho foram concluídas, o aplicativo cliente ou serviço pode baixar os dados de saída para processamento adicional ou avaliação.

Lembre-se de que essa é apenas uma maneira de usar o Lote, e este cenário descreve apenas alguns de seus recursos disponíveis. Por exemplo, você pode executar [várias tarefas em paralelo](batch-parallel-node-tasks.md) em cada nó de computação e pode usar [tarefas de preparo e conclusão de trabalhos](batch-job-prep-release.md) para preparar os nós para os trabalhos e realizar a limpeza posteriormente.

## <a name="next-steps"></a>Próximas etapas
Agora que você já viu uma visão geral de alto nível do serviço Lote, é hora de se aprofundar para saber como pode usá-lo para processar suas cargas de trabalho paralelas com uso intensivo de computação.

* Leia ae [Visão geral de recursos do Lote para desenvolvedores](batch-api-basics.md), informações essenciais para qualquer pessoa que está se preparando para usar o Lote. O artigo contém informações mais detalhadas sobre os recursos de serviço do Lote, como pools, nós, trabalhos e tarefas, e os muitos recursos da API que você pode usar ao criar o aplicativo do Lote.
* Saiba mais sobre as [Ferramentas e APIs do Lote](batch-apis-tools.md) disponíveis para a criação de soluções do Lote.
* [Introdução à biblioteca do Lote do Azure para .NET](batch-dotnet-get-started.md) para aprender a usar o C# e a biblioteca do .NET do Lote para executar uma carga de trabalho simples usando um fluxo de trabalho comum do Lote. Este artigo deve ser uma de suas primeiras etapas ao aprender a usar o serviço de Lote. Também há uma [Versão do Python](batch-python-tutorial.md) do tutorial.
* Baixe os [exemplos de código no GitHub][github_samples] para ver como o C# e o Python podem interagir com o Lote para agendar e processar cargas de trabalho de exemplo.
* Confira o [Roteiro de Aprendizagem do Lote][learning_path] para ter uma noção dos recursos disponíveis para você ao aprender a trabalhar com o Lote.


[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png
