---
title: As funções e os requisitos para a visualização de compartilhamento de dados do Azure
description: As funções e os requisitos para a visualização de compartilhamento de dados do Azure
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: a5d70b9aa611b4f939cb46b5d25655edd818cb35
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807531"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>As funções e os requisitos para a visualização de compartilhamento de dados do Azure

Este artigo descreve as funções necessárias para compartilhar dados usando dados compartilhar versão prévia do Azure, bem como para aceitar e receber dados usando o Azure Preview de compartilhar dados. 

## <a name="roles-and-requirements"></a>Requisitos e funções

Para compartilhar ou receber dados usando o compartilhamento de dados do Azure, a conta de usuário que você usa para entrar no Azure deve ser capaz de conceder permissões de compartilhamento de dados para a conta de armazenamento que você está compartilhando dados de ou receber dados em. Normalmente isso é uma permissão que existe na **proprietário** função ou uma função personalizada com a permissão de gravação/atribuições Microsoft.Authorization/role atribuída. 

Para compartilhar ou receber dados de ou para uma conta de armazenamento do Azure, você deve ser um proprietário da conta de armazenamento. Mesmo que você criou a conta de armazenamento, isso não concede automaticamente é propriedade da conta de armazenamento. Para adicionar por conta própria à função de proprietário da sua conta de armazenamento do Azure, siga estas etapas.

1. Navegue até a conta de armazenamento no portal do Azure
1. Selecione **controle de acesso (IAM)**
1. Clique em **Adicionar**
1. Adicione a mesmo no como proprietário

Para exibir as permissões que você tem na assinatura, portal do Azure, selecione seu nome de usuário no canto superior direito e selecione **Permissões**. Se tiver acesso a várias assinaturas, selecione a que for adequada. 

## <a name="resource-provider-registration"></a>Registro do provedor de recursos 

Ao aceitar um convite de compartilhamento de dados do Azure, você precisará registrar manualmente o provedor de recursos Microsoft.DataShare em sua assinatura. Siga estas etapas para registrar o provedor de recursos Microsoft.DataShare em sua assinatura do Azure. 

1. No portal do Azure, navegue até **assinaturas**
1. Selecione a assinatura que você está usando para o compartilhamento de dados do Azure
1. Clique em **provedores de recursos**
1. Procure Microsoft.DataShare
1. Clique em **Registrar**

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as funções no Azure – [Entender as definições de função](../role-based-access-control/role-definitions.md)

