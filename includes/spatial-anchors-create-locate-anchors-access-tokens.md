---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: c9b4e0fdf95c2bb4888a3a11820f1785d8545bd4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993036"
---
### <a name="access-tokens"></a>Tokens de acesso

Tokens de acesso são um método mais robusto para autenticar com Âncoras Espaciais do Azure. Isso é válido especialmente à medida que você prepara seu aplicativo para uma implantação de produção. O resumo dessa abordagem é configurar um serviço de back-end com o qual seu aplicativo cliente possa se autenticar com segurança. O serviço de back-end interage com o AAD em runtime e com o serviço de token de Âncoras Espaciais do Azure para solicitar um token de acesso. Esse token é então entregue ao aplicativo cliente e usado no SDK para autenticar com as Âncoras Espaciais do Azure.
