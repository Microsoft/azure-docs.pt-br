---
title: "Preparar dados para o tutorial Classificando a Íris nos serviços de Machine Learning (versão prévia) | Microsoft Docs"
description: "Este tutorial completo mostra como usar os serviços do Microsoft Azure Machine Learning (versão prévia) de ponta a ponta. Esta é a parte 1 na preparação de dados."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/28/2017
ms.openlocfilehash: 975a86c1f9d9692f6eadd232177f33cbbbeeff2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="classifying-iris-part-1-prepare-data"></a>Parte 1 de Classificando a Íris: preparar dados
Os serviços do Azure Machine Learning (versão prévia) são uma solução integrada de análise avançada e de ciência de dados de ponta a ponta para cientistas de dados profissionais prepararem dados, desenvolverem testes e implantarem modelos em escala de nuvem.

Este tutorial é a parte um de uma série de três partes. Neste tutorial, percorreremos as noções básicas dos serviços do Azure Machine Learning (versão prévia). Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um projeto no Azure Machine Learning Workbench
> * Criar um pacote de preparação de dados
> * Gerar código Python/PySpark para invocar o pacote de preparação de dados

Este tutorial usa o [Conjunto de dados de flor de íris](https://en.wikipedia.org/wiki/Iris_flower_data_set) atemporal para manter as coisas simples. As capturas de tela são específicos do Windows, mas a experiência no macOS é quase idêntica.

## <a name="prerequisites"></a>Pré-requisitos
- Criar uma Conta de Experimentação do Azure Machine Learning
- Instalar o Azure Machine Learning Workbench

Siga o [Guia de início rápido para instalação e criação](quickstart-installation.md) para instalar o aplicativo Azure Machine Learning Workbench. Essa instalação também inclui a interface de linha de comando (CLI).

## <a name="create-a-new-project-in-azure-ml-workbench"></a>Criar um novo projeto no Azure ML Workbench
1. Inicie o aplicativo Azure Machine Learning Workbench e faça logon se necessário. No painel **PROJETOS**, clique no ícone **+** para criar um **Novo Projeto**.

   ![novo espaço de trabalho](media/tutorial-classifying-iris/new_ws.png)

2. Preencha os detalhes de **Criar Novo Projeto**. 

   ![Novo Projeto](media/tutorial-classifying-iris/new_project.png)

   - Preencha o campo **Nome do projeto** com um nome para o projeto. Por exemplo, use o valor **myIris**.
   - Escolha o **Diretório do projeto** no qual o projeto é criado. Por exemplo, use o valor **C:\Temp**. 
   - Insira a **Descrição do projeto**. 
   - O campo **Repositório Git** é opcional e pode ser deixado em branco. Você pode fornecer um repositório Git vazio (um repositório sem ramificação mestre) existente no VSTS. Fazer isso permite a você habilitar os cenários de roaming e compartilhamento mais tarde. Para obter mais informações, consulte o artigo [Usando o repositório Git](using-git-ml-project.md). 
   - Escolha um **Espaço de trabalho**, por exemplo, este tutorial usa **IrisGarden**. 
   - Selecione o modelo **Classificando a Íris** da lista de modelos de projeto. 

3. Clique no botão **Criar** para criar o projeto. O projeto está agora criado e aberto para você.

## <a name="create-a-data-preparation-package"></a>Criar um pacote de preparação de dados
1. Abra o arquivo **iris.csv** na **Exibição do Arquivo**. O arquivo é uma tabela simples com 5 colunas e 150 linhas. Ele tem quatro colunas de recurso numéricas e uma coluna de destino da cadeia de caracteres. Ele não tem cabeçalhos de coluna.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Não é recomendável incluir arquivos de dados na pasta do projeto, especialmente quando o tamanho do arquivo é grande. Incluímos **iris.csv** nesse modelo para fins de demonstração porque ele é pequeno. Para obter mais informações, consulte o artigo [Como ler e gravar arquivos de dados grandes](how-to-read-write-files.md).

2. Na **Exibição de Dados**, clique no ícone **+** para adicionar uma nova fonte de dados. O assistente **Adicionar Fonte de Dados** é iniciado. 

   ![exibição de dados](media/tutorial-classifying-iris/data_view.png)

3. Conclua o Assistente de preparação dos dados. 
   - Na primeira tela, selecione a opção **Arquivo(s)/Diretório**, em seguida, clique em **Próximo**.
   - Na segunda tela, escolha o arquivo local **iris.csv**, por exemplo, "C:\Temp\myIris\iris.csv".
   - Na terceira tela **Detalhes do Arquivo**, aceite os valores padrão.
   - Na quarta tela **Tipos de Dados**, altere o **TIPO DE DADOS** da _Cadeia de caracteres_ para _Numérico_ para a _Coluna1_por meio da _Coluna4_ dado que essas colunas são valores numéricos. 
   - Na quinta e sexta tela, aceite as configurações padrão.
   - Clique no botão **Concluir**.

   ![selecionar iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Verifique se você selecionou o arquivo **iris.csv** de dentro do diretório do projeto atual para este exercício, caso contrário as últimas etapas podem falhar. 

4. Um novo arquivo **iris-1.dsource** é criado. O arquivo é nomeado exclusivamente com "-1", pois o projeto de exemplo já é fornecido com um arquivo **iris.dsource** não numerado.  

   O arquivo é aberto e os dados são mostrados. Uma série de cabeçalhos de coluna, de **Coluna1** a **Coluna5**, são automaticamente adicionados a esse conjunto de dados. Role para baixo e observe que a última linha do conjunto de dados está vazia. Isso ocorre devido a uma quebra de linha extras no arquivo csv.

   ![exibição de dados de íris](media/tutorial-classifying-iris/iris_data_view.png)

5. Agora, clique no botão **Métricas**. Observe os histogramas e um conjunto completo de estatísticas que são calculados para você para cada coluna. Você também pode clicar no botão **Dados** para ver os dados novamente. 

   ![exibição de dados de íris](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Clique no botão **Preparar**. A caixa de diálogo **Preparar** aparece. 

   O projeto de exemplo é fornecido com um arquivo **iris.dprep**, então ele solicita por padrão que você crie um novo fluxo de dados nesse pacote de preparação de dados **iris.dprep** existente. 

   Altere o valor da lista suspensa para **+Novo Pacote de Preparação de Dados**, insira um novo valor "iris-1" e clique em **OK**.

   ![Exibição de dados de íris](media/tutorial-classifying-iris/new_dprep.png)

   Um novo pacote de preparação de dados chamado **iris-1.dprep** é criado e aberto no editor de preparação de dados.

7. Agora, faremos um pouco de preparação de dados básicos. Renomeie as colunas clicando em cada cabeçalho de coluna e tornando o texto do cabeçalho editável. 

   Digite **comprimento da sépala**, **largura da sépala**, **comprimento da pétala**, **largura da pétala**, e **espécies** para o cinco colunas, respectivamente.

   ![Renomear colunas](media/tutorial-classifying-iris/rename_column.png)

8. Para contar valores distintos, selecione a coluna **Espécies** e clique com o botão direito do mouse nela. Escolha **Contagens de Valor**. 

   ![Clicar em Contagens de Valor](media/tutorial-classifying-iris/value_count.png)

   Essa ação abre o painel **Inspetores** e exibe um histograma com quatro barras. Observe que a coluna de destino tem três valores distintos: **Iris_virginica**, **Iris_versicolor**, **Iris-setosa**e um valor **(nulo)**.

9. Filtre os valores nulos selecionando a barra do gráfico que representa o valor nulo. Há uma linha com um valor **(nulo)**. Para eliminar essa linha, clique no botão de filtro  **-** .

   ![Histograma de contagem de valor](media/tutorial-classifying-iris/filter_out.png)

10. Observe as etapas individuais detalhadas no painel **ETAPAS**. Como você trabalhou na coluna renomear e filtragem de linhas de valor nulo, cada ação foi gravada como uma etapa de preparação de dados. Você pode editar as etapas individuais para ajustar as configurações, reorganizar as etapas e até mesmo remover etapas.

   ![Etapas](media/tutorial-classifying-iris/steps.png)

11. Agora, feche o editor de preparação de dados clicando em **X** na guia denominada **iris-1** com o ícone de gráfico. O trabalho é salvo automaticamente no arquivo **iris-1.dprep** mostrado no cabeçalho **Preparações de Dados**.

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>Gerar código Python/PySpark para invocar o pacote de preparação de dados

1. Clique com o botão direito do mouse no arquivo **iris-1.dprep** para acessar o menu de contexto e escolha **Gerar Arquivo de Código de Acesso a Dados**. 

   ![gerar código](media/tutorial-classifying-iris/generate_code.png)

2. Um novo arquivo denominado **iris-1.py** abre com as linhas de código a seguir:

   ```python
   # This code snippet will load the referenced package and return a DataFrame.
   # If the code is run in a PySpark environment, the code will return a
   # Spark DataFrame. If not, the code will return a Pandas DataFrame.

   from azureml.dataprep.package import run
   df = run('iris.dprep', dataflow_idx=0)
   ```

   Esse trecho de código invoca a lógica que você criou como um pacote de preparação de dados. Dependendo do contexto no qual esse código é executado, `df` representa os tipos diferentes de quadros de dados. Um [DataFrame Pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) é usado durante o tempo de execução do Python, ou um [DataFrame Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html) é usado quando executado em um contexto Spark. 

   Para obter mais informações sobre como preparar dados no Azure Machine Learning Workbench, consulte o guia [Introdução à preparação de dados](data-prep-getting-started.md).

## <a name="next-steps"></a>Próximas etapas
Nessa primeira parte da série de tutoriais de três partes, você aprendeu a usar o Azure Machine Learning Workbench para:
> [!div class="checklist"]
> * Criar um novo projeto 
> * Criar um pacote de preparação de dados
> * Gerar código Python/PySpark para invocar o pacote de preparação de dados

Você está pronto para passar para a próxima parte da série, para criar uma modelo de aprendizado de máquina.
> [!div class="nextstepaction"]
> [Criar um modelo](tutorial-classifying-iris-part-2.md)
