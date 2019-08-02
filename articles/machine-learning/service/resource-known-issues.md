---
title: Problemas conhecidos e soluções
titleSuffix: Azure Machine Learning service
description: Obter uma lista dos problemas conhecidos, soluções alternativas e solução de problemas do Serviço do Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 206a8d9ba45dcb948dfffff86bab17b58a33e464
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358622"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problemas conhecidos e solução de problemas do serviço Azure Machine Learning

Este artigo ajuda a localizar e corrigir os erros ou as falhas encontrados ao usar o serviço Azure Machine Learning.

## <a name="visual-interface-issues"></a>Problemas de interface visual

Interface visual para problemas do serviço de Machine Learning.

### <a name="long-compute-preparation-time"></a>Tempo de preparação de computação longa

Criar nova computação ou evocar deixar computação leva tempo, pode ser de alguns minutos ou ainda mais. A equipe está trabalhando para otimização.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Não é possível executar um experimento somente contém DataSet 

Talvez você queira executar um experimento que contenha apenas o conjunto de um para visualizar o conjunto de um. No entanto, não é permitido executar um experimento apenas no momento. Estamos corrigindo ativamente esse problema.
 
Antes da correção, você pode conectar o conjunto de dados a qualquer módulo de Data Transformation (selecione as colunas no DataSet, editar metadados, dividir dados etc.) e executar o experimento. Em seguida, você pode visualizar o conjunto de os. 

A imagem abaixo mostra como ![: visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Problemas de instalação do SDK

**Mensagem de erro: não é possível desinstalar 'PyYAML'**

SDK do Azure Machine Learning para Python: O PyYAML é um projeto de distutils instalado. Portanto, não é possível determinar com precisão quais arquivos pertencem a ele no caso de uma desinstalação parcial. Para continuar a instalação do SDK ignorando esse erro, use:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemas ao criar a computação do Azure Machine Learning

Há uma chance rara que alguns usuários que criaram seu espaço de trabalho do Machine Learning do Azure no portal do Azure antes da versão GA não consigam criar a computação do Azure Machine Learning nesse espaço de trabalho. Você pode gerar uma solicitação de suporte no serviço ou criar um novo espaço de trabalho por meio do Portal ou do SDK para desbloqueio imediato.

## <a name="image-building-failure"></a>Falha na criação da imagem

Falha na criação da imagem ao implantar o serviço Web. Uma solução alternativa é adicionar “pynacl==1.2.1” como uma dependência de pip para o arquivo Conda da imagem de configuração.

## <a name="deployment-failure"></a>Falha na implantação

Caso veja `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, altere o SKU das VMs usadas na implantação para uma que tenha mais memória.

## <a name="fpgas"></a>FPGAs

Não será possível implantar modelos em FPGAs até que você tenha solicitado e recebido aprovação para cota de FPGA. Para solicitar acesso, preencha o formulário de solicitação de cota: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Aprendizado de máquina automatizado

O aprendizado de máquina automatizado do tensor Flow atualmente não dá suporte à versão 1,13 do fluxo do tensor. A instalação desta versão fará com que as dependências do pacote parem de funcionar. Estamos trabalhando para corrigir esse problema em uma versão futura. 

### <a name="experiment-charts"></a>Gráficos de experimento

Os gráficos de classificação binária (recall de precisão, ROC, curva de lucro etc.) mostrados em iterações de experimento de ML automatizadas não são renderizados de uma interface de usuário, desde 4/12. Atualmente, as plotagens de gráfico estão mostrando resultados inversos, onde os modelos de melhor desempenho são mostrados com resultados mais baixos. Uma resolução está sob investigação.

## <a name="databricks"></a>Databricks

Problemas do Databricks e do Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Falha ao instalar pacotes

A instalação do SDK do Azure Machine Learning falha em Azure Databricks quando mais pacotes são instalados. Alguns pacotes, como `psutil`, podem causar conflitos. Para evitar erros de instalação, instale pacotes congelando a versão da biblioteca. Esse problema está relacionado ao databricks e não ao SDK do serviço de Azure Machine Learning. Você também pode experimentar esse problema com outras bibliotecas. Exemplo:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Como alternativa, você pode usar scripts de inicialização se continuar enfrentando problemas de instalação com bibliotecas do Python. Essa abordagem não tem suporte oficial. Para obter mais informações, consulte [scripts de inicialização no escopo do cluster](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Cancelar uma execução de Machine Learning automatizada

Quando você usa recursos automatizados de aprendizado de máquina no Azure Databricks, para cancelar uma execução e iniciar uma nova execução de experimento, reinicie o cluster Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iterações para o aprendizado de máquina automatizado

Em configurações automatizadas do Machine Learning, se você tiver mais de 10 iterações `show_output` , `False` defina como quando enviar a execução.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget para o SDK do Azure Machine Learning/Machine Learning automatizado

O widget SDK do Azure Machine Learning não tem suporte em um bloco de anotações do databricks porque os notebooks não podem analisar widgets HTML. Você pode exibir o widget no portal usando este código Python na célula Azure Databricks notebook:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Erro de importação: Nenhum módulo chamado ' pandas. Core. Indexes '

Se você vir esse erro ao usar o aprendizado de máquina automatizado:

1. Execute este comando para instalar dois pacotes no cluster de Azure Databricks: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Desanexe e anexe novamente o cluster ao seu bloco de anotações. 

Se essas etapas não resolverem o problema, tente reiniciar o cluster.

### <a name="failtosendfeather"></a>FailToSendFeather

Se você vir um `FailToSendFeather` erro ao ler dados no cluster Azure Databricks, consulte as seguintes soluções:

* Atualize `azureml-sdk[automl_databricks]` o pacote para a versão mais recente.
* Adicione `azure-dataprep` a versão 1.1.8 ou superior.
* Adicione `pyarrow` a versão 0,11 ou superior.

## <a name="azure-portal"></a>Portal do Azure

Se você for diretamente exibir seu espaço de trabalho a partir de um link de compartilhamento do SDK ou do portal, não poderá exibir a página de Visão Geral normal com as informações de assinatura na extensão. Você também não poderá alternar para outro espaço de trabalho. Se você precisar exibir outro workspace, a solução alternativa será ir diretamente para o [portal do Azure](https://portal.azure.com) e procurar o nome do workspace.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

Às vezes, pode ser útil fornecer informações de diagnóstico ao pedir ajuda. Para ver alguns logs, visite [portal do Azure](https://portal.azure.com) e vá para seu espaço de trabalho e selecione **espaço de trabalho > teste > executar logs do >** .

## <a name="resource-quotas"></a>Cotas de recursos

Saiba mais sobre as [cotas de recursos](how-to-manage-quotas.md) que você pode encontrar ao trabalhar com o Azure Machine Learning.

## <a name="authentication-errors"></a>Erros de autenticação

Se executar uma operação de gerenciamento em um destino de computação de um trabalho remoto, você receberá um dos seguintes erros:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Por exemplo, você receberá um erro se tentar criar ou anexar um destino de computação de um Pipeline de ML que é enviado para execução remota.
