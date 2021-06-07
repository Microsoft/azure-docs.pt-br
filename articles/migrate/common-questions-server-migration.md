---
title: Perguntas comuns sobre a migração de servidor de migrações para Azure
description: Obtenha respostas para perguntas comuns sobre como usar a migração de servidor de migração do Azure para migrar computadores.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: 63c7f226dcd99ec8040f2078ce12be0fe3c594df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99548791"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migração de servidor de migrações para Azure: perguntas comuns

Este artigo responde a perguntas comuns sobre o migrações para Azure: ferramenta de migração de servidor. Se você tiver outras dúvidas, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre as migrações para Azure
- Perguntas sobre o [dispositivo migrações para Azure](common-questions-appliance.md)
- Perguntas sobre [descoberta, avaliação e visualização de dependência](common-questions-discovery-assessment.md)
- Obter perguntas respondidas no [Fórum de migrações para Azure](https://aka.ms/AzureMigrateForum)

## <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>As migrações para Azure convertem computadores baseados em UEFI em computadores baseados em BIOS e os migra para o Azure como VMs da geração 1 do Azure?
Migrações para Azure: a ferramenta de migração de servidor migra todas as máquinas baseadas em UEFI para o Azure como VMs da geração 2 do Azure. Não damos mais suporte à conversão de VMs baseadas em UEFI em VMs baseadas em BIOS. Observe que todos os computadores baseados em BIOS são migrados para o Azure como somente VMs da geração 1 do Azure.

## <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>Como posso migrar computadores baseados em UEFI para o Azure como VMs da geração 1 do Azure?
Migrações para Azure: a ferramenta de migração de servidor migra computadores baseados em UEFI para o Azure como VMs da geração 2 do Azure. Se você quiser migrá-los para VMs do Azure geração 1, converta o tipo de inicialização para BIOS antes de iniciar a replicação e, em seguida, use a ferramenta migrações para Azure: servidor de migração para migrar para o Azure.
 
## <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>Quais sistemas operacionais têm suporte para migração de computadores baseados em UEFI para o Azure?

| **Sistemas operacionais com suporte para computadores baseados em UEFI** | **VMware sem agente para o Azure**                                                                                                             | **Hyper-V sem agente para o Azure** | **VMware, físico e outras nuvens baseadas em agente para o Azure** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019, 2016, 2012 R2, 2012                | Y                                                                                                                                         | Y                              | Y                                                          |
| Windows 10 pro, Windows 10 Enterprise                   | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | Y                                                                                                                                         | Y                              | Y                                                          |
| Ubuntu Server 16.04, 18.04, 19.04, 19.10                | Y                                                                                                                                         | Y                              | Y                                                          |
| RHEL 8,1, 8,0, 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x        | Y<br>                 _O RHEL 8. x requer [preparação manual](./prepare-for-migration.md#linux-machines)_   | Y                              | Y                                                          |
| Cent OS 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 6. x               | Y<br>_O Cent OS 8. x requer [preparação manual](./prepare-for-migration.md#linux-machines)_ | Y                              | Y                                                          |
| Oracle Linux 7.7, 7.7-CI                                |  Y                                                                                                                                        | Y                              | Y                                                          |

## <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>Posso usar o cofre dos serviços de recuperação criado pelas migrações para Azure para cenários de recuperação de desastre?
Não recomendamos o uso do cofre dos serviços de recuperação criado pela migração do Azure para cenários de recuperação de desastre. Isso pode resultar em Iniciar falhas de replicação nas migrações para Azure. 

## <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>Onde devo instalar o dispositivo de replicação para migrações baseadas em agente?

O dispositivo de replicação deve ser instalado em um computador dedicado. O dispositivo de replicação não deve ser instalado em um computador de origem que você deseja replicar nem no dispositivo de avaliação e descoberta das Migrações para Azure que pode ter sido instalado antes. Siga o [tutorial](./tutorial-migrate-physical-virtual-machines.md) para obter mais detalhes.

## <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>Como posso migrar minhas instâncias do AWS EC2 para o Azure?

Examine este [artigo](./tutorial-migrate-aws-virtual-machines.md) para descobrir, avaliar e migrar suas instâncias do AWS EC2 para o Azure.

## <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>Posso migrar VMs AWS que executam o sistema operacional Amazon Linux?

As VMs que executam o Amazon Linux não podem ser migradas no estado em que se encontram porque o sistema operacional do Amazon Linux só tem suporte no AWS.
Para migrar cargas de trabalho em execução no Amazon Linux, você pode criar uma VM CentOS/RHEL no Azure e migrar a carga de trabalho em execução no computador Linux da AWS usando uma abordagem de migração de carga de trabalho relevante. Por exemplo, dependendo da carga de trabalho, pode haver ferramentas específicas de carga de trabalho para ajudar a migração – como para bancos de dados ou ferramentas de implantação no caso de servidores Web.

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Quais geografias têm suporte para migração com migrações para Azure?

Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>Podemos usar o mesmo projeto de migrações para Azure para migrar para várias regiões?

Embora seja possível criar Avaliações para várias regiões em um projeto de migrações para Azure, um projeto de migrações para Azure pode ser usado para migrar servidores para apenas uma região do Azure. Você pode criar projetos adicionais de migrações para Azure para cada região para a qual você precisa migrar.

- Para migrações do VMware sem agente, a região de destino é bloqueada quando você habilita a primeira replicação.
- Para migrações baseadas em agente (VMware, servidores físicos e servidores de outras nuvens), a região de destino é bloqueada quando o botão "criar recursos" é clicado no portal durante a configuração do dispositivo de replicação.
- Para migrações do Hyper-V sem agente, a região de destino é bloqueada quando o botão "criar recursos" é clicado no portal durante a configuração do provedor de replicação do Hyper-V.

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>Podemos usar o mesmo projeto de migrações para Azure para migrar para várias assinaturas? 

Sim, você pode migrar para várias assinaturas na mesma região de destino para um projeto de migrações para Azure. Você pode selecionar a assinatura de destino ao habilitar a replicação para um computador ou um conjunto de computadores. A região de destino é bloqueada após a primeira replicação para migrações do VMware sem agente e durante a instalação do dispositivo de replicação e do provedor do Hyper-V para migrações baseadas em agente e migrações do Hyper-V sem agente, respectivamente.

## <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Quais são as opções de migração em migrações para Azure: migração de servidor?

A ferramenta migrações para Azure: Server Migration fornece duas opções para executar migrações de seus servidores de origem/VMs para o Azure – migração sem agente e migração baseada em agente.

Independentemente da opção de migração escolhida, a primeira etapa para migrar um servidor usando a migração do Azure: migração de servidor é habilitar a replicação para o servidor. Isso executa uma replicação inicial dos dados de servidor/VM para o Azure. Após a conclusão da replicação inicial, uma replicação contínua (sincronização delta contínua) é estabelecida para migrar dados incrementais para o Azure. Depois que a operação atingir o estágio de sincronização Delta, você poderá optar por migrar para o Azure a qualquer momento.  

Aqui estão algumas considerações para ter em mente ao decidir sobre a opção de migração.

As **migrações sem agente** não exigem que nenhum software (agentes) seja implantado nas VMs/servidores de origem que estão sendo migrados. A opção sem agente orquestra a replicação integrando-se com a funcionalidade fornecida pelo provedor de virtualização.
As opções de replicação sem agente estão disponíveis para VMs [VMware](./tutorial-migrate-vmware.md) e [VMs do Hyper-V](./tutorial-migrate-hyper-v.md).

As **migrações baseadas em agente** exigem que o software de migração do Azure (agentes) seja instalado nas VMs/máquinas de origem a serem migradas. A opção baseada em agente não depende da plataforma de virtualização para a funcionalidade de replicação e pode, portanto, ser usada com qualquer servidor que esteja executando uma arquitetura x86/x64 e uma versão de um sistema operacional com suporte do método de replicação baseado em agente.

A opção de migração baseada em agente pode ser usada para [VMs VMware](./tutorial-migrate-vmware-agent.md), [VMs do Hyper-V](./tutorial-migrate-physical-virtual-machines.md), [servidores físicos](./tutorial-migrate-physical-virtual-machines.md), [VMs em execução no AWS](./tutorial-migrate-aws-virtual-machines.md), VMS em execução no GCP ou VMS em execução em um provedor de virtualização diferente. A migração baseada em agente trata seus computadores como servidores físicos para fins de migração.

Embora a migração sem agente ofereça conveniência e simplicidade adicionais sobre as opções de replicação baseadas em agente para os cenários com suporte (VMWare e Hyper-V), talvez você queira considerar o uso do cenário baseado em agente para os seguintes casos de uso:

- Ambiente restrito de IOPS: a replicação sem agente usa instantâneos e consome IOPS/largura de banda de armazenamento. Recomendamos o método de migração baseado em agente se houver restrições de armazenamento/IOPS em seu ambiente.
- Se você não tiver uma vCenter Server, poderá tratar suas VMs do VMware como servidores físicos e usar o fluxo de trabalho de migração baseado em agente.

Para saber mais, leia este [artigo](./server-migrate-overview.md) para comparar as opções de migração para migrações do VMware.

## <a name="how-does-agentless-migration-work"></a>Como funciona a migração sem agente?

Migrações para Azure: a migração de servidor fornece opções de replicação sem agente para a migração de máquinas virtuais VMware e máquinas virtuais Hyper-V executando Windows ou Linux. A ferramenta também fornece uma opção de replicação baseada em agente adicional para servidores Windows e Linux que podem ser usados para migrar servidores físicos, bem como máquinas virtuais x86/x64 no VMware, Hyper-V, AWS, GCP, etc. A opção de replicação baseada em agente requer a instalação do software do agente no servidor/máquina virtual que está sendo migrado, enquanto que, na opção sem agente, nenhum software precisa ser instalado nas máquinas virtuais, oferecendo, assim, conveniência e simplicidade adicionais sobre a opção de replicação baseada em agente.

A opção de replicação sem agente funciona usando mecanismos fornecidos pelo provedor de virtualização (VMware, Hyper-V). No caso de máquinas virtuais VMware, o mecanismo de replicação sem agente usa os instantâneos do VMware e a tecnologia de rastreamento de bloco alterado pela VMware para replicar dados de discos de máquina virtual. Esse mecanismo é semelhante ao usado por muitos produtos de backup. No caso de máquinas virtuais do Hyper-V, o mecanismo de replicação sem agente usa instantâneos de VM e o recurso de controle de alterações da réplica do Hyper-V para replicar dados de discos de máquina virtual.

Quando a replicação é configurada para uma máquina virtual, ela passa pela primeira vez por uma fase de replicação inicial. Durante a replicação inicial, um instantâneo de VM é obtido e uma cópia completa dos dados dos discos de instantâneo é replicada para discos gerenciados em sua assinatura. Após a conclusão da replicação inicial da VM, o processo de replicação faz a transição para uma fase de replicação incremental (replicação delta). Na fase de replicação incremental, as alterações de dados ocorridas desde o último ciclo de replicação concluído são periodicamente replicadas e aplicadas aos discos gerenciados de réplica, mantendo assim a replicação em sincronia com as alterações que ocorrem na VM. No caso de máquinas virtuais VMware, a tecnologia de rastreamento de bloco alterada VMware é usada para manter o controle das alterações entre os ciclos de replicação. No início do ciclo de replicação, um instantâneo de VM é obtido e o controle de bloco alterado é usado para obter as alterações entre o instantâneo atual e o último instantâneo replicado com êxito. Dessa forma, somente os dados que foram alterados desde o último ciclo de replicação concluído precisam ser replicados para manter a replicação da VM em sincronia. No final de cada ciclo de replicação, o instantâneo é liberado e a consolidação de instantâneo é executada para a máquina virtual. Da mesma forma, no caso de máquinas virtuais do Hyper-V, o mecanismo de controle de alterações da réplica do Hyper-V é usado para manter o controle das alterações entre os ciclos de replicação consecutivos.
Ao executar a operação de migração em uma máquina virtual de replicação, você tem a opção de desligar a máquina virtual local e executar uma replicação incremental final para garantir zero perda de dados. Ao executar a opção Migrar, os discos gerenciados de réplica correspondentes à máquina virtual são usados para criar a máquina virtual no Azure.

Para começar, consulte os tutoriais de migração sem agente e de [migração do Hyper-V](./tutorial-migrate-hyper-v.md) sem agente do [VMware](./tutorial-migrate-vmware.md) .

## <a name="how-does-agent-based-migration-work"></a>Como funciona a migração baseada em agente?

Além das opções de migração sem agente para máquinas virtuais VMware e máquinas virtuais do Hyper-V, a ferramenta de migração de servidor fornece uma opção de migração baseada em agente para migrar servidores Windows e Linux em execução em servidores físicos ou em execução como máquinas virtuais x86/x64 no VMware, Hyper-V, AWS, Google Cloud Platform, etc.

O método de migração baseado em agente usa o software do agente instalado no servidor que está sendo migrado para replicar dados do servidor no Azure. O processo de replicação usa uma arquitetura de descarregamento na qual o agente retransmite dados de replicação para um servidor de replicação dedicado chamado de dispositivo de replicação ou servidor de configuração (ou para um servidor de processo de expansão). [Saiba mais](./agent-based-migration-architecture.md) sobre como a opção de migração baseada em agente funciona. 

Observação: o dispositivo de replicação é diferente do dispositivo de descoberta de migrações para Azure e deve ser instalado em um computador separado/dedicado.

## <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>Como fazer medir o requisito de largura de banda para minhas migrações?

A largura de banda para replicação de dados para o Azure depende de uma variedade de fatores e é uma função de quão rápido o dispositivo de migração do Azure local pode ler e replicar os dados no Azure. A replicação tem duas fases: replicação inicial e replicação delta.

Quando a replicação é iniciada para uma VM, ocorre um ciclo de replicação inicial no qual as cópias completas dos discos são replicadas. Após a conclusão da replicação inicial, os ciclos de replicação incremental (ciclos Delta) são agendados periodicamente para transferir as alterações ocorridas desde o ciclo de replicação anterior.

### <a name="agentless-vmware-vm-migration"></a>Migração de VM do VMware sem agente

Você pode solucionar o requisito de largura de banda com base no volume de dados necessário para ser movido na onda e no horário em que você deseja que a replicação inicial seja concluída (o ideal é que você queira que a replicação inicial tenha concluído pelo menos 3-4 dias antes da janela de migração real para dar tempo suficiente para executar uma migração de teste antes da janela real e para manter o período de inatividade

Você pode estimar a largura de banda ou o tempo necessário para a migração de VM do VMware sem agente usando a seguinte fórmula:

Tempo para concluir a replicação inicial = {tamanho dos discos (ou tamanho usado, se disponível) * 0,7 (supondo uma média de compactação de 30 por cento – estimativa conservadora)}/Bandwidth disponíveis para replicação.

### <a name="agent-based-vmware-vm-migration"></a>Migração de VM VMware baseada em agente

Para um método de replicação baseado em agente, o planejador de implantação pode ajudar a criar o perfil do ambiente para a rotatividade de dados e ajudar a prever o requisito necessário de largura de banda. Para saber mais, veja este [artigo](./agent-based-migration-architecture.md#plan-vmware-deployment). 

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Como fazer a replicação de limitação no uso do dispositivo de migração do Azure para replicação do VMware sem agente?  

Você pode limitar usando NetQosPolicy. Por exemplo:

O AppNamePrefix a ser usado no NetQosPolicy é "GatewayWindowsService.exe". Você pode criar uma política no dispositivo de migrações para Azure para limitar o tráfego de replicação do dispositivo criando uma política como esta:

New-NetQosPolicy-Name "ThrottleReplication"-AppPathNameMatchCondition "GatewayWindowsService.exe"-ThrottleRateActionBitsPerSecond 1 MB

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>Como os dados são transmitidos do ambiente local para o Azure? Ele é criptografado antes da transmissão?

O dispositivo migrações para Azure no caso de replicação sem agente compacta dados e criptografa antes de carregar. Os dados são transmitidos por um canal de comunicação seguro por HTTPS e usam o TLS 1,2 ou posterior. Além disso, o armazenamento do Azure criptografa automaticamente os dados quando eles são persistidos para a nuvem (criptografia em repouso).  

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Como a taxa de rotatividade afeta a replicação sem agente?

Como a replicação sem agente dobra em dados, o *padrão de rotatividade* é mais importante do que a *taxa de rotatividade*. Quando um arquivo é gravado novamente e novamente, a taxa não tem muito impacto. No entanto, um padrão no qual cada outro setor é escrito causa alta rotatividade no próximo ciclo. Como minimizamos a quantidade de dados que transferimos, permitimos que os dados dobrem o máximo possível antes de agendarmos o próximo ciclo.

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Com que frequência um ciclo de replicação é agendado?

A fórmula para agendar o próximo ciclo de replicação é (tempo de ciclo anterior/2) ou uma hora, o que for maior.

Por exemplo, se uma VM levar quatro horas para um ciclo Delta, o próximo ciclo será agendado em duas horas, e não na próxima hora. O processo é diferente imediatamente após a replicação inicial, quando o primeiro ciclo Delta é agendado imediatamente.

## <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>Como fazer migrar o Windows Server 2003 em execução no VMware/Hyper-V para o Azure?

O [suporte estendido do Windows Server 2003](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) terminou em 14 de julho de 2015.  A equipe de suporte do Azure continua ajudando a solucionar problemas que se preocupam em executar o Windows Server 2003 no Azure. No entanto, esse suporte é limitado a problemas que não exigem patches ou solução de problemas no nível do sistema operacional.
Migrar seus aplicativos para instâncias do Azure que executam uma versão mais recente do Windows Server é a abordagem recomendada para garantir que você esteja efetivamente aproveitando a flexibilidade e a confiabilidade da nuvem do Azure.

No entanto, se você ainda optar por migrar seu Windows Server 2003 para o Azure, poderá usar a ferramenta migrações para Azure: Server Migration se o seu Windows Server for uma VM em execução no VMware ou no Hyper-V examine este artigo para [preparar seus computadores com o Windows Server 2003 para migração](./prepare-windows-server-2003-migration.md).

## <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>Qual é a diferença entre as operações de migração de teste e de migração?

A migração de teste fornece uma maneira de testar e validar as migrações antes da migração real. A migração de teste funciona permitindo que você crie cópias de teste de replicação de VMs em um ambiente de área restrita no Azure. O ambiente de área restrita é demarcadas por uma rede virtual de teste que você especificar. A operação de migração de teste não causa interrupções, com aplicativos que continuam a ser executados na origem, permitindo que você execute testes em uma cópia clonada em um ambiente de área restrita isolada. Você pode executar vários testes conforme necessário para validar a migração, executar testes de aplicativos e resolver problemas antes da migração real.

## <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>Haverá suporte para o Windows Server 2008 e 2008 R2 no Azure após a migração?

Você pode migrar seus servidores do Windows Server 2008 e 2008 R2 locais para máquinas virtuais do Azure e obter atualizações de segurança estendidas por três anos após o fim das datas de suporte sem custo adicional acima do custo da execução da máquina virtual. Você pode usar a ferramenta migrações para Azure: migração de servidor para migrar suas cargas de trabalho do Windows Server 2008 e 2008 R2.

## <a name="is-there-a-rollback-option-for-azure-migrate"></a>Há uma opção de reversão para migrações para Azure?

Você pode usar a opção de migração de teste para validar a funcionalidade e o desempenho do aplicativo no Azure. Você pode executar qualquer número de migrações de teste e pode executar a migração final depois de estabelecer a confiança por meio da operação de migração de teste. Uma migração de teste não afeta o computador local, que permanece operacional e continua replicando até que você execute a migração real. Se houver erros durante o UAT de migração de teste, você poderá optar por adiar a migração final e manter a VM/servidor de origem em execução e replicando no Azure. Você pode tentar novamente a migração final depois de resolver os erros.  
Observação: depois de executar uma migração final para o Azure e a máquina de origem local ser desligada, você não poderá executar uma reversão do Azure para seu ambiente local.

## <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>Posso selecionar a rede virtual e a sub-rede a serem usadas para migrações de teste?

Você pode selecionar uma rede virtual para migrações de teste. A sub-rede é selecionada automaticamente com base na seguinte lógica:

- Se uma sub-rede de destino (diferente de padrão) tiver sido especificada como uma entrada ao habilitar a replicação, as migrações para Azure priorizarão o uso de uma sub-rede com o mesmo nome na rede virtual selecionada para a migração de teste.
- Se a sub-rede com o mesmo nome não for encontrada, as migrações para Azure selecionarão a primeira sub-rede disponível em ordem alfabética que não é uma sub-rede gateway/gateway de aplicativo/firewall/bastiões.

## <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>Por que o botão de migração de teste está desabilitado para meu servidor?

O botão de migração de teste pode estar em um estado desabilitado nos seguintes cenários:

- Você não pode iniciar uma migração de teste até que uma replicação inicial (IR) tenha sido concluída para a VM. O botão de migração de teste será desabilitado até que o processo IR seja concluído. Você pode executar uma migração de teste quando sua VM estiver em um estágio de sincronização Delta.
- O botão pode ser desabilitado se uma migração de teste já foi concluída, mas uma limpeza de migração de teste não foi executada para essa VM. Execute uma limpeza de migração de teste e repita a operação.

## <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>O que acontece se eu não limpar minha migração de teste?

A migração de teste simula a migração real criando um teste de VM do Azure usando dados replicados. O servidor será implantado com uma cópia pontual dos dados replicados no grupo de recursos de destino (selecionado ao habilitar a replicação) com um sufixo "-Test". As migrações de teste destinam-se à validação da funcionalidade do servidor para que os problemas após a migração sejam minimizados. Se a migração de teste não for limpa após o teste, a máquina virtual de teste continuará a ser executada no Azure e incorrerá em encargos. Para limpar o post a migração de teste, vá para a exibição máquinas de replicação na ferramenta de migração de servidor e use a ação ' limpar migração de teste ' no computador.

## <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>Posso migrar Active Directory controladores de domínio usando as migrações para Azure?

A ferramenta de migração de servidor é independente de aplicativo e funciona para a maioria dos aplicativos. Quando você migra um servidor usando a ferramenta de migração de servidor, todos os aplicativos instalados no servidor são migrados junto com ele. No entanto, para alguns aplicativos, métodos alternativos de migração diferentes da migração do servidor podem ser mais adequados para a migração.  Por Active Directory, no caso de ambientes híbridos em que o site local está conectado ao seu ambiente do Azure, você pode estender seu diretório no Azure adicionando mais controladores de domínio no Azure e configurando a replicação Active Directory. Se você estiver migrando para um ambiente isolado no Azure exigindo seus próprios controladores de domínio (ou testando aplicativos em um ambiente de área restrita), poderá migrar servidores usando a ferramenta de migração de servidor.

## <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>O que acontecerá se eu não parar a replicação após a migração?

Quando você interrompe a replicação, a ferramenta migrações para Azure: servidor de migração limpa os discos gerenciados na assinatura que foram criados para replicação. Se você não parar a replicação após uma migração, você continuará a incorrer em encargos para esses discos. Interromper a replicação não afetará os discos anexados aos computadores que já foram migrados.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Preciso do VMware vCenter para migrar VMs VMware?

Para [migrar VMs VMware](server-migrate-overview.md) usando a migração baseada em agente do VMware ou sem agente, os hosts ESXi nos quais as VMs estão localizadas devem ser gerenciados pelo vCenter Server. Se você não tiver vCenter Server, poderá migrar VMs VMware migrando-as como servidores físicos. [Saiba mais](migrate-support-matrix-physical-migration.md).

## <a name="can-i-upgrade-my-os-while-migrating"></a>Posso atualizar meu sistema operacional durante a migração?

A ferramenta migrações para Azure: Server Migration só dá suporte a migrações semelhantes a semelhantes. A ferramenta não dá suporte à atualização da versão do sistema operacional durante a migração. O computador migrado terá o mesmo sistema operacional que o computador de origem.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>Implantei dois (ou mais) dispositivos para descobrir VMs em meu vCenter Server. No entanto, quando tento migrar as VMs, só vejo as VMs correspondentes a um dos dispositivos.

Se houver vários dispositivos configurados, será necessário que não haja nenhuma sobreposição entre as VMs nas contas do vCenter fornecidas. Uma descoberta com essa sobreposição é um cenário sem suporte.

## <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>Como fazer saber se minha VM foi migrada com êxito?

Depois de migrar a VM/servidor com êxito, você poderá exibir e gerenciar a VM na página máquinas virtuais. Conecte-se à VM migrada para validá-la.
Como alternativa, você pode examinar o ' status do trabalho ' para a operação a fim de verificar se a migração foi concluída com êxito. Se você encontrar erros, resolva-os e repita a operação de migração.

## <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>Posso consolidar várias VMs de origem em uma VM durante a migração?

Os recursos de migração do servidor de migrações para Azure oferecem suporte como para migrações no momento. Não damos suporte à consolidação de servidores nem à atualização do sistema operacional como parte da migração. 

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Como a replicação sem agente afeta os servidores VMware?

A replicação sem agente resulta em algum impacto no desempenho em hosts VMware vCenter Server e VMware ESXi. Como a replicação sem agente usa instantâneos, ela consome IOPS no armazenamento, portanto, é necessária alguma largura de banda de armazenamento de IOPS. Não recomendamos o uso da replicação sem agente se você tiver restrições de armazenamento ou IOPs em seu ambiente.


## <a name="next-steps"></a>Próximas etapas

Leia a [visão geral de migrações para Azure](migrate-services-overview.md).
