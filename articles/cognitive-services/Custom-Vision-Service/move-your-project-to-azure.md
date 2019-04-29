---
title: Mover um projeto de avaliação limitado para o Azure
titlesuffix: Azure Cognitive Services
description: Saiba como mover um projeto de avaliação limitada para o Azure.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816524"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Como mover seu projeto de avaliação limitada para o Azure

Como o serviço de visão personalizada é concluída a migração para o Azure, suporte para projetos de avaliação limitada fora do Azure está terminando. Este documento mostrará como usar as APIs de visão personalizada para copiar seu projeto de avaliação limitada para um recurso do Azure.

Suporte à exibição de projetos de avaliação limitado sobre o [site de visão personalizada](https://customvision.ai) terminou em 25 de março de 2019. Este documento agora mostra como usar as APIs de visão personalizada com uma [script de migração de python](https://github.com/Azure-Samples/custom-vision-move-project) no GitHub) duplicar seu projeto para um recurso do Azure.

Para obter mais detalhes, incluindo prazos importantes no processo de substituição de avaliação limitado, consultem o [notas de versão](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) ou para comunicações por email enviadas para proprietários de projetos de avaliação limitados.

O [script de migração](https://github.com/Azure-Samples/custom-vision-move-project) permite que você recrie um projeto, baixando e carregando, em seguida, todas as marcas, regiões e imagens em sua iteração atual. Ele irá deixá-lo com um novo projeto na sua nova assinatura que você pode, em seguida, treinar.

## <a name="prerequisites"></a>Pré-requisitos

- Você precisará de uma assinatura do Azure válida associada com a conta da Microsoft ou conta do Azure Active Directory (AAD) que você deseja usar para fazer logon na [site de visão personalizada](https://customvision.ai). 
    - Se você não tiver uma conta do Azure, [criar uma conta](https://azure.microsoft.com/free/) gratuitamente.
    - Para obter uma introdução aos conceitos de assinaturas e recursos do Azure, consulte a [guia do desenvolvedor do Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions).
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Criar recursos de visão personalizada no portal do Azure

Para usar o serviço de visão personalizada com o Azure, você precisará criar recursos de treinamento de visão personalizada e previsão na [portal do Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Vários projetos podem ser associados a um recurso único. Mais detalhes sobre [preços e limites](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) está disponível. Para continuar a usar o serviço personalizado de visão gratuitamente, você pode selecionar a classe F0 no portal do Azure. 

> [!NOTE]
> Quando você move o seu projeto de visão personalizada para um recurso do Azure, ele herda subjacente [permissões]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) do recurso do Azure. Se outros usuários em sua organização são proprietários do seu projeto estiver no recurso do Azure, eles poderão acessar seu projeto na [site de visão personalizada](https://customvision.ai). Da mesma forma, a exclusão de seus recursos excluirá seus projetos.  

## <a name="find-your-limited-trial-project-information"></a>Localizar as informações do projeto de avaliação limitado

Para mover o seu projeto, será necessário o _ID do projeto_ e _chave de treinamento_ para o projeto que você está tentando migrar. Se você não tiver essas informações, visite [ https://limitedtrial.customvision.ai/projects ](https://limitedtrial.customvision.ai/projects) para obter a ID e a chave para cada um dos seus projetos. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Use o código de exemplo do Python para copiar seu projeto para o Azure

Siga as [instruções de código de exemplo](https://github.com/Azure-Samples/custom-vision-move-project), usando sua chave de avaliação limitado e a ID do projeto como os materiais de "origem" e a chave do novo recurso do Azure que você criou como "destino".

Por padrão, todos os projetos de avaliação limitado são hospedados na região Sul Central dos EUA do Azure.

## <a name="next-steps"></a>Próximas etapas

Seu projeto agora foi movido para um recurso do Azure. Você precisará atualizar suas chaves de treinamento e previsão em quaisquer aplicativos que você tenha escrito.

Para exibir seu projeto sobre o [site de visão personalizada](https://customvision.ai), entrar com a mesma conta usada para entrar no portal do Azure. Se você não vir seu projeto, confirme que você está no mesmo diretório na [site de visão personalizada](https://customvision.ai) como o diretório onde os seus recursos estão localizados no portal do Azure. No portal do Azure e para CustomVision.ai, você pode selecionar seu diretório do usuário no menu suspenso menu no canto superior direito da tela.