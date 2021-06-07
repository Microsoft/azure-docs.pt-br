---
author: peterclu
ms.service: machine-learning
ms.topic: include
ms.date: 03/08/2021
ms.author: peterlu
ms.openlocfilehash: ff64a0948402ff152e45bd4702d986f51b9547aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563175"
---
A tabela a seguir resume as principais diferenças entre o ML Studio (clássico) e o Azure Machine Learning.

| Recurso | ML Studio (clássico) | Azure Machine Learning |
|---| --- | --- |
| Interface de "arrastar e soltar" | Experiência clássica | Experiência atualizada – [Designer do Azure Machine Learning](../articles/machine-learning/concept-designer.md)| 
| SDKs de código | Sem suporte | Totalmente integrado com as SDKs para [R](https://github.com/Azure/azureml-sdk-for-r) e para [Python do Azure Machine Learning](/python/api/overview/azure/ml/) |
| Experimento | Escalável (limite de 10 GB para dados de treinamento) | Escala com destino de computação |
| Destinos de computação de treinamento | Destino de computação proprietário, apenas suporte à CPU | Ampla gama de [destinos de computação de treinamento](../articles/machine-learning/concept-compute-target.md#train) personalizáveis. Inclui suporte à GPU e à CPU | 
| Destinos de computação de implantação | Formato do serviço Web proprietário, não personalizável | Ampla gama de [destinos de computação de implantação](../articles/machine-learning/concept-compute-target.md#deploy) personalizáveis. Inclui suporte à GPU e à CPU |
| Pipeline de ML | Sem suporte | Crie [pipelines](../articles/machine-learning/concept-ml-pipelines.md) flexíveis e modulares para automatizar fluxos de trabalho |
| MLOps | Gerenciamento e implantação de modelos básicos; implantações somente da CPU | Controle de versão de entidade (modelo, dados, fluxos de trabalho), automação de fluxo de trabalho, integração às ferramentas de CI/CD, implantações de CPU e GPU, [entre outros](../articles/machine-learning/concept-model-management-and-deployment.md) |
| Formato do modelo | Formato proprietário, somente Studio (clássico) | Vários formatos compatíveis dependendo do tipo de trabalho de treinamento |
| Treinamento de modelo automatizado e ajuste de hiperparâmetro |  Sem suporte | [Com suporte](../articles/machine-learning/concept-automated-ml.md). Opções code first e sem código. | 
| Detecção de descompasso de dados | Sem suporte | [Com suporte](../articles/machine-learning/how-to-monitor-datasets.md) |
| Projetos de rotulagem de dados | Sem suporte | [Com suporte](../articles/machine-learning/how-to-create-labeling-projects.md) |
| RBAC (Controle de Acesso Baseado em Função) | Apenas função de proprietário e de colaborador | [Definição de função flexível e controle RBAC](../articles/machine-learning/how-to-assign-roles.md) |
| Galeria de IA | Com suporte ([https://gallery.azure.ai/](https://gallery.azure.ai/)) | Sem suporte <br><br> Saiba mais com os [notebooks de exemplo de SDK do Python](https://github.com/Azure/MachineLearningNotebooks). |