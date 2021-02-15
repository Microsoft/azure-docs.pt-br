---
title: Exibir detalhes de autenticação do Azure Maps
description: Use o portal do Azure para exibir detalhes de autenticação do Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a6ffcbf5a8c36958dd3ea74de4d826fe25a1139c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87126441"
---
Você pode exibir os detalhes de autenticação da conta do Azure Maps na portal do Azure. Lá, em sua conta, no menu **configurações** , selecione **autenticação**.

![Detalhes de autenticação](../media/how-to-manage-authentication/how-to-view-auth.png)

Depois que uma conta do Azure Maps é criada, o valor do Azure Maps `x-ms-client-id` está presente na página de detalhes de portal do Azure autenticação. Esse valor representa a conta que será usada para solicitações da API REST. Esse valor deve ser armazenado na configuração do aplicativo e recuperado antes de fazer solicitações HTTP ao usar a autenticação do Azure AD com o Azure Maps.
