---
title: Simulação de R paralelo com o Lote do Azure
description: 'Tutorial: instruções passo a passo para executar uma simulação financeira Monte Carlo no Lote do Azure usando o pacote doAzureParallel em R'
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: r
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 7fad37af268d3dcd3d4d974d8e839ac47f171b50
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321899"
---
# <a name="tutorial-run-a-parallel-r-simulation-with-azure-batch"></a>Tutorial: Executar uma simulação de R paralelo com o Lote do Azure 

Execute suas cargas de trabalho R paralelas em larga escala usando [doAzureParallel](https://www.github.com/Azure/doAzureParallel), um pacote em R leve que permite que você use o Lote do Azure diretamente na sessão de R. O pacote doAzureParallel se baseia no pacote popular em R [foreach](https://cran.r-project.org/web/packages/foreach/index.html). doAzureParallel usa cada iteração do loop foreach e o envia como uma tarefa do Lote do Azure.

Este tutorial mostra como implantar um pool do Lote e executar um trabalho de R paralelo no Lote do Azure diretamente no RStudio. Você aprenderá como:
 

> [!div class="checklist"]
> * Instalar doAzureParallel e configurá-lo para acessar suas contas do Lote e de armazenamento
> * Criar um pool do Lote como um back-end paralelo para a sessão de R
> * Executar uma simulação paralela do exemplo no pool

## <a name="prerequisites"></a>Pré-requisitos

* Uma distribuição [R](https://www.r-project.org/) instalada, como o [Microsoft R Open](https://mran.microsoft.com/open). Use a versão do R 3.3.1 ou posterior.

* [RStudio](https://www.rstudio.com/), a edição comercial ou o [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) de software livre. 

* Uma conta do Lote do Azure e uma conta do Armazenamento do Azure. Para criar essas contas, consulte os guias de início rápido do Lote usando o [portal do Azure](quick-create-portal.md) ou a [CLI do Azure](quick-create-cli.md). 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 
## <a name="install-doazureparallel"></a>Instalar doAzureParallel

No console do RStudio, instale o [Pacote doAzureParallel do GitHub](https://www.github.com/Azure/doAzureParallel). Os comandos abaixo baixam e instalam o pacote e suas dependências na sessão atual do R: 

```R
# Install the devtools package  
install.packages("devtools") 

# Install rAzureBatch package
devtools::install_github("Azure/rAzureBatch") 

# Install the doAzureParallel package 
devtools::install_github("Azure/doAzureParallel") 
 
# Load the doAzureParallel library 
library(doAzureParallel) 
```
A instalação poderá levar vários minutos.

Para configurar doAzureParallel com as credenciais de conta que você obteve anteriormente, gere um arquivo de configuração chamado *credentials.json* no diretório de trabalho: 

```R
generateCredentialsConfig("credentials.json") 
``` 

Preencha o arquivo com os nomes de conta e chaves do armazenamento e do Lote. Deixe a configuração `githubAuthenticationToken` inalterada.

Ao concluir, o arquivo de credenciais será semelhante ao seguinte: 

```json
{
  "batchAccount": {
    "name": "mybatchaccount",
    "key": "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==",
    "url": "https://mybatchaccount.mybatchregion.batch.azure.com"
  },
  "storageAccount": {
    "name": "mystorageaccount",
    "key": "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=="
  },
  "githubAuthenticationToken": ""
}
```

Salve o arquivo. Em seguida, execute o comando abaixo para definir as credenciais para a sessão atual do R: 

```R
setCredentials("credentials.json") 
```

## <a name="create-a-batch-pool"></a>Criar um pool do Lote 

doAzureParallel inclui uma função para gerar um pool do Lote do Azure (cluster) a fim de executar trabalhos em R paralelos. Os nós executam uma [máquina virtual de Ciência de Dados do Azure](../machine-learning/data-science-virtual-machine/overview.md) baseada em Ubuntu. O Microsoft R Open e pacotes de R populares vêm pré-instalados na imagem. Você pode exibir ou personalizar determinadas configurações de cluster, como o número e o tamanho dos nós. 

Para gerar um arquivo JSON de configuração de cluster no diretório de trabalho: 
 
```R
generateClusterConfig("cluster.json")
``` 
 
Abra o arquivo para exibir a configuração padrão, que inclui três nós dedicado e três nós de [baixa prioridade](batch-low-pri-vms.md). Essas configurações são apenas exemplos que você pode experimentar ou modificar. Nós dedicados são reservados para o pool. Nós de baixa prioridade são oferecidos a um preço menor do excedente de capacidade da VM no Azure. Nós de baixa prioridade ficam indisponíveis quando o Azure não tem capacidade suficiente. 

Para este tutorial, altere a configuração da seguinte maneira:

* Aumente o `maxTasksPerNode` para *2*, para aproveitar os dois núcleos em cada nó
* Defina `dedicatedNodes` como *0* para poder experimentar as VMs de baixa prioridade disponíveis para o Lote. Defina o `min` de `lowPriorityNodes` como *5*. e o `max` como *10*, ou escolha números menores se desejar. 

Mantenha os padrões para as configurações restantes e salve o arquivo. O arquivo deve ser semelhante ao seguinte:

```json
{
  "name": "myPoolName",
  "vmSize": "Standard_D2_v2",
  "maxTasksPerNode": 2,
  "poolSize": {
    "dedicatedNodes": {
      "min": 0,
      "max": 0
    },
    "lowPriorityNodes": {
      "min": 5,
      "max": 10
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": []
}
```

Agora crie o cluster. O Lote cria o pool imediatamente, mas leva alguns minutos para alocar e iniciar os nós de computação. Depois que o cluster estiver disponível, registre-o como o back-end paralelo para sua sessão de R. 

```R
# Create your cluster if it does not exist; this takes a few minutes
cluster <- makeCluster("cluster.json") 
  
# Register your parallel backend 
registerDoAzureParallel(cluster) 
  
# Check that the nodes are running 
getDoParWorkers() 
```

A saída mostra o número de "trabalhos de execução" para doAzureParallel. Esse número é o número de nós multiplicado pelo valor de `maxTasksPerNode`. Se você modificou a configuração do cluster conforme descrito anteriormente, o número é *10*. 
 
## <a name="run-a-parallel-simulation"></a>Executar uma simulação paralela

Agora que o cluster foi criado, você está pronto para executar o loop foreach com seu back-end paralelo registrado (pool do Lote do Azure). Por exemplo, execute uma simulação financeira Monte Carlo, primeiro localmente, usando um loop foreach padrão e, em seguida, executando o foreach com o Lote. Este exemplo é uma versão simplificada de prever o preço de um estoque simulando um grande número de resultados diferentes depois de cinco anos.

Suponha que as ações da Contoso Corporation em média ganha, em média, 1.001 vezes seu preço de abertura por dia, mas tem volatilidade (desvio padrão) de 0,01. Dado um preço inicial de US$100, use um preço da simulação Monte Carlo para calcular o preço de estoque da Contoso depois de cinco anos.

Parâmetros para a simulação Monte Carlo:

```R
mean_change = 1.001 
volatility = 0.01 
opening_price = 100 
```

Para simular preços de fechamento, defina a função abaixo:

```R
getClosingPrice <- function() { 
  days <- 1825 # ~ 5 years 
  movement <- rnorm(days, mean=mean_change, sd=volatility) 
  path <- cumprod(c(opening_price, movement)) 
  closingPrice <- path[days] 
  return(closingPrice) 
} 
```

Primeiro, execute 10 mil simulações localmente usando um loop foreach padrão com a palavra-chave `%do%`:

```R
start_s <- Sys.time() 
# Run 10,000 simulations in series 
closingPrices_s <- foreach(i = 1:10, .combine='c') %do% { 
  replicate(1000, getClosingPrice()) 
} 
end_s <- Sys.time() 
```


Plote os preços de fechamento em um histograma para mostrar a distribuição de resultados:

```R
hist(closingPrices_s)
``` 

A saída deverá ser semelhante a esta:

![Distribuição de preços de fechamento](media/tutorial-r-doazureparallel/closing-prices-local.png)
  
Uma simulação local é concluída em alguns segundos ou menos:

```R
difftime(end_s, start_s) 
```

O tempo de execução estimado para 10 milhões de resultados localmente, usando uma aproximação linear, é em torno de 30 minutos:

```R 
1000 * difftime(end_s, start_s, unit = "min") 
```


Agora execute o código usando `foreach` com a palavra-chave `%dopar%` para comparar quanto tempo leva para executar 10 milhões de simulações no Azure. Para paralelizar a simulação com o Lote, execute 100 iterações de 100 mil simulações:

```R
# Optimize runtime. Chunking allows running multiple iterations on a single R instance.
opt <- list(chunkSize = 10) 
start_p <- Sys.time()  
closingPrices_p <- foreach(i = 1:100, .combine='c', .options.azure = opt) %dopar% { 
  replicate(100000, getClosingPrice()) 
} 
end_p <- Sys.time() 
```

A simulação distribui as tarefas aos nós no pool do Lote. Você pode ver a atividade no mapa de calor do pool no portal do Azure. Vá para **Contas do Lote** > *myBatchAccount*. Clique em **Pools** > *myPoolName*. 

![Mapa de calor do pool executando tarefas de R paralelas](media/tutorial-r-doazureparallel/pool.png)

Depois de alguns minutos, a simulação é concluída. O pacote mescla os resultados automaticamente e os recebe dos nós. Em seguida, você está pronto para usar os resultados em sua sessão de R. 

```R
hist(closingPrices_p) 
```

A saída deverá ser semelhante a esta:

![Distribuição de preços de fechamento](media/tutorial-r-doazureparallel/closing-prices.png)

Quanto tempo levou a simulação paralela? 

```R
difftime(end_p, start_p, unit = "min")  
```

Você deve ver que a execução da simulação no pool do Lote fornece um aumento significativo no desempenho ao longo do tempo esperado para executar a simulação localmente. 

## <a name="clean-up-resources"></a>Limpar recursos

O trabalho é excluído automaticamente após a sua conclusão. Quando o cluster não for mais necessário, chame a função `stopCluster` no pacote doAzureParallel para excluí-lo:

```R
stopCluster(cluster)
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> Instalar doAzureParallel e configurá-lo para acessar suas contas do Lote e de armazenamento
> * Criar um pool do Lote como um back-end paralelo para a sessão de R
> * Executar uma simulação paralela do exemplo no pool


Para saber mais sobre doAzureParallel, consulte a documentação e os exemplos no GitHub.

> [!div class="nextstepaction"]
> [doAzureParallel package](https://github.com/Azure/doAzureParallel/)




