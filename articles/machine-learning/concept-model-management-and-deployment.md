---
title: 'MLOps: gerenciamento de modelos de ML'
titleSuffix: Azure Machine Learning
description: 'Saiba mais sobre o gerenciamento de modelos com o Azure Machine Learning (MLOps). Implante, gerencie e monitore seus modelos para melhorá-los continuamente. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 32777d9f8313457036b103777f251329687b3997
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508003"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: gerenciamento de modelos, implantação e monitoramento com Azure Machine Learning

Neste artigo, saiba mais sobre como usar Azure Machine Learning para gerenciar o ciclo de vida de seus modelos. Azure Machine Learning usa uma abordagem de MLOps (operações de Machine Learning). O MLOps melhora a qualidade e a consistência de suas soluções de aprendizado de máquina. 

## <a name="what-is-mlops"></a>O que é MLOps?

As operações de Machine Learning (MLOps) baseiam-se em princípios e práticas do [DevOps](https://azure.microsoft.com/overview/what-is-devops/) que aumentam a eficiência dos fluxos de trabalho. Por exemplo, integração, entrega e implantação contínuas. O MLOps aplica esses princípios ao processo de aprendizado de máquina, com o objetivo de:

* Experimentação e desenvolvimento mais rápidos de modelos
* Implantação mais rápida de modelos na produção
* Garantia de qualidade

Azure Machine Learning fornece os seguintes recursos de MLOps:

- **Crie pipelines ml reproduzíveis**. Machine Learning pipelines permitem que você defina etapas reproduzíveis e reutilizáveis para seus processos de preparação, treinamento e pontuação de dados.
- **Crie ambientes de software reutilizáveis** para treinamento e implantação de modelos.
- **Registre, empacote e implante modelos de qualquer lugar**. Você também pode rastrear os metadados associados necessários para usar o modelo.
- **Capture os dados de governança para o ciclo de vida de ml de ponta a ponta**. As informações registradas podem incluir quem está publicando modelos, por que as alterações foram feitas e quando os modelos foram implantados ou usados na produção.
- **Notificar e alertar sobre eventos no ciclo de vida am**. Por exemplo, a conclusão do experimento, o registro do modelo, a implantação do modelo e a detecção de descompasso de dados.
- **Monitore aplicativos ml para problemas operacionais e relacionados ao ml**. Compare as entradas de modelo entre treinamento e inferência, explore métricas específicas de modelo e forneça monitoramento e alertas em sua infraestrutura de ML.
- **Automatize o ciclo de vida de ml de ponta a ponta com Azure Machine Learning e Azure pipelines**. O uso de pipelines permite atualizar modelos com frequência, testar novos modelos e distribuir continuamente novos modelos de ML junto com seus outros aplicativos e serviços.

## <a name="create-reproducible-ml-pipelines"></a>Criar pipelines ML reproduzíveis

Use pipelines ML do Azure Machine Learning para unir todas as etapas envolvidas no processo de treinamento do modelo.

Um pipeline ML pode conter etapas de preparação de dados para extração de recursos para ajuste de hiperparâmetro para avaliação de modelo. Para obter mais informações, consulte [pipelines do ml](concept-ml-pipelines.md).

Se você usar o [Designer](concept-designer.md) para criar seus pipelines de ml, você poderá, a qualquer momento, clicar em **"..."** na parte superior direita da página do designer e, em seguida, selecionar **clonar**. Clonar seu pipeline permite que você itere seu design de pipeline sem perder suas versões antigas.  

## <a name="create-reusable-software-environments"></a>Criar ambientes de software reutilizáveis

Os ambientes de Azure Machine Learning permitem que você acompanhe e reproduza as dependências de software de seus projetos à medida que eles evoluem. Os ambientes permitem garantir que as compilações sejam reproduzíveis sem configurações manuais de software.

Os ambientes descrevem as dependências Pip e Conda para seus projetos e podem ser usados tanto para treinamento quanto para implantação de modelos. Para obter mais informações, consulte [o que são ambientes de Azure Machine Learning](concept-environments.md).

## <a name="register-package-and-deploy-models-from-anywhere"></a>Registre, empacote e implante modelos de qualquer lugar

### <a name="register-and-track-ml-models"></a>Registrar e acompanhar modelos de ML

O registro de modelo permite que você armazene e controle a versão de seus modelos na nuvem do Azure em seu workspace. O registro de modelo torna mais fácil organizar e manter o controle de seus modelos treinados.

> [!TIP]
> Um modelo registrado é um contêiner lógico para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo em seu workspace do Azure Machine Learning. Após o registro, você pode baixar ou implantar o modelo registrado e receber todos os arquivos registrados.

Modelos registrados são identificados por nome e versão. Cada vez que você registra um modelo com o mesmo nome de um já existente, o Registro incrementa a versão. Marcas de metadados adicionais podem ser fornecidas durante o registro. Essas marcas são usadas durante a pesquisa de um modelo. Azure Machine Learning dá suporte a qualquer modelo que possa ser carregado usando Python 3.5.2 ou superior.

> [!TIP]
> Você também pode registrar modelos treinados fora do Azure Machine Learning.

Você não pode excluir um modelo registrado que está sendo usado em uma implantação ativa.
Para obter mais informações, consulte a seção sobre registro de modelos em [Implantar modelos](how-to-deploy-and-where.md#registermodel).

> [!IMPORTANT]
> Ao usar filtrar por `Tags` opção na página modelos do Azure Machine Learning Studio, em vez de usar `TagName : TagValue` os clientes devem usar `TagName=TagValue` (sem espaço)

### <a name="profile-models"></a>Modelos de perfil

Azure Machine Learning pode ajudá-lo a entender os requisitos de CPU e memória do serviço que será criado quando você implantar seu modelo. A criação de perfil testa o serviço que executa o modelo e retorna informações como uso da CPU, uso de memória e latência de resposta. Ele também fornece uma recomendação de CPU e memória com base no uso de recursos.
Para obter mais informações, consulte a seção criação de perfil de [implantar modelos](how-to-deploy-profile-model.md).

### <a name="package-and-debug-models"></a>Modelos de pacote e depuração

Antes de implantar um modelo na produção, ele é empacotado em uma imagem do Docker. Na maioria dos casos, a criação de imagem ocorre automaticamente em segundo plano durante a implantação. Você pode especificar a imagem manualmente.

Se você tiver problemas com a implantação, poderá implantar em seu ambiente de desenvolvimento local para solução de problemas e depuração.

Para obter mais informações, consulte [implantar modelos](how-to-deploy-and-where.md#registermodel) e [solucionar problemas de implantações](how-to-troubleshoot-deployment.md).

### <a name="convert-and-optimize-models"></a>Converter e otimizar modelos

A conversão do modelo para [abrir a rede neural](https://onnx.ai) (ONNX) pode melhorar o desempenho. Em média, a conversão para ONNX pode gerar um aumento de desempenho de 2x.

Para obter mais informações sobre o ONNX com Azure Machine Learning, consulte o artigo [criar e acelerar modelos de ml](concept-onnx.md) .

### <a name="use-models"></a>Usar modelos

Os modelos de aprendizado de máquina treinados são implantados como serviços Web na nuvem ou localmente. Você também pode implantar modelos para Azure IoT Edge dispositivos. As implantações usam CPU, GPU ou FPGA (matrizes de portão programável por campo) para inferência. Você também pode usar modelos do Power BI.

Ao usar um modelo como um serviço Web ou dispositivo IoT Edge, você fornece os seguintes itens:

* Os modelos que são usados para pontuar os dados enviados para o serviço/dispositivo.
* Um script de entrada. Esse script aceita solicitações, usa os modelos para pontuar os dados e retornar uma resposta.
* Um ambiente Azure Machine Learning que descreve as dependências Pip e Conda exigidas pelos modelos e pelo script de entrada.
* Quaisquer ativos adicionais, como texto, dados etc., que são necessários para o (s) modelos e o script de entrada.

Você também fornece a configuração da plataforma de implantação de destino. Por exemplo, o tipo de família de VMs, a memória disponível e o número de núcleos durante a implantação no serviço kubernetes do Azure.

Quando a imagem é criada, também são adicionados os componentes exigidos pelo Azure Machine Learning. Por exemplo, os ativos necessários para executar o serviço Web e interagir com o IoT Edge.

#### <a name="batch-scoring"></a>Pontuação do lote
A pontuação de lote tem suporte por meio de pipelines de ML. Para obter mais informações, consulte [previsões de lote em Big data](./tutorial-pipeline-batch-scoring-classification.md).

#### <a name="real-time-web-services"></a>Serviços Web em tempo real

Você pode usar seus modelos em **Serviços Web** com os seguintes destinos de computação:

* Azure Container Instance
* Serviço de Kubernetes do Azure
* Ambiente de desenvolvimento local

Para implantar o modelo como um serviço Web, você deve fornecer os seguintes itens:

* O modelo ou Ensemble de modelos.
* Dependências necessárias para usar o modelo. Por exemplo, um script que aceita solicitações e invoca o modelo, dependências Conda, etc.
* Configuração de implantação que descreve como e onde implantar o modelo.

Para obter mais informações, consulte [implantar modelos](how-to-deploy-and-where.md).

#### <a name="controlled-rollout"></a>Distribuição controlada

Ao implantar no serviço kubernetes do Azure, você pode usar a distribuição controlada para habilitar os seguintes cenários:

* Criar várias versões de um ponto de extremidade para uma implantação
* Execute testes A/B roteando o tráfego para versões diferentes do ponto de extremidade.
* Alterne entre as versões de ponto de extremidade atualizando a porcentagem de tráfego na configuração do ponto de extremidade.

Para obter mais informações, consulte [distribuição controlada de modelos de ml](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview).

#### <a name="iot-edge-devices"></a>Dispositivos IoT Edge

Você pode usar modelos com dispositivos IoT por meio de **módulos Azure IOT Edge**. IoT Edge módulos são implantados em um dispositivo de hardware, o que permite a inferência, ou a Pontuação do modelo, no dispositivo.

Para obter mais informações, consulte [implantar modelos](how-to-deploy-and-where.md).

### <a name="analytics"></a>Análise

O Microsoft Power BI dá suporte ao uso de modelos de aprendizado de máquina para análise de dados. Para obter mais informações, consulte [integração de Azure Machine Learning no Power bi (versão prévia)](/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Capturar os dados de governança necessários para capturar o ciclo de vida de ML de ponta a ponta

O Azure ML oferece a capacidade de acompanhar a trilha de auditoria de ponta a ponta de todos os seus ativos de ML usando metadados.

- O Azure ML [integra-se com o Git](how-to-set-up-training-targets.md#gitintegration) para rastrear informações sobre a qual repositório/Branch/confirmação seu código veio.
- Os conjuntos de dados [do Azure ml](how-to-create-register-datasets.md) ajudam a acompanhar, criar o perfil e a versão.
- A [interpretação](how-to-machine-learning-interpretability.md) permite explicar seus modelos, atender à conformidade regulatória e entender como os modelos chegam em um resultado para determinada entrada.
- O histórico de execuções do Azure ML armazena um instantâneo do código, dos dados e das computações usados para treinar um modelo.
- O registro de modelo do Azure ML captura todos os metadados associados ao seu modelo (que experimento o treina, onde ele está sendo implantado, se suas implantações estiverem íntegras).
- A [integração com o Azure](how-to-use-event-grid.md) permite que você atue em eventos no ciclo de vida do ml. Por exemplo, eventos de registro, implantação, descompasso de dados e treinamento (execução) do modelo.

> [!TIP]
> Embora algumas informações sobre modelos e conjuntos de dados sejam capturadas automaticamente, você pode adicionar informações adicionais usando __marcas__. Ao procurar por modelos registrados e conjuntos de valores em seu espaço de trabalho, você pode usar marcas como um filtro.
>
> A associação de um conjunto de um com um modelo registrado é uma etapa opcional. Para obter informações sobre como fazer referência a um conjunto de dados ao registrar um modelo, consulte a referência de classe de [modelo](/python/api/azureml-core/azureml.core.model%28class%29) .


## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Notificar, automatizar e alertar sobre eventos no ciclo de vida am
O Azure ML publica eventos-chave no Azure EventGrid, que pode ser usado para notificar e automatizar eventos no ciclo de vida do ML. Para obter mais informações, consulte [este documento](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Monitorar problemas operacionais de & ML

O monitoramento permite que você entenda quais dados estão sendo enviados para seu modelo e as previsões que ele retorna.

Essas informações ajudam a entender como seu modelo está sendo usado. Os dados de entrada coletados também podem ser úteis para treinar versões futuras do modelo.

Para obter mais informações, consulte [Como habilitar a coleta de dados de modelo](how-to-enable-data-collection.md).

## <a name="retrain-your-model-on-new-data"></a>Treinar novamente seu modelo em novos dados

Muitas vezes, você desejará validar seu modelo, atualizá-lo ou até mesmo retreiná-lo do zero, ao receber novas informações. Às vezes, o recebimento de novos dados é uma parte esperada do domínio. Em outras ocasiões, conforme discutido em [detectar descompasso de dados (versão prévia) em DataSets](how-to-monitor-datasets.md), o desempenho do modelo pode ser prejudicado diante de coisas como alterações em um sensor específico, alterações naturais de dados, como efeitos sazonais, ou recursos que se deslocam em sua relação com outros recursos. 

Não há nenhuma resposta universal para "Como fazer saber se devo treinar novamente?" Mas as ferramentas de monitoramento e eventos do Azure ML abordadas anteriormente são bons pontos de partida para a automação. Depois de decidir treinar novamente, você deve: 

- Pré-processar seus dados usando um processo reproduzível e automatizado
- Treinar seu novo modelo
- Compare as saídas do seu novo modelo com as de seu modelo antigo
- Use critérios predefinidos para escolher se deseja substituir o modelo antigo 

Um tema das etapas acima é que o seu novo treinamento deve ser automatizado, não ad hoc. [Azure Machine Learning pipelines](concept-ml-pipelines.md) são uma boa resposta para a criação de fluxos de trabalho relacionados à preparação, ao treinamento, à validação e à implantação de dados. Leia [modelos de readaptação com Azure Machine Learning designer](how-to-retrain-designer.md) para ver como os pipelines e o designer de Azure Machine Learning se encaixam em um cenário de novo treinamento. 

## <a name="automate-the-ml-lifecycle"></a>Automatizar o ciclo de vida do ML 

Você pode usar o GitHub e o Azure Pipelines para criar um processo de integração contínua que treina um modelo. Em um cenário típico, quando um cientista de dados verifica uma alteração no repositório Git para um projeto, o pipeline do Azure iniciará uma execução de treinamento. Os resultados da execução podem ser inspecionados para ver as características de desempenho do modelo treinado. Você também pode criar um pipeline que implanta o modelo como um serviço Web.

A [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) torna mais fácil trabalhar com Azure pipelines. Ele fornece os seguintes aprimoramentos para Azure Pipelines:

* Habilita a seleção de espaço de trabalho ao definir uma conexão de serviço.
* Permite que os pipelines de versão sejam disparados por modelos treinados criados em um pipeline de treinamento.

Para obter mais informações sobre como usar Azure Pipelines com Azure Machine Learning, consulte os links a seguir:

* [Integração e implantação contínuas de modelos de ML com Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Azure Machine Learning repositório MLOps](https://aka.ms/mlops) .
* [Azure Machine Learning repositório MLOpsPython](https://github.com/Microsoft/MLOpspython) .

Você também pode usar Azure Data Factory para criar um pipeline de ingestão de dados que prepara os dados para uso com treinamento. Para obter mais informações, consulte [pipeline de ingestão de dados](how-to-cicd-data-ingestion.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais lendo e explorando os seguintes recursos:

+ [Como & onde implantar modelos](how-to-deploy-and-where.md) com Azure Machine Learning

+ [Tutorial: implantar um modelo de classificação de imagem em ACI](tutorial-deploy-models-with-aml.md).

+ [Repositório de exemplos de MLOps de ponta a ponta](https://github.com/microsoft/MLOps)

+ [CI/CD de modelos de ML com Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)

+ Criar clientes que [consomem um modelo implantado](how-to-consume-web-service.md)

+ [Aprendizado de máquina em escala](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Arquiteturas de referência do AI do Azure & representante de práticas recomendadas](https://github.com/microsoft/AI)