---
title: 'ML Studio (clássico): migrar para Azure Machine Learning-consumir pontos de extremidade de pipeline'
description: Integre pontos de extremidade de pipeline com aplicativos cliente no Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bf0624e0667c9fc6998fb28898a3376ca409180d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564821"
---
# <a name="consume-pipeline-endpoints-from-client-applications"></a>Consumir pontos de extremidade de pipeline de aplicativos cliente

Neste artigo, você aprenderá a integrar aplicativos cliente com pontos de extremidade de Azure Machine Learning. Para obter mais informações sobre como escrever o código do aplicativo, consulte [consumir um ponto de extremidade Azure Machine Learning](../how-to-consume-web-service.md).

Este artigo faz parte do estúdio (clássico) para Azure Machine Learning série de migração. Para obter mais informações sobre como migrar para o Azure Machine Learning, consulte [o artigo Visão geral da migração](migrate-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Um Workspace do Azure Machine Learning. [Criar um workspace do Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Um ponto de extremidade [Azure Machine Learning em tempo real ou de pipeline](migrate-rebuild-web-service.md).


## <a name="consume-a-real-time-endpoint"></a>Consumir um ponto de extremidade em tempo real 

Se você implantou seu modelo como um **ponto de extremidade em tempo real**, você pode encontrar seu ponto de extremidade REST e código de consumo gerado previamente em C#, Python e R:

1. Vá para Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. Vá para a guia **pontos de extremidade** .
1. Selecione seu ponto de extremidade em tempo real.
1. Selecione **consumir**.

> [!NOTE]
> Você também pode encontrar a especificação do Swagger para seu ponto de extremidade na guia **detalhes** . Use a definição do Swagger para entender o esquema do ponto de extremidade. Para obter mais informações sobre a definição do Swagger, consulte a [documentação oficial do Swagger](https://swagger.io/docs/specification/2-0/what-is-swagger/).


## <a name="consume-a-pipeline-endpoint"></a>Consumo de um ponto de extremidade de pipeline

Há duas maneiras de consumir um ponto de extremidade de pipeline:

- Chamadas à API REST
- Integração com o Azure Data Factory

### <a name="use-rest-api-calls"></a>Usar chamadas à API REST

Chame o ponto de extremidade REST do seu aplicativo cliente. Você pode usar a especificação do Swagger para seu ponto de extremidade para entender seu esquema:

1. Vá para Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. Vá para a guia **pontos de extremidade** .
1. Selecione **Pontos de extremidade do pipeline**.
1. Selecione o ponto de extremidade do pipeline.
1. No painel **visão geral do ponto de extremidade do pipeline** , selecione o link em **documentação do ponto de extremidade REST**.

### <a name="use-azure-data-factory"></a>Usar o Azure Data Factory

Você pode chamar seu pipeline de Azure Machine Learning como uma etapa em um pipeline de Azure Data Factory. Para obter mais informações, consulte [Execute Azure Machine Learning pipelines no Azure data Factory](../../data-factory/transform-data-machine-learning-service.md).


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a encontrar o esquema e o código de exemplo para seus pontos de extremidade de pipeline. Para obter mais informações sobre como consumir pontos de extremidade do aplicativo cliente, consulte [consumir um ponto de extremidades Azure Machine Learning](../how-to-consume-web-service.md).

Consulte o restante dos artigos na série de migração de Azure Machine Learning: 
1. [Visão geral da migração](migrate-overview.md).
1. [Migrar conjunto](migrate-register-dataset.md)de um.
1. [Recompile um pipeline de treinamento do Studio (clássico)](migrate-rebuild-experiment.md).
1. [Recompile um serviço Web Studio (clássico)](migrate-rebuild-web-service.md).
1. **Integre um serviço web Azure Machine Learning com aplicativos cliente**.
1. [Migrar executar script R](migrate-execute-r-script.md).