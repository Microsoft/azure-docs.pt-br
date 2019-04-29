---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8a6ceee0f7db4b7b368dce1a28c8edc0b2b11180
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754228"
---
Para dados em repouso:

- Criptografia de BitLocker XTS-AES de 256 bits é usada para proteger dados locais.
- Acesso aos dados armazenados em compartilhamentos é restrito.

    - Clientes SMB que acessam o compartilhamento de dados precisam de credenciais de usuário associadas ao compartilhamento. Essas credenciais são definidas quando o compartilhamento for criado.
    - Os endereços IP dos clientes NFS que acessam um compartilhamento precisarão ser adicionados quando o compartilhamento for criado.