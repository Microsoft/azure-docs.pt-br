---
title: Criptografia dupla no Microsoft Azure
description: Este artigo descreve como o Microsoft Azure fornece criptografia dupla para dados em repouso e dados em trânsito.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: terrylan
ms.openlocfilehash: 020e8249f57ccb1a14da798a717a00dcc3962389
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88227000"
---
# <a name="double-encryption"></a>Criptografia dupla
A criptografia dupla é onde duas ou mais camadas independentes de criptografia são habilitadas para proteger contra comprometimentos de qualquer camada de criptografia. O uso de duas camadas de criptografia reduz as ameaças que vêm com a criptografia de dados. Por exemplo:

- Erros de configuração na criptografia de dados
- Erros de implementação no algoritmo de criptografia
- Comprometimento de uma única chave de criptografia

O Azure fornece criptografia dupla para dados em repouso e dados em trânsito.

## <a name="data-at-rest"></a>Dados em repouso
A abordagem da Microsoft para habilitar duas camadas de criptografia para dados em repouso é:

- **Criptografia de disco usando chaves gerenciadas pelo cliente**. Você fornece sua própria chave para criptografia de disco. Você pode trazer suas próprias chaves para seu Key Vault (BYOK – Bring Your Own Key) ou gerar novas chaves no Azure Key Vault para criptografar os recursos desejados.
- **Criptografia de infraestrutura usando chaves gerenciadas por plataforma**.  Por padrão, os discos são criptografados automaticamente em repouso usando chaves de criptografia gerenciadas pela plataforma.

## <a name="data-in-transit"></a>Dados em trânsito
A abordagem da Microsoft para habilitar duas camadas de criptografia para os dados em trânsito é:

- **Criptografia de trânsito usando TLS (segurança de camada de transporte) 1,2 para proteger dados quando está viajando entre os serviços de nuvem e você**. Todo o tráfego que sai de um datacenter é criptografado em trânsito, mesmo se o destino do tráfego for outro controlador de domínio na mesma região. O TLS 1,2 é o protocolo de segurança padrão usado. O TLS fornece autenticação forte, privacidade de mensagem e integridade (habilitando a detecção de adulteração, interceptação e falsificação de mensagens), interoperabilidade, flexibilidade de algoritmo e facilidade de implantação e uso.
- **Camada adicional de criptografia fornecida na camada de infraestrutura**. Um método de criptografia de camada de vínculo de dados que usa os padrões de segurança do MAC do IEEE 802.1 AE (também conhecido como MACsec) é aplicado de ponto a ponto em todo o hardware de rede subjacente. Sempre que o tráfego do cliente do Azure se move entre data centers--fora dos limites físicos não controlados pela Microsoft (ou em nome da Microsoft) – os pacotes são criptografados e descriptografados nos dispositivos antes de serem enviados, evitando ataques físicos "Man-in-the-middle" ou de espionagem/wiretapping. Como essa tecnologia é integrada ao próprio hardware de rede, ela fornece criptografia de taxa de linha no hardware de rede sem aumento de latência de link mensurável. Essa criptografia MACsec está ativada por padrão para todo o tráfego do Azure que viaja dentro de uma região ou entre regiões, e nenhuma ação é necessária na parte dos clientes para habilitar.

## <a name="next-steps"></a>Próximas etapas
Saiba como a [criptografia é usada no Azure](encryption-overview.md).
