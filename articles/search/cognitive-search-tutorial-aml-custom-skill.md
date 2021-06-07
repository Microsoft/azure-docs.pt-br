---
title: 'Exemplo: Criar e implantar uma habilidade personalizada com o Azure Machine Learning'
titleSuffix: Azure Cognitive Search
description: Esse exemplo demonstra como usar o Azure Machine Learning para criar e implantar uma habilidade personalizada no pipeline de enriquecimento de IA do Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 98d8395236bf955eed88f36c03c96981fa0e4b6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745627"
---
# <a name="example-build-and-deploy-a-custom-skill-with-azure-machine-learning"></a>Exemplo: Criar e implantar uma habilidade personalizada com o Azure Machine Learning 

Nesse exemplo, você usará o [conjunto de dados de avaliações de hotel](https://www.kaggle.com/datafiniti/hotel-reviews) (distribuído sob a licença Creative Commons [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.txt)) para criar uma [habilidade personalizada](./cognitive-search-aml-skill.md) usando o Azure Machine Learning para extrair os sentimentos baseados em aspectos das avaliações. Isso permite que a atribuição de sentimentos positivos e negativos na mesma avaliação seja atribuída corretamente para entidades identificadas como equipe, quarto, saguão ou piscina.

Para treinar o modelo de sentimentos baseado em aspectos no Azure Machine Learning, você usará o [repositório de receitas nlp](https://github.com/microsoft/nlp-recipes/tree/master/examples/sentiment_analysis/absa). O modelo será então implantado como um ponto de extremidade em um cluster do Kubernetes do Azure. Depois de implantado, o ponto de extremidade é adicionado ao pipeline de enriquecimento como uma habilidade do AML para uso pelo serviço Cognitive Search.

Dois conjuntos de dados são fornecidos. Se você quiser treinar o modelo por conta própria, o arquivo hotel_reviews_1000.csv será necessário. Prefere ignorar a etapa de treinamento? Baixe o hotel_reviews_100.csv.

> [!div class="checklist"]
> * Criar uma instância do Azure Cognitive Search
> * Criar um Workspace do Azure Machine Learning (o serviço de pesquisa e o workspace devem estar na mesma assinatura)
> * Treinar e implantar um modelo em um cluster Kubernetes do Azure
> * Vincular um pipeline de enriquecimento de IA ao modelo implantado
> * Ingerir a saída do modelo implantado como uma habilidade personalizada

> [!IMPORTANT] 
> Essa habilidade está atualmente em versão prévia pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Atualmente, o SDK do .NET não é compatível.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – obtenha uma [assinatura gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Serviço Cognitive Search](./search-get-started-arm.md)
* [Recurso dos Serviços Cognitivos](../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)
* [Conta do Armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Workspace do Azure Machine Learning](../machine-learning/how-to-manage-workspace.md)

## <a name="setup"></a>Instalação

* Clonar ou baixar os conteúdos do [repositório de exemplo](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill).
* Extraia o conteúdo se o download for um arquivo zip. Verifique se os arquivos são de leitura/gravação.
* Ao configurar as contas e os serviços do Azure, copie os nomes e as chaves para um arquivo de texto facilmente acessado. Os nomes e as chaves serão adicionados à primeira célula no notebook, em que as variáveis para acessar os serviços do Azure são definidas.
* Se você não estiver familiarizado com Azure Machine Learning e os respectivos requisitos, convém examinar estes documentos antes de começar:
 * [Configurar um ambiente de desenvolvimento para Azure Machine Learning](../machine-learning/how-to-configure-environment.md)
 * [Criar e gerenciar workspaces do Azure Machine Learning no portal do Azure](../machine-learning/how-to-manage-workspace.md)
 * Ao configurar o ambiente de desenvolvimento para o Azure Machine Learning, considere o uso da [instância de computação baseada em nuvem](../machine-learning/how-to-configure-environment.md#compute-instance) para uma introdução rápida e fácil.
* Carregue o arquivo do conjunto de dados em um contêiner na conta de armazenamento. O arquivo maior será necessário se você quiser executar a etapa de treinamento no notebook. Se você preferir ignorar a etapa de treinamento, será recomendável usar o arquivo menor.

## <a name="open-notebook-and-connect-to-azure-services"></a>Abra o notebook e conecte-se aos serviços do Azure

1. Coloque dentro da primeira célula todas as informações necessárias para as variáveis que permitirão o acesso aos serviços do Azure e execute essa célula.
1. A execução da segunda célula confirmará que você se conectou ao serviço de pesquisa para a assinatura.
1. As seções 1,1 a 1,5 criarão o indexador, o índice, o conjunto de habilidades e o armazenamento de dados de serviços de pesquisa.

Neste ponto, você pode optar por ignorar as etapas para criar o conjunto de dados de treinamento e experimentar no Azure Machine Learning, bem como ignorar diretamente o registro dos dois modelos fornecidos na pasta de modelos do repositório GitHub. Se você ignorar essas etapas, no notebook, você passará direto para a seção 3.5, Escrever um script de pontuação. Isso economizará tempo; as etapas de download e upload de dados podem levar até 30 minutos para ser concluídas.

## <a name="creating-and-training-the-models"></a>Como criar e treinar os modelos

A seção 2 tem seis células que baixam o arquivo de incorporações GloVe do repositório de receitas NLP. Após o download, o arquivo é carregado para o armazenamento de dados do Azure Machine Learning. O arquivo .zip é sobre 2G e levará algum tempo para executar essas tarefas. Depois de carregado, os dados de treinamento são então extraídos e agora você está pronto para passar para a seção 3.

## <a name="train-the-aspect-based-sentiment-model-and-deploy-your-endpoint"></a>Treinar o modelo de sentimentos baseado em aspectos e implantar seu ponto de extremidade

A seção 3 do notebook treinará os modelos que foram criados na seção 2, registrará esses modelos e os implantará como um ponto de extremidade em um cluster Kubernetes do Azure. Se você não estiver familiarizado com o Kubernetes do Azure, é altamente recomendável examinar os seguintes artigos antes de tentar criar um cluster de inferência:

* [Visão geral do serviço de Kubernetes do Azure](../aks/intro-kubernetes.md)
* [Conceitos de Kubernetes para o serviço de Kubernetes do Azure (AKS)](../aks/concepts-clusters-workloads.md)
* [Cotas, restrições de tamanho de máquina virtual e disponibilidade de região no AKS (Serviço de Kubernetes do Azure)](../aks/quotas-skus-regions.md)

O processo de criação e implantação do cluster de inferência pode levar até 30 minutos. É recomendável testar o serviço Web antes de passar para as etapas finais, atualizar seu conjunto de habilidades e executar o indexador.

## <a name="update-the-skillset"></a>Atualizar o conjunto de habilidades

A seção 4 do notebook tem quatro células que atualizam o conjunto de habilidades e o indexador. Como alternativa, você pode usar o portal para selecionar e aplicar a nova habilidade ao conjunto de habilidades e, em seguida, executar o indexador para atualizar o serviço de pesquisa.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Active-Learning-with-Azure-Cognitive-Search/player#time=19m35s:paused/03/player]

No portal, acesse o conjunto de habilidades e selecione o link Definição do Conjunto de Habilidades (JSON). O portal exibirá o JSON do seu conjunto de habilidades que foi criado nas primeiras células do notebook. À direita da exibição, há um menu suspenso em que você pode selecionar o modelo de definição de habilidade. Selecione o modelo do AML (Azure Machine Learning). Forneça o nome do workspace do Azure ML e o ponto de extremidade para o modelo implantado no cluster de inferência. O modelo será atualizado com o URI e a chave do ponto de extremidade.

> :::image type="content" source="media/cognitive-search-aml-skill/portal-aml-skillset-definition.png" alt-text="Modelo de definição de conjunto de habilidades":::

Copie o modelo do conjunto de habilidades da janela e cole-o na definição de conjunto de habilidades à esquerda. Edite o modelo para fornecer os valores ausentes para:

* Nome
* Descrição
* Contexto
* Nome e origem de "inputs"
* Nome e targetName de "outputs"

Salve o conjunto de habilidades.

Depois de salvar o conjunto de habilidades, vá para o indexador e selecione o link Definição do Indexador (JSON). O portal exibirá o JSON do indexador que foi criado nas primeiras células do notebook. Os mapeamentos de campo de saída precisarão ser atualizados com mapeamentos de campo adicionais para garantir que o indexador possa tratá-los e passá-los corretamente. Salve as alterações e, em seguida, selecione Executar. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Examinar a API Web de habilidades personalizadas](./cognitive-search-custom-skill-web-api.md)
> [Saiba mais sobre como adicionar habilidades personalizadas ao pipeline de enriquecimento](./cognitive-search-custom-skill-interface.md)