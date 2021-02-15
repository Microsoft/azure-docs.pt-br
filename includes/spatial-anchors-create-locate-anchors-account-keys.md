---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 7210b05566f5cd6f3c56792bce0904b3c9b645da
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993086"
---
## <a name="set-up-authentication"></a>Configurar a autenticação

Para acessar o serviço, você precisa fornecer uma chave de conta, um token de acesso ou um token de autenticação do Azure Active Directory. Leia também mais sobre isso na [página sobre o conceito de autenticação](../articles/spatial-anchors/concepts/authentication.md).

### <a name="account-keys"></a>Chaves de conta

Chaves de conta são uma credencial que permite que o aplicativo seja autenticado no serviço Âncoras Espaciais do Azure. A finalidade pretendida das chaves de conta é ajudar você com uma rápida introdução. Especialmente durante a fase de desenvolvimento da integração do aplicativo às Âncoras Espaciais do Azure. Assim, você pode usar chaves de conta inserindo-as em seus aplicativos cliente durante o desenvolvimento. À medida que você progride para além do desenvolvimento, é altamente recomendável migrar para um mecanismo de autenticação que seja de nível de produção, com suporte de tokens de acesso ou da autenticação de usuário do Azure Active Directory. Para obter uma chave de conta para desenvolvimento, visite a sua conta das Âncoras Espaciais do Azure e navegue até a guia "Chaves".