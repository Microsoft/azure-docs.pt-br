---
title: ALM no Azure Machine Learning | Microsoft Docs
description: "Aplicar práticas recomendadas do Gerenciamento do Ciclo de Vida do Aplicativo no Azure Machine Learning Studio"
keywords: "ALM, AML, Azure ML, Gerenciamento do Ciclo de Vida do Aplicativo, Controle de Versão"
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 1be6577d-f2c7-425b-b6b9-d5038e52b395
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: haining
ms.openlocfilehash: 9d1fcc761115c64fafb811d6ca1c2389babfdc15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Gerenciamento do Ciclo de Vida do Aplicativo no Azure Machine Learning Studio
O Azure Machine Learning Studio é uma ferramenta para desenvolvimento de testes de aprendizado de máquina que é operacionalizada na plataforma da nuvem no Azure. Ele é parecido com o IDE do Visual Studio e o serviço de nuvem escalável mesclados em uma única plataforma. Você pode incorporar práticas padrão de ALM (Gerenciamento do Ciclo de Vida do Aplicativo), desde controle de versão de vários ativos até a execução implantação automatizada, ao Azure Machine Learning Studio. Este artigo discute algumas das opções e abordagens.

## <a name="versioning-experiment"></a>Teste de controle de versão
Há duas maneiras recomendadas de controlar a versão de seus testes. Você pode confiar no histórico de execução interno, ou exportar o teste no formato JavaScript Object Notation (JSON) e gerenciá-lo externamente. Cada abordagem tem seus prós e contras.

### <a name="experiment-snapshots-using-run-history"></a>Instantâneos de teste usando o Histórico de Execução
No modelo de execução de teste de aprendizado do Azure Machine Learning Studio, sempre que você clica no botão **Executar** no editor do teste, um instantâneo imutável do teste é enviado ao agendador de trabalho. Você pode exibir essa lista de instantâneos clicando no botão **Histórico de Execução** na barra de comandos, na visualização do editor de teste.

![Botão Histórico de Execução](./media/version-control/runhistory.png)

Assim, você pode abrir um instantâneo no modo Bloqueado clicando no nome do teste, no momento em que o teste foi enviado para execução e o instantâneo foi tirado. Observe que apenas o primeiro item na lista, que representa o teste atual, está em um estado Editável. Observe também que cada instantâneo pode apresentar diversos estados de Status, incluindo Concluído (Execução parcial), Falha, Falha (Execução parcial) ou Rascunho.

![Lista do Histórico de Execução](./media/version-control/runhistorylist.png)

Depois de aberto, você pode salvar o instantâneo do teste como um novo teste e, depois, modificá-lo. Se o instantâneo do teste contiver ativos, como um modelo treinado, transformação ou conjunto de dados, que têm versões atualizadas, o instantâneo manterá as referências à versão original quando o instantâneo foi tirado. Se você salvar o instantâneo bloqueado como um novo teste, o Azure Machine Learning Studio detectará a existência de uma versão mais recente desses ativos e os atualizará automaticamente no novo experimento.

Se você excluir o teste, todos os instantâneos desse teste serão excluídos.

### <a name="exportimport-experiment-in-json-format"></a>Importar/exportar teste no formato JSON
Os instantâneos do histórico de execução mantêm uma versão imutável do teste no Azure Machine Learning Studio sempre que é enviado para execução. Você pode também salvar uma cópia local do teste e selecioná-lo para o seu sistema de controle de origem favorito, como o Team Foundation Server e, posteriormente, recriar um teste desde o arquivo local. Você pode usar os cmdlets do [PowerShell do Azure Machine Learning](http://aka.ms/amlps) [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph), e [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) para fazer isso.

O arquivo JSON é uma representação textual do gráfico de teste, que pode incluir referência aos ativos no espaço de trabalho, como um conjunto de dados ou um modelo treinado. Ele não contém uma versão serializada do ativo. Se você tentar importar o documento JSON de volta para o espaço de trabalho, os ativos referenciados já devem existir com as mesmas IDs de ativo referenciadas no teste. Caso contrário você não será capaz de acessar o teste importado.

## <a name="versioning-trained-model"></a>Controle de versão do modelo treinado
Um modelo treinado no Azure Machine Learning é serializado em um formato conhecido como arquivo .iLearner, e é armazenados na conta de Armazenamento de Blobs do Azure associada ao espaço de trabalho. Uma maneira de obter uma cópia do arquivo .iLearner é por meio da API de novos treinamentos. [Este artigo](retrain-models-programmatically.md) explica como funciona a API de novos treinamentos funciona. As etapas de alto nível:

1. Configurar seu teste de treinamento.
2. Adicionar uma porta de saída do serviço Web ao módulo Treinar Modelo, ou ao módulo que produza o modelo treinado, por exemplo, Ajustar Hiperparâmetro de Modelo ou Criar Modelo R.
3. Executar seu teste de treinamento e, depois, implantá-lo como o serviço Web de treinamento de um modelo.
4. Chamar o ponto de extremidade BES do serviço Web de treinamento e especificar o nome do arquivo .iLearner desejado e o local da conta de Armazenamento de Blobs onde ele será armazenado.
5. Coletar o arquivo .iLearner produzido após a conclusão da chamada do BES.

Outra maneira de recuperar o arquivo .iLearner é por meio do cmdlet do PowerShell [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Isso será mais fácil se você quiser apenas obter uma cópia do arquivo .iLearner sem a necessidade de treinar novamente o modelo programaticamente.

Depois que você tiver o arquivo .iLearner contendo o modelo treinado, é possível implantar sua própria estratégia de versão. A estratégia pode ser simples como aplicar um prefixo/sufixo como uma convenção de nomenclatura e deixar o arquivo .iLearner no armazenamento do Blob ou copiar/importar o mesmo para o sistema de controle de versão.

Assim, o arquivo .iLearner salvo pode ser usado para pontuação por meio de serviços Web implantados.

## <a name="versioning-web-service"></a>Controle de versão do serviço Web
Você pode implantar dois tipos de serviços Web a partir de um teste do Azure Machine Learning. O serviço Web clássico está intimamente ligado ao teste e ao espaço de trabalho. O novo serviço Web usa a estrutura do Azure Resource Manager e não está mais ligado ao teste original nem ao espaço de trabalho.

### <a name="classic-web-service"></a>Serviço Web clássico
Para controlar a versão de um serviço Web clássico, você pode aproveitar a construção do ponto de extremidade do serviço Web. Este é um fluxo típico:

1. De seu teste de previsão, implante um novo serviço Web clássico, que contém um ponto de extremidade padrão.
2. Crie um novo ponto de extremidade chamado ep2, que expõe a versão atual do teste/modelo treinado.
3. Volte e atualize seu teste de previsão e modelo treinado.
4. Reimplante o teste de previsão, que atualizará o ponto de extremidade padrão. Mas isso não alterará o ep2.
5. Crie um ponto de extremidade adicional denominado ep3, que expõe a nova versão do teste e do modelo treinado.
6. Volte para a etapa 3, se for necessário.

Com o tempo, será possível criar vários pontos de extremidade no mesmo serviço Web. Cada ponto de extremidade representa uma cópia pontual do teste contendo a versão pontual do modelo treinado. Em seguida, use a lógica externa para determinar qual ponto de extremidade chamar, o que significa efetivamente a seleção de uma versão do modelo treinado para a execução da pontuação.

Também é possível criar vários pontos de extremidade de serviço Web idênticos e, em seguida, aplicar versões diferentes do arquivo .iLearner ao ponto de extremidade para obter um efeito semelhante. [Este artigo](create-models-and-endpoints-with-powershell.md) explica com mais detalhes sobre como fazer isso.

### <a name="new-web-service"></a>Novo serviço Web
Se você criar um novo serviço Web baseado no Azure Resource Manager, a construção do ponto de extremidade não estará mais disponível. Em vez disso, você pode gerar arquivos WSD (definição de serviço Web), no formato JSON, a partir de seu teste de previsão usando o cmdlet do PowerShell [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) ou usando o cmdlet do PowerShell [*Export-AzureRmMlWebservice*](https://msdn.microsoft.com/library/azure/mt767935.aspx) a partir de um serviço Web baseado no Resource Manager implantado.

Depois de exportar o arquivo WSD e controlar a versão dele, você também poderá implantar o WSD como um novo serviço Web em um plano de serviço Web diferente, em uma região diferente do Azure. Apenas forneça a configuração apropriada da conta de armazenamento, bem como a nova ID do plano do serviço Web. Para aplicar arquivos de .iLearner diferentes, modifique o arquivo WSD e atualize a referência ao local do modelo treinado, e implante-o como um novo serviço Web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizar a implantação e a execução do teste
Um aspecto importante do ALM é ser capaz de automatizar a execução e o processo de implantação do aplicativo. No Azure Machine Learning, você pode fazer isso usando o [módulo do PowerShell](http://aka.ms/amlps). Veja um exemplo das etapas de ponta a ponta relevantes para um processo automatizado padrão de implantação/execução de ALM usando o [módulo do PowerShell do Azure Machine Learning Studio](http://aka.ms/amlps). Cada etapa é vinculada a um ou mais cmdlets do PowerShell que você pode usar para realizar essa etapa.

1. [Carregar um conjunto de dados](https://github.com/hning86/azuremlps#upload-amldataset).
2. Copie um teste de treinamento no espaço de trabalho a partir de um [espaço de trabalho](https://github.com/hning86/azuremlps#copy-amlexperiment) ou de uma [Galeria](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery) ou [importe](https://github.com/hning86/azuremlps#import-amlexperimentgraph) um teste [exportado](https://github.com/hning86/azuremlps#export-amlexperimentgraph) do disco local.
3. [Atualizar o conjunto de dados](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) no teste de treinamento.
4. [Executar o teste de treinamento](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Promover o modelo treinado](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Copiar um teste de previsão](https://github.com/hning86/azuremlps#copy-amlexperiment) no espaço de trabalho.
7. [Atualizar o modelo treinado](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) no teste de previsão.
8. [Executar o teste de previsão](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Implantar um serviço Web](https://github.com/hning86/azuremlps#new-amlwebservice) do teste de previsão.
10. Testar o ponto de extremidade [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) ou [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) do serviço Web.

## <a name="next-steps"></a>Próximas etapas
* Baixe o módulo do [PowerShell do Azure Machine Learning Studio](http://aka.ms/amlps) e comece a automatizar suas tarefas de ALM.
* Saiba como [criar e gerenciar uma grande quantidade de modelos de ML, usando apenas um único teste](create-models-and-endpoints-with-powershell.md) por meio do PowerShell e da readaptação da API.
* Saiba mais sobre [como implantar serviços Web do Azure Machine Learning](publish-a-machine-learning-web-service.md).
