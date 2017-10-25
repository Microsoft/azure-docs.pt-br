---
title: "Guia detalhado de como usar a API de execução da Preparação de Dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece detalhes sobre como executar pacotes de preparação de dados e fontes de dados previamente projetados"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: ceecc718f8198e9a88cae4acbe97e7f26d95f984
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="executing-data-sources-and-data-preparation-packages-from-python"></a>Executando fontes de dados e pacotes de preparação de dados do Python

Para usar uma fonte de dados ou um pacote de preparação de dados dentro do Python:
- Navegue até a guia Dados do projeto.
- Clique com o botão direito do mouse na fonte apropriada.
- Escolha “Gerar Arquivo de Código de Acesso a Dados”.

Essa ação cria um script de Python curto que executa o pacote e retorna um dataframe.

## <a name="data-sources"></a>Fontes de dados

O módulo `azureml.dataprep.datasource` contém uma única função para executar uma fonte de dados e retornar um dataframe: `load_datasource(path, secrets=None, spark=None)`.
- `path` é o caminho para a fonte de dados (arquivo .dsource).
- `secrets` é um dicionário opcional que mapeia as chaves para os segredos.
- `spark` é um booleano opcional que especifica se deve retornar um dataframe Spark ou um dataframe Pandas. Por padrão, o Azure ML Workbench determina que tipo de dataframe retornar em tempo de execução com base no contexto.

## <a name="data-preparation-packages"></a>Pacotes de preparação de dados

O módulo `azureml.dataprep.package` contém três funções que executam um fluxo de dados de um pacote de preparação de dados.

### <a name="execution-functions"></a>Funções de execução

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` – envia o fluxo de dados especificado para execução, mas não retorna um dataframe.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` – executa o fluxo de dados especificado e retorna os resultados como um dataframe.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` – executa o fluxo de dados especificado com base em uma fonte de dados na memória e retorna os resultados como um dataframe. O argumento `user_config` é um dicionário que mapeia o caminho absoluto de uma fonte de dados (arquivo .dsource) a uma fonte de dados na memória, representada como uma lista de listas.

### <a name="common-arguments"></a>Argumentos comuns

- `package_path` é o caminho para o pacote de preparação de dados (arquivo .dprep).
- `dataflow_idx` é o índice com base em zero do qual o fluxo de dados no pacote é executado. Se o fluxo de dados especificado fizer referência a outras fontes de dados ou fluxos de dados, eles serão executados também.
- `secrets` é um dicionário opcional que mapeia as chaves para os segredos.
- `spark` é um booleano opcional que especifica se deve retornar um dataframe Spark ou um dataframe Pandas. Por padrão, o Azure ML Workbench determina que tipo de dataframe retornar em tempo de execução com base no contexto.
