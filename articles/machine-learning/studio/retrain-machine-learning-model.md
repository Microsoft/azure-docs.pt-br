---
title: Readaptar um modelo do Machine Learning | Microsoft Docs
description: "Aprenda como readaptar um modelo e atualizar o serviço Web para usar o modelo recentemente adaptado no Machine Learning do Azure."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 0270a43f79202d102a8cdfe9cba7011a882e8117
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-a-machine-learning-model"></a>Readaptar um modelo do Machine Learning
Como parte do processo de operacionalização de modelos de aprendizado de máquina no Azure Machine Learning, um modelo deve ser treinado e salvo. Em seguida, use-o para criar um serviço Web predicativo. O serviço Web pode ser consumido nos sites, painéis e aplicativos móveis. 

Os modelos que você cria usando o Machine Learning geralmente não são estáticos. Conforme novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados, o modelo precisa ser readaptado. 

A readaptação pode ocorrer com frequência. Com o recurso da API de Readaptação Programática, você pode recuperar programaticamente o modelo usando as APIs de Readaptação e atualizar o Serviço Web com o modelo treinado recentemente. 

Este documento descreve o processo de readaptação e mostra como usar as APIs de Readaptação.

## <a name="why-retrain-defining-the-problem"></a>Por que readaptar: definindo o problema
Como parte do processo de treinamento do Machine Learning, um modelo é treinado usando um conjunto de dados. Os modelos que você cria usando o Machine Learning geralmente não são estáticos. Conforme novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados, o modelo precisa ser readaptado.

Nesses cenários, uma API de programação fornece uma maneira conveniente de permitir que você ou o consumidor de suas APIs criem um cliente que pode, em uma base única ou regular, readaptar o modelo usando seus próprios dados. Em seguida, podem avaliar os resultados da readaptação e atualizar a API do serviço Web para usar o modelo readaptado recentemente.

> [!NOTE]
> Se você tiver um Teste de Treinamento e um Novo serviço Web, talvez seja conveniente conferir Readaptar um serviço Web Preditivo existente em vez de seguir o passo a passo mencionado na seção a seguir.
> 
> 

## <a name="end-to-end-workflow"></a>Fluxos de trabalho completos
O processo envolve os seguintes componentes: um Teste de Treinamento e um Teste Preditivo publicados como um serviço Web. Para habilitar a readaptação de um modelo treinado, o Teste de Treinamento também deve ser publicado como um serviço Web com a saída de um modelo treinado. Isso permite o acesso de API para o modelo de readaptação. 

As etapas a seguir se aplicam aos serviços Web Novo e Clássico:

Crie o serviço Web Preditivo inicial:

* Criar um teste de treinamento
* Criar um teste da Web preditivo
* Implantar um serviço Web preditivo

Readapte o serviço Web:

* Atualizar teste de treinamento para permitir a readaptação
* Implantar o serviço Web de readaptação
* Usar o código Serviço de Execução em Lotes para readaptar o modelo

Para ver uma passo a passo das etapas anteriores, confira [Readaptar os modelos do Machine Learning de forma programática](retrain-models-programmatically.md).

> [!NOTE] 
> Para implantar um novo serviço Web, você precisa ter permissões suficientes na assinatura na qual o serviço Web está sendo implantado. Para obter mais informações, consulte [Gerenciar um serviço Web usando o portal de Serviços Web do Azure Machine Learning](manage-new-webservice.md). 

Se você implantou um Serviço Web Clássico:

* Criar um novo ponto de extremidade no serviço Web Preditivo
* Obter a URL de PATCH e o código
* Usar a URL de PATCH para apontar o novo ponto de extremidade no modele readaptado 

Para ver um passo a passo das etapas anteriores, confira [Readaptar um serviço Web Clássico](retrain-a-classic-web-service.md).

Se estiver enfrentando dificuldades ao readaptar um serviço Web Clássico, confira [Solução de problemas de readaptação de um serviço Web Clássico do Machine Learning do Azure](troubleshooting-retraining-models.md).

Se você tiver implantado um Novo serviço Web:

* Entrar em sua conta do Azure Resource Manager
* Obter a definição do serviço Web
* Exportar a Definição do Serviço Web como JSON
* Atualizar a referência para o blob `ilearner` no JSON
* Importar o JSON para uma Definição do Serviço Web
* Atualizar o serviço Web com a nova Definição do Serviço Web

Para ver um passo a passo das etapas anteriores, confira [Readaptar um Novo serviço Web usando os cmdlets do PowerShell de Gerenciamento do Machine Learning](retrain-new-web-service-using-powershell.md).

O processo para configurar a readaptação de um serviço Web Clássico envolve as seguintes etapas:

![Visão geral do processo readaptação][1]

O processo para configurar a readaptação de um Novo serviço Web envolve as seguintes etapas:

![Visão geral do processo readaptação][7]

## <a name="other-resources"></a>Outros recursos
* [Retraining and Updating Azure Machine Learning models with Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/) (Readaptando e atualizando modelos do Machine Learning com o Azure Data Factory)
* [Criar vários modelos do Machine Learning e pontos de extremidade de serviço Web com base em um teste usando o PowerShell](create-models-and-endpoints-with-powershell.md)
* O vídeo [AML Retraining Models Using APIs](https://www.youtube.com/watch?v=wwjglA8xllg) (Modelos de readaptação do AML usando APIs) mostra como readaptar modelos do Machine Learning criados no Machine Learning do Azure usando as APIs de readaptação e o PowerShell.

<!--image links-->
[1]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

