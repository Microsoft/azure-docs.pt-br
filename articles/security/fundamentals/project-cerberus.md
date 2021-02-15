---
title: Integridade do firmware-segurança do Azure
description: Saiba mais sobre as medidas criptográficas para garantir a integridade do firmware.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f085858a9d550623704efd4f051ed525e55a37e0
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557502"
---
# <a name="project-cerberus"></a>O Cerberus do projeto

O Cerberus é uma raiz de confiança de hardware compatível com NIST 800-193 com uma identidade que não pode ser clonada. O o Cerberus foi projetado para aumentar ainda mais a postura de segurança da infraestrutura do Azure, fornecendo uma forte âncora de confiança para a integridade do firmware.

## <a name="enabling-an-anchor-of-trust"></a>Habilitando uma âncora de confiança
Cada chip O Cerberus tem uma identidade criptográfica exclusiva que é estabelecida usando uma cadeia de certificados assinadas com raiz para uma autoridade de certificação (CA) da Microsoft. As medidas obtidas de O Cerberus podem ser usadas para validar a integridade de componentes, como:

- Host
- Controlador de Gerenciamento de Placa-base (BMC)
- Todos os periféricos, incluindo a placa de interface de rede e [o sistema em chip](https://en.wikipedia.org/wiki/System_on_a_chip) (SOC)

Essa âncora de confiança ajuda a proteger o firmware da plataforma de:

- Binários de firmware comprometidos em execução na plataforma
- Malware e hackers que exploram bugs no sistema operacional, aplicativo ou hipervisor
- Certos tipos de ataques de cadeia de fornecimento (fabricação, assembly, trânsito)
- Pessoas mal-intencionadas mal-intencionados com privilégios administrativos ou acesso ao hardware

## <a name="cerberus-attestation"></a>Atestado de o Cerberus
O o Cerberus autentica a integridade do firmware para os componentes do servidor usando um manifesto de firmware de plataforma (PFM). PFM define uma lista de versões de firmware autorizadas e fornece uma medição de plataforma para o [serviço de atestado de host](measured-boot-host-attestation.md)do Azure. O serviço de atestado de host valida as medidas e faz uma determinação para permitir que apenas hosts confiáveis ingressem nas cargas de trabalho de cliente do Azure frota e host.

Em conjunto com o serviço de atestado de host, os recursos do O Cerberus aprimoram e promovem uma infraestrutura de produção do Azure altamente segura.

> [!NOTE]
> Para saber mais, Confira as informações de [o Cerberus do projeto](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) no github.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que fazemos para impulsionar a integridade e a segurança da plataforma, consulte:

- [Segurança do firmware](firmware.md)
- [Inicialização segura](secure-boot.md)
- [Atestado de inicialização e host medidos](measured-boot-host-attestation.md)
- [Criptografia em repouso](encryption-atrest.md)
- [Segurança do hipervisor](hypervisor.md)