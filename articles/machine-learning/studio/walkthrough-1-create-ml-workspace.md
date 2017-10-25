---
title: "Etapa 1: Criar um Espaço de Trabalho do Machine Learning | Microsoft Docs"
description: "Etapa 1 - desenvolver um passo a passo de solução de previsão: Saiba como configurar um novo espaço de trabalho do Azure Machine Learning Studio."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b3c97e3d-16ba-4e42-9657-2562854a1e04
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: ff857e83a37b95bceb751539bb34e9fb7f202931
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-step-1-create-a-machine-learning-workspace"></a>Etapa 1 do passo a passo: Criar um espaço de trabalho de Machine Learning
Esta é a primeira etapa do passo a passo, [Desenvolver uma solução de análise preditiva com o Azure Machine Learning](walkthrough-develop-predictive-solution.md).

1. **Criar um espaço de trabalho do Machine Learning**
2. [Carregar dados existentes](walkthrough-2-upload-data.md)
3. [Criar um novo teste](walkthrough-3-create-new-experiment.md)
4. [Treinar e avaliar os modelos](walkthrough-4-train-and-evaluate-models.md)
5. [Implantar o serviço Web](walkthrough-5-publish-web-service.md)
6. [Acessar o serviço Web](walkthrough-6-access-web-service.md)

- - -
<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

Para usar o Machine Learning Studio, você precisa ter um espaço de trabalho do Microsoft Azure Machine Learning. Esse espaço de trabalho contém as ferramentas necessárias para criar, gerenciar e publicar testes.  

<!--
## To create a workspace
1. Sign in to the [Azure classic portal](https://manage.windowsazure.com).
2. In the  Azure services panel, click **MACHINE LEARNING**.  
   ![Create workspace][1]
3. Click **CREATE AN ML WORKSPACE**.
4. On the **QUICK CREATE** page, enter your workspace information and then click **CREATE AN ML WORKSPACE**.
-->

O administrador de sua assinatura do Azure precisa criar o espaço de trabalho e, em seguida, adicioná-lo como um proprietário ou colaborador. Para obter mais detalhes, consulte [Criar e compartilhar um espaço de trabalho do Azure Machine Learning](create-workspace.md).

Após a criação do espaço de trabalho, abra o Machine Learning Studio:[https://studio.azureml.net/Home](https://studio.azureml.net/Home). Se você tiver mais de um espaço de trabalho, poderá selecionar o espaço de trabalho na barra de ferramentas no canto superior direito da janela.

![Selecionar o espaço de trabalho no estúdio][2]

> [!TIP]
> Se você tiver sido adicionado como proprietário do espaço de trabalho, será possível compartilhar os testes em que está trabalhando convidando outras pessoas para seu espaço de trabalho. Pode fazer isso no Machine Learning Studio na página **CONFIGURAÇÕES** . Basta ter a conta da Microsoft ou a conta da empresa de cada usuário.
> 
> Na página **CONFIGURAÇÕES**, clique em **USUÁRIOS** e, em seguida, clique em **CONVIDAR MAIS USUÁRIOS** na parte inferior da janela.
> 
> 

- - -
**Em seguida:[ Carregar dados existentes](walkthrough-2-upload-data.md)**

[1]: ./media/walkthrough-1-create-ml-workspace/create1.png
[2]: ./media/walkthrough-1-create-ml-workspace/open-workspace.png
