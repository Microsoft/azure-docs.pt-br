---
title: Controle de acesso baseado em função do Azure-Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como configurar o controle de acesso baseado em função do Azure para seus projetos de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: d265b6698a87c1c651dff1413cf48dd4984cd606
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94616885"
---
# <a name="azure-role-based-access-control"></a>Controle de acesso baseado em função do Azure

A Visão Personalizada dá suporte ao RBAC (controle de acesso baseado em função) do Azure, um sistema de autorização para gerenciar o acesso individual aos recursos do Azure. Usando o RBAC do Azure, você atribui diferentes níveis diferentes de permissões de membros da equipe para seus projetos de Visão Personalizada. Para obter mais informações sobre o RBAC do Azure, consulte a [documentação do RBAC do Azure](../../role-based-access-control/index.yml).

## <a name="add-role-assignment-to-custom-vision-resource"></a>Adicionar atribuição de função a Visão Personalizada recurso

O RBAC do Azure pode ser atribuído a um recurso de Visão Personalizada. Para conceder acesso a um recurso do Azure, você adiciona uma atribuição de função.
1. No [portal do Azure](https://ms.portal.azure.com/), selecione **Todos os serviços**. 
1. Em seguida, selecione os **Serviços cognitivas** e navegue até o recurso específico de treinamento visão personalizada.
   > [!NOTE]
   > Você também pode configurar o RBAC do Azure para grupos de recursos, assinaturas ou grupos de gerenciamento inteiros. Faça isso selecionando o nível de escopo desejado e, em seguida, navegando até o item desejado (por exemplo, selecionando **grupos de recursos** e, em seguida, clicando até o grupo de recursos desejado).
1. Selecione **controle de acesso (iam)** no painel de navegação à esquerda.
1. Selecione a guia **atribuições de função** para exibir as atribuições de função para esse escopo.
1. Selecione **Adicionar** -> **Adicionar atribuição de função**.
1. Na lista suspensa **função** , selecione uma função que você deseja adicionar.
1. Na lista **Selecionar**, selecione um usuário, grupo, entidade de serviço ou identidade gerenciada. Se você não vir a entidade de segurança na lista, poderá digitar a caixa Selecionar para pesquisar o diretório em busca de nomes de exibição, endereços de email e identificadores de objeto.
1. Selecione **Salvar** para atribuir a função.

Em alguns minutos, o destino será atribuído à função selecionada no escopo selecionado.

## <a name="custom-vision-role-types"></a>Tipos de função Visão Personalizada

Use a tabela a seguir para determinar as necessidades de acesso para seus recursos de Visão Personalizada.

|Função  |Permissões  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | Acesso completo aos projetos, incluindo a capacidade de criar, editar ou excluir um projeto.        |
|`Cognitive Services Custom Vision Trainer`     | Acesso completo, exceto a capacidade de criar ou excluir um projeto. Os treinadores podem exibir e editar projetos e treinar, publicar, cancelar a publicação ou exportar os modelos.        |
|`Cognitive Services Custom Vision Labeler`     | Capacidade de carregar, editar ou excluir imagens de treinamento e criar, adicionar, remover ou excluir marcas. Rotuladores podem exibir projetos, mas não podem atualizar nada além de imagens e marcas de treinamento.         |
|`Cognitive Services Custom Vision Deployment`     | Capacidade de publicar, cancelar a publicação ou exportar os modelos. Os implantadores podem exibir projetos, mas não podem atualizar um projeto, imagens de treinamento ou marcas.        |
|`Cognitive Services Custom Vision Reader`     | Capacidade de exibir projetos. Os leitores não podem fazer nenhuma alteração.        |