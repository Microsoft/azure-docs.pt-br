---
title: 'ML Studio (clássico): gerenciamento do ciclo de vida do aplicativo – Azure'
description: Aplicar as práticas recomendadas de gerenciamento do ciclo de vida do aplicativo no Azure Machine Learning Studio (clássico)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 10/27/2016
ms.openlocfilehash: 82d6f1fd250621ec34015970e1339ccfbd134675
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518395"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Gerenciamento do ciclo de vida do aplicativo no Azure Machine Learning Studio (clássico)

**APLICA-SE A:**  ![Aplica-se a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Azure Machine Learning Studio (clássico) é uma ferramenta para o desenvolvimento de experimentos de aprendizado de máquina que estão operacionais na plataforma de nuvem do Azure. Ele é parecido com o IDE do Visual Studio e o serviço de nuvem escalável mesclados em uma única plataforma. Você pode incorporar as práticas de ALM (gerenciamento do ciclo de vida do aplicativo) padrão de controle de versão de vários ativos à execução e implantação automatizadas, em Azure Machine Learning Studio (clássico). Este artigo discute algumas das opções e abordagens.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Teste de controle de versão
Há duas maneiras recomendadas de controlar a versão de seus testes. Você pode confiar no histórico de execução interno, ou exportar o teste em formato JSON para gerenciá-lo externamente. Cada abordagem tem seus prós e contras.

### <a name="experiment-snapshots-using-run-history"></a>Instantâneos de teste usando o Histórico de Execução
No modelo de execução do experimento de aprendizado Azure Machine Learning Studio (clássico), um instantâneo imutável do experimento é enviado para o Agendador de trabalhos sempre que você clica em **executar** no editor de experimento. Para exibir essa lista de instantâneos clique em **Histórico de Execução** na barra de comandos na visualização do editor de teste.

![Botão Histórico de Execução](./media/version-control/runhistory.png)

Assim, você pode abrir um instantâneo no modo Bloqueado clicando no nome do teste, no momento em que o teste foi enviado para execução e o instantâneo foi tirado. Observe que apenas o primeiro item na lista, que representa o teste atual, está em um estado Editável. Observe também que cada instantâneo pode apresentar diversos estados de Status, incluindo Concluído (Execução parcial), Falha, Falha (Execução parcial) ou Rascunho.

![Lista do Histórico de Execução](./media/version-control/runhistorylist.png)

Depois de aberto, você pode salvar o instantâneo do teste como um novo teste e, depois, modificá-lo. Se o instantâneo do teste contiver ativos, como modelos treinados, transformações ou conjuntos de dados, que têm versões atualizadas, o instantâneo manterá as referências à versão original quando o instantâneo foi tirado. Se você salvar o instantâneo bloqueado como um novo experimento, Azure Machine Learning Studio (clássico) detectará a existência de uma versão mais recente desses ativos e os atualizará automaticamente no novo experimento.

Se você excluir o teste, todos os instantâneos desse teste serão excluídos.

### <a name="exportimport-experiment-in-json-format"></a>Importar/exportar teste no formato JSON
Os instantâneos de histórico de execução mantêm uma versão imutável do experimento em Azure Machine Learning Studio (clássico) sempre que é enviado para execução. Você pode também salvar uma cópia local do teste e selecioná-lo para o seu sistema de controle de origem favorito, como o Team Foundation Server e, posteriormente, recriar um teste desde o arquivo local. Você pode usar os cmdlets do [PowerShell do Azure Machine Learning](https://aka.ms/amlps)[*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph), e [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) para fazer isso.

O arquivo JSON é uma representação textual do grafo de teste, que pode incluir referência aos ativos no workspace, como um conjunto de dados ou um modelo treinado. Ele não contém uma versão serializada do ativo. Se você tentar importar o documento JSON de volta para o workspace, os ativos referenciados já devem existir com as mesmas IDs de ativo referenciadas no teste. Caso contrário, você não poderá acessar o teste importado.

## <a name="versioning-trained-model"></a>Controle de versão do modelo treinado
Um modelo treinado no Azure Machine Learning Studio (clássico) é serializado em um formato conhecido como um arquivo iLearner ( `.iLearner` ) e é armazenado na conta de armazenamento de BLOBs do Azure associada ao espaço de trabalho. Uma maneira de obter uma cópia do arquivo iLearner é por meio da API de novos treinamentos. [Este artigo](./retrain-machine-learning-model.md) explica como funciona a API de novos treinamentos funciona. As etapas de alto nível:

1. Configurar seu teste de treinamento.
2. Adicionar uma porta de saída do serviço Web ao módulo Treinar Modelo, ou ao módulo que produza o modelo treinado, por exemplo, Ajustar Hiperparâmetro de Modelo ou Criar Modelo R.
3. Executar seu teste de treinamento e, depois, implantá-lo como o serviço Web de treinamento de um modelo.
4. Chamar o ponto de extremidade BES do serviço Web de treinamento e especificar o nome do arquivo iLearner desejado e o local da conta de Armazenamento de Blobs onde ele será armazenado.
5. Coletar o arquivo iLearner produzido após a conclusão da chamada do BES.

Outra maneira de recuperar o arquivo iLearner é por meio do cmdlet do PowerShell [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Isso pode ser mais fácil se você quiser apenas obter uma cópia do arquivo iLearner sem a necessidade de readaptar o modelo programaticamente.

Depois que você tiver o arquivo iLearner contendo o modelo treinado, é possível implantar sua própria estratégia de versão. A estratégia pode ser simples como aplicar um prefixo/sufixo como uma convenção de nomenclatura e deixar o arquivo iLearner no armazenamento do Blob ou copiar/importar o mesmo para o sistema de controle de versão.

Assim, o arquivo iLearner salvo pode ser usado para pontuação por meio de serviços Web implantados.

## <a name="versioning-web-service"></a>Controle de versão do serviço Web
Você pode implantar dois tipos de serviços Web de um experimento Azure Machine Learning Studio (clássico). O serviço Web clássico está intimamente ligado ao teste e ao workspace. O novo serviço Web usa a estrutura do Azure Resource Manager e não está mais ligado ao teste original nem ao workspace.

### <a name="classic-web-service"></a>Serviço Web clássico
Para controlar a versão de um serviço Web clássico, você pode aproveitar a construção do ponto de extremidade do serviço Web. Este é um fluxo típico:

1. De seu teste de previsão, implante um novo serviço Web clássico, que contém um ponto de extremidade padrão.
2. Crie um novo ponto de extremidade chamado ep2, que expõe a versão atual do teste/modelo treinado.
3. Volte e atualize seu teste de previsão e modelo treinado.
4. Reimplante o teste de previsão, que atualizará o ponto de extremidade padrão. Mas isso não alterará o ep2.
5. Crie um ponto de extremidade adicional denominado ep3, que expõe a nova versão do teste e do modelo treinado.
6. Volte para a etapa 3, se for necessário.

Com o tempo, será possível criar vários pontos de extremidade no mesmo serviço Web. Cada ponto de extremidade representa uma cópia pontual do teste contendo a versão pontual do modelo treinado. Em seguida, use a lógica externa para determinar qual ponto de extremidade chamar, o que significa efetivamente a seleção de uma versão do modelo treinado para a execução da pontuação.

Também é possível criar vários pontos de extremidade de serviço Web idênticos e, em seguida, aplicar versões diferentes do arquivo iLearner ao ponto de extremidade para obter um efeito semelhante. [Este artigo](create-models-and-endpoints-with-powershell.md) explica com mais detalhes sobre como fazer isso.

### <a name="new-web-service"></a>Novo serviço Web
Se você criar um novo serviço Web baseado no Azure Resource Manager, a construção do ponto de extremidade não estará mais disponível. Em vez disso, você pode gerar arquivos de definição de serviço Web (WSD), no formato JSON, do teste de previsão usando o commandlet do PowerShell [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) ou usando o commandlet do PowerShell [*Export-AzMlWebservice*](/powershell/module/az.machinelearning/export-azmlwebservice) de um serviço Web baseado no Resource Manager implantado.

Depois de exportar o arquivo WSD e controlar a versão dele, você também poderá implantar o WSD como um novo serviço Web em um plano de serviço Web diferente, em uma região diferente do Azure. Apenas forneça a configuração apropriada da conta de armazenamento, bem como a nova ID do plano do serviço Web. Para aplicar arquivos de iLearner diferentes, modifique o arquivo WSD e atualize a referência ao local do modelo treinado, e implante-o como um novo serviço Web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizar a implantação e a execução do teste
Um aspecto importante do ALM é ser capaz de automatizar a execução e o processo de implantação do aplicativo. No Azure Machine Learning Studio (clássico), você pode fazer isso usando o [módulo do PowerShell](https://aka.ms/amlps). Aqui está um exemplo de etapas de ponta a ponta que são relevantes para um processo de execução/implantação automatizado de ALM padrão usando o [módulo do PowerShell Azure Machine Learning Studio (clássico)](https://aka.ms/amlps). Cada etapa é vinculada a um ou mais cmdlets do PowerShell que você pode usar para realizar essa etapa.

1. [Carregar um conjunto de uma](https://github.com/hning86/azuremlps#upload-amldataset).
2. Copie um teste de treinamento no workspace a partir de um [workspace](https://github.com/hning86/azuremlps#copy-amlexperiment) ou de uma [Galeria](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery) ou [importe](https://github.com/hning86/azuremlps#import-amlexperimentgraph) um teste [exportado](https://github.com/hning86/azuremlps#export-amlexperimentgraph) do disco local.
3. [Atualizar o conjunto de dados](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) no teste de treinamento.
4. [Execute o teste de treinamento](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Promover o modelo treinado](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Copiar um teste de previsão](https://github.com/hning86/azuremlps#copy-amlexperiment) no workspace.
7. [Atualizar o modelo treinado](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) no teste de previsão.
8. [Executar o teste de previsão](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Implantar um serviço Web](https://github.com/hning86/azuremlps#new-amlwebservice) do teste de previsão.
10. Testar o ponto de extremidade [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) ou [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) do serviço Web.

## <a name="next-steps"></a>Próximas etapas
* Baixe o módulo do [PowerShell Azure Machine Learning Studio (clássico)](https://aka.ms/amlps) e comece a automatizar suas tarefas de ALM.
* Saiba como [criar e gerenciar uma grande quantidade de modelos de ML, usando apenas um único teste](create-models-and-endpoints-with-powershell.md) por meio do PowerShell e da readaptação da API.
* Saiba mais sobre [como implantar serviços Web do Azure Machine Learning](deploy-a-machine-learning-web-service.md).