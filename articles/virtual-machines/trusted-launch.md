---
title: 'Versão prévia: inicialização confiável para VMs do Azure'
description: Saiba mais sobre o lançamento confiável para máquinas virtuais do Azure.
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: conceptual
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: 449eb1d65e0104e6c5c74a78901cf29c5aeb3e57
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609083"
---
# <a name="trusted-launch-for-azure-virtual-machines-preview"></a>Inicialização confiável para máquinas virtuais do Azure (versão prévia)

O Azure oferece uma inicialização confiável como uma maneira simples de melhorar a segurança de VMs de [geração 2](generation-2.md) . O lançamento confiável protege contra técnicas de ataque avançadas e persistentes. A inicialização confiável é composta por várias tecnologias de infraestrutura coordenadas que podem ser habilitadas independentemente. Cada tecnologia fornece outra camada de defesa contra ameaças sofisticadas.

> [!IMPORTANT]
> O lançamento confiável requer a criação de novas máquinas virtuais. Você não pode habilitar a inicialização confiável em máquinas virtuais existentes que foram criadas inicialmente sem ele.
>
> A inicialização confiável está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
>
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="benefits"></a>Benefícios 

- Implante com segurança máquinas virtuais com carregadores de inicialização verificados, kernels do sistema operacional e drivers.
- Proteger chaves, certificados e segredos com segurança nas máquinas virtuais.
- Obter informações e confiança da integridade da cadeia de inicialização inteira.
- Garanta que as cargas de trabalho sejam confiáveis e verificáveis.

## <a name="public-preview-limitations"></a>Limitações da visualização pública

**Suporte a tamanho**: todos os tamanhos de VM de [geração 2](generation-2.md) , exceto:

- Série Lsv2 
- Série M 
- Série Mv2 
- Série NDv4 
- Série NVv4

**Suporte do so**:
- RedHat Enterprise Linux 8,3
- SUSE 15 SP2
- Ubuntu 20.04 LTS
- Ubuntu 18.04 LTS
- Windows Server 2019
- Windows Server 2016
- Windows 10 Pro
- Windows 10 Enterprise

**Regiões**: 
- Centro-Sul dos Estados Unidos
- Norte da Europa

**Preços**: nenhum custo adicional para o preço da VM existente.

**Os recursos a seguir não têm suporte nesta visualização**:
- Backup
- Azure Site Recovery
- Galeria de imagens compartilhadas
- Disco do so efêmero
- Disco compartilhado
- Imagem gerenciada
- Host Dedicado do Azure 

## <a name="secure-boot"></a>Inicialização Segura

Na raiz de inicialização confiável está a inicialização segura para sua VM. Esse modo, que é implementado no firmware da plataforma, protege contra a instalação de rootkits baseados em malware e kits de inicialização. A inicialização segura funciona para garantir que apenas sistemas operacionais e drivers assinados possam ser inicializados. Ele estabelece uma "raiz de confiança" para a pilha de software em sua VM. Com a inicialização segura habilitada, todos os componentes de inicialização do sistema operacional (carregador de inicialização, kernel, drivers de kernel) devem ser assinados por editores confiáveis. O Windows e o selecione as distribuições do Linux dão suporte à inicialização segura. Se a inicialização segura falhar ao autenticar que a imagem foi assinada por um fornecedor confiável, a VM não terá permissão para inicializar. Para saber mais, confira [Inicialização Segura](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-secure-boot).

## <a name="vtpm"></a>vTPM

O lançamento confiável também apresenta vTPM para VMs do Azure. Esta é uma versão virtualizada de um hardware [Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-overview), em conformidade com a especificação do TPM 2.0. Ele serve como um cofre seguro dedicado para chaves e medições. O lançamento confiável fornece sua VM com sua própria instância do TPM dedicada, em execução em um ambiente seguro fora do alcance de qualquer VM. O vTPM habilita o [atestado](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation) medindo toda a cadeia de inicialização de sua VM (UEFI, so, sistema e drivers). 

O lançamento confiável usa o vTPM para executar o atestado remoto pela nuvem. Isso é usado para verificações de integridade da plataforma e para tomar decisões baseadas em confiança. Como uma verificação de integridade, a inicialização confiável pode certificar criptograficamente que sua VM foi inicializada corretamente. Se o processo falhar, possivelmente porque sua VM está executando um componente não autorizado, a central de segurança do Azure emitirá alertas de integridade. Os alertas incluem detalhes sobre quais componentes não passaram nas verificações de integridade.

## <a name="virtualization-based-security"></a>Segurança com base em virtualização

A [proteção baseada em virtualização](/windows-hardware/design/device-experiences/oem-vbs) (vbs) usa o hipervisor para criar uma região segura e isolada de memória. O Windows usa essas regiões para executar várias soluções de segurança com maior proteção contra vulnerabilidades e explorações mal-intencionadas. O lançamento confiável permite habilitar a política HVAC (integridade de código do hipervisor) e o Windows Defender Credential Guard.

O política HVAC é uma poderosa mitigação do sistema que protege os processos do modo kernel do Windows contra injeção e execução de código mal-intencionado ou não verificado. Ele verifica os drivers e os binários do modo kernel antes de executá-los, impedindo que arquivos não assinados sejam carregados na memória. Isso garante que esse código executável não possa ser modificado depois que ele tiver permissão para ser carregado. Para obter mais informações sobre VBS e política HVAC, consulte [segurança baseada em virtualização (vbs) e integridade de código imposta do hipervisor (política HVAC)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571).

Com o Launch e o VBS confiáveis, você pode habilitar o Windows Defender Credential Guard. Esse recurso isola e protege segredos para que somente o software de sistema privilegiado possa acessá-los. Ele ajuda a impedir o acesso não autorizado a segredos e ataques de roubo de credenciais, como ataques de Pass-the-hash (PtH). Para obter mais informações, consulte [Credential Guard](https://docs.microsoft.com/windows/security/identity-protection/credential-guard/credential-guard).


## <a name="security-center-integration"></a>Integração da Central de Segurança

O lançamento confiável é integrado à central de segurança do Azure para garantir que suas VMs estejam configuradas corretamente. A central de segurança do Azure avaliará continuamente as VMs compatíveis e emitirá recomendações relevantes.

- **Recomendação para habilitar a inicialização segura** -essa recomendação se aplica somente a VMs que dão suporte à inicialização confiável. A central de segurança do Azure identificará as VMs que podem habilitar a inicialização segura, mas desativadas. Ele emitirá uma recomendação de baixa severidade para habilitá-la.
- **Recomendação para habilitar o vTPM** -se sua VM tiver o vTPM habilitado, a central de segurança do Azure poderá usá-lo para executar o atestado de convidado e identificar padrões de ameaças avançados. Se a central de segurança do Azure identificar as VMs que dão suporte à inicialização confiável e tiver o vTPM desabilitado, ele emitirá uma recomendação de baixa severidade para habilitá-la. 
- **Avaliação de integridade de atestado** -se sua VM tiver o vTPM habilitado, uma extensão da central de segurança do Azure poderá validar remotamente que sua VM foi inicializada de forma íntegra. Isso é conhecido como atestado remoto. A central de segurança do Azure emite uma avaliação, indicando o status do atestado remoto.

## <a name="azure-defender-integration"></a>Integração do Azure defender

Se suas VMs estiverem configuradas corretamente com inicialização confiável, o Azure defender poderá detectar e alertar você sobre problemas de integridade da VM.

- **Alerta de falha de atestado de VM** -o Azure defender realizará o atestado periodicamente em suas VMs. Isso também ocorre depois que a VM é inicializada. Se o atestado falhar, ele irá disparar um alerta de severidade média.
    O atestado de VM pode falhar pelos seguintes motivos:
    - As informações atestadas, que incluem um log de inicialização, se desviam de uma linha de base confiável. Isso pode indicar que os módulos não confiáveis foram carregados e o sistema operacional pode estar comprometido.
    - Não foi possível verificar a cotação de atestado para originar do vTPM da VM atestada. Isso pode indicar que o malware está presente e pode estar interceptando o tráfego para o vTPM.
    - A extensão de atestado na VM não está respondendo. Isso pode indicar um ataque de negação de serviço por malware ou um administrador do sistema operacional.

    > [!NOTE]
    >  Esse alerta está disponível para VMs com vTPM habilitado e a extensão de atestado instalada. A inicialização segura deve ser habilitada para que o atestado passe. O atestado falhará se a inicialização segura estiver desabilitada. Se você precisar desabilitar a inicialização segura, poderá suprimir esse alerta para evitar falsos positivos.

- **Alerta para o módulo kernel do Linux não confiável** – para inicialização confiável com a inicialização segura habilitada, é possível que uma VM seja inicializada mesmo se um driver de kernel falhar na validação e não for permitido o carregamento. Se isso acontecer, o Azure defender emitirá um alerta de baixa severidade. Embora não haja nenhuma ameaça imediata, como o driver não confiável não foi carregado, esses eventos devem ser investigados. Considere o seguinte:
    - Qual driver de kernel falhou? Estou familiarizado com esse driver e espero que ele seja carregado?
    - Esta é a versão exata do driver que estou esperando? Os binários de driver estão intactos? Se esse for um driver de terceiros, o fornecedor passará os testes de conformidade do sistema operacional para assiná-lo?


## <a name="faq"></a>Perguntas frequentes

Perguntas frequentes sobre a inicialização confiável.

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>Por que devo usar o lançamento confiável? Em que proteção de inicialização confiável?

As proteções de inicialização confiáveis contra kits de inicialização, rootkits e malware de nível de kernel. Esses tipos sofisticados de malware são executados no modo kernel e permanecem ocultos dos usuários. Por exemplo:
- Rootkits de firmware: Esses kits substituem o firmware do BIOS da máquina virtual, portanto, o rootkit pode ser iniciado antes do sistema operacional. 
- Kits de inicialização: Esses kits substituem o carregador de inicialização do sistema operacional, de forma que a máquina virtual carregue o boot kit antes do sistema operacional.
- Rootkits de kernel: Esses kits substituem uma parte do kernel do sistema operacional para que o rootkit possa ser iniciado automaticamente quando o sistema operacional for carregado.
- Rootkits de driver: Esses kits fingem ser um dos drivers confiáveis que o sistema operacional usa para se comunicar com os componentes da máquina virtual.

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>Quais são as diferenças entre a inicialização segura e a inicialização medida?

Na cadeia de inicialização segura, cada etapa no processo de inicialização verifica uma assinatura criptográfica das etapas subsequentes. Por exemplo, o BIOS verificará uma assinatura no carregador, e o carregador verificará as assinaturas em todos os objetos kernel carregados e assim por diante. Se qualquer um dos objetos for comprometido, a assinatura não corresponderá e a VM não será inicializada. Para saber mais, confira [Inicialização Segura](/windows-hardware/design/device-experiences/oem-secure-boot). A inicialização medida não interrompe o processo de inicialização, ele mede ou computa o hash dos próximos objetos na cadeia e armazena os hashes nos registradores de configuração de plataforma (PCRs) no vTPM. Os registros de inicialização medidos são usados para o monitoramento de integridade da inicialização.

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>O que acontece quando uma falha de integridade é detectada?

O lançamento confiável para máquinas virtuais do Azure é monitorado para ameaças avançadas. Se essas ameaças forem detectadas, um alerta será disparado. Os alertas só estão disponíveis na [camada Standard](/azure/security-center/security-center-pricing) da central de segurança do Azure.
A central de segurança do Azure executa periodicamente o atestado. Se o atestado falhar, um alerta de severidade médio será disparado. O atestado de inicialização confiável pode falhar pelos seguintes motivos: 
- As informações atestadas, que incluem um log da base de computação confiável (TCB), se desviam de uma linha de base confiável (como quando a inicialização segura está habilitada). Isso pode indicar que os módulos não confiáveis foram carregados e que o sistema operacional pode estar comprometido.
- Não foi possível verificar a cotação de atestado para originar do vTPM da VM atestada. Isso pode indicar que o malware está presente e pode estar interceptando o tráfego para o TPM. 
- A extensão de atestado na VM não está respondendo. Isso pode indicar um ataque de negação de serviço por malware ou um administrador do sistema operacional.

  
### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>Como a inicialização confiável é comparada à VM blindada do Hyper-V?
A VM blindada do Hyper-V atualmente está disponível somente no Hyper-V. A [VM blindada do Hyper-V](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) normalmente é implantada em conjunto com a malha protegida. Uma malha protegida consiste em um HGS (serviço guardião de host), um ou mais hosts protegidos e um conjunto de VMs blindadas. As VMs blindadas do Hyper-V são destinadas para uso em malhas em que os dados e o estado da máquina virtual devem ser protegidos de administradores de malha e software não confiável que podem estar em execução nos hosts Hyper-V. A inicialização confiável por outro lado pode ser implantada como uma máquina virtual autônoma ou conjuntos de dimensionamento de máquinas virtuais no Azure Sem implantação adicional e gerenciamento do HGS. Todos os recursos de inicialização confiáveis podem ser habilitados com uma simples alteração no código de implantação ou uma caixa de seleção na portal do Azure.  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>Como posso converter VMs existentes em inicialização confiável?
Para a VM de geração 2, o caminho de migração para converter para a inicialização confiável é destinado à disponibilidade geral (GA).

## <a name="next-steps"></a>Próximas etapas

Implante uma [VM de inicialização confiável usando o portal](trusted-launch-portal.md).
