---
title: Integridade e segurança da plataforma Azure-segurança do Azure
description: Visão geral técnica da integridade e da segurança da plataforma Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 4755bc19a645d196487f0b8e0f4d1ef2120723ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557543"
---
# <a name="platform-integrity-and-security-overview"></a>Visão geral da integridade e da segurança da plataforma
A frota do Azure é composta por milhões de servidores (hosts) com milhares mais adicionados diariamente. Milhares de hosts também passam por manutenção diariamente por meio de reinicializações, atualizações do sistema operacional ou reparos. Antes que um host possa ingressar no frota e começar a aceitar cargas de trabalho do cliente, a Microsoft verifica se o host está em um estado seguro e confiável. Essa verificação garante que as alterações mal-intencionadas ou involuntárias não tenham ocorrido nos componentes da sequência de inicialização durante a cadeia de fornecedores ou fluxos de trabalho de manutenção.

## <a name="securing-azure-hardware-and-firmware"></a>Protegendo hardware e firmware do Azure
Esta série de artigos descreve como a Microsoft garante a integridade e a segurança dos hosts por meio de vários estágios em seu ciclo de vida, desde a fabricação até o pôr o sol. O artigos aborda:
 
- [Segurança do firmware](firmware.md)
- [Inicialização segura da UEFI](secure-boot.md)
- [Atestado de inicialização e host medidos](measured-boot-host-attestation.md)
- [Projeto Cerberus](project-cerberus.md)
- [Criptografia em repouso](encryption-atrest.md)
- [Segurança do hipervisor](hypervisor.md)
 
## <a name="next-steps"></a>Próximas etapas

- Saiba como a Microsoft está ativamente em parceria com o ecossistema de hardware de nuvem para gerar [melhorias de segurança de firmware](firmware.md)contínua.

- Entenda sua [responsabilidade compartilhada na nuvem](shared-responsibility.md).