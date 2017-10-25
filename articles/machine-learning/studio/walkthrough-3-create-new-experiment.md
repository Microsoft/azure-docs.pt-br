---
title: 'Etapa 3: Criar um novo teste de Machine Learning | Microsoft Docs'
description: "Etapa 3 do desenvolvimento de um passo a passo de solução de previsão: criar um novo teste de treinamento no Azure Machine Learning Studio."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 660e3c27-55ef-4c33-a4e9-dff4d1224630
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: a8f1764204740a8f5ef757e5e2ad63cfd43af150
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Passo a passo Etapa 3: Criar um novo teste de Azure Machine Learning
Esta é a terceira etapa do passo a passo, [Desenvolver uma solução de análise preditiva com o Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Criar um espaço de trabalho do Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Carregar dados existentes](walkthrough-2-upload-data.md)
3. **Criar um novo teste**
4. [Treinar e avaliar os modelos](walkthrough-4-train-and-evaluate-models.md)
5. [Implantar o serviço Web](walkthrough-5-publish-web-service.md)
6. [Acessar o serviço Web](walkthrough-6-access-web-service.md)

- - -
A próxima etapa neste passo a passo é criar um teste no Machine Learning Studio que usa o conjunto de dados que carregamos.  

1. No Estúdio, clique em **+NOVO** na parte inferior da janela.
2. Selecione **TESTE**e, em seguida, selecione "Teste em branco". 

    ![Criar um novo experimento][0]

2. Selecione o nome do teste padrão na parte superior da tela e renomeie-o para algo significativo.

    ![Renomear o teste][5]
   
   > [!TIP]
   > É uma boa prática preencher**Resumo** e **Descrição** para o experimento no painel **Propriedades**. Essas propriedades lhe dão a chance de documentar o experimento para que qualquer pessoa que olhe para ele mais tarde compreenda as suas metas e a metodologia.
   > 
   > ![Propriedades de teste][6]
   > 
3. Na paleta do módulo à esquerda das telas de teste, expanda **Conjuntos de dados salvos**.
4. Localize o conjunto de dados que você criou em **Meus Conjuntos de Dados** e arraste-o para a tela. Você também pode localizar o conjunto de dados inserindo o nome na caixa **Pesquisar** acima da paleta.  

    ![Adicionar o conjunto de dados ao teste][7]

## <a name="prepare-the-data"></a>Preparar os dados
É possível exibir as 100 primeiras linhas dos dados e algumas informações estatísticas de todo o conjunto de dados clicando na porta de saída do conjunto de dados (o círculo pequeno na parte inferior) e selecionando **Visualizar**.  

Como o arquivo de dados não foi fornecido com títulos de coluna, o Estúdio forneceu títulos genéricos (Col1, Col2 *etc.*). Bons títulos de coluna não são essenciais para criar um modelo, mas facilitam o trabalho com os dados no teste. Além disso, quando eventualmente publicarmos esse modelo em um serviço Web, os títulos ajudarão a identificar as colunas para o usuário do serviço.  

É possível adicionar títulos de coluna usando o módulo [Editar Metadados][edit-metadata].
O módulo [Editar Metadados][edit-metadata] é usado para alterar os metadados associados a um conjunto de dados. Nesse caso, ele fornece nomes mais amigáveis para títulos de coluna. 

Para usar o módulo [Editar Metadados][edit-metadata], é necessário especificar quais colunas você deseja modificar (nesse caso, todas). Em seguida, especifique a ação a ser executada nessas colunas (nesse caso, alterar os cabeçalhos de coluna.)

1. Na paleta de módulo, digite "metadados" na caixa **Pesquisar** . [Editar Metadados][edit-metadata] aparecerá na lista de módulos.

2. Clique e arraste o módulo [Editar Metadados][edit-metadata] na tela e solte-o abaixo do conjunto de dados adicionado anteriormente.

3. Conecte o conjunto de dados ao módulo [Editar Metadados][edit-metadata]: clique na porta de saída do conjunto de dados (o círculo pequeno na parte inferior do conjunto de dados), arraste para a porta de entrada do [Editar Metadados][edit-metadata] (o círculo pequeno na parte superior do módulo) e, em seguida, solte o botão do mouse. O conjunto de dados e o módulo permanecem conectados mesmo se você mover um deles nas telas.
   
   O teste deve se parecer como o seguinte:  
   
   ![Adicionar Editar Metadados][1]
   
   O ponto de exclamação vermelho indica que não definimos as propriedades deste módulo ainda. Faremos isso em seguida.
   
   > [!TIP]
   > É possível adicionar um comentário em um módulo ao clicar duas vezes nele e inserir o texto. Isso pode ajudar a ver rapidamente o que o módulo está fazendo em seu experimento. Nesse caso, clique duas vezes no módulo [Editar Metadados][edit-metadata] e digite o comentário "Adicionar títulos de coluna". Clique em qualquer lugar na tela para fechar a caixa de texto. Para exibir o comentário, clique na seta para baixo no módulo.
   > 
   > ![Módulo Editar Metadados com comentário adicionado][8]
   > 
4. Selecione [Editar Metadados][edit-metadata] e, no painel **Propriedades**, à direita da tela, clique em **Iniciar seletor de colunas**.

5. Na caixa de diálogo **Selecionar colunas**, selecione todas as linhas de **Colunas disponíveis** e clique em > para movê-las para **Colunas selecionadas**.
   A caixa de diálogo deve ter esta aparência:

   ![Seletor de coluna com todas as colunas selecionadas][2]

6. Clique na marca de seleção **OK**.

7. Volte ao painel **Propriedades**, procure o parâmetro **Novos nomes de coluna**. Neste campo, insira uma lista de nomes para as 21 colunas no conjunto de dados, separadas por vírgulas e na ordem da coluna. Você pode obter os nomes de colunas na documentação do conjunto de dados no site UCI ou, por conveniência, você pode copiar e colar a seguinte lista:  
   
       Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   
   O painel de Propriedades tem esta aparência:
   
   ![Propriedades de Editar Metadados][3]

> [!TIP]
> Se quer verificar os títulos de coluna, execute o teste (clique em **EXECUTAR** abaixo da tela do teste). Quando ele terminar a execução (uma marca de seleção verde aparecerá em [Editar Metadados][edit-metadata]), clique na porta de saída do módulo [Editar Metadados][edit-metadata] e selecione **Visualizar**. Você pode exibir a saída de qualquer módulo da mesma maneira para exibir o progresso dos dados durante o teste.
> 
> 

## <a name="create-training-and-test-datasets"></a>Criar conjuntos de dados de treinamento e teste
Precisamos de alguns dados para treinar o modelo e alguns para testá-lo.
Portanto, na próxima etapa do teste, dividiremos o conjunto de dados em dois conjuntos de dados separados: um para treinar nosso modelo e outro para testá-lo.

Para isso, usamos o módulo [Dividir Dados][split].  

1. Localize o módulo [Dividir Dados][split], arraste-o para a tela e conecte-o ao módulo [Editar Metadados][edit-metadata].

2. Por padrão, a taxa de divisão é 0,5 e o parâmetro **Divisão aleatória** é definido. Isso significa que metade dos dados aleatórios sairá por uma porta do módulo [Dividir Dados][split] e a outra metade sairá por outra porta. É possível ajustar isso, bem como o parâmetro **Semente aleatória**, a fim de alterar a divisão entre dados de treinamento e teste. Neste exemplo, deixaremos como está.
   
   > [!TIP]
   > A propriedade **Fração de linhas no primeiro conjunto de dados de saída** determina a quantidade de dados que saem através da porta de saída à *esquerda*. Por exemplo, se você definir a taxa em 0,7, então, 70% dos dados sairão pela porta esquerda e 30% pela porta direita.  
   > 
   > 

3. Clique duas vezes no módulo [Dividir Dados][split] e insira o comentário, "Dividir dados de treinamento/teste em 50%". 

É possível usar as saídas do módulo [Dividir Dados][split] da forma que desejarmos, mas vamos escolher usar a saída à esquerda para dados de treinamento e a saída à direita para dados de teste.  

Como mencionado na [etapa anterior](walkthrough-2-upload-data.md), o custo de uma classificação incorreta de um risco de crédito alto como baixo é cinco vezes maior do que o custo da classificação incorreta de um risco baixo como alto. Para isso, geramos um novo conjunto de dados que reflita essa função de custo. No novo conjunto de dados, cada exemplo de alto risco é replicado cinco vezes, enquanto cada exemplo de baixo risco não será replicado.   

Podemos fazer essa replicação usando o código R:  

1. Localize e arraste o módulo [Executar Script R][execute-r-script] para a tela de teste. 

2. Conecte a porta de saída à esquerda do módulo [Dividir Dados][split] à primeira porta de entrada (“Dataset1”) do módulo [Executar Script R][execute-r-script].

3. Clique duas vezes no módulo [Executar Script R][execute-r-script] e insira o comentário "Definir ajuste de custo".

4. No painel **Propriedades**, exclua o texto padrão no parâmetro **Script R** e insira esse script:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![Script R no módulo Executar Script R][9]

É necessário fazer essa mesma operação de replicação para cada saída do módulo [Dividir Dados][split] de forma que os dados de treinamento e teste tenham os mesmos ajustes de custo. A maneira mais fácil de fazer isso é duplicando o módulo [Executar Script R][execute-r-script] que acabamos de criar e conectando-o a outra porta de saída do módulo [Dividir Dados][split].

1. Clique com o botão direito do mouse no módulo [Executar Script R][execute-r-script] e selecione **Copiar**.

2. Clique com o botão direito do mouse nas telas de teste e selecione **Colar**.

3. Arraste o novo módulo para a posição correta e, em seguida, conecte a porta de saída à direita do módulo [Dividir Dados][split] à primeira porta de entrada desse novo módulo [Executar Script R][execute-r-script]. 

4. Na parte inferior da tela, clique em **Executar**. 

> [!TIP]
> A cópia do módulo Executar script R contém o mesmo script que o módulo original. Quando você copia e cola um módulo nas telas, a cópia mantém todas as propriedades do original.  
> 
> 

Nosso teste agora se parece com esse:

![Adicionando módulo Divisão e Scripts R][4]

Para obter mais informações sobre como usar scripts R em seus testes, consulte [Estender seu teste com R](extend-your-experiment-with-r.md).

**Em seguida: [Treinar e avaliar os modelos](walkthrough-4-train-and-evaluate-models.md)**

[0]: ./media/walkthrough-3-create-new-experiment/create-new-experiment.png
[5]: ./media/walkthrough-3-create-new-experiment/rename-experiment.png
[6]: ./media/walkthrough-3-create-new-experiment/experiment-properties.png
[7]: ./media/walkthrough-3-create-new-experiment/add-dataset-to-experiment.png
[8]: ./media/walkthrough-3-create-new-experiment/edit-metadata-with-comment.png
[9]: ./media/walkthrough-3-create-new-experiment/execute-r-script.png
[1]: ./media/walkthrough-3-create-new-experiment/experiment-with-edit-metadata-module.png
[2]: ./media/walkthrough-3-create-new-experiment/select-columns.png
[3]: ./media/walkthrough-3-create-new-experiment/edit-metadata-properties.png
[4]: ./media/walkthrough-3-create-new-experiment/experiment.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
