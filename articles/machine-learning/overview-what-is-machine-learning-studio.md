---
title: O que é o Azure Machine Learning Studio?
description: O estúdio é um portal da Web para workspaces do Azure Machine Learning. O estúdio combina experiências sem código e code first para obter uma plataforma de ciência de dados inclusiva.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
adobe-target: true
ms.openlocfilehash: 320840277483776dfc74b98ab70410503008df67
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374228"
---
# <a name="what-is-azure-machine-learning-studio"></a>O que é o Azure Machine Learning Studio?

Neste artigo, você saberá mais sobre o estúdio do Azure Machine Learning, o portal da Web para desenvolvedores cientistas de dados no [Azure Machine Learning](overview-what-is-azure-ml.md). O estúdio combina experiências sem código e code first para obter uma plataforma de ciência de dados inclusiva.

Neste artigo, você aprende:
>[!div class="checklist"]
> - Como [criar projetos de machine learning](#author-machine-learning-projects) no estúdio.
> - Como [gerenciar ativos e recursos](#manage-assets-and-resources) no estúdio.
> - A diferença entre o [Estúdio do Azure Machine Learning e o ML Studio (clássico)](#ml-studio-classic-vs-azure-machine-learning-studio).

É recomendável que você use o navegador mais atualizado compatível com o sistema operacional. Há suporte para os seguintes navegadores:
  * Microsoft Edge (a versão mais recente, não o Microsoft Edge herdado)
  * Safari (última versão, apenas Mac)
  * Chrome (última versão)
  * Firefox (última versão)

## <a name="author-machine-learning-projects"></a>Criar projetos de machine learning

O estúdio oferece várias experiências de criação, dependendo do tipo de projeto e do nível de experiência do usuário.

+ **Notebooks**

  Escreva e execute seu código em [servidores gerenciados do Jupyter Notebook](how-to-run-jupyter-notebooks.md) que estão diretamente integrados no estúdio. 

:::image type="content" source="media/overview-what-is-azure-ml-studio/notebooks.gif" alt-text="Captura de tela: gravar e executar o código em um notebook":::

+ **Designer do Azure Machine Learning**

  Use o designer para treinar e implantar modelos de machine learning sem nenhuma codificação. Arraste e solte conjuntos de dados e módulos para criar pipelines de ML. Experimente o [tutorial do designer](tutorial-designer-automobile-price-train-score.md).

    ![Exemplo do Azure Machine Learning Designer](media/concept-designer/designer-drag-and-drop.gif)

+ **Interface do usuário de machine learning automatizado**

  Saiba como criar [experimentos de ML automatizado](tutorial-first-experiment-automated-ml.md) com uma interface fácil de usar. 

  [![Painel de navegação do Azure Machine Learning Studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **Rotulagem de dados**

    Use a [rotulagem de dados do Azure Machine Learning](how-to-create-labeling-projects.md) para coordenar com eficiência os projetos de rotulagem de dados.

## <a name="manage-assets-and-resources"></a>Gerenciar ativos e recursos

Gerencie os seus ativos do machine learning diretamente no seu navegador. Os ativos são compartilhados no mesmo workspace entre o SDK e o estúdio para uma experiência simplificada. Use o estúdio para gerenciar:

- Modelos
- Conjunto de dados
- Armazenamentos de dados
- Recursos de computação
- Notebooks
- Testes
- Logs de execução
- Pipelines 
- Pontos de extremidade do pipeline

Mesmo que você seja um desenvolvedor experiente, o estúdio pode simplificar a maneira como você gerencia os recursos do workspace.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio (clássico) versus estúdio do Azure Machine Learning

Lançado em 2015, o **ML Studio (clássico)** foi nosso primeiro construtor de machine learning do tipo "arrastar e soltar". É um serviço autônomo que oferece apenas uma experiência visual. O Studio (clássico) não interopera com o Azure Machine Learning.

O **Azure Machine Learning** é um serviço separado e modernizado que fornece uma plataforma de ciência de dados completa. Ele dá suporte a experiências com código baixo e code first.

**O estúdio do Azure Machine Learning** é um portal da Web *no* Azure Machine Learning que contém opções de código baixo e sem código para criação de projetos e gerenciamento de ativos. 

Recomendamos que novos usuários escolham o **Azure Machine Learning**, em vez do ML Studio (clássico), para o intervalo mais recente de ferramentas de ciência de dados.

### <a name="feature-comparison"></a>Comparação de recursos

A tabela a seguir resume as principais diferenças entre o ML Studio (clássico) e o Azure Machine Learning.

| Recurso | ML Studio (clássico) | Azure Machine Learning |
|---| --- | --- |
| Interface de "arrastar e soltar" | Experiência clássica | Experiência atualizada – [Designer do Azure Machine Learning](concept-designer.md)| 
| SDKs de código | Sem suporte | Totalmente integrado com as SDKs para [R](https://github.com/Azure/azureml-sdk-for-r) e para [Python do Azure Machine Learning](/python/api/overview/azure/ml/) |
| Experimento | Escalável (limite de 10 GB para dados de treinamento) | Escala com destino de computação |
| Destinos de computação de treinamento | Destino de computação proprietário, apenas suporte à CPU | Ampla gama de [destinos de computação de treinamento](concept-compute-target.md#train) personalizáveis. Inclui suporte à GPU e à CPU | 
| Destinos de computação de implantação | Formato do serviço Web proprietário, não personalizável | Ampla gama de [destinos de computação de implantação](concept-compute-target.md#deploy) personalizáveis. Inclui suporte à GPU e à CPU |
| Pipeline de ML | Sem suporte | Crie [pipelines](concept-ml-pipelines.md) flexíveis e modulares para automatizar fluxos de trabalho |
| MLOps | Gerenciamento e implantação de modelos básicos; implantações somente da CPU | Controle de versão de entidade (modelo, dados, fluxos de trabalho), automação de fluxo de trabalho, integração às ferramentas de CI/CD, implantações de CPU e GPU, [entre outros](concept-model-management-and-deployment.md) |
| Formato do modelo | Formato proprietário, somente Studio (clássico) | Vários formatos compatíveis dependendo do tipo de trabalho de treinamento |
| Treinamento de modelo automatizado e ajuste de hiperparâmetro |  Sem suporte | [Com suporte](concept-automated-ml.md). Opções code first e sem código. | 
| Detecção de descompasso de dados | Sem suporte | [Com suporte](how-to-monitor-datasets.md) |
| Projetos de rotulagem de dados | Sem suporte | [Com suporte](how-to-create-labeling-projects.md) |

## <a name="troubleshooting"></a>Solução de problemas

* **Itens da interface do usuário ausentes no estúdio**: o controle de acesso baseado em função do Azure pode ser usado para restringir ações que podem ser executadas com o Azure Machine Learning. Essas restrições podem impedir que itens da interface do usuário apareçam no Estúdio do Azure Machine Learning. Por exemplo, caso seja atribuída a você uma função que não pode criar uma instância de computação, a opção para criá-la não será exibida no estúdio. Para obter mais informações, confira [Gerenciar usuários e funções](how-to-assign-roles.md).

## <a name="next-steps"></a>Próximas etapas

Visite o [estúdio](https://ml.azure.com) ou explore as diferentes opções de criação com estes tutoriais:  

- + [Comece a usar seu próprio ambiente de desenvolvimento](tutorial-1st-experiment-sdk-setup-local.md)
  + [Use blocos de anotações do Jupyter em uma instância de computação para treinar e implantar modelos](tutorial-1st-experiment-sdk-setup.md)
  + [Usar o machine learning automatizado para treinar e implantar modelos](tutorial-first-experiment-automated-ml.md)  
  + [Usar o designer para treinar e implantar modelos](tutorial-designer-automobile-price-train-score.md)
  + [Usar o estúdio em uma rede virtual segura](how-to-enable-studio-virtual-network.md)