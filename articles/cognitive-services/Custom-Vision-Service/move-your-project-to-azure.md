---
title: Mover um projeto de avaliação limitado para o Azure
titleSuffix: Azure Cognitive Services
description: Saiba como mover um projeto de avaliação limitado para o Azure.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 22c3767dfac1e377890f1e01517d18263e694854
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560928"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Como mover seu projeto de avaliação limitada para o Azure

Como Serviço de Visão Personalizada conclui sua mudança para o Azure, o suporte para projetos de avaliação limitada fora do Azure está terminando. Este documento mostrará como usar as APIs de Visão Personalizada para copiar seu projeto de avaliação limitada para um recurso do Azure.

O suporte para a exibição de projetos de avaliação limitada no [site visão personalizada](https://customvision.ai) foi encerrado em 25 de março de 2019. Este documento agora mostra como usar as APIs de Visão Personalizada com um [script Python de migração](https://github.com/Azure-Samples/custom-vision-move-project) no GitHub) para duplicar seu projeto para um recurso do Azure.

Para obter mais detalhes, incluindo prazos-chave no processo de substituição de avaliação limitada, consulte as [notas de versão](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) ou para comunicações por email enviadas aos proprietários de projetos de avaliação limitados.

O [script de migração](https://github.com/Azure-Samples/custom-vision-move-project) permite recriar um projeto baixando e carregando todas as marcas, regiões e imagens em sua iteração atual. Ele deixará você com um novo projeto em sua nova assinatura, que você pode treinar.

## <a name="prerequisites"></a>Pré-requisitos

- Você precisará de uma assinatura válida do Azure associada à conta do conta Microsoft ou Azure Active Directory (AAD) que deseja usar para fazer logon no [site do visão personalizada](https://customvision.ai). 
    - Se você não tiver uma conta do Azure, [crie uma conta](https://azure.microsoft.com/free/) gratuitamente.
    - Para obter uma introdução aos conceitos de assinaturas e recursos do Azure, consulte o guia do [desenvolvedor do Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Criar recursos de Visão Personalizada no portal do Azure

Para usar Serviço de Visão Personalizada com o Azure, será necessário criar Visão Personalizada recursos de treinamento e previsão no [portal do Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Vários projetos podem ser associados a um único recurso. Mais detalhes sobre [preços e limites](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) estão disponíveis. Para continuar a usar Serviço de Visão Personalizada gratuitamente, você pode selecionar a camada F0 na portal do Azure. 

> [!NOTE]
> Quando você move o projeto Visão Personalizada para um recurso do Azure, ele herda as [permissões]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) subjacentes desse recurso do Azure. Se outros usuários em sua organização forem proprietários do recurso do Azure no qual seu projeto está, eles poderão acessar seu projeto no [site do visão personalizada](https://customvision.ai). Da mesma forma, a exclusão de seus recursos excluirá seus projetos.  

## <a name="find-your-limited-trial-project-information"></a>Encontre suas informações limitadas do projeto de avaliação

Para mover seu projeto, você precisará da _ID do projeto_ e da _chave de treinamento_ para o projeto que você está tentando migrar. Se você não tiver essas informações, visite [https://limitedtrial.customvision.ai/projects](https://limitedtrial.customvision.ai/projects) para obter a ID e a chave de cada um dos seus projetos. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Use o código de exemplo do Python para copiar seu projeto para o Azure

Siga as [instruções de código de exemplo](https://github.com/Azure-Samples/custom-vision-move-project), usando sua chave de avaliação limitada e a ID do projeto como os materiais de "origem" e a chave do novo recurso do Azure criado como "destino".

Por padrão, todos os projetos de avaliação limitados são hospedados no Sul EUA Central região do Azure.

## <a name="next-steps"></a>Próximas etapas

Agora, seu projeto foi movido para um recurso do Azure. Você precisará atualizar suas chaves de treinamento e de previsão em qualquer aplicativo que tenha escrito.

Para exibir seu projeto no [site visão personalizada](https://customvision.ai), entre com a mesma conta usada para entrar no portal do Azure. Se você não vir seu projeto, confirme se você está no mesmo diretório no [site visão personalizada](https://customvision.ai) como o diretório em que os recursos estão localizados na portal do Azure. No portal do Azure e no CustomVision.ai, você pode selecionar o diretório no menu suspenso do usuário no canto superior direito da tela.