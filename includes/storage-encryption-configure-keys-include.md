---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/16/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 3076065d23d4701711c0ecb60349e77ee5c9b7c8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172137"
---
O armazenamento do Azure dá suporte à criptografia em repouso com chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente. Chaves gerenciadas pelo cliente permitem que você criar, girar, desabilitar e revogar os controles de acesso.

Use o Azure Key Vault para gerenciar suas chaves e auditar o uso. Você pode criar suas próprias chaves e armazená-los em um cofre de chaves, ou você pode usar as APIs do Azure Key Vault para gerar chaves. A conta de armazenamento e o cofre de chaves devem estar na mesma região, mas podem estar em assinaturas diferentes. Para obter mais informações sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](../articles/key-vault/key-vault-overview.md)
