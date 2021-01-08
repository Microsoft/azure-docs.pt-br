---
title: Criar um projeto de rotulagem de dados
titleSuffix: Azure Machine Learning
description: Saiba como criar e executar projetos de rotulagem para marcar dados para o aprendizado de máquina.  Use a rotulagem assistida por ML ou a rotulagem de humanos no loop para ajudar com a tarefa.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: cd35cea28e23e88ba97bb7a27dc252d6bebd65e4
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739647"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Criar um projeto de rotulagem de dados e exportar rótulos 



A rotulagem de um grande volume de dados em projetos de Machine Learning costuma ser um problema. Os projetos que têm um componente de pesquisa visual computacional, como classificação de imagem ou detecção de objetos, geralmente exigem rótulos para milhares de imagens.
 
A rotulagem de dados do [Azure Machine Learning](https://ml.azure.com/) oferece um lugar central para criar, gerenciar e monitorar projetos de rotulagem. Use-o para coordenar dados, rótulos e membros da equipe, a fim de gerenciar tarefas de rotulagem com eficiência. O Machine Learning dá suporte à classificação de imagem, de vários rótulos ou multiclasse, e à identificação do objeto com caixas delimitadoras.

A rotulagem de dados acompanha o progresso e mantém a fila de tarefas de rotulagem incompletas.

É possível iniciar e parar o projeto e controlar o progresso do rótulo. É possível examinar os dados rotulados e exportá-los rotulados no formato COCO ou como um conjunto de dados do Azure Machine Learning.

> [!Important]
> Somente projetos de rotulagem de classificação de imagens e identificação de objetos são compatíveis no momento. Além disso, as imagens de dados devem estar disponíveis em um armazenamento de blobs do Azure. (Se você não tiver um armazenamento de dados existente, poderá carregar imagens durante a criação do projeto).

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um projeto
> * Especificar os dados e a estrutura do projeto
> * Executar e monitorar o projeto
> * Exportar os rótulos


## <a name="prerequisites"></a>Pré-requisitos

* Os dados que deseja rotular, em arquivos locais ou no Armazenamento de Blobs do Azure.
* O conjunto de rótulos que deseja aplicar.
* As instruções para rotulagem.
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um Workspace do Machine Learning. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Criar um projeto de rotulagem

Os projetos de rotulagem são administrados no Azure Machine Learning. Use a página **Projetos de rotulagem** para gerenciar os projetos.

Caso os seus dados já estejam no Armazenamento de Blobs do Azure, você deverá disponibilizá-los como um armazenamento de dados antes de criar o projeto de rotulagem. Para obter um exemplo de como usar um armazenamento de dados, veja o [Tutorial: Criar seu primeiro projeto de rotulagem de classificação de imagens](tutorial-labeling.md).

Para criar um projeto, selecione **Adicionar projeto**. Dê ao projeto um nome apropriado e selecione **Tipo de tarefa de rotulagem**.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Assistente de criação de projeto de rotulagem":::

* Escolha **Classificação de Imagem Multiclasse** para os projetos quando só desejar aplicar *um rótulo* de um conjunto de rótulos a uma imagem.
* Escolha **Classificação de Imagem de Vários Rótulos** para os projetos quando desejar aplicar *um ou mais* rótulos de um conjunto de rótulos a uma imagem. Por exemplo, uma foto de um cachorro pode ser rotulada com *cachorro* e *dia*.
* Escolha **Identificação do Objeto (Caixa Delimitadora)** para os projetos quando desejar atribuir um rótulo e uma caixa delimitadora a cada objeto dentro de uma imagem.

Selecione **Avançar** quando estiver pronto para continuar.

## <a name="specify-the-data-to-label"></a>Especificar os dados a serem rotulados

Se já tiver criado um conjunto de dados que contenha seus dados, selecione-o na lista suspensa **Selecionar um conjunto de dados existente**. Ou, então, selecione **Criar um conjunto de dados** para usar um armazenamento de dados do Azure existente ou fazer upload de arquivos locais.

> [!NOTE]
> Um projeto não pode conter mais que 500.000 imagens.  Se o conjunto de dados tiver mais, somente as primeiras 500.000 imagens serão carregadas.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Criar um conjunto de dados de um armazenamento de dados do Azure

Em muitos casos, é suficiente apenas fazer upload de arquivos locais. Mas o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) fornece uma maneira mais rápida e robusta de transferir uma grande quantidade de dados. Recomendamos o Gerenciador de Armazenamento como o método padrão de mover arquivos.

Para criar um conjunto de dados com base nos dados que você já armazenou no Armazenamento de Blobs do Azure:

1. Selecione **Criar um conjunto de dados** > **Com base no armazenamento de dados**.
1. Atribua um **Nome** ao conjunto de dados.
1. Escolha **Arquivo** como o **Tipo de conjunto de dados**.  Somente os tipos de conjunto de dados de arquivo são compatíveis.
1. Selecione o armazenamento de dados.
1. Se os dados estiverem em uma subpasta no Armazenamento de Blobs, escolha **Procurar** para selecionar o caminho.
    * Acrescente "/**" ao caminho para incluir todos os arquivos nas subpastas do caminho selecionado.
    * Acrescente "* */* .*" para incluir todos os dados no contêiner atual e nas subpastas.
1. Forneça uma descrição para o conjunto de dados.
1. Selecione **Avançar**.
1. Confirme os detalhes. Selecione **Voltar** para modificar as configurações ou **Criar** para criar o conjunto de dados.


### <a name="create-a-dataset-from-uploaded-data"></a>Criar um conjunto de dados com base nos dados carregados

Para fazer upload dos dados diretamente:

1. Selecione **Criar um conjunto de dados** > **Com base nos arquivos locais**.
1. Atribua um **Nome** ao conjunto de dados.
1. Escolha "Arquivo" como o **Tipo de conjunto de dados**.
1. *Opcional:* Selecione **Configurações avançadas** para personalizar o armazenamento de dados, o contêiner e o caminho para os dados.
1. Selecione **Procurar** para selecionar os arquivos locais a serem carregados.
1. Forneça uma descrição do conjunto de dados.
1. Selecione **Avançar**.
1. Confirme os detalhes. Selecione **Voltar** para modificar as configurações ou **Criar** para criar o conjunto de dados.

Os dados são carregados no armazenamento de blobs padrão ("workspaceblobstore") do Workspace do Machine Learning.

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a> Configurar a atualização incremental

Se você planeja adicionar novas imagens ao conjunto de dados, use a atualização incremental para adicionar essas novas imagens ao projeto.   Quando a **atualização incremental** está habilitada, o conjunto de dados é verificado periodicamente em busca de novas imagens a serem adicionadas ao projeto, com base na taxa de conclusão de rotulagem.   A verificação de novos dados é interrompida quando o projeto alcança o máximo de 500.000 imagens.

Para adicionar mais imagens ao projeto, use o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para carregar na pasta apropriada do armazenamento de blobs. 

Marque a caixa para **Habilitar a atualização incremental** quando quiser que o projeto monitore continuamente a inclusão de novos dados no armazenamento de dados. Esses dados serão obtidos em seu projeto uma vez por dia, quando a opção estiver habilitada.

Desmarque essa caixa se não quiser que as novas imagens que aparecem no armazenamento de dados sejam adicionadas ao projeto.

Você pode encontrar o carimbo de data/hora da atualização mais recente na seção de **Atualização incremental** da guia **Detalhes** do projeto.


## <a name="specify-label-classes"></a>Especificar classes de rótulo

Na página **Classes de rótulo**, especifique o conjunto de classes para categorizar os dados. Faça isso com cuidado, pois a precisão e a velocidade dos rotuladores serão afetadas pela capacidade deles de fazer escolhas entre as classes. Por exemplo, em vez de escrever por extenso o gênero e a espécie de plantas ou animais, use um código de campo ou abrevie o gênero.

Insira um rótulo por linha. Use o botão **+** para adicionar uma nova linha. Se você tiver mais de três ou quatro rótulos, porém, menos de 10, o ideal será colocar números como prefixos para os nomes ("1: ", "2: "), de modo que os rotuladores possam usar as teclas numéricas para acelerar o trabalho.

## <a name="describe-the-labeling-task"></a>Descrever a tarefa de rotulagem

É importante explicar a tarefa de rotulagem de maneira clara. Na página **Instruções de rotulagem**, você pode adicionar um link a um site externo para instruções de rotulagem ou fornecer instruções na caixa de edição da página. Mantenha as instruções orientadas a tarefas e apropriadas para o público-alvo. Considere estas perguntas:

* Quais são os rótulos que as pessoas verão e como elas escolherão entre eles? Há um texto de referência para consulta?
* O que elas deverão fazer se nenhum rótulo parecer apropriado?
* O que elas deverão fazer se vários rótulos parecerem apropriados?
* Qual limite de confiança elas devem aplicar a um rótulo? Você deseja que elas forneçam o "melhor palpite" se não tiverem certeza?
* O que elas deverão fazer com objetos de interesse parcialmente obstruídos ou sobrepostos?
* O que elas deverão fazer se um objeto de interesse for recortado pela borda da imagem?
* O que elas deverão fazer depois de enviarem um rótulo se acreditarem que cometeram um erro?

Com relação às caixas delimitadoras, entre as perguntas importantes se incluem:

* Como a caixa delimitadora é definida para essa tarefa? Ela deverá estar totalmente no interior do objeto ou deverá estar no exterior? Ela deverá ser cortada o mais próximo possível ou um espaço livre é aceitável?
* Qual nível de cuidado e de consistência você espera que os rotuladores apliquem na definição das caixas delimitadoras?
* Como rotular o objeto que é parcialmente mostrado na imagem? 
* Como rotular o objeto que é parcialmente coberto por outro objeto?

>[!NOTE]
> Observe que os rotuladores poderão selecionar os nove primeiros rótulos usando as teclas numéricas 1 a 9.

## <a name="use-ml-assisted-labeling"></a>Usar a rotulagem assistida por ML

A página **Rotulagem assistida por ML** permite que você dispare modelos de machine learning automáticos para acelerar a tarefa de rotulagem. No início do projeto de rotulagem, as imagens são embaralhadas em ordem aleatória para reduzir o desvio potencial. No entanto, qualquer desvio presente no conjunto de dados será refletido no modelo treinado. Por exemplo, se 80% das imagens forem de uma só classe, aproximadamente 80% dos dados usados para treinar o modelo serão dessa classe. Este treinamento não inclui o aprendizado ativo.

Selecione *Habilitar a rotulagem assistida por ML* e especifique uma GPU para habilitar a rotulagem assistida, que consiste em duas fases:
* Clustering
* Pré-rotulagem

O número exato de imagens rotuladas necessárias para iniciar a rotulagem assistida não é um número fixo.  Isso pode variar significativamente de um projeto de rotulagem para outro. Para alguns projetos, às vezes, é possível ver tarefas de pré-rótulo ou cluster depois de 300 imagens terem sido rotuladas manualmente. A rotulagem assistida por ML usa uma técnica chamada *transferência de aprendizado*, que usa um modelo pré-treinado para iniciar rapidamente o processo de treinamento. Se as classes do conjunto de dados forem semelhantes àquelas do modelo pré-treinado, os pré-rótulos poderão estar disponíveis após somente algumas centenas de imagens rotuladas manualmente. Se o conjunto de dados for significativamente diferente dos dados usados para pré-treinar o modelo, ele poderá levar muito mais tempo.

Como os rótulos finais ainda dependem da entrada do rotulador, essa tecnologia, às vezes, é chamada de rotulagem *humanos no loop*.

> [!NOTE]
> A rotulagem de dados assistida da ML não é compatível com contas de armazenamento padrão protegidas uma [rede virtual](how-to-network-security-overview.md). Você deve usar uma conta de armazenamento não padrão para rotular dados assistidos por ML. A conta de armazenamento não padrão pode ser protegida pela rede virtual. 

### <a name="clustering"></a>Clustering

Depois que um número especificado de rótulos é enviado, o modelo de machine learning para classificação de imagens começa a agrupar imagens semelhantes.  Essas imagens semelhantes são apresentadas aos rotuladores na mesma tela para acelerar a marcação manual. O clustering é especialmente útil quando o rotulador exibe uma grade de 4, 6 ou 9 imagens. 

Depois que um modelo de machine learning for treinado nos dados rotulados manualmente, o modelo será truncado para a última camada totalmente conectada. As imagens sem rótulo são passadas pelo modelo truncado em um processo normalmente conhecido como "incorporação" ou "personalização". Isso incorpora cada imagem em um espaço altamente dimensional definido por essa camada de modelo. As imagens que são os vizinhos mais próximos no espaço são usadas para tarefas de clustering. 

A fase de clustering não é exibida para modelos de detecção de objetos.

### <a name="prelabeling"></a>Pré-rotulagem

Depois que rótulos de imagem suficientes são enviados, um modelo de classificação é usado para prever marcas de imagem. Ou um modelo de detecção de objetos é usado para prever caixas delimitadoras. O rotulador agora vê as páginas que contêm rótulos previstos já presentes em cada imagem. Para a detecção de objetos, as caixas previstas também são mostradas. A tarefa passa a ser a análise dessas previsões e a correção das imagens incorretamente rotuladas antes de enviar a página.  

Depois que um modelo de machine learning for treinado nos dados rotulados manualmente, o modelo será avaliado em um conjunto de teste de imagens rotuladas manualmente para determinar a precisão em uma variedade de limites de confiança diferentes. Esse processo de avaliação é usado para determinar um limite de confiança acima do qual o modelo é preciso o suficiente para mostrar os pré-rótulos. O modelo é então avaliado em relação aos dados sem rótulo. As imagens com previsões mais confiantes do que esse limite são usadas para a pré-rotulagem.

## <a name="initialize-the-labeling-project"></a>Inicializar o projeto de rotulagem

Depois que o projeto de rotulagem for inicializado, alguns aspectos do projeto serão imutáveis. Não será possível alterar o tipo de tarefa nem o conjunto de dados. Você *poderá* modificar os rótulos e a URL para a descrição da tarefa. Examine cuidadosamente as configurações antes de criar o projeto. Depois de enviar o projeto, você será direcionado novamente para a home page **Rotulagem de Dados**, que mostrará o projeto como **Inicializando**.

> [!NOTE]
> Essa página talvez não seja atualizada automaticamente. Portanto, após uma pausa, atualize manualmente a página para ver o status do projeto como **Criado**.

## <a name="run-and-monitor-the-project"></a>Executar e monitorar o projeto
Depois que você inicializar o projeto, o Azure começará a executá-lo. Selecione o projeto na página principal **Rotulagem de Dados** para conferir os detalhes do projeto

Para pausar ou reiniciar o projeto, alterne o status **Em execução** no canto superior direito. Você só pode rotular dados quando o projeto está em execução.

### <a name="dashboard"></a>Painel

A guia **Painel** mostra o progresso da tarefa de rotulagem.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="Painel de rotulagem de dados":::

O gráfico de progresso mostra quantos itens foram rotulados e quantos ainda não foram feitos.  Os itens pendentes podem estar:

* Ainda não adicionados a uma tarefa
* Incluídos em uma tarefa que é atribuída a um rotulador, mas ainda não concluídos 
* Na fila de tarefas que ainda devem ser atribuídas

A seção intermediária mostra a fila de tarefas que ainda devem ser atribuídas. Quando a rotulagem assistida do ML está desativada, esta seção mostra o número de tarefas manuais a serem atribuídas. Quando a rotulagem assistida ML estiver ativada, esta seção também mostrará:

* Tarefas que contêm itens clusterizados na fila
* Tarefas que contêm itens pré-rotulados na fila

Além disso, quando a rotulagem assistida ML estiver habilitada, uma pequena barra de progresso mostrará quando a próxima execução de treinamento ocorrerá.  As seções de Experimentos fornecem links para cada uma das execuções de machine learning.

* Treinamento – treina um modelo para prever os rótulos
* Validação – determina se a previsão do modelo será usada para rotulagem prévia dos itens 
* Inferência – execução de previsão para novos itens
* Definição de recursos – itens de clusters (somente para projetos de classificação de imagens)

No lado direito, há uma distribuição dos rótulos para as tarefas concluídas.  Lembre-se de que em alguns tipos de projeto, um item pode ter vários rótulos e, nesse caso, o número total de rótulos pode ser maior que o número total de itens.

### <a name="data-tab"></a>Guia Dados

Na guia **Dados**, você poderá ver o conjunto de dados e examinar os dados rotulados. Se você perceber que há dados rotulados incorretamente, selecione-os e escolha **Rejeitar**, o que removerá os rótulos e colocará os dados novamente na fila sem rótulo.

### <a name="details-tab"></a>Guia Detalhes

Veja detalhes do seu projeto.  Nessa guia, é possível:

* Exibir detalhes do projeto e conjuntos de dados de entrada
* Habilitar a atualização incremental
* Exibir detalhes do contêiner de armazenamento usado para armazenar as saídas rotuladas no projeto
* Adicionar rotuladores ao projeto
* Editar as instruções que você fornece aos seus rótulos
* Editar detalhes da rotulagem assistida de ML, incluindo habilitar/desabilitar

### <a name="access-for-labelers"></a>Acesso a rotuladores

Qualquer pessoa que tenha acesso ao seu workspace pode rotular dados em seu projeto.  Você também pode personalizar as permissões para seus rotuladores para que o possa acessar a rotulagem, mas não outras partes do workspace ou do seu projeto de rotulagem.  Para obter mais detalhes, confira [Gerenciar o acesso a um Workspace do Azure Machine Learning](how-to-assign-roles.md) e saiba como criar a [função personalizada de rotulador](how-to-assign-roles.md#labeler).

## <a name="add-new-label-class-to-a-project"></a>Adicionar uma nova classe de rótulo a um projeto

Durante o processo de rotulagem, você pode achar que rótulos adicionais são necessários para classificar suas imagens.  Por exemplo, você pode adicionar um rótulo "Desconhecido" ou "Outro" para indicar imagens confusas.

Use estas etapas para adicionar um ou mais rótulos a um projeto:

1. Selecione o projeto na página principal **Rotulagem de Dados**.
1. Na parte superior direita da página, alterne **Em execução** para **Em pausa** para interromper a atividade dos rotuladores.
1. Selecione a guia **Detalhes**.
1. Na lista à esquerda, selecione **Classes de rótulo**.
1. No topo da lista, selecione **+ Adicionar rótulos** ![Adicionar um rótulo](media/how-to-create-labeling-projects/add-label.png)
1. No formulário, adicione o novo rótulo e escolha como continuar.  Como alterou os rótulos disponíveis para uma imagem, você escolhe como tratar os dados já rotulados:
    * Recomece, removendo todos os rótulos existentes.  Escolha esta opção se desejar começar a rotular desde o início com o novo conjunto completo de rótulos. 
    * Recomece, mantendo todos os rótulos existentes.  Escolha esta opção para marcar todos os dados como sem rótulo, mas mantenha os rótulos existentes como uma marca padrão para imagens que foram rotuladas anteriormente.
    * Continue, mantendo todos os rótulos existentes. Escolha esta opção para manter todos os dados já rotulados como estão, e comece a usar o novo rótulo para dados ainda não rotulados.
1. Modifique a página de instruções conforme necessário para os novos rótulos.
1. Depois de adicionar todos os novos rótulos, na parte superior direita da página, alterne **Em pausa** para **Em execução** para reiniciar o projeto.  

## <a name="export-the-labels"></a>Exportar os rótulos

Você pode exportar os dados de rótulo para experimentação no Machine Learning a qualquer momento. Os rótulos de imagens podem ser exportados no [formato COCO](http://cocodataset.org/#format-data) ou como um [conjunto de dados do Azure Machine Learning com rótulos](how-to-use-labeled-dataset.md). Use o botão **Exportar** na página **Detalhes do projeto** do projeto de rotulagem.

O arquivo COCO é criado no armazenamento de blobs padrão do Workspace do Azure Machine Learning em uma pasta dentro de *export/coco*. Acesse o conjunto de dados exportado do Azure Machine Learning na seção **Conjuntos de dados** do Machine Learning. A página de detalhes do conjunto de dados também fornece um código de exemplo para acessar seus rótulos por meio do Python.

![Conjunto de dados exportado](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>Solução de problemas

Use essas dicas caso veja algum desses problemas.

|Problema  |Resolução  |
|---------|---------|
|Somente conjuntos de dados criados em armazenamentos de dados de um blob poderão ser usados.     |  Essa é uma limitação conhecida da versão atual.       |
|Após a criação, o projeto mostrará a mensagem "Inicializando" por um longo período.     | Atualize a página de modo manual. A inicialização deverá prosseguir por aproximadamente 20 pontos de dados por segundo. A ausência de uma atualização automática é um problema conhecido.         |
|As imagens recém rotuladas não serão mostradas durante uma revisão.     |   Para carregar todas as imagens rotuladas, clique no botão **Primeiro**. O botão **Primeiro** levará você de volta ao início da lista. No entanto, ele carregará todos os dados rotulados.      |
|Pressionar a tecla ESC durante a rotulagem de uma detecção de objetos criará um rótulo de tamanho igual a zero no canto superior esquerdo. Haverá falha ao enviar rótulos nesse estado.     |   Exclua o rótulo clicando no ícone de x vermelho ao lado dele.  |

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Criar seu primeiro projeto de rotulagem de classificação de imagens](tutorial-labeling.md).
* Rotular imagens para [classificação de imagens ou detecção de objetos](how-to-label-images.md)
* Saiba mais sobre [o Azure Machine Learning e o Machine Learning Studio (clássico)](./overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
