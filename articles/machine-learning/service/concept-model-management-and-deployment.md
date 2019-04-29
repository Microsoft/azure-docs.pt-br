---
title: Gerenciar, registrar, implantar e monitorar modelos de ML
titleSuffix: Azure Machine Learning service
description: Saiba como usar os Serviços do Azure Machine Learning para implantar, gerenciar e monitorar seus modelos para melhorá-los continuamente. Você pode implantar os modelos que treinou com os Serviços do Azure Machine Learning no computador local ou de outras fontes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 1/23/2019
ms.custom: seodec18
ms.openlocfilehash: 2cd2d328d33744854bc525e5ecf1dfa3b6e4bcc8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821125"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Gerenciar, implantar e monitorar modelos com os Serviços do Azure Machine Learning

Neste artigo, você pode aprender a usar os Serviços do Azure Machine Learning para implantar, gerenciar e monitorar seus modelos para melhorá-los continuamente. Você pode implantar os modelos que treinou com o Azure Machine Learning em seu computador local ou de outras fontes. 

O diagrama a seguir ilustra o fluxo de trabalho de implantação completo: [![Fluxo de trabalho de implantação para o Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

O fluxo de trabalho de implantação inclui as seguintes etapas:
1. **Registrar o modelo** em um registro hospedado no espaço de trabalho dos Serviços do Azure Machine Learning
1. **Registrar uma imagem** que emparelhe um modelo com um script de pontuação e suas dependências em um contêiner portátil 
1. **Implantar** a imagem como um serviço Web na nuvem ou em dispositivos de borda
1. **Monitorar e coletar dados**
1. **Atualizar** uma implantação para usar uma nova imagem.

Cada etapa pode ser executada independentemente ou como parte de um comando de implantação única. Além disso, você pode integrar a implantação em um **Fluxo de trabalho de CI/CD** conforme ilustrado neste gráfico.

[![' Ciclo/integração contínua (CI/CD) de implantação do azure Machine Learning'](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

## <a name="step-1-register-model"></a>Etapa 1: Registrar modelo

O registro de modelo permite que você armazene e controle a versão de seus modelos na nuvem do Azure em seu workspace. O registro de modelo torna mais fácil organizar e manter o controle de seus modelos treinados.
 
Modelos registrados são identificados por nome e versão. Cada vez que você registra um modelo com o mesmo nome de um já existente, o Registro incrementa a versão. Você também pode fornecer marcas de metadados adicionais durante o registro, que podem ser usadas ao pesquisar modelos. O Serviço do Azure Machine Learning dá suporte a quaisquer modelos que possam ser armazenados usando o Python 3. 

Você não pode excluir modelos que estão sendo usados por uma imagem.

Para obter mais informações, consulte a seção sobre registro de modelos em [Implantar modelos](how-to-deploy-and-where.md#registermodel).

Para obter um exemplo de registro de um modelo armazenado no formato pickle, consulte [Tutorial: treinar um modelo de classificação de imagem](tutorial-deploy-models-with-aml.md).

Para obter informações sobre o uso de modelos ONNX, consulte o documento [ONNX e Azure Machine Learning](how-to-build-deploy-onnx.md).

## <a name="step-2-register-image"></a>Etapa 2: Registrar a imagem

Imagens permitem a implantação confiável de modelo, juntamente com todos os componentes necessários para usar o modelo. Uma imagem contém os seguintes itens:

* O modelo
* O mecanismo de pontuação
* O arquivo ou aplicativo de pontuação
* Quaisquer dependências necessárias para pontuar o modelo

A imagem também pode incluir componentes do SDK para registro em log e monitoramento. Os dados de logs do SDK podem ser usados para ajustar ou treinar novamente seu modelo, incluindo entrada e saída do modelo.

O Azure Machine Learning dá suporte para as estruturas mais populares, porém, em geral, qualquer estrutura que possa ser instalada com pip poderá funcionar.

Quando seu workspace foi criado, também foram criados vários outros recursos do Azure usados pelo workspace.
Todos os objetos usados para criar a imagem padrão são armazenados na conta de armazenamento do Azure no seu espaço de trabalho. Você pode fornecer as marcas de metadados adicionais ao criar a imagem. As marcas de metadados também são armazenadas pelo registro de imagem e podem ser consultadas para localizar a imagem.

Você também pode usar imagens personalizadas, que podem ser carregadas no registro de contêiner do Azure e usadas pelo serviço de Azure Machine Learning.

Para obter mais informações, consulte a seção sobre configuração e registro de imagens em [Implantar modelos](how-to-deploy-and-where.md#configureimage).

## <a name="step-3-deploy-image"></a>Etapa 3: Implantar a imagem

Você pode implantar imagens registradas na nuvem ou em dispositivos de borda. O processo de implantação cria os recursos necessários para monitorar, balancear carga e dimensionar seu modelo automaticamente. O acesso aos serviços implantados pode ser protegido com a autenticação baseada em certificado, fornecendo os ativos de segurança durante a implantação. Você também pode atualizar uma implantação existente para usar uma imagem mais recente.

Implantações de serviço Web também podem ser pesquisadas. Por exemplo, você pode pesquisar todas as implantações de uma imagem ou de um modelo específico.

[![Destinos de inferência](media/concept-model-management-and-deployment/inferencing-targets.png)](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

Você pode implantar suas imagens nos seguintes destinos de implantação na nuvem:

* Azure Container Instance
* Serviço de Kubernetes do Azure
* Computador FPGA do Azure
* Dispositivos do Azure IoT Edge

Conforme seu serviço é implantado, a solicitação de inferência automaticamente recebe balanceamento de carga e o cluster é dimensionado para atender a quaisquer picos de demanda. A [Telemetria sobre seu serviço pode ser capturada](how-to-enable-app-insights.md) no serviço do Azure Application Insights associado ao espaço de trabalho.

Para obter mais informações, consulte a seção sobre implantação em [Implantar modelos](how-to-deploy-and-where.md#deploy).

## <a name="step-4-monitor-models-and-collect-data"></a>Etapa 4: Monitorar modelos e coletar dados

Um SDK para a captura de dados e registro em log de modelo está disponível para que você possa monitorar a entrada, a saída e outros dados relevantes do seu modelo. Os dados são armazenados como um blob na conta de Armazenamento do Azure para seu workspace.

Para usar o SDK com seu modelo, você pode importar o SDK para seu aplicativo ou script de pontuação. Em seguida, pode usar o SDK para registrar em log dados como parâmetros, resultados ou detalhes de entrada.

Se você decidir habilitar a coleta de dados de modelo sempre que implantar a imagem, os detalhes necessários para capturar os dados, como as credenciais para o seu armazenamento de blobs pessoal, serão provisionados automaticamente.

> [!Important]
> A Microsoft não vê os dados que você coleta do seu modelo. Os dados são enviados diretamente para a conta de Armazenamento do Azure para seu workspace.

Para obter mais informações, consulte [Como habilitar a coleta de dados de modelo](how-to-enable-data-collection.md).

## <a name="step-5-update-the-deployment"></a>Etapa 5: Atualizar a implantação

Atualizações em seu modelo não são registradas automaticamente. Da mesma forma, registrar uma nova imagem não atualiza automaticamente as implantações que foram criadas de uma versão anterior da imagem. Em vez disso, você deve registrar manualmente o modelo, registrar a imagem e, em seguida, atualizar o modelo. Para obter mais informações, consulte a seção sobre atualização em [Implantar modelos](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como e em que local você pode implantar modelos](how-to-deploy-and-where.md) com o serviço do Azure Machine Learning. Para obter um exemplo de implantação, consulte [Tutorial: Implantar um modelo de classificação de imagem em instâncias de contêiner do Azure](tutorial-deploy-models-with-aml.md).

Saiba como criar aplicativos cliente e serviços que [Consomem um modelo implantado como um serviço Web](how-to-consume-web-service.md).
