---
title: Criptografia de dados do Hub IoT do Azure em repouso por meio de chaves gerenciadas pelo cliente | Microsoft Docs
description: Criptografia de dados em repouso com chaves gerenciadas pelo cliente para o Hub IoT
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 352da24b36124ff0446a81c1ecbc584da545bb16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92142211"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Criptografia de dados em repouso com chaves gerenciadas pelo cliente para o Hub IoT

O Hub IoT dá suporte à criptografia de dados em repouso com chaves gerenciadas pelo cliente (CMK), também conhecida como traga sua própria chave (BYOK). O Hub IoT do Azure fornece criptografia de dados em repouso e em trânsito conforme eles são gravados em nossos data centers e descriptografa-os para você à medida que você o acessa. Por padrão, o Hub IoT usa chaves gerenciadas pela Microsoft para criptografar os dados em repouso. Com o CMK, você pode obter outra camada de criptografia na parte superior da criptografia padrão e pode optar por criptografar dados em repouso com uma chave de criptografia de chave, gerenciada por meio de seu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Isso oferece a flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Se o BYOK estiver configurado para o Hub IoT, também forneceremos a criptografia dupla, que oferece uma segunda camada de proteção, enquanto permite que você controle a chave de criptografia por meio de seu Azure Key Vault.

Esse recurso requer a criação de um novo hub IoT (camada básica ou Standard). Para experimentar esse recurso, entre em contato conosco por meio [do suporte da Microsoft](https://azure.microsoft.com/support/create-ticket/). Compartilhe o nome da sua empresa e a ID da assinatura ao entrar em contato com o suporte da Microsoft.


## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Hub IoT](./about-iot-hub.md)

* [Saiba mais sobre o Azure Key Vault](../key-vault/general/overview.md)