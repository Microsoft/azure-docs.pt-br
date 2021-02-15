---
title: Criptografia de dados do serviço de provisionamento de dispositivos do Azure em repouso por meio de chaves gerenciadas pelo cliente | Microsoft Docs
description: Criptografia de dados em repouso com chaves gerenciadas pelo cliente para o serviço de provisionamento de dispositivos
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: d22a01bab81fc330484e7715a65c89a1cfd7802c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967169"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Criptografia de dados em repouso com chaves gerenciadas pelo cliente para o serviço de provisionamento de dispositivos

## <a name="overview"></a>Visão geral

O DPS (serviço de provisionamento de dispositivos) dá suporte à criptografia de dados em repouso com chaves gerenciadas pelo cliente (CMK), também conhecida como traga sua própria chave (BYOK). O DPS fornece criptografia de dados em repouso e em trânsito conforme eles são gravados em nossos data centers e descriptografa-os para você, conforme você o acessa. Por padrão, o DPS usa chaves gerenciadas pela Microsoft para criptografar os dados em repouso. Com o CMK, você pode obter uma camada adicional de criptografia sobre a criptografia de plataforma padrão ao optar por criptografar dados em repouso com uma chave-Encryption-Key, gerenciada por meio de seu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Isso oferece a flexibilidade para criar, girar, desabilitar e revogar chaves. Se o CMK estiver configurado para seu DPS, ele implica que a criptografia dupla está habilitada com duas camadas de proteção que protegem seus dados ativamente. 

Essa funcionalidade requer a criação de um novo DPS. Para experimentar esse recurso, entre em contato conosco por meio [do suporte da Microsoft](https://azure.microsoft.com/support/create-ticket/). Compartilhe o nome da sua empresa e a ID da assinatura ao entrar em contato com o suporte da Microsoft.


## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o serviço de provisionamento de dispositivos](./index.yml)

* [Saiba mais sobre o Azure Key Vault](../key-vault/general/overview.md)