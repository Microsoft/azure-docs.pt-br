---
title: FAQs (Perguntas frequentes) sobre o Azure Machine Learning | Microsoft Docs
description: "Introdução ao Azure Machine Learning: perguntas frequentes sobre cobrança, recursos e limitações de um serviço de nuvem para modelagem preditiva simplificada."
keywords: "introdução ao aprendizado de máquina, modelagem preditiva, o que é aprendizado de máquina"
services: machine-learning
documentationcenter: 
author: garyericson
manager: paulettm
editor: cgronlun
ms.assetid: a4a32a06-dbed-4727-a857-c10da774ce66
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/02/2017
ms.author: garye
ms.openlocfilehash: 2b4d04af7fe7a40a1d907a06ab8772f20956bc19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-frequently-asked-questions-billing-capabilities-limitations-and-support"></a>Perguntas frequentes (FAQ) sobre o Azure Machine Learning: cobrança, recursos, limitações e suporte
Aqui estão algumas perguntas frequentes e as respostas correspondentes sobre o Azure Machine Learning, um serviço de nuvem para o desenvolvimento de modelos de previsão e soluções de operacionalização por meio de serviços Web. Essas perguntas frequentes fornecem perguntas sobre como usar o serviço, o que inclui o modelo de cobrança, os recursos, as limitações e o suporte.

**Tem alguma pergunta que você não encontrou aqui?**

O aprendizado de máquina do Azure tem um fórum no MSDN onde membros da comunidade de ciência de dados podem fazer perguntas sobre o Azure Machine Learning. A equipe do Azure Machine Learning monitora o fórum. Acesse o [Fórum do Azure Machine Learning](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para encontrar respostas ou publique uma pergunta nova.

## <a name="general-questions"></a>Perguntas gerais
**O que é Azure Machine Learning?**

O Azure Machine Learning é um serviço totalmente gerenciado que você pode usar para criar, testar, operar e gerenciar soluções analíticas preditivas na nuvem. Com apenas um navegador, agora você pode entrar, fazer upload de dados e iniciar imediatamente experimentos de aprendizado de máquina. Modelagem de previsão do tipo "arrastar e soltar", um grande palete de módulos e uma biblioteca de modelos de início tornam as tarefas comuns de Aprendizado de Máquina algo rápido e simples. Para saber mais, consulte [Visão geral do serviço de Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Para obter uma introdução ao aprendizado de máquina que explica os conceitos e a terminologia principal, confira [Introdução ao Azure Machine Learning](what-is-machine-learning.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

**O que é o Machine Learning Studio?**

O Machine Learning Studio é um ambiente de trabalho que você acessa usando um navegador da Web. O Machine Learning Studio hospeda uma paleta de módulos em uma interface de composição visual que o habilita a criar um fluxo de trabalho de ciência de dados de ponta a ponta na forma de um teste.

Para saber mais sobre o Estúdio do Machine Learning, confira [O que é o Machine Learning Studio](what-is-ml-studio.md)

**O que é a o serviço de API de Machine Learning do Azure?**

O serviço de API do Machine Learning permite implantar modelos preditivos, como os internos do Machine Learning Studio, como serviços Web escalonáveis e tolerantes a falhas. Os serviços Web criados pelo serviço de API do Machine Learning são APIs REST, que fornecem uma interface para comunicação entre aplicativos externos e seu modelo de análise preditiva.

Para saber mais, veja [Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

**Onde estão listados os meus serviços Web clássicos? Onde estão listados os meus novos serviços da Web do Azure Resource Manager?**

Serviços Web criados usando o modelo de implantação Clássico e serviços Web criados usando o novo modelo de implantação do Azure Resource Manager são listados no portal de [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/).

Os serviços Web clássicos estão listados no [Machine Learning Studio](http://studio.azureml.net), na guia **Serviços Web**.

## <a name="azure-machine-learning-questions"></a>Perguntas sobre o Azure Machine Learning
**O que são Serviços Web do Azure Machine Learning?**

Os Serviços Web do Machine Learning fornecem uma interface entre um aplicativo e um modelo de pontuação do fluxo de trabalho do Machine Learning. Um aplicativo externo pode usar o Azure Machine Learning para se comunicar com um modelo de pontuação do fluxo de trabalho do Machine Learning em tempo real. Uma chamada a um serviço Web do Machine Learning retorna resultados de previsão para um aplicativo externo. Para fazer uma chamada a um serviço Web, você passa uma chave de API que foi criada quando você implantou o serviço Web. Um serviço Web do Machine Learning baseia-se em REST, uma opção popular de arquitetura para projetos de programação da Web.

O Azure Machine Learning tem dois tipos de serviços Web:

* RRS (Serviço de Solicitação-Resposta) – um serviço de baixa latência e altamente escalonável que fornece uma interface para os modelos sem monitoração de estado criados e implantados usando o Machine Learning Studio.
* Serviço de Execução de Lote (BES) – Um serviço assíncrono que pontua um lote de registros de dados.

Há várias maneiras para consumir a API REST e acessar o serviço Web. Por exemplo, você pode escrever um aplicativo em C#, R ou Python usando o código de exemplo gerado quando implantou o serviço Web.

O código de exemplo está disponível em:
- A página Consumo do serviço Web no portal de Serviços Web do Azure Machine Learning
- Página de Ajuda da API no painel do serviço Web no Machine Learning Studio

Você também pode usar a pasta de trabalho de exemplo do Microsoft Excel que é criada e está disponível no painel de serviço Web no Machine Learning Studio.

**Quais são as principais atualizações no Azure Machine Learning?**

Para ver as atualizações mais recentes, confira [O que há de novo no Azure Machine Learning](whats-new.md).

## <a name="machine-learning-studio-questions"></a>Perguntas sobre o Machine Learning Studio
### <a name="import-and-export-data-for-machine-learning"></a>Importar e exportar dados para o Machine Learning
**Para quais fontes de dados o Machine Learning dá suporte?**

Você pode baixar os dados para um experimento do Machine Learning Studio de três maneiras:

- Carregar um arquivo local como um conjunto de dados
- Usar um módulo para importar dados de serviços de dados de nuvem
- Importar um conjunto de dados salvo de outro experimento

Para saber mais sobre formatos de arquivo com suporte, confira [Importar dados de treinamento para o Machine Learning Studio](import-data.md).

#### <a id="ModuleLimit"></a>Que tamanho o conjunto de dados para os meus módulos pode ter?
Os módulos do Machine Learning Studio dão suporte a conjuntos de dados com até 10 GB de dados numéricos densos para casos de uso comuns. Se um módulo receber mais de uma entrada, o valor de 10 GB será o total dos tamanhos das entradas. Você também pode conjuntos de dados de exemplo maiores usando consultas do Hive ou de banco de dados Azure SQL ou pode usar o Learning por meio contagens de pré-processamento antes da ingestão.  

Os seguintes tipos de dados podem ser expandidos para conjuntos de dados maiores durante a normalização de recursos e são limitados a menos de 10 GB:

* Esparsos
* Categóricos
* Cadeias de caracteres
* Dados binários

Os seguintes módulos são limitados a conjuntos de dados com menos de 10 GB:

* Módulos de recomendação
* Módulo SMOTE (Técnica de Sobreamostragem Minoritária Sintética)
* Módulos de script: R, Python, SQL
* Módulos em que o tamanho dos dados de saída pode ser maior que o tamanho dos dados de entrada, como Join ou Feature Hashing
* Validação cruzada, Hiperparâmetros de Modelo de Ajuste, Regressão Ordinal e Classes Múltiplas, um versos todos, quando o número de iterações é muito grande

#### <a id="UploadLimit"></a>Quais são os limites para o upload de dados?
Para conjuntos de dados maiores do que alguns GB, carregue dados no Armazenamento do Azure ou no banco de dados Azure SQL ou use o Azure HDInsight em vez de carregar diretamente de um arquivo local.

**Eu posso ler dados da Amazon S3?**

Se tiver uma pequena quantidade de dados e quiser expô-los por meio de uma URL HTTP, você pode usar o módulo [Importar Dados][import-data]. Para uma quantidade maior de dados, transfira-os para o Armazenamento do Azure primeiro e use o módulo [Importar Dados][import-data] para colocá-los em seu teste.
<!--

<SEE CLOUD DS PROCESS>
-->

**Há uma funcionalidade interna de entrada de imagem?**

Você pode aprender sobre a funcionalidade de entrada de imagem na referência [Importar imagens][image-reader].

### <a name="modules"></a>Módulos
**O algoritmo, a fonte de dados, o formato de dados ou a operação de transformação de dados que estou procurando não está no Estúdio do Azure Machine Learning. Quais são minhas opções?**

Você pode acessar o [fórum de comentários dos usuários](http://go.microsoft.com/fwlink/?LinkId=404231) para ver solicitações de recurso que estamos acompanhando. Inclua seu voto em uma solicitação se um recurso que está procurando já foi solicitado. Se o recurso que está procurando não existe, crie uma nova solicitação. É possível exibir o status de sua solicitação neste fórum também. Acompanhamos esta lista de perto e atualizamos o status da disponibilidade do recurso com frequência. Além disso, você pode usar o suporte interno para R e Python para criar transformações personalizadas quando necessário.

**Posso trazer meu código existente para o Machine Learning Studio?**

Sim, é possível levar seus códigos existentes de R ou Python para o Machine Learning Studio, executá-los no mesmo teste com os aprendizes fornecidos pelo Azure Machine Learning e implantar a solução como um serviço Web por meio do Azure Machine Learning. Para obter mais informações, consulte [Estender seu experimento com R](extend-your-experiment-with-r.md) e [Executar scripts Python de aprendizado de máquina no Azure Machine Learning Studio](execute-python-scripts.md).

**É possível usar algo como [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) para definir um modelo?**

Não, não há suporte para PMML (Predictive Model Markup Language). Você pode usar código R e Python personalizado para definir um módulo.

**Quantos módulos posso executar em paralelo em meu experimento?**  

Você pode executar até quatro módulos em paralelo em um experimento.

### <a name="data-processing"></a>Processamento de dados
**Há uma capacidade de visualizar dados (além de visualizações de R) interativamente dentro do experimento?**

Clique na saída de um módulo para visualizar os dados e obter estatísticas.

**Ao visualizar resultados ou dados em um navegador, o número de linhas e colunas é limitado. Por quê?**

Como grandes quantidades de dados podem ser enviadas a um navegador, o tamanho dos dados é limitado para evitar a lentidão no Machine Learning Studio. Para visualizar todos os dados/resultados, é melhor baixar os dados e usar o Excel ou outra ferramenta.

### <a name="algorithms"></a>Algoritmos
**Quais algoritmos existentes têm suporte no Machine Learning Studio?**

O Machine Learning Studio fornece algoritmos de última geração, como Árvores de Decisão Aumentadas Escalonáveis, sistemas de Recomendação Bayesiana, Redes Neurais Profundas e Selvas de Decisão desenvolvidos na Microsoft Research. Pacotes de aprendizado de máquina escalonáveis de software livre, como Vowpal Wabbit, também estão incluídos. O Machine Learning Studio dá suporte a algoritmos de aprendizado de máquina para classificação binária e de múltiplas classes, de regressão e de clustering. Consulte a lista completa de [Módulos do Machine Learning][machine-learning-modules].

**Vocês sugerem automaticamente o algoritmo correto do Machine Learning para usar com meus dados?**

Não, mas o Machine Learning Studio tem várias maneiras de comparar os resultados de cada algoritmo para determinar o que é adequado para o seu problema.

**Vocês têm alguma orientação sobre a seleção de um algoritmo em vez de outro para os algoritmos fornecidos?**

Consulte [Como escolher um algoritmo](algorithm-choice.md).

**Os algoritmos fornecidos são escritos em R ou Python?**

Não, esses algoritmos são escritos, em sua maioria, em linguagens compiladas para fornecer um melhor desempenho.

**São fornecidos detalhes dos algoritmos?**

A documentação fornece algumas informações sobre os algoritmos, e os parâmetros para ajuste são descritos para otimizar o algoritmo para seu uso.  

**Há algum suporte para aprendizado online?**

Não, atualmente há suporte apenas para treinamento programático.

**Posso visualizar as camadas de um Modelo de Rede Neural usando o módulo interno?**

Não.

**Posso criar meus próprio módulos em C# ou em outra linguagem?**

No momento, você só pode usar R para criar novos módulos personalizados.

### <a name="r-module"></a>Módulo R
**Quais pacotes de R estão disponíveis no Machine Learning Studio?**

O Machine Learning Studio dá suporte a mais de 400 pacotes CRAN R atualmente. Aqui está a [lista atual](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de todos os pacotes incluídos. Consulte também [Estender seu experimento com R](extend-your-experiment-with-r.md) para saber como recuperar essa lista por conta própria. Se o pacote desejado não estiver nessa lista, forneça o nome do pacote no [fórum de comentários do usuário](http://go.microsoft.com/fwlink/?LinkId=404231).

**É possível criar um módulo personalizado em R?**

Sim, consulte [Criar módulos personalizados em R no Azure Machine Learning](custom-r-modules.md) para saber mais.

**Há um ambiente REPL para R?**

Não, não há um ambiente REPL (Read-Eval-Print-Loop) para R no Studio.

### <a name="python-module"></a>Módulo de Python
**É possível criar um módulo personalizado em Python?**

Atualmente, não, mas você pode usar um ou mais módulos [Executar script Python][python] para obter o mesmo resultado.

**Há um ambiente REPL para Python?**

Você pode usar os blocos de notas Jupyter no Machine Learning Studio. Para saber mais, confira [Introdução aos blocos de notas Jupyter no Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## <a name="web-service"></a>Serviço Web
### <a name="retrain"></a>Treinar novamente
**Como posso readaptar os modelos de Machine Learning de forma programática?**

Use as APIs de novos treinamentos. Para obter mais informações, consulte [Treinar novamente os modelos de Machine Learning de forma programática](retrain-models-programmatically.md). O exemplo de código também está disponível na [Demonstração de readaptação com o Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

### <a name="create"></a>Criação
**Posso implantar o modelo localmente ou em um aplicativo que não tem uma conexão com a Internet?**

Não.

**Há uma latência de linha de base que é esperada para todos os serviços Web?**

Confira [Limites da assinatura do Azure](../../azure-subscription-service-limits.md).

### <a name="use"></a>Uso
**Quando seria melhor executar meu modelo de previsão como um serviço de Execução em Lotes em vez de um serviço de Resposta da Solicitação?**

O Serviço de Solicitação-Resposta (RRS) é um serviço Web de baixa latência e alta escala usado para fornecer uma interface para modelos sem monitoração de estado que são criados e implantados por meio do ambiente de testes. O BES (Serviço de Execução em Lote) é um serviço que classifica de forma assíncrona um lote de registros de dados. A entrada para BES é como os dados de entrada que RRS usa. A principal diferença é que o BES lê um bloco de registros de uma variedade de fontes, como o armazenamento de Blobs do Azure, o armazenamento de Tabelas do Azure, um banco de dados do SQL Azure, o HDInsight (consulta hive) e fontes HTTP. Para saber mais, veja [Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

**Como posso atualizar o modelo para o serviço Web implantado?**

Para atualizar um modelo de previsão para um serviço já implantado, modifique e execute novamente o experimento que você usou para criar e salvar o modelo treinado. Depois que você tiver uma nova versão do modelo adaptado disponível, o Machine Learning Studio perguntará se você deseja atualizar o serviço Web. Para obter detalhes sobre como atualizar um serviço Web implantado, confira [Implantar um serviço Web do Machine Learning](publish-a-machine-learning-web-service.md).

Você também pode usar as APIs de novos treinamentos.
Para obter mais informações, consulte [Treinar novamente os modelos de Machine Learning de forma programática](retrain-models-programmatically.md). O exemplo de código também está disponível na [Demonstração de readaptação com o Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

**Como posso monitorar meu serviço Web implantado na produção?**

Após implantar um modelo de previsão, você pode monitorá-lo no portal clássico do Azure (somente os serviços Web clássicos) ou no portal dos Serviços Web do Azure Machine Learning. Cada serviço implantado tem seu próprio painel, onde você pode ver informações de monitoramento do serviço. Para obter mais informações sobre como gerenciar os serviços Web implantados, confira [Gerenciar um serviço Web usando o portal dos Serviços Web do Azure Machine Learning](manage-new-webservice.md) e [Gerenciar um espaço de trabalho do Azure Machine Learning](manage-workspace.md).

**Existe um lugar onde posso ver a saída do meu RRS/BES?**

Para RRS, a resposta do serviço Web é normalmente onde você vê o resultado. Você também pode escrevê-lo no armazenamento de Blobs do Azure. Para BES, a saída é gravada em um blob, por padrão. Você também pode gravar a saída em um banco de dados ou em uma tabela usando o módulo [Exportar Dados][export-data].

**Posso criar serviços Web apenas de modelos criados no Machine Learning Studio?**

Não, você também pode criar serviços Web diretamente usando blocos de anotações do Jupyter e RStudio.

**Onde posso encontrar informações sobre códigos de erro?**

Confira [Códigos de erro do módulo de Machine Learning](https://msdn.microsoft.com/library/azure/dn905910.aspx) para obter uma lista dos códigos de erro e descrições.

## <a name="scalability"></a>Escalabilidade
**Qual é a escalabilidade do serviço Web?**

Atualmente, o ponto de extremidade padrão é provisionado com 20 solicitações RRS simultâneas por ponto de extremidade. Você pode dimensioná-lo com até 200 solicitações simultâneas por ponto de extremidade e os serviços Web com até 10.000 pontos de extremidade por serviço Web, como descrito em [Dimensionando um serviço Web](scaling-webservice.md). Para BES, cada ponto de extremidade pode processar 40 solicitações por vez. Acima de 40 solicitações, as restantes são enfileiradas. Essas solicitações enfileiradas são executadas automaticamente conforme a fila anda.

**Trabalhos em R são distribuídos entre nós?**

Nº  

**Quantos dados posso usar para treinamento?**

Os módulos do Machine Learning Studio dão suporte a conjuntos de dados com até 10 GB de dados numéricos densos para casos de uso comuns. Se um módulo tiver mais de uma entrada, o tamanho total de todas as entradas será de 10 GB. Você também pode criar amostras de conjuntos de dados maiores por meio de consultas Hive, por meio de consultas ao Banco de Dados SQL do Azure ou fazer o pré-processamento com módulos [Aprendizado por contagens][counts] antes do uso.  

Os seguintes tipos de dados podem ser expandidos para conjuntos de dados maiores durante a normalização de recursos e são limitados a menos de 10 GB:

* Esparsos
* Categóricos
* Cadeias de caracteres
* Dados binários

Os seguintes módulos são limitados a conjuntos de dados com menos de 10 GB:

* Módulos de recomendação
* Módulo SMOTE (Técnica de Sobreamostragem Minoritária Sintética)
* Módulos de script: R, Python, SQL
* Módulos em que o tamanho dos dados de saída pode ser maior que o tamanho dos dados de entrada, como Join ou Feature Hashing
* Validação Cruzada, Hiperparâmetros de Modelo de Ajuste, Regressão Ordinal e Classes Múltiplas, um versos todos, quando o número de iterações é muito grande

Para conjuntos com mais de alguns GB, faça upload de dados para o armazenamento do Azure ou Banco de Dados Azure SQL ou use o HDInsight, em vez de fazer upload diretamente de um arquivo local.

**Há qualquer limitação de tamanho de vetores?**

Linhas e colunas são têm a limitação do .NET de Int Máximo: 2.147.483.647.

**Posso ajustar o tamanho da máquina virtual que executa o serviço Web?**

Não.  

## <a name="security-and-availability"></a>Segurança e disponibilidade
**Quem pode acessar o ponto de extremidade http para o serviço Web por padrão? Como faço para restringir o acesso ao ponto de extremidade?**

Depois que um serviço Web for implantado, criaremos um ponto de extremidade padrão para esse serviço. O ponto de extremidade padrão pode ser chamado usando sua chave de API. Você pode adicionar mais pontos de extremidade com suas próprias chaves no portal clássico do Azure ou de forma programática, usando as APIs de Gerenciamento de Serviço Web. São necessárias chaves de acesso para fazer chamadas ao serviço Web. Para saber mais, veja [Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

**O que acontece se minha conta de armazenamento do Azure não puder ser encontrada?**

O Machine Learning Studio depende de uma conta de armazenamento do Azure fornecida pelo usuário para salvar dados intermediários ao executar o fluxo de trabalho. Esta conta de armazenamento é fornecida ao Machine Learning Studio quando um espaço de trabalho é criado. Após o espaço de trabalho ser criado, se a conta de armazenamento for excluída e não puder mais ser encontrada, o espaço de trabalho deixará de funcionar e todos os testes nele falharão.

Se você excluir acidentalmente a conta de armazenamento, recrie essa conta de armazenamento exatamente com o mesmo nome e exatamente na mesma região que a conta de armazenamento excluída. Depois disso, sincronize novamente a chave de acesso.

**O que acontecerá se a chave de acesso da minha conta de armazenamento não estiver sincronizada?**

O Machine Learning Studio depende de uma conta de armazenamento do Azure fornecida pelo usuário para armazenar dados intermediários ao executar o fluxo de trabalho. Esta conta de armazenamento é fornecida ao Machine Learning Studio quando um espaço de trabalho é criado e as chaves de acesso são associadas a ele. Se as chaves de acesso forem alteradas, depois que o espaço de trabalho for criado, ele não poderá mais acessar a conta de armazenamento. Ele deixará de funcionar e todos os testes no espaço de trabalho falharão.

Se tiver alterado as chaves de acesso da conta de armazenamento, sincronize novamente as chaves de acesso na configuração do espaço de trabalho usando o portal clássico do Azure.  

## <a name="support-and-training"></a>Suporte e treinamento
**Onde posso obter treinamento para o Azure Machine Learning?**

O [Centro de Documentação do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) hospeda tutoriais em vídeo e guias de instruções. Esses guias passo a passo apresentam os serviços e explicam o ciclo de vida de ciência de dados de importação de dados, limpeza de dados, criação de modelos de previsão e sua implantação em produção usando o Azure Machine Learning.

Incluímos novo material continuamente no Machine Learning Center. Você pode enviar solicitações de material de aprendizado adicional no Centro de Machine Learning no [fórum de comentários dos usuários](https://windowsazure.uservoice.com/forums/257792-machine-learning).

Você também pode encontrar treinamento na [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Como fazer para obter suporte ao Azure Machine Learning?**

Para obter suporte técnico para o Azure Machine Learning, vá para [Suporte do Azure](/support/options/) e selecione **Machine Learning**.

O Azure Machine Learning também possui um fórum de comunidade no MSDN, em que é possível fazer perguntas sobre o Azure Machine Learning. A equipe do Azure Machine Learning monitora o fórum. Acesse o [Fórum do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## <a name="billing-questions"></a>Perguntas sobre cobrança
**Como a cobrança do Machine Learning funciona?**

O Azure Machine Learning tem dois componentes: Machine Learning e serviços Web do Machine Learning Studio.

Enquanto você estiver avaliando o Machine Learning Studio, poderá usar a camada de cobrança Gratuita. A camada Gratuita permite implantar um serviço Web clássico que tem capacidade limitada.

Se decidir que o Azure Machine Learning atende às suas necessidades, você poderá se inscrever na camada Standard. Para se inscrever, você precisa ter uma assinatura do Microsoft Azure.

Na camada Standard, você será cobrado mensalmente por cada espaço de trabalho que definir no Machine Learning Studio. Quando executa um teste no estúdio, você é cobrado por recursos de computação durante a execução do teste. Quando você implanta um serviço Web clássico, as transações e horas de computação são Pré-pagas.

Novos serviços Web (baseados no Gerenciador de Recursos) introduzem planos de cobranças que permitem maior previsibilidade nos custos. Preços em camadas oferecem taxas com desconto para clientes que precisam de uma maior capacidade.

Quando você cria um plano, assume um custo fixo com uma quantidade incluída de horas de computação de API e de transações de API. Se precisar de mais quantidades incluídas, poderá adicionar instâncias ao plano. Se precisar de muito mais quantidades incluídas, você poderá escolher um plano de camada superior que tenha quantidades incluídas ainda maiores e uma melhor taxa de desconto.

Depois que as quantidades incluídas em instâncias existentes forem consumidas, o uso adicional será cobrado na taxa excedente associada à camada de plano de cobrança.

> [!NOTE]
As quantidades incluídas são realocadas a cada 30 dias e as quantidades não usadas não passam para o período seguinte.

Para saber mais sobre preços e cobrança, confira [Preços do Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

**O Machine Learning tem uma avaliação gratuita?**

 O Azure Machine Learning tem uma opção de assinatura gratuita que é explicada em [Preços do Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/). O Machine Learning Studio tem uma avaliação rápida de oito horas que está disponível quando você entra no [Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2).

 Além disso, quando você se inscrever em uma avaliação gratuita do Azure, poderá experimentar qualquer serviço do Azure por um mês. Para saber mais sobre a avaliação gratuita do Azure, visite [Perguntas frequentes sobre a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial-faq/).

**O que é uma transação?**

Uma transação representa uma chamada à API a qual responde o Azure Machine Learning. AS chamadas RRS (Serviço de solicitação-resposta) e BES (Serviço de execução em lote) são agregadas e cobradas em seu plano de cobrança.

**Posso usar as quantidades de transação incluídas em um plano para transações RRS e BES?**

Sim, as transações dos RRS e BES são agregadas e cobradas em seu plano de cobrança.

**O que é uma hora de computação de API?**

Uma hora de computação da API é a unidade de cobrança para o tempo que as chamadas de API levam para realizar a execução usando recursos de computação do Machine Learning. Todas as chamadas são agregadas para fins de cobrança.

**Quanto tempo demora uma chamada típica à API de produção?**

Os tempos de chamada de API de produção podem variar de forma significativa, geralmente variando de centenas de milissegundos a alguns segundos. Algumas chamadas de API podem levar minutos, dependendo da complexidade do processamento de dados e do modelo de aprendizado de máquina. A melhor forma de estimar os tempos de chamada à API de produção é submeter um modelo a benchmark no serviço Machine Learning.

**O que é uma hora de computação do Studio?**

Uma hora de computação do Studio é a unidade de cobrança para o tempo agregado em que seus testes usam recursos de computação no Estúdio.

**Em novos serviços Web (com base no Azure Resource Manager), para que serve a camada de Desenvolvimento/Teste?**

Serviços Web baseados no Gerenciador de Recursos fornecem várias camadas que você pode usar para provisionar seu plano de cobrança. O tipo de preços de Desenvolvimento/Teste fornece quantidades limitadas incluídas que permitem que você teste o experimento como um serviço Web sem incorrer em custos. Você tem a oportunidade de ver como ele funciona.

**Existem encargos de armazenamento separados?**

A camada Gratuita do Machine Learning não exige nem permite armazenamento separado. A camada Standard do Machine Learning exige que os usuários tenham uma conta de armazenamento do Azure. O Armazenamento do Azure [é cobrado separadamente](https://azure.microsoft.com/pricing/details/storage/).

**O Machine Learning dá suporte a alta disponibilidade?**

Sim. Para obter detalhes, confira [Preços do Machine Learning](https://azure.microsoft.com/en-us/pricing/details/machine-learning/) para obter uma descrição do SLA (contrato de nível de serviço).

**Em quais tipos específicos de recursos de computação minhas chamadas à API de produção serão executadas?**

O Machine Learning é um serviço multilocatário. Os recursos de computação real que são usados no back-end variam e são otimizados para desempenho e previsibilidade.

### <a name="management-of-new-resource-manager-based-web-services"></a>Gerenciamento de novos serviços Web (com base no Gerenciador de Recursos)
**O que acontece se eu excluir meu plano?**

O plano é removido de sua assinatura e você é cobrado pelo uso rateado.

> [!NOTE]
Você não pode excluir um plano que um serviço Web está usando. Para excluir o plano, atribua um novo plano ao serviço Web ou exclua o serviço Web.

**O que é uma instância de plano?**

Uma instância de plano é uma unidade de quantidades incluídas que você pode adicionar ao seu plano de cobrança. Quando você seleciona uma camada de cobrança para o plano de cobrança, ela vem com uma instância. Se você precisar de mais quantidades incluídas, poderá adicionar instâncias da camada de cobrança selecionada ao seu plano.

**Quantas instâncias do plano posso adicionar?**

Você pode ter uma instância do tipo de preços de Desenvolvimento/Teste em uma assinatura.

Para as camadas Standard S1, Standard S2 e Standard S3, você pode adicionar tantas quantas forem necessárias.

> [!NOTE]
Dependendo de seu uso antecipado, pode ser mais econômico atualizar para uma camada que tenha mais quantidades incluídas, em vez de adicionar instâncias à camada atual.

**O que acontece quando eu mudo de camadas de plano (atualização/downgrade)?**

O plano antigo será excluído e o uso atual será cobrado em uma base rateada. Um novo plano com as quantidades incluídas completas da camada atualizada/com downgrade é criado para o restante do período.

> [!NOTE]
As quantidades incluídas são alocadas por período e as quantidades não usadas não são transferidas para o período seguinte.

**O que acontece quando eu aumento as instâncias em um plano?**

As quantidades estão incluídas em uma base rateada e podem levar até 24 horas para entrar em vigor.

**O que acontece quando eu excluo uma instância de um plano?**

A instância é removida de sua assinatura e você é cobrado pelo uso rateado.

### <a name="sign-up-for-new-resource-manager-based-web-services-plans"></a>Inscrever-se em novos planos de serviços Web (com base no Gerenciador de Recursos)
**Como faço para assinar um plano?**

Existem duas maneiras de criar planos de cobrança.

Ao implantar pela primeira vez um serviço Web baseado no Gerenciador de Recursos, você pode escolher um plano existente ou criar um novo.

Planos criados dessa maneira ficam em sua região padrão, e o serviço Web é implantado nessa região.

Se quiser implantar serviços em regiões diferentes da sua região padrão, convém definir seus planos de cobrança antes de implantá-lo.

Nesse caso, você pode entrar no portal de Serviços Web do Azure Machine Learning e acessar a página Planos. A partir daí, você pode adicionar e excluir planos e modificar os existentes.

**Com que plano devo começar?**

Recomendamos que você comece com a camada Standard S1 e monitore o uso de seu serviço. Se estiver consumindo suas quantidades incluídas rapidamente, poderá adicionar instâncias ou mudar para uma camada mais elevada e obter melhores taxas de desconto. Você pode ajustar seu plano de cobrança conforme o necessário em todo seu ciclo de cobrança.

**Em quais regiões os novos planos estão disponíveis?**

Os novos planos de cobrança estão disponíveis nas três regiões de produção em que há suporte para os novos serviços Web:

* Centro-Sul dos Estados Unidos
* Europa Ocidental
* Sudeste da Ásia

**Tenho serviços Web em várias regiões. Preciso de um plano para cada região?**

Sim. Os preços de planos variam para cada região. Ao implantar um serviço Web em outra região, você precisará atribuir a ele um plano específico dessa região. Para obter mais informações, veja [Produtos disponíveis por região]( https://azure.microsoft.com/regions/services/).

### <a name="new-web-services-overages"></a>Novos serviços Web: Excedentes
**Como verificar se excedi o uso do serviço Web?**

Você pode exibir o uso em todos os seus planos na página Planos no portal de serviços Web de Machine Learning. Entre no portal e clique na opção de menu **Planos**.

Nas colunas **Transações** e **Computação** da tabela, você pode ver as quantidades incluídas do plano e a porcentagem de uso.

**O que acontece quando uso toda a quantidade incluída no tipo de preços de Desenvolvimento/Teste?**

Serviços que têm um tipo de preços de Desenvolvimento/Teste atribuída a eles são interrompidos até o próximo período ou até que você os mova para uma camada paga.

**Para os serviços Web Clássico e excedentes de serviços Web Novos (com base no Gerenciador de Recursos), como os preços são calculados para cargas de trabalho de RRS (Solicitação de Resposta) e BES (Lote)?**

Para uma carga de trabalho RRS, você será cobrado por todas as chamadas de transação de API que fizer, além do tempo de cálculo associado a essas solicitações. Seus custos da transação de API de produção RRS são, portanto, calculados como o número total de chamadas de API que você faz multiplicado pelo preço por mil transações (rateado por transação individual). Seus custos de horas de computação da API de produção de API RRS são calculados como a quantia de tempo necessária para cada chamada à API ser executada, multiplicada pelo número total de transações de API multiplicado pelo preço por hora de computação da API de produção.

Por exemplo, para excedente em Standard S1, um milhão de transações de API que demoram 0,72 segundo cada para execução resultariam em (1.000.000 * US$ 0,50/1K de transações API) em US$ 500 em custos de transação de API de produção e (1,000,000 * 0,72 s * US$ 2/h) US$ 400 nas horas de computação da API de produção, para um total de US$ 900.

Para uma carga de trabalho do BES, você é cobrado da mesma maneira. No entanto, os custos de transação de API representam o número de trabalhos de lote que você enviou e os custos calculados representam o tempo calculado associado a esses trabalhos de lote. Seus custos da transação de API de produção BES são, portanto, calculados como o número total de trabalhos enviados multiplicado pelo preço por mil transações (rateado por transação individual). Seus custos de horas de computação de API de produção de API BES são calculados como a quantia de tempo necessária para cada linha em seu trabalho para execução multiplicada pelo número total de linhas em seu trabalho, multiplicado pelo número total de trabalhos, multiplicado pelo preço por hora de computação da API de produção. Quando você usa a calculadora Machine Learning, a métrica de transação representa o número de trabalhos que planeja enviar e o tempo por campo de transação representa o tempo combinado necessário para execução de todas as linhas em cada trabalho.

Por exemplo, suponha que Standard S1 seja excedente e você envie 100 trabalhos por dia, cada um consistindo em 500 linhas que utilizam 0,72 segundo. Os custos mensais excedentes seriam (100 trabalhos por dia = 3.100 trabalhos/mês * US$ 0,50/1K transações de API) US$ 1,55 em custos de transação de API de produção e (500 linhas * 0,72 s * 3.100 trabalhos * US$ 2/hora) US$ 620 em horas de computação de API de produção, para um total de US$ 621,55.

### <a name="azure-machine-learning-classic-web-services"></a>Serviços Web Clássicos do Azure Machine Learning
**O pré-pago ainda está disponível?**

Sim, os serviços Web clássicos ainda estão disponíveis no Azure Machine Learning.  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Camadas Gratuita e Standard do Machine Learning
**O que está incluído na Camada gratuita do Azure Machine Learning?**

A camada Gratuita do Azure Machine Learning tem o objetivo de fornecer uma introdução abrangente ao Azure Machine Learning Studio. Tudo o que você precisa é de uma conta da Microsoft para se conectar. A Camada gratuita inclui acesso gratuito a um espaço de trabalho do Azure Machine Learning Studio por [conta da Microsoft](https://www.microsoft.com/account/default.aspx). Nessa camada, você pode usar até 10 GB de armazenamento e colocar em operação modelos como APIs de preparo. Cargas de trabalho de camada gratuita não são cobertas por um SLA e destinam-se ao desenvolvimento e uso pessoal. 

Os espaços de trabalho da camada gratuita têm as limitações a seguir:

* As cargas de trabalho não podem acessar dados conectando-se a um servidor local que executa o SQL Server.
* Não é possível implantar os serviços Web base do Novo Gerenciador de Recursos.


**O que está incluso na camada e nos planos Standard do Azure Machine Learning?**

A camada Standard do Azure Machine Learning é uma versão paga do Azure Machine Learning Studio. A taxa mensal do Azure Machine Learning Studio é cobrada por estação, por mês e rateada em meses parciais. Horas de experimento do Azure Machine Learning Studio são cobradas por hora de computação por experimento ativo. A cobrança é rateada em horas parciais.  

O serviço de API do Azure Machine Learning é cobrado dependendo de ser um serviço Web clássico ou um novo serviço Web (com base no Gerenciador de Recursos).

As cobranças a seguir são agregadas por espaço de trabalho para sua assinatura.

* Assinatura de espaço de trabalho do Machine Learning: a assinatura de espaço de trabalho do Machine Learning é uma taxa mensal que fornece acesso a um espaço de trabalho do Machine Learning Studio. A assinatura é necessária para executar experimentos no Studio e utilizar as APIs de produção.
* Horas de Teste no Estúdio: esse medidor agrega todas as cobranças de computação vencidas ao executar testes no Machine Learning Studio e executar chamadas de API de produção no ambiente de preparo.
* Dados de acesso por conexão com um servidor local que executa o SQL Server em seus modelos para treinamento e pontuação.
* Para serviços Web clássicos:
  * Horas de Computação da API de Produção: esse medidor inclui cobranças de computação vencidas por serviços Web em execução na produção.
  * Transações de API de Produção (em milhares): esse medidor inclui cobranças vencidas por chamada ao serviço Web de produção.

Além dos encargos anteriores, no caso do serviço Web baseado no Gerenciador de Recursos, os encargos são agregados para o plano selecionado:

* Plano de API Standard S2/S1/S3 (unidades): esse medidor representa o tipo de instância selecionada para serviços Web baseados no Gerenciador de Recursos.
* Horas de computação de API excedente S2/S1/S3 Standard: esse medidor inclui encargos de computação que são acumulados pelos serviços Web baseados no Gerenciador de Recursos executados em produção depois que as quantidades incluídas em instâncias existentes são usadas. O uso adicional será cobrado com a sobretaxa associada à camada de plano S1/S2/S3.
* Transações de API excedentes Standard S1/S2/S3 (em milhares): esse medidor inclui encargos que são acumulados por chamada ao serviço Web baseado no Gerenciador de Recursos de produção depois que as quantidades incluídas em instâncias existentes são usadas. O uso adicional será cobrado com a sobretaxa associada à camada de plano S1/S2/S3.
* Quantidade Incluída de Horas de Computação de API: com os serviços Web baseados no Gerenciador de Recursos, esse medidor representa a quantidade de horas de computação de API incluídas.
* Quantidade Incluída de Transações de API (em milhares): com serviços Web baseados no Gerenciador de Recursos, esse medidor representa a quantidade de transações de API incluídas.

**Como assinar a camada gratuita do Azure Machine Learning?**

Tudo o que você precisa é de uma conta da Microsoft. Vá para o [início do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)e clique no botão **Iniciar agora**. Entre com sua conta da Microsoft e um espaço de trabalho na camada Gratuita será criado para você. É possível começar a explorar e criar testes do Machine Learning imediatamente.

**Como assinar a camada Standard do Azure Machine Learning?**

Primeiro, você deve ter acesso a uma assinatura do Azure para criar um espaço de trabalho Standard do Machine Learning. Você pode inscrever-se em uma assinatura do Azure com avaliação gratuita de 30 dias e atualizar posteriormente para uma assinatura paga ou comprar uma assinatura do Azure paga imediatamente. Em seguida, é possível criar um espaço de trabalho do Machine Learning no portal clássico do Microsoft Azure depois de ter acesso à assinatura. Veja as [instruções passo a passo](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

Se preferir, é possível ser convidado por um proprietário de espaço de trabalho Standard Machine Learning para acessar o espaço de trabalho do proprietário.

**Posso especificar minha própria conta de armazenamento de Blobs do Azure para usar com a camada Gratuita?**

Não. A camada Standard é equivalente à versão do serviço de Machine Learning que estava disponível antes de as camadas terem sido introduzidas.

**Posso implantar meus modelos de aprendizado de máquina como APIs na camada Gratuita?**

Sim. É possível operacionalizar os modelos de aprendizado de máquina para serviços de API de preparo como parte da camada Gratuita. Para colocar o serviço de API de preparo em produção e obter um ponto de extremidade de produção para o serviço operacionalizado, é necessário usar a camada Standard.

**Qual é a diferença entre a avaliação Gratuita do Azure e a camada Gratuita do Azure Machine Learning?**

A [avaliação gratuita do Microsoft Azure](https://azure.microsoft.com/free/) oferece créditos que podem ser aplicados a qualquer serviço do Azure em um mês. A Camada gratuita do Azure Machine Learning oferece acesso contínuo especificamente ao Azure Machine Learning para cargas de trabalho que não são de produção.

**Como devo mover um teste da camada Gratuita para a camada Standard?**

Para copiar seus testes da Camada gratuita para a camada Standard:

1. Entre no Azure Machine Learning Studio e verifique se está vendo o espaço de trabalho Gratuito e o Standard no seletor na barra de navegação superior.
2. Alterne para o espaço de trabalho Gratuito se estiver na Standard.
3. Na exibição de lista de testes, selecione um teste que você deseja copiar e clique no botão de comando **Copiar**.
4. Selecione o espaço de trabalho Standard na caixa de diálogo que é aberta e clique no botão **Copiar**.
   Todos os conjuntos de dados associados, modelos treinados e afins são copiados com o teste no espaço de trabalho Standard.
5. Você precisa executar novamente o teste e republicá-lo no serviço Web no espaço de trabalho Standard.

### <a name="studio-workspace"></a>Espaço de trabalho do Studio
**Existem faturas diferentes para espaços de trabalho diferentes?**

As cobranças referentes a espaços de trabalho são divididas em separado para cada medidor aplicável em uma única fatura.

**Em quais tipos de recursos de computação específicos meus testes serão executados?**

O Machine Learning é um serviço multilocatário. Os recursos de computação real que são usados no back-end variam e são otimizados para desempenho e previsibilidade.

### <a name="guest-access"></a>Acesso de Convidado
**O que é o acesso de convidado para o Machine Learning Studio Azure?**

O Acesso de Convidado é uma experiência de avaliação restrita. Você pode criar e executar experimentos no Azure Machine Learning Studio sem custo adicional e sem autenticação. As sessões de convidado são não persistentes (não podem ser salvas) e limitadas a oito horas. Outras limitações incluem falta de suporte para R e Python, falta de APIs de preparo e capacidade de armazenamento e tamanho restritos do conjunto de dados. Por comparação, os usuários que escolhem se conectar com uma conta da Microsoft têm acesso completo à camada gratuita do Machine Learning Studio descrito anteriormente, que inclui um espaço de trabalho persistente e recursos mais abrangentes. Para escolher sua experiência do Machine Learning gratuita, clique em **Introdução** em [https://studio.azureml.net](https://studio.azureml.net)e selecione **Acesso de convidado** ou entre com uma conta da Microsoft.

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx
