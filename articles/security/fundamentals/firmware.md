---
title: Segurança de firmware-segurança do Azure
description: Saiba como a Microsoft protege o hardware e o firmware do Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 8233cc714d977083f4d55716d35c0b7094a069ea
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557539"
---
# <a name="firmware-security"></a>Segurança do firmware
Este artigo descreve como a Microsoft protege o ecossistema de hardware de nuvem e as cadeias de suprimentos.

## <a name="securing-the-cloud-hardware-ecosystem"></a>Protegendo o ecossistema de hardware de nuvem
A Microsoft está ativamente em parceria com o ecossistema de hardware de nuvem para impulsionar melhorias de segurança contínuas:

- Colaborar com parceiros de hardware e firmware do Azure (como fabricantes de componentes e integradores de sistemas) para atender aos requisitos de segurança de firmware e hardware do Azure.

- Permitir que os parceiros realizem a avaliação contínua e a melhoria da postura de segurança de seus produtos usando requisitos definidos pela Microsoft em áreas como:

  - Inicialização segura do firmware
  - Recuperação segura de firmware
  - Atualização segura do firmware
  - Criptografia de firmware
  - Hardware bloqueado
  - Telemetria de depuração granular
  - Suporte do sistema para hardware do TPM 2,0 para habilitar a inicialização medida

- Envolver e contribuir com o projeto de segurança do [projeto de computação aberta (OCP)](https://www.opencompute.org/wiki/Security) por meio do desenvolvimento de especificações. As especificações promovem a consistência e a clareza para o design e a arquitetura seguros no ecossistema.

   > [!NOTE]
   > Um exemplo de nossa contribuição para o projeto de segurança OCP é a especificação de [inicialização segura de hardware](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0) .

## <a name="securing-hardware-and-firmware-supply-chains"></a>Protegendo cadeias de suprimentos de hardware e firmware
Fornecedores de hardware de nuvem e fornecedores do Azure também são necessários para cumprir os processos de segurança da cadeia de fornecedores e os requisitos desenvolvidos pela Microsoft. Os processos de implantação e desenvolvimento de hardware e firmware são necessários para seguir os processos do Microsoft [Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl) (SDL), como:

- Modelagem de ameaças
- Revisões de design seguras
- Análises de firmware e teste de penetração
- Ambientes de compilação e teste seguros
- Gerenciamento de vulnerabilidades de segurança e resposta a incidentes

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que fazemos para impulsionar a integridade e a segurança da plataforma, consulte:

- [Inicialização segura](secure-boot.md)
- [Atestado de inicialização e host medidos](measured-boot-host-attestation.md)
- [O Cerberus do projeto](project-cerberus.md)
- [Criptografia em repouso](encryption-atrest.md)
- [Segurança do hipervisor](hypervisor.md)