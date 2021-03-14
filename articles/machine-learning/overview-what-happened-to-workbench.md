---
title: O que aconteceu com o Workbench?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning é uma solução de ciência de dados integrada para modelar e implantar aplicativos ML em escala de nuvem. O recurso Workbench foi desativado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: larryfr
author: BlackMist
ms.date: 03/05/2020
ms.openlocfilehash: c800901ae139b16e75613b0cc8de3a9485186896
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521366"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>O que aconteceu com o Workbench de aprendizado de máquina do Azure?

O aplicativo Azure Machine Learning Workbench e alguns outros recursos iniciais foram preteridos e substituídos na versão de **setembro de 2018** para abrir caminho para uma [arquitetura](concept-azure-machine-learning-architecture.md) aprimorada.

A versão contém muitas atualizações significativas solicitadas pelos comentários dos clientes para melhorar sua experiência. A funcionalidade principal de execuções de experimentos para implantação de modelo não foi alterada. Mas agora, você pode usar o eficiente <a href="/python/api/overview/azure/ml/intro" target="_blank">Python SDK</a>, R SDK, e a [CLI do Azure](reference-azure-machine-learning-cli.md) para realizar seus pipelines e tarefas de aprendizado de máquina.

A maioria dos artefatos criados na versão anterior do Azure Machine Learning é armazenada em seu próprio armazenamento local ou em nuvem. Esses artefatos já não desaparecem.

Neste artigo, você aprenderá sobre o que mudou e como isso afeta seu trabalho preexistente com Azure Machine Learning Workbench e suas APIs.

>[!Warning]
>Este artigo não é para usuários do Azure Machine Learning Studio. Destina-se aos clientes do Azure Machine Learning que instalaram o aplicativo Workbench (versão prévia) e/ou têm contas de versão prévia de gerenciamento de modelos e experimentação.


## <a name="what-changed"></a>O que mudou?

A última versão do Azure Machine Learning inclui os seguintes recursos:
+ Um [modelo simplificado de recursos do Azure](concept-azure-machine-learning-architecture.md).
+ Uma [nova interface do usuário do portal](how-to-track-experiments.md) para gerenciar suas experiências e destinos de computação.
+ Um <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK</a>do Python novo e mais abrangente.
+ A nova [extensão de CLI do Azure](reference-azure-machine-learning-cli.md) expandida para aprendizado de máquina.

A [arquitetura](concept-azure-machine-learning-architecture.md) foi reprojetada visando facilidade de uso. Em vez de várias contas e recursos do Azure, você só precisa de um [Workspace do Azure Machine Learning](concept-workspace.md). Você pode criar workspaces rapidamente no [Portal do Azure](how-to-manage-workspace.md). Usando um workspace, múltiplos usuários podem armazenar destinos de computação de treinamento e de implantação, experimentos de modelo, imagens do Docker, modelos implementados e assim por diante.

Embora existam novos clientes da CLI e do SDK aprimorados na versão atual, o próprio aplicativo de workbench da área de trabalho foi desativado. Os testes podem ser gerenciados no [painel de workspace no studio de Azure Machine Learning](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal). Use o painel para obter o histórico de experimentos, gerenciar as metas de computação anexadas ao seu workspace, gerenciar seus modelos e imagens do Docker e até mesmo implementar serviços da web.

<a name="timeline"></a>

## <a name="support-timeline"></a>Linha do tempo de suporte

Em 9 de Janeiro, 2019 suporte para Machine Learning Workbench, Experimentação do Azure Machine Learning e contas de Gerenciamento de Modelos, e o SDK e a CLI associados foram encerrados.

Todas as funcionalidades mais recentes estão disponíveis usando este <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK</a>, a [CLI](reference-azure-machine-learning-cli.md) e o [portal](how-to-manage-workspace.md).

## <a name="what-about-run-histories"></a>E o histórico de execução?

Os históricos de execução mais antigos não estão mais acessíveis, mas você ainda pode ver suas execuções na versão mais recente.

Os históricos de execução agora são chamados de **testes**. Você pode coletar os experimentos do seu modelo e explorá-los usando o SDK, a CLI ou o studio do Azure Machine Learning.

Painel de workspace do portal é compatível apenas com os navegadores Microsoft Edge, Chrome e Firefox:

[![Portal online](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Comece treinando seus modelos e acompanhando os históricos de execução usando o novo CLI e SDK. Saiba como fazer isso com o [Tutorial: treinar modelos com o Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="will-projects-persist"></a>Projetos persistirá?

Você não perderá nenhum código ou trabalho. Na versão mais antiga, os projetos são entidades na nuvem com um diretório local. Na última versão, você anexa diretórios locais ao Workspace do Azure Machine Learning usando um arquivo de configuração local. Veja um [diagrama da arquitetura mais recente](concept-azure-machine-learning-architecture.md).

Grande parte do conteúdo do projeto já estava em seu computador local. Sendo assim, você só precisa criar um arquivo de configuração nesse diretório e referenciá-lo em seu código para se conectar ao seu workspace. Para continuar usando o diretório local que contém seus arquivos e scripts, especifique o nome do diretório no comando de Python ['experiment.submit'](/python/api/azureml-core/azureml.core.experiment.experiment) ou usando o comando da CLI `az ml project attach`.  Por exemplo:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Criar um workspace](how-to-manage-workspace.md) para começar a usar.

## <a name="what-about-my-registered-models-and-images"></a>E quanto aos meus modelos e imagens registrados?

Os modelos registrados em seu registro de modelo antigo devem ser migrados para o seu novo workspace se você quiser continuar a usá-los. Para migrar seus modelos, baixe-os e registre-os novamente no novo workspace.

As imagens criadas em seu antigo Registro de imagens não podem ser migradas diretamente para o novo workspace. Na maioria dos casos, o modelo pode ser implantado sem a necessidade de criar uma imagem. Se necessário, é possível criar uma imagem para o modelo no novo workspace. Para saber mais, confira [Manage, register, deploy, and monitor machine learning models](concept-model-management-and-deployment.md) (Gerenciar, registrar, implantar e monitorar modelos de machine learning).

## <a name="what-about-deployed-web-services"></a>E serviços web implantados?

Agora que o suporte para a CLI antiga foi encerrado, não é mais possível reimplantar modelos ou gerenciar os serviços Web que você implantou originalmente com sua conta de Gerenciamento de Modelos. No entanto, esses serviços da Web continuarão a funcionar enquanto o Azure Container Service (ACS) ainda for suportado.

Na versão mais recente, os modelos são implantados como serviços Web para clusters da ACI (Instâncias de Contêiner do Azure) ou do AKS (Serviço de Kubernetes do Azure). Você também pode implantar em FPGAs e no Azure IoT Edge.

Saiba mais nestes artigos:
+ [Onde e como implantar modelos](how-to-deploy-and-where.md)
+ [Tutorial: implantar modelos com Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [arquitetura mais recente do Azure Machine Learning](concept-azure-machine-learning-architecture.md).

Para obter uma visão geral do serviço, leia [O que é o Azure Machine Learning?](overview-what-is-azure-ml.md).

Crie seu primeiro experimento com seu método preferencial:

  + [Usar seu próprio ambiente](tutorial-1st-experiment-sdk-setup-local.md)
  + [Usar notebooks do Python](tutorial-1st-experiment-sdk-setup.md)
  + [Usar R Markdown](https://github.com/Azure/azureml-sdk-for-r) 
  + [Usar aprendizado de máquina automatizado](tutorial-designer-automobile-price-train-score.md) 
  + [Usar os recursos de arrastar e soltar do designer](tutorial-first-experiment-automated-ml.md) 
  + [Usar a extensão ML para a CLI](tutorial-train-deploy-model-cli.md)
