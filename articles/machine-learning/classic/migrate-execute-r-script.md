---
title: 'ML Studio (clássico): migrar para Azure Machine Learning-executar script R'
description: Rebuild Studio (clássico) executar módulos de script R para executar em Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: ac9ad296029451d624345d8b3bb365d881ba9a84
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564768"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>Migrar executar módulos de script R no estúdio (clássico)

Neste artigo, você aprenderá a recriar um módulo de execução de **script R** (clássico) do estúdio no Azure Machine Learning.

Para obter mais informações sobre como migrar do Studio (clássico), consulte o [artigo Visão geral da migração](migrate-overview.md).

## <a name="execute-r-script"></a>Executar script R

O designer de Azure Machine Learning agora é executado no Linux. O Studio (clássico) é executado no Windows. Devido à alteração da plataforma, você deve ajustar a **execução do script R** durante a migração, caso contrário, o pipeline falhará.

Para migrar um módulo **Executar script R** do Studio (clássico), você deve substituir `maml.mapInputPort` as `maml.mapOutputPort` interfaces e por funções padrão.

A tabela a seguir resume as alterações no módulo de script R:

|Recurso|Studio (clássico)|Designer do Azure Machine Learning|
|---|---|---|
|Interface de script|`maml.mapInputPort` e `maml.mapOutputPort`|Interface de função|
|Plataforma|Windows|Linux|
|Acessível pela Internet |Não|Sim|
|Memória|14 GB|Dependente do SKU de computação|

### <a name="how-to-update-the-r-script-interface"></a>Como atualizar a interface de script R

Aqui estão os conteúdos de um módulo **Executar script R** de exemplo no estúdio (clássico):
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

Aqui estão os conteúdos atualizados no designer. Observe que o `maml.mapInputPort` e `maml.mapOutputPort` foram substituídos pela interface de função padrão `azureml_main` . 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
Para obter mais informações, consulte a [referência do módulo executar script R](../algorithm-module-reference/execute-r-script.md)do designer.

### <a name="install-r-packages-from-the-internet"></a>Instalar pacotes do R da Internet

Azure Machine Learning designer permite que você instale pacotes diretamente do CRAN.

Essa é uma melhoria em relação ao estúdio (clássico). Como o Studio (clássico) é executado em um ambiente de área restrita sem acesso à Internet, você precisava carregar scripts em um pacote zip para instalar mais pacotes. 

Use o código a seguir para instalar pacotes do CRAN no módulo **Executar script R** do designer:
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a migrar os módulos executar script R para Azure Machine Learning.

Consulte os outros artigos da série de migração do Studio (clássico):

1. [Visão geral da migração](migrate-overview.md).
1. [Migrar conjunto](migrate-register-dataset.md)de um.
1. [Recompile um pipeline de treinamento do Studio (clássico)](migrate-rebuild-experiment.md).
1. [Recompile um serviço Web Studio (clássico)](migrate-rebuild-web-service.md).
1. [Integre um serviço web Azure Machine Learning com aplicativos cliente](migrate-rebuild-integrate-with-client-app.md).
1. **Migre os módulos executar script R**.