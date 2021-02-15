---
title: arquivo de inclusão
description: arquivo de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: aa8aea66c5481454f0d7d4d118934f5fbf34a911
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89147339"
---
A **identidade gerenciada padrão** é a identidade gerenciada atribuída pelo sistema ou a primeira identidade gerenciada atribuída pelo usuário.

Durante uma execução, há dois aplicativos de uma identidade:

1. O sistema usa uma identidade para configurar as montagens de armazenamento do usuário, o registro de contêiner e os armazenamentos de Datastore.

    * Nesse caso, o sistema usará a identidade gerenciada por padrão.

1. O usuário aplica uma identidade para acessar recursos de dentro do código para uma execução enviada

    * Nesse caso, forneça o *client_id* correspondente à identidade gerenciada que você deseja usar para recuperar uma credencial.
    * Como alternativa, obtenha a ID do cliente da identidade atribuída pelo usuário por meio da variável de ambiente *DEFAULT_IDENTITY_CLIENT_ID* .

    Por exemplo, para recuperar um token para um repositório de armazenamento com a identidade gerenciada por padrão:

    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')
    ```