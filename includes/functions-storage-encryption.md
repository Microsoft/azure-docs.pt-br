---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 0bc5977a581006dc760c0435f9d68371ced7e4cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83648794"
---
O Armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Para obter mais informações, consulte [Criptografia do Armazenamento do Azure para dados em repouso](../articles/storage/common/storage-service-encryption.md).

Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter controle adicional sobre as chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para usar para criptografia de dados de blob e arquivo. Essas chaves devem estar presentes no Azure Key Vault para que o Functions possa acessar a conta de armazenamento. Para saber mais, confira [Criptografia em repouso usando chaves gerenciadas pelo cliente](../articles/azure-functions/configure-encrypt-at-rest-using-cmk.md).  