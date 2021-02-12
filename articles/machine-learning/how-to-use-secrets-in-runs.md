---
title: Segredos de autenticação no treinamento
titleSuffix: Azure Machine Learning
description: Saiba como passar segredos para o treinamento é executado de maneira segura usando o Azure Key Vault para seu espaço de trabalho.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: ce47041d888b8a7786f0f87f54e725919638e7f7
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349011"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Usar segredos de credencial de autenticação em Azure Machine Learning execuções de treinamento


Neste artigo, você aprenderá a usar os segredos no treinamento em execução com segurança. As informações de autenticação, como seu nome de usuário e senha, são segredos. Por exemplo, se você se conectar a um banco de dados externo a fim de consultar dado de treinamento, você precisará passar seu nome de usuário e senha para o contexto de execução remota. Codificar esses valores em scripts de treinamento em texto não criptografado é inseguro, pois ele exporia o segredo. 

Em vez disso, seu espaço de trabalho Azure Machine Learning tem um recurso associado chamado de [Azure Key Vault](../key-vault/general/overview.md). Use esta Key Vault para passar segredos para execuções remotas com segurança por meio de um conjunto de APIs no SDK do Azure Machine Learning Python.

O fluxo padrão para usar segredos é:
 1. No computador local, faça logon no Azure e conecte-se ao seu espaço de trabalho.
 2. No computador local, defina um segredo no Key Vault do espaço de trabalho.
 3. Enviar uma execução remota.
 4. Na execução remota, obtenha o segredo de Key Vault e use-o.

## <a name="set-secrets"></a>Definir segredos

No Azure Machine Learning, a classe [keyvault](/python/api/azureml-core/azureml.core.keyvault.keyvault?preserve-view=true&view=azure-ml-py) contém métodos para definir segredos. Em sua sessão do Python local, primeiro obtenha uma referência ao seu espaço de trabalho Key Vault e, em seguida, use o [`set_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault?preserve-view=true&view=azure-ml-py#&preserve-view=trueset-secret-name--value-) método para definir um segredo por nome e valor. O método __set_secret__ atualizará o valor secreto se o nome já existir.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Não coloque o valor secreto em seu código Python, pois ele não é seguro para armazená-lo no arquivo como texto não criptografado. Em vez disso, obtenha o valor secreto de uma variável de ambiente, por exemplo, o segredo de compilação do Azure DevOps ou da entrada interativa do usuário.

Você pode listar nomes de segredo usando o [`list_secrets()`](/python/api/azureml-core/azureml.core.keyvault.keyvault?preserve-view=true&view=azure-ml-py#&preserve-view=truelist-secrets--) método e também há uma versão do lote,[set_secrets ()](/python/api/azureml-core/azureml.core.keyvault.keyvault?preserve-view=true&view=azure-ml-py#&preserve-view=trueset-secrets-secrets-batch-) que permite que você defina vários segredos de cada vez.

## <a name="get-secrets"></a>Obter segredos

No código local, você pode usar o [`get_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-secret-name-) método para obter o valor secreto por nome.

Para execuções enviadas [`Experiment.submit`](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-)  , use o [`get_secret()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-secret-name-) método com a [`Run`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) classe. Como uma execução enviada está ciente de seu espaço de trabalho, esse método faz o atalho da instanciação do espaço de trabalho e retorna o valor secreto diretamente.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Tenha cuidado para não expor o valor secreto escrevendo ou imprimindo-o.

Também há uma versão do lote, [get_secrets ()](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-secrets-secrets-) para acessar vários segredos de uma só vez.

## <a name="next-steps"></a>Próximas etapas

 * [Exibir bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Saiba mais sobre o Enterprise Security com Azure Machine Learning](concept-enterprise-security.md)