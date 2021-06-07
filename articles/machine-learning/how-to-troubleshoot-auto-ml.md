---
title: Solucionar testes de ML automatizados
titleSuffix: Azure Machine Learning
description: Saiba como solucionar problemas em seus experimentos de aprendizado de máquina automatizados.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl, references_regions
ms.openlocfilehash: 28aac830326d60161f54d7ad5fa03326c1d66462
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563667"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>Solucionar testes de ML automatizados em Python

Neste guia, saiba como identificar e resolver problemas conhecidos em seus experimentos de aprendizado de máquina automatizados com o [SDK do Azure Machine Learning](/python/api/overview/azure/ml/intro).

## <a name="version-dependencies"></a>Dependências de versão

**`AutoML` dependências às versões mais recentes do pacote desmembram a compatibilidade**. Após a versão 1.13.0 do SDK, os modelos não são carregados em SDKs mais antigos devido à incompatibilidade entre as versões mais antigas fixadas em `AutoML` pacotes anteriores e as versões mais recentes fixadas hoje.

Erros de espera, como:

* O módulo não encontrou erros como,

  `No module named 'sklearn.decomposition._truncated_svd`

* Erros de importação, como,

  `ImportError: cannot import name 'RollingOriginValidator'`,
* Erros de atributo como,

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

As resoluções dependem da sua `AutoML` versão de treinamento do SDK:

* Se sua `AutoML` versão de treinamento do SDK for maior que 1.13.0, você precisará de `pandas == 0.25.1` e `scikit-learn==0.22.1` .

    * Se houver uma incompatibilidade de versão, atualize scikit-Learn e/ou pandas para corrigir a versão com o seguinte

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* Se sua `AutoML` versão de treinamento do SDK for menor ou igual a 1.12.0, você precisará de `pandas == 0.23.4` e `sckit-learn==0.20.3` .
  * Se houver uma incompatibilidade de versão, faça o downgrade de scikit-Learn e/ou pandas para corrigir a versão com o seguinte
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>Instalação

`AutoML` as alterações de pacote desde a versão 1.0.76 exigem que a versão anterior seja desinstalada antes de atualizar para a nova versão.

* **`ImportError: cannot import name AutoMLConfig`**

    Se você encontrar esse erro após a atualização de uma versão do SDK antes de v 1.0.76 para v 1.0.76 ou posterior, resolva o erro executando: `pip uninstall azureml-train automl` e, em seguida `pip install azureml-train-automl` . O script automl_setup. cmd faz isso automaticamente.

* **automl_setup falha**

  * No Windows, execute automl_setup de um prompt do Anaconda. [Instale o Miniconda](https://docs.conda.io/en/latest/miniconda.html).

  * Verifique se o Conda 64-bit versão 4.4.10 ou posterior está instalado. Você pode verificar o bit com o `conda info` comando. O `platform` deve ser `win-64` para Windows ou `osx-64` para Mac. Para verificar a versão, use o comando `conda -V` . Se você tiver uma versão anterior instalada, poderá atualizá-la usando o comando: `conda update conda` . Para verificar 32 bits executando 

  * Verifique se o Conda está instalado. 

  * Linux `gcc: error trying to exec 'cc1plus'`

    1. Se o `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` erro for encontrado, instale as ferramentas de Build gcc para sua distribuição do Linux. Por exemplo, no Ubuntu, use o comando `sudo apt-get install build-essential` .

    1. Passe um novo nome como o primeiro parâmetro para automl_setup para criar um novo ambiente Conda. Exibir ambientes Conda existentes usando `conda env list` e removê-los com o `conda env remove -n <environmentname>` .

* **automl_setup_linux. sh falhará**: se automl_setup_linus. sh falhar em Ubuntu Linux com o erro: `unable to execute 'gcc': No such file or directory`

  1. Verifique se as portas de saída 53 e 80 estão habilitadas. Em uma máquina virtual do Azure, você pode fazer isso na portal do Azure selecionando a VM e clicando em **rede**.
  1. Execute o comando: `sudo apt-get update`
  1. Execute o comando: `sudo apt-get install build-essential --fix-missing`
  1. Executar `automl_setup_linux.sh` novamente

* **Configuration. ipynb falha**:

  * Para Conda locais, primeiro verifique se o `automl_setup` foi executado com êxito.
  * Verifique se o subscription_id está correto. Localize os subscription_id na portal do Azure selecionando todos os serviços e, em seguida, assinaturas. Os caracteres "<" e ">" não devem ser incluídos no valor de subscription_id. Por exemplo, `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` tem o formato válido.
  * Verifique se o acesso de colaborador ou proprietário à assinatura.
  * Verifique se a região é uma das regiões com suporte:,,,,,, `eastus2` `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` , `southcentralus` .
  * Verifique o acesso à região usando o portal do Azure.
  
* **Workspace.from_config falha**:

  Se a chamada `ws = Workspace.from_config()` falhar:

  1. Verifique se o bloco de anotações Configuration. ipynb foi executado com êxito.
  1. Se o bloco de anotações estiver sendo executado de uma pasta que não está sob a pasta em que o `configuration.ipynb` foi executado, copie a pasta aml_config e o config.jsde arquivo que ele contém para a nova pasta. Workspace.from_config lê o config.jsem para a pasta do bloco de anotações ou sua pasta pai.
  1. Se uma nova assinatura, um grupo de recursos, um espaço de trabalho ou uma região estiver sendo usada, certifique-se de executar o `configuration.ipynb` bloco de anotações novamente. Alterar config.jsdiretamente só funcionará se o espaço de trabalho já existir no grupo de recursos especificado na assinatura especificada.
  1. Se você quiser alterar a região, altere o espaço de trabalho, o grupo de recursos ou a assinatura. `Workspace.create` não criará ou atualizará um espaço de trabalho se ele já existir, mesmo se a região especificada for diferente.

## <a name="tensorflow"></a>TensorFlow

A partir da versão 1.5.0 do SDK, o Machine Learning automatizado não instala modelos TensorFlow por padrão. Para instalar o TensorFlow e usá-lo com seus experimentos de ML automatizados, instale `tensorflow==1.12.0` por meio do `CondaDependencies` .

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>Falhas de numpy

* **`import numpy` falha no Windows**: alguns ambientes do Windows veem um erro ao carregar o numpy com a versão mais recente do Python 3.6.8. Se você vir esse problema, tente com a versão 3.6.7 do Python.

* **`import numpy` falha**: Verifique a versão do TensorFlow no ambiente Conda do ml automatizado. As versões com suporte são < 1,13. Desinstale o TensorFlow do ambiente se a versão for >= 1,13.

Você pode verificar a versão do TensorFlow e desinstalar da seguinte maneira:

  1. Inicie um shell de comando, ative o ambiente Conda onde os pacotes de ml automatizados são instalados.
  1. Insira `pip freeze` e procure `tensorflow` , se encontrado, a versão listada deve ser < 1,13
  1. Se a versão listada não for uma versão com suporte, `pip uninstall tensorflow` no Shell de comando e digite y para confirmação.

## `jwt.exceptions.DecodeError`

Mensagem de erro exata: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

Para versões do SDK <= 1.17.0, a instalação pode resultar em uma versão sem suporte do PyJWT. Verifique se a versão PyJWT no ambiente Conda do ml automatizado é uma versão com suporte. Essa é a versão PyJWT < 2.0.0.

Você pode verificar a versão do PyJWT da seguinte maneira:

1. Inicie um shell de comando e ative o ambiente Conda onde os pacotes de ML automatizados são instalados.

1. Insira `pip freeze` e procure `PyJWT` , se encontrado, a versão listada deve ser < 2.0.0

Se a versão listada não for uma versão com suporte:

1. Considere atualizar para a versão mais recente do SDK do AutoML: `pip install -U azureml-sdk[automl]`

1. Se isso não for viável, desinstale o PyJWT do ambiente e instale a versão correta da seguinte maneira:

    1. `pip uninstall PyJWT` no Shell de comando e digite `y` para confirmação.
    1. Instale o usando o `pip install 'PyJWT<2.0.0'` .
  
## <a name="databricks"></a>Databricks
Consulte [como configurar um experimento de ml automatizado com o databricks](how-to-configure-databricks-automl-environment.md#troubleshooting).

## <a name="forecasting-r2-score-is-always-zero"></a>Previsão de Pontuação do R2 é sempre zero

 Esse problema ocorrerá se os dados de treinamento fornecidos tiverem uma série temporal que contém o mesmo valor para os últimos `n_cv_splits`  +  `forecasting_horizon` pontos de dados.

Se esse padrão for esperado em sua série temporal, você poderá alternar a métrica primária para um **erro de quadrado médio de raiz normalizado**.

## <a name="failed-deployment"></a>Falha na implantação

 Para versões <= 1.18.0 do SDK, a imagem base criada para implantação pode falhar com o seguinte erro: `ImportError: cannot import name cached_property from werkzeug` .

  As etapas a seguir podem contornar o problema:

  1. Baixar o pacote de modelo
  1. Descompactar o pacote
  1. Implantar usando os ativos descompactados

## <a name="azure-functions-application"></a>Azure Functions aplicativo
  
  No momento, o ML automatizado não oferece suporte a aplicativos Azure Functions. 

## <a name="sample-notebook-failures"></a>Falhas de notebook de exemplo

 Se um bloco de anotações de exemplo falhar com um erro que a propriedade, o método ou a biblioteca não existe:

* Verifique se o kernel correto foi selecionado na Jupyter Notebook. O kernel é exibido no canto superior direito da página do bloco de anotações. O padrão é *azure_automl*. O kernel é salvo como parte do bloco de anotações. Se você alternar para um novo ambiente Conda, precisará selecionar o novo kernel no bloco de anotações.

  * Por Azure Notebooks, ele deve ser Python 3,6.
  * Para ambientes Conda locais, ele deve ser o nome do ambiente Conda que você especificou em automl_setup.

* Para garantir que o notebook seja a versão do SDK que você está usando,
  * Verifique a versão do SDK executando `azureml.core.VERSION` em uma célula Jupyter notebook.
  * Você pode baixar a versão anterior dos blocos de anotações de exemplo do GitHub com estas etapas:
    1. Selecione o `Branch` botão
    1. Navegue até a `Tags` guia
    1. Selecione a versão

## <a name="next-steps"></a>Próximas etapas

+ Saiba mais sobre [como treinar um modelo de regressão com o aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [como treinar usando o aprendizado de máquina automatizado em um recurso remoto](how-to-auto-train-remote.md).

+ Saiba mais sobre [como e onde implantar um modelo](how-to-deploy-and-where.md).
