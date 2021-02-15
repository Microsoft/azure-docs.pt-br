---
title: Segurança do hipervisor no Azure frota-segurança do Azure
description: Visão geral técnica da segurança do hipervisor no Azure frota.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 766266edd663b75b893a5883e30bb48eed7bbfdf
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696108"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>Segurança do hipervisor no Azure frota

O sistema de hipervisor do Azure é baseado no Windows Hyper-V. O sistema de hipervisor permite que o administrador do computador especifique as partições de convidado que têm espaços de endereço separados. Os espaços de endereço separados permitem que você carregue um sistema operacional e aplicativos operando em paralelo ao sistema operacional (host) que é executado na partição raiz do computador. O sistema operacional host (também conhecido como partição raiz privilegiada) tem acesso direto a todos os dispositivos físicos e periféricos no sistema (controladores de armazenamento, adaptações de rede). O sistema operacional host permite que as partições de convidado compartilhem o uso desses dispositivos físicos expondo "dispositivos virtuais" a cada partição de convidado. Assim, um sistema operacional em execução em uma partição de convidado tem acesso a dispositivos periféricos virtualizados que são fornecidos pelos serviços de virtualização em execução na partição raiz.

O hipervisor do Azure é criado tendo em mente os seguintes objetivos de segurança:

| Objetivo | Fonte |
|--|--|
| Isolamento | Uma política de segurança não exige transferência de informações entre VMs. Essa restrição requer recursos no Virtual Machine Manager (VMM) e no hardware para isolamento de memória, dispositivos, rede e recursos gerenciados, como dados persistentes. |
| Integridade do VMM | Para obter a integridade geral do sistema, a integridade dos componentes individuais do hipervisor é estabelecida e mantida. |
| Integridade da plataforma | A integridade do hipervisor depende da integridade do hardware e do software do qual ele se baseia. Embora o hipervisor não tenha controle direto sobre a integridade da plataforma, o Azure conta com mecanismos de hardware e firmware, como o chip [o Cerberus](project-cerberus.md) , para proteger e detectar a integridade da plataforma subjacente. O VMM e os convidados serão impedidos de serem executados se a integridade da plataforma for comprometida. |
| Acesso restrito | As funções de gerenciamento são exercidas somente por administradores autorizados conectados por conexões seguras. Um princípio de privilégios mínimos é imposto pelos mecanismos do controle de acesso baseado em função (RBAC do Azure). |
| Audit | O Azure permite que a funcionalidade de auditoria Capture e proteja dados sobre o que acontece em um sistema para que ele possa ser inspecionado posteriormente. |

A abordagem da Microsoft para proteger o hipervisor do Azure e o subsistema de virtualização pode ser dividida nas três categorias a seguir.

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>Limites de segurança definidos fortemente impostos pelo hipervisor

O hipervisor do Azure impõe vários limites de segurança entre:

- Partições virtualizadas "convidadas" e partição privilegiada ("host")
- Vários convidados
- A si mesmo e o host
- A si mesmo e todos os convidados

A confidencialidade, a integridade e a disponibilidade são garantidas pelos limites de segurança do hipervisor. Os limites se defendem contra uma variedade de ataques, incluindo vazamentos de informações de canal lateral, negação de serviço e elevação de privilégio.

O limite de segurança do hipervisor também fornece segmentação entre locatários para tráfego de rede, dispositivos virtuais, armazenamento, recursos de computação e todos os outros recursos de VM.

## <a name="defense-in-depth-exploit-mitigations"></a>Mitigações de exploração de defesa aprofundada

No caso improvável de um limite de segurança ter uma vulnerabilidade, o hipervisor do Azure inclui várias camadas de mitigações, incluindo:

- Isolamento do processo baseado em host que hospeda componentes entre VMs
- VBS (segurança baseada em virtualização) para garantir a integridade dos componentes do usuário e do modo kernel de um mundo seguro
- Vários níveis de mitigações de exploração. As mitigações incluem ASLR (Address Space layout Randomization), DEP (prevenção de execução de dados), proteção de código arbitrária, integridade do fluxo de controle e prevenção de corrupção de dados
- Inicialização automática de variáveis de pilha no nível do compilador
- APIs de kernel que inicializam automaticamente as alocações de heap de kernel feitas pelo Hyper-V

Essas atenuações foram projetadas para tornar a vulnerabilidade de desenvolvimento de uma exploração de VM cruzada inviável.

## <a name="strong-security-assurance-processes"></a>Processos de garantia de segurança forte

A superfície de ataque relacionada ao hipervisor inclui rede de software, dispositivos virtuais e todas as superfícies entre VMs. A superfície de ataque é controlada por meio da integração de compilação automatizada, que dispara revisões de segurança periódicas.

Todas as superfícies de ataque de VM são modeladas, revisadas por código, difusas e testadas por nossa equipe vermelha para violações de limites de segurança. A Microsoft tem um [programa de Bounty de bugs](https://www.microsoft.com/msrc/bounty-hyper-v) que paga um prêmio em busca de vulnerabilidades relevantes em versões de produtos elegíveis para Microsoft Hyper-V.

> [!NOTE]
> Saiba mais sobre [processos de garantia de segurança forte](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes) no Hyper-V.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que fazemos para impulsionar a integridade e a segurança da plataforma, consulte:

- [Segurança do firmware](firmware.md)
- [Inicialização segura](secure-boot.md)
- [Atestado de inicialização e host medidos](measured-boot-host-attestation.md)
- [Projeto Cerberus](project-cerberus.md)
- [Criptografia em repouso](encryption-atrest.md)