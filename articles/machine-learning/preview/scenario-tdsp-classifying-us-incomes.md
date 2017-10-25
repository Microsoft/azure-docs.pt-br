---
title: "Classificação de renda – Processo de Ciência de Dados de Equipe – Azure Machine Learning | Microsoft Docs"
description: "Como usar o modelo do Processo de Ciência de Dados de Equipe para criar um projeto no Azure Machine Learning que classifica as rendas dos EUA."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: bradsev
ms.openlocfilehash: c0fbd9b3e6f9f9f4f7a5d3e6bda18ce1312650e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>Classificação de renda com Projeto TDSP (Processo de Ciência de Dados de Equipe)

## <a name="introduction"></a>Introdução

A padronização da estrutura e da documentação de projetos de ciência de dados, ancorados a um [ciclo de vida de ciência de dados](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), é fundamental para facilitar a colaboração eficaz em equipes de ciência de dados. A criação de projetos do Azure Machine Learning com o modelo [TDSP (Processo de Ciência de Dados de Equipe)](https://github.com/Azure/Microsoft-TDSP) oferece uma estrutura para essa padronização.

Tínhamos lançado anteriormente um [repositório do GitHub para os modelos e a estrutura do projeto TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Mas não foi possível, até agora, criar uma instância dos modelos e da estrutura TDSP em uma ferramenta de ciência de dados. Agora habilitamos a criação de projetos do Azure Machine Learning que têm uma instância criada com os [modelos de documentação e a estrutura TDSP para o Azure Machine Learning](https://github.com/amlsamples/tdsp). Instruções sobre como usar os modelos e a estrutura TDSP no Azure Machine Learning são fornecidas [aqui](https://aka.ms/how-to-use-tdsp-in-aml). Aqui fornecemos um exemplo de como um projeto de aprendizado de máquina real pode ser criado usando a estrutura TDSP, preenchido com o código específico do projeto, artefatos e documentos e executado dentro do Azure Machine Learning.

## <a name="link-to-github-repository"></a>Link para o repositório do GitHub
Fornecemos uma documentação resumida [aqui](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) sobre o exemplo. Mais documentação extensiva pode ser encontrada no site do GitHub.

### <a name="purpose"></a>Finalidade
A principal finalidade desse exemplo é mostrar como criar uma instância de um projeto de aprendizado de máquina e executá-lo usando os modelos e a estrutura [TDSP (Processo de Ciência de Dados de Equipe)](https://github.com/Azure/Microsoft-TDSP) no Azure Machine Learning. Para essa finalidade, usamos os [dados conhecidos do Censo de 1994 dos EUA do repositório de aprendizado de máquina UCI](https://archive.ics.uci.edu/ml/datasets/adult). A tarefa de modelagem é prever a classes de renda anuais dos EUA nas informações do Censo dos EUA (por exemplo, idade, etnia, nível de instrução, país de origem, etc.)

### <a name="scope"></a>Escopo
 * Exploração de dados, treinamento e implantação de um modelo de aprendizado de máquina que resolva o problema de previsão descrito na Visão geral de caso de uso. 
 * Execução do projeto no Azure Machine Learning usando o modelo TDSP (Processo de Ciência de Dados de Equipe) do Azure Machine Learning para este projeto. Para a execução e o relatório do projeto, usaremos o ciclo de vida TDSP.
 * Operacionalização da solução diretamente do Azure Machine Learning nos Serviços de Contêiner do Azure.

 O projeto destaca vários recursos do Azure Machine Learning, como criação de uma instância e uso da estrutura TDSP, execução do código em blocos de anotações do Jupyter, bem como arquivos Python, e fácil operacionalização nos Serviços de Contêiner do Azure usando o Docker e o Kubernetes.


## <a name="team-data-science-process-tdsp-lifecycle"></a>Ciclo de vida TDSP (Processo de Ciência de Dados de Equipe)
Consulte [Team Data Science Process (TDSP) Lifecycle](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md) (Ciclo de vida TDSP (Processo de Ciência de Dados de Equipe))

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>Pré-requisitos
### <a name="required-subscription-hardware-software"></a>Obrigatórios: assinatura, hardware, software
1. Uma [assinatura](https://azure.microsoft.com) do Azure. É possível obter uma [assinatura gratuita](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg) para executar este exemplo também.
2. Uma [DSVM (Máquina virtual de Ciência de Dados do Azure) Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm), (Tamanho da VM: [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) com 4 CPUs virtuais e 14 GB RAM). Embora testado em uma DSVM do Azure, é provável que funcione em qualquer computador Windows 10.
3. Examine a documentação no Azure Machine Learning e seus serviços relacionados (consulte abaixo para obter links).
4. Certifique-se de que você instalou o Azure Machine Learning devidamente por meio do [guia de instalação de início rápido](quickstart-installation.md).

O conjunto de dados para este exemplo é do repositório de ML do UCI [[link]](https://archive.ics.uci.edu/ml/datasets/adult). Ele é extraído do banco de dados do Censo de 1994 dos EUA e contém informações do censo e de renda de aproximadamente 50.000 indivíduos. Este é o conjunto de dados estruturado que tem recursos numéricos e categóricos e um destino categórico composto por duas categorias de renda ('> 50 mil' ou '<=50 mil'). 

### <a name="optional-version-control-repository"></a>Opcional: repositório de controle de versão
Se você desejar salvar e fazer a versão do seu projeto e do seu conteúdo, será necessário ter um repositório de controle de versão em que isso possa ser feito. É possível inserir o local do repositório do Git ao criar o novo projeto usando o modelo TDSP no Azure Machine Learning. Consulte [como usar o Git no Azure Machine Learning](using-git-ml-project.md) para obter mais detalhes.

### <a name="informational-about-azure-machine-learning"></a>Informativo: saiba mais sobre o Azure Machine Learning
* [Perguntas frequentes – Como começar](frequently-asked-questions.md)
* [Visão geral](overview-what-is-azure-ml.md)
* [Instalação](quickstart-installation.md)
* [Execução](experiment-execution-configuration.md)
* [Usando o TDSP](https://aka.ms/how-to-use-tdsp-in-aml)
* [Ler e gravar arquivos](how-to-read-write-files.md)
* [Usando o Git com o Azure Machine Learning](using-git-ml-project.md)
* [Implantando um modelo do ML como um serviço Web](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>Criar um novo projeto de workbench

Crie um novo projeto usando este exemplo como modelo:
1.  Abra o Azure Machine Learning Workbench
2.  Na página **Projetos**, clique no sinal **+** e selecione **Novo Projeto**
3.  No painel **Criar Novo Projeto**, preencha as informações do seu novo projeto
4.  Na caixa de pesquisa **Pesquisar modelos de projeto**, digite "Classificar rendas dos EUA – projeto TDSP" e selecione o modelo
5.  Clique em **Criar**

Se você fornecer um local de repositório do Git vazio durante a criação do projeto (na caixa adequada), então esse repositório será preenchido com a estrutura e o conteúdo do projeto após a criação deste.

## <a name="use-case-overview"></a>Visão geral de casos de uso
O problema é entender como dados socioeconômicos capturados no Censo dos EUA podem ajudar a prever a renda anual de indivíduos nos Estados Unidos. Com base nesses recursos do Censo, a tarefa de aprendizado de máquina é prever se a renda de um indivíduo é superior a US$ 50.000 ou não (tarefa de classificação binária).

## <a name="data-description"></a>Descrição dos dados
Para obter informações detalhadas sobre os dados, consulte a [descrição](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names) no repositório do UCI. 

Esses dados foram extraídos do banco de dados da Agência de Recenseamento dos EUA localizado em: https://www.census.gov/en.html. 


* Há um total de 48.842 instâncias (antes de qualquer filtragem), combinação de contínuas e discretas (treino = 32.561, teste = 16.281)
* Probabilidade para o rótulo '>50 mil': 23,93%/24,78% (sem desconhecidos)
* Probabilidade para o rótulo '<=50 mil': 76,07%/75,22% (sem desconhecidos)  

* **DESTINO**: classe de renda '>50 mil', '<=50 mil'. Elas são substituídas por 1 e 0 respectivamente na fase de preparação dos dados.
* **RECURSOS**: idade, classe de trabalho, nível de instrução, nível de instrução, etnia, sexo, horas de trabalho por semana, etc.


## <a name="project-structure-execution-and-reporting"></a>Estrutura, execução e relatórios do projeto

### <a name="structure"></a>Estrutura
Para este projeto, usamos a estrutura de pastas TDSP e os modelos de documentação (abaixo), que seguem o [ciclo de vida TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). 

O projeto é criado com base nas instruções fornecidas [aqui](https://aka.ms/how-to-use-tdsp-in-aml). Depois que ele é preenchido com o código e os artefatos do projeto, a estrutura tem a seguinte aparência (consulte a estrutura do projeto demarcada em vermelho na figura abaixo).


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>Execução
Neste exemplo, executamos o código no **ambiente de computação local**. Consulte os documentos do Azure Machine Learning para obter mais detalhes sobre [opções de execução](experiment-execution-configuration.md).

Executar um script Python em um tempo de execução local do Python é fácil:

    az ml experiment submit -c local my_script.py

Os arquivos do bloco de anotações IPython podem ser clicados duas vezes na estrutura do projeto à esquerda da interface do usuário do Azure Machine Learning e executados no Servidor de bloco de anotações do Jupyter.


O fluxo de trabalho de ciência de dados passo a passo era o seguinte:

* [**Aquisição de dados e entendimento**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

Os dados foram baixados no formato .csv das URLs no Repositório de ML do UCI [[link]](https://archive.ics.uci.edu/ml/datasets/adult). Os recursos, o destino e suas transformações são descritos com detalhes no arquivo ProjectReport.md.

O código para a aquisição de dados e reconhecimento está localizado em: /code/01_data_acquisition_and_understanding.

A exploração de dados é realizada usando o utilitário [IDEAR (Exploração e Relatório de dados interativos)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python) do Python 3 publicado como parte do [pacote TDSP de ferramentas de ciência de dados](https://github.com/Azure/Azure-TDSP-Utilities). Este utilitário ajuda a gerar relatórios de exploração de dados padronizados para dados que contêm destino e recursos numéricos e categóricos. Detalhes de como o utilitário IDEAR do Python 3 foi usado são fornecidos abaixo. 

O local do relatório de exploração de dados final é [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs). É mostrado abaixo um modo de exibição do relatório IDEAR:

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**Modelagem**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

Criamos dois modelos com uma validação cruzada de 3 partições: Elastic Net e Random Forest. Usamos uma [amostragem de 59 pontos](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf) para pesquisa de grade aleatória como uma estratégia para validação cruzada e otimização do parâmetro do modelo. A precisão dos modelos foi medida usando AUC (Área sob a curva) no conjunto de dados de teste. 

O código para modelagem está localizado em: /code/02_modeling.

A AUC dos modelos Elastic Net e Random Forest foi > 0,85. Salvamos os modelos em arquivos pickled.pkl e transmitimos os gráficos ROC para ambos os modelos. A AUC do modelo Random Forest era 0,92 e a do modelo Elastic Net era 0,90. Além disso, para a interpretação de modelo, a importância de recursos para o modelo Random Forest é transmitida em um arquivo .csv e plotado em um pdf (20 principais recursos preditivos apenas).

A curva ROC do **modelo Random Forest** nos dados de teste é mostrada abaixo. Esse era o modelo que foi implantado:

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

A importância do recurso (os 20 principais) do modelo Random Forest é mostrada abaixo. Ele mostra a quantidade de ganho de capital, instrução, estado civil dos recursos que têm a mais alta importância do recurso.

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**Implantação**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

Implantamos o modelo Random Forest como um serviço Web em um cluster no [ACS (Serviço de Contêiner do Azure)](https://azure.microsoft.com/services/container-service/). O ambiente de operacionalização provisiona o Docker e o Kubernetes no cluster para gerenciar a implantação do serviço Web. É possível encontrar mais informações sobre o processo de operacionalização [aqui](model-management-service-deploy.md). 

O código para implantação está localizado em: /code/03_deployment.


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[Relatório de projeto final](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
São fornecidos detalhes sobre as seções acima no relatório de projeto final compilado [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). O relatório de projeto também contém mais detalhes sobre o caso de uso, métricas de desempenho do modelo, implantação e infraestrutura na qual foi o projeto foi desenvolvido e implantado.

O relatório de projeto, juntamente com o conteúdo de toda a pasta do projeto e o repositório do controle de versão podem ser entregues ao cliente.


## <a name="conclusion"></a>Conclusão

Neste exemplo, agora mostramos como usar a estrutura e os modelos TDSP no Azure Machine Learning. Por meio do documento e dos modelos de artefato, é possível:
1. Definir devidamente a finalidade e o escopo de um projeto
2. Criar uma equipe de projeto com funções e responsabilidades distribuídas
3. Estruturar e executar projetos de acordo com os estágios de ciclo de vida TDSP
4. Desenvolver relatórios padronizados usando os utilitários de ciência de dados TDSP (como a exploração de dados e o relatório de visualização IDEAR).
5. Preparar um relatório de projeto de ciência de dados definitivo que pode ser entregue a um cliente

Esperamos que você use esse recurso do Azure Machine Learning para facilitar a padronização e a colaboração em suas equipes de ciência de dados.

## <a name="next-steps"></a>Próximas etapas

Consulte as referências abaixo para começar:

[Como usar o TDSP (Processo de Ciência de Dados de Equipe) no Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)

[TDSP (Processo de Ciência de dados de Equipe)](https://github.com/Azure/Microsoft-TDSP)

[Modelo do projeto TDSP para o Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)

[Conjunto de dados de Renda dos EUA do repositório de ML do UCI](https://archive.ics.uci.edu/ml/datasets/adult)