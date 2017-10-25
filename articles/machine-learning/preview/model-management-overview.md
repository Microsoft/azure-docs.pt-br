---
title: "Visão geral conceitual Gerenciamento de Modelos do Azure Machine Learning | Microsoft Docs"
description: Este documento explica os conceitos de Gerenciamento de Modelos do Azure Machine Learning.
services: machine-learning
author: nk773
ms.author: neerajkh, padou
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 31b859d86e82c92839462280721c5f84f1d923cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-model-management"></a>Gerenciamento de Modelos do Azure Machine Learning

O Gerenciamento de Modelos do Azure Machine Learning permite gerenciar e implantar fluxos de trabalho e modelos de aprendizado de máquina. 

O Gerenciamento de Modelos fornece recursos para:
- Controle de versão de modelo
- Acompanhamento dos modelos em produção
- Implantação de modelos para produção por meio do Ambiente de Computação do AzureML com o [Serviço de Contêiner do Azure](https://azure.microsoft.com/services/container-service/) e o [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Criação de contêineres do Docker com os modelos e teste local
- Retreinamento de modelo automatizado
- Captura de telemetria de modelo para insights acionáveis. 

O Gerenciamento de Modelos do Azure Machine Learning fornece um registro das versões do modelo. Ele também fornece fluxos de trabalho automatizados para empacotar e implantar os contêineres de Machine Learning como APIs REST. Os modelos e suas dependências de tempo de execução são empacotados no contêiner do Docker com base em Linux com a API de previsão. 

Os Ambientes de Computação do Azure Machine Learning ajudam a configurar e gerenciar clusters escalonáveis para hospedar os modelos. O ambiente de computação é baseado nos Serviços de Contêiner do Azure. Os Serviços de Contêiner do Azure fornecem exposição automática das APIs de Machine Learning como pontos de extremidade da API REST com os seguintes recursos:

- Autenticação
- Balanceamento de carga
- Expansão automática
- Criptografia

O Gerenciamento de Modelos do Azure Machine Learning fornece esses recursos por meio da CLI, da API e do Portal do Azure. 

O Gerenciamento de Modelos do Azure Machine Learning usa as seguintes informações:

 - Arquivo de modelo ou um diretório com os arquivos de modelo
 - Arquivo Python criado pelo usuário implementando uma função de pontuação de modelo
 - Arquivo de dependência Conda listando as dependências de tempo de execução
 - Escolha do ambiente do tempo de execução e 
 - Arquivo de esquema para parâmetros de API 

Essas informações são usadas ao executar as seguintes ações:

- Registro de um modelo
- Criação de um manifesto que é usado ao criar um contêiner
- Criação de uma imagem de contêiner do Docker
- Implantação de um contêiner em Serviço de Contêiner do Azure
 
A figura a seguir mostra uma visão geral de como os modelos são registrados e implantados no cluster. 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>Criar e gerenciar modelos 
Você pode registrar os modelos com o Gerenciamento de Modelos do Azure Machine Learning para acompanhar as versões de modelos em produção. Para facilitar a governança e a capacidade de reprodução, o serviço captura todas as dependências e as informações associadas. Para obter insights mais aprofundados de desempenho, você pode capturar a telemetria de modelo usando o SDK fornecido. A telemetria do modelo é arquivada no armazenamento fornecido pelo usuário. A telemetria de modelo pode ser usada posteriormente para analisar o desempenho do modelo, treinamento e obter insights para seu negócio.

## <a name="create-and-manage-manifests"></a>Criar e gerenciar manifestos 
Os modelos exigem artefatos adicionais para implantar na produção. O sistema fornece a capacidade de criar um manifesto que abrange o modelo, as dependências, o script de inferência (também conhecido como script de pontuação), os dados de exemplo, o esquema etc. Esse manifesto atua como a receita usada para criar uma imagem de contêiner do Docker. As empresas podem gerar automaticamente o manifesto, criar versões diferentes e gerenciar seus manifestos. 

## <a name="create-and-manage-docker-container-images"></a>Criar e gerenciar imagens de contêiner do Docker 
Você pode usar o manifesto da etapa anterior para criar imagens de contêiner com base em Docker em seus respectivos ambientes. As imagens em contêineres com base em Docker fornecem às empresas a flexibilidade para executar essas imagens nos seguintes ambientes de computação:

- [Serviço de Contêiner do Azure com base em Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Serviços de contêiner locais
- Ambientes de desenvolvimento
- Dispositivos IoT

Essas imagens em contêineres com base em Docker são autossuficientes com todas as dependências necessárias exigidas para gerar previsões. 

## <a name="deploy-docker-container-images"></a>Implantar imagens de contêiner do Docker 
Com o Gerenciamento de Modelos do Azure Machine Learning, você pode implantar imagens de contêiner com base em Docker com um único comando para o Serviço de Contêiner do Azure gerenciado pelo Ambiente de Computação ML. Essas implantações são criadas com um servidor front-end que fornece os seguintes recursos:

- Previsões de baixa latência em escala
- Balanceamento de carga
- Dimensionamento automático de pontos de extremidade ML
- Autorização da chave de API
- Documento de swagger de API

Você pode controlar a escala de implantação e telemetria por meio das configurações a seguir:

- Registro em log do sistema e telemetria do modelo para cada nível de serviço Web. Se habilitada, todos os logs de stdout são transmitidos ao [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). A telemetria de modelo é arquivada no armazenamento fornecido por você. 
- Limites de dimensionamento automático e simultaneidade. Essas configurações automaticamente aumentam o número de contêineres implantados com base no carregamento do cluster existente. Elas também controlam a taxa de transferência e a consistência de latência de previsão.

## <a name="consumption"></a>Consumo 
O Gerenciamento de Modelos do Azure Machine Learning cria a API REST para o modelo implantado juntamente com o documento de swagger. Você pode consumir modelos implantados chamando as APIs REST com a API de chave e entradas do modelo para obter as previsões como parte dos aplicativos de linha de negócios. O código de exemplo está disponível no GitHub para linguagens Java, [Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py)e C# para chamar APIs REST. A CLI do Gerenciamento de Modelos do Azure Machine Learning fornece uma maneira fácil de trabalhar com essas APIs REST. Você pode consumir as APIs usando um único comando da CLI, aplicativos habilitados para swagger ou rotação. 

## <a name="retraining"></a>Retreinamento 
O Gerenciamento de Modelos do Azure Machine Learning fornece APIs que você pode usar para treinar novamente seus modelos. Você também pode usar as APIs para atualizar as implantações existentes com as versões atualizadas do modelo. Como parte do fluxo de trabalho de ciência de dados, você pode recriar o modelo em seu ambiente de experimentação. Em seguida, registre o modelo com o Gerenciamento de Modelos e atualize as implantações existentes. As atualizações são executadas usando um único comando UPDATE da CLI. O comando UPDATE atualiza as implantações existentes sem alterar a URL da API ou a chave. Os aplicativos que consomem o modelo continuam a trabalhar sem qualquer alteração de código e começam a obter melhores previsões usando o novo modelo.

O fluxo de trabalho completo que descreve esses conceitos é capturado na figura a seguir:

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ) 
- Há suporte para quais tipos de dados? Pode passar matrizes NumPy diretamente como entrada para o serviço Web?

   Se estiver fornecendo o arquivo de esquema que foi criado usando o SDK generate_schema, você poderá passar o NumPy e/ou o Pandas DF. Você também pode passar qualquer entrada serializável JSON. Você também pode passar a imagem como cadeia de caracteres codificada como binária.

- O serviço Web dá suporte a várias entradas ou analisa entradas diferentes? 

   Sim, você pode tomar várias entradas empacotadas na solicitação JSON como um dicionário. Cada entrada corresponde a uma chave única de dicionário exclusiva.

- A chamada é ativada por uma solicitação para o serviço Web, uma chamada de bloqueio ou uma chamada assíncrona?

   Se o serviço foi criado usando a opção de tempo real como parte da CLI ou API, então ele é uma chamada síncrona/de bloqueio. Espera-se que seja rápido e em tempo real. No entanto, no lado do cliente, você pode chamá-lo usando a biblioteca HTTP assíncrona para evitar o bloqueio de thread de cliente.

- Quantas solicitações o serviço Web pode manipular simultaneamente?

   Depende da escala do serviço Web e do cluster. Você pode expandir seu serviço para 100x de réplicas e, em seguida, ele pode lidar com muitas solicitações simultaneamente. Você também pode configurar um máximo de solicitações simultâneas por réplica para aumentar a taxa de transferência do serviço.

- Quantas solicitações o serviço Web pode colocar na fila?

   É configurável. Por padrão, ele é definido para cerca de 10 por réplica única, mas você pode aumentar/diminuir de acordo com os requisitos do aplicativo. Normalmente, aumentar o número de solicitações em fila aumenta a taxa de transferência do serviço, mas torna as latências piores em percentuais mais altos. Para manter as latências consistentes, você pode definir o enfileiramento para um valor baixo (1-5) e aumentar o número de réplicas para lidar com a taxa de transferência. Você também pode ativar o dimensionamento automático para fazer com que o número de réplicas seja ajustado automaticamente com base no carregamento. 

- O mesmo computador ou cluster pode ser usado para vários pontos de extremidade de serviço Web?

   Com certeza. Você pode executar 100x de pontos de extremidade/serviços no mesmo cluster. 

## <a name="next-steps"></a>Próximas etapas
Para começar a trabalhar com o Gerenciamento de Modelos, consulte [Configuração do Gerenciamento de Modelos](model-management-configuration.md).
