---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/04/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 169e25aeb8503a11f768a2a3062022eef51a76b8
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659734"
---
**Última atualização do documento**: 4 de junho de 2019 3 18H PST.

A divulgação de uma [nova classe de vulnerabilidades de CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) conhecida como ataques de canal paralelo de execução especulativa resultou em várias perguntas dos clientes que queriam mais esclarecimentos sobre o assunto.  

A Microsoft implantou atenuações em todos os nossos serviços de nuvem. A infraestrutura que executa o Azure e isola as cargas de trabalho do cliente entre elas está protegida. Isso significa que um invasor potencial que usa a mesma infraestrutura não pode atacar seu aplicativo usando essas vulnerabilidades.

O Azure usa a [manutenção da preservação da memória](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) sempre que possível, para minimizar o impacto para o cliente e eliminar a necessidade de reinicializações. O Azure continuará utilizando esses métodos ao fazer atualizações em todo o sistema do host e proteger nossos clientes.

Mais informações sobre como a segurança é integrada em todos os aspectos do Azure estão disponíveis no site [Documentação de segurança do Azure](https://docs.microsoft.com/azure/security/). 

> [!NOTE] 
> Desde a primeira publicação deste documento, foram divulgadas várias variantes dessa classe de vulnerabilidade. A Microsoft continua investindo intensamente na proteção de nossos clientes e no fornecimento de diretrizes. Esta página será atualizada conforme continuamos liberando correções adicionais. 
> 
> No dia 14 de maio de 2019 [Intel divulgada](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html) um novo conjunto de vulnerabilidade de canal do lado de execução especulativa conhecido como microarquitetura amostragem de dados (MDS consulte as diretrizes de segurança da Microsoft [ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)), que recebeu vários CVEs: 
> - CVE-2019-11091 - dados microarquitetura amostragem de memória Uncacheable (MDSUM)
> - CVE-2018-12126 - Store microarquitetura Buffer os dados de amostragem (MSBDS) 
> - CVE-2018-12127 - dados de carga da porta microarquitetura amostragem (MLPDS)
> - CVE-2018-12130 - microarquitetura preenchimento Buffer os dados de amostragem (MFBDS)
>
> Essa vulnerabilidade afeta os processadores Intel® Core® e Intel® Xeon®.  Microsoft Azure lançou atualizações do sistema operacional e está implantando novos microcódigo, conforme ele é disponibilizado pela Intel, em toda nossa frota para proteger nossos clientes em relação a essas novas vulnerabilidades.   Azure está trabalhando intimamente Intel para testar e validar o novo microcódigo, antes de seu lançamento oficial na plataforma. 
>
> **Os clientes que estão em execução não confiáveis de código dentro de sua VM** precisa tomar medidas para proteger contra essas vulnerabilidades lendo abaixo para obter orientação adicional sobre todas as vulnerabilidades de canal lateral de execução especulativa (Microsoft consultorias ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018), e [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Outros clientes devem avaliar essas vulnerabilidades de uma linha de defesa na perspectiva de profundidade e considerar as implicações de segurança e desempenho de sua configuração escolhida.



## <a name="keeping-your-operating-systems-up-to-date"></a>Manter seus sistemas operacionais atualizados

Embora uma atualização do sistema operacional não seja necessária para isolar os aplicativos em execução no Azure de outros clientes do Azure, é sempre uma melhor prática manter o software atualizado. Os últimos Pacotes Cumulativos de Atualizações de Segurança para o Windows contêm mitigações de várias vulnerabilidades de canal paralelo de execução especulativa. Da mesma forma, as distribuições Linux liberaram várias atualizações para resolver essas vulnerabilidades. Aqui estão nossas ações recomendadas para atualizar o sistema operacional:

| Oferta | Ação recomendada  |
|----------|---------------------|
| Serviços de nuvem do Azure  | Habilite a [atualização automática](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) ou verifique se você está executando o Sistema Operacional Convidado mais recente. |
| Máquinas Virtuais do Linux do Azure | Instale atualizações do provedor do sistema operacional. Para obter mais informações, confira [Linux](#linux) mais adiante neste documento. |
| Máquinas Virtuais do Windows do Azure  | Instale o pacote cumulativo de atualizações de segurança mais recente.
| Outros Serviços de PaaS do Azure | Não é necessária nenhuma ação para clientes que usam esses serviços. O Azure mantém automaticamente suas versões de Sistema Operacional atualizadas. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Orientações adicionais se você estiver executando código não confiável 

Os clientes que permitem que usuários não confiáveis executem um código arbitrário podem desejar implementar alguns recursos de segurança adicionais nas Máquinas Virtuais do Azure ou nos Serviços de Nuvem. Esses recursos protegem contra vetores de divulgação dentro do processo descritos por várias vulnerabilidades de execução especulativa.

Cenários de exemplo em que os recursos de segurança adicionais são recomendados:

- Você permite que um código não confiável seja executado em sua VM.  
    - *Por exemplo, você permite que um de seus clientes carregue um binário ou um script que você, em seguida, executa no aplicativo*. 
- Você permite que os usuários não confiáveis façam logon em sua VM usando contas com baixos privilégios.   
    - *Por exemplo, você permite que um usuário com poucos privilégios faça logon em uma de suas VMs usando a área de trabalho remota ou o SSH*.  
- Você permite que usuários não confiáveis acessem máquinas virtuais implementadas por meio da virtualização aninhada.  
    - *Por exemplo, você controla o host Hyper-V, mas aloca as VMs a usuários não confiáveis*. 

Os clientes que não implementam um cenário que envolva um código não confiável não precisam habilitar esses recursos de segurança adicionais. 

## <a name="enabling-additional-security"></a>Habilitando a segurança adicional 

Se você estiver executando o código não confiável, você pode habilitar recursos de segurança adicionais dentro de sua VM ou serviço de nuvem. Em paralelo, verifique se que seu sistema operacional é atualizado para habilitar os recursos de segurança dentro de sua VM ou serviço de nuvem

### <a name="windows"></a>Windows 

O sistema operacional de destino precisa estar atualizado para habilitar esses recursos de segurança adicionais. Embora várias mitigações de ataques de canal paralelo de execução especulativa estejam habilitadas por padrão, os recursos adicionais descritos aqui precisam ser habilitados manualmente e podem causar um impacto no desempenho. 


**Etapa 1: Desabilitar o hyper-threading na VM** -os clientes que executam código não confiável em uma VM do hyper-threaded serão preciso desabilitar o hyper-threading ou mover para um tamanho VM não-hyper-threading. Referência [este documento](https://docs.microsoft.com/azure/virtual-machines/windows/acu) para obter uma lista dos tamanhos de VM hyper-threading (em que proporção de vCPU para núcleo é 2:1). Para verificar se sua VM tiver o hyper-threading habilitado, consulte o script usando a linha de comando do Windows de dentro da VM abaixo.

Tipo `wmic` para entrar na interface interativa. Digite abaixo para exibir a quantidade de física e lógica processadores na máquina virtual.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Se o número de processadores lógicos for maior do que processadores físicos (núcleos), em seguida, hyper-threading está habilitada.  Se você estiver executando uma VM hyper-threading, por favor [entre em contato com o suporte do Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para obter hyperthreading desabilitado.  Depois que o hyper-threading estiver desabilitada, **o suporte exigirá uma reinicialização completa da VM**. Consulte [contagem de núcleo](#core-count) para entender por que a contagem de núcleos VM é reduzido.


**Etapa 2**: Em paralelo à etapa 1, siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para verificar se as proteções estão habilitadas usando o [SpeculationControl](https://aka.ms/SpeculationControlPS) módulo do PowerShell.

> [!NOTE]
> Se você já baixou este módulo, precisará instalar a versão mais recente.
>


A saída do script do PowerShell deve ter os valores para validar abaixo habilitado proteções contra essas vulnerabilidades:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

Se a saída mostra `MDS mitigation is enabled: False`, por favor [entre em contato com o suporte do Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para as opções de atenuação disponíveis.



**Etapa 3**: Para habilitar o suporte de injeção de destino da ramificação (BTI) do sistema operacional e de Kernel virtuais endereço sombreamento (KVAS), siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para habilitar proteções usando o `Session Manager` chaves do registro. Uma reinicialização é necessária.


**Etapa 4**: Para implantações que usam [virtualização aninhada](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 e E3 somente): Essas instruções se aplicam dentro da VM que você está usando como um host Hyper-V.

1.  Siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para habilitar proteções usando o `MinVmVersionForCpuBasedMitigations` chaves do registro.
2.  Defina o tipo de Agendador do hipervisor `Core` seguindo as instruções [aqui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>A habilitação do conjunto de recursos de segurança adicionais internamente exige que o sistema operacional de destino esteja totalmente atualizado. Algumas mitigações serão habilitadas por padrão. A seção a seguir descreve os recursos que estão desativados por padrão e/ou que são dependentes de suporte de hardware (microcódigo). A habilitação desses recursos pode causar um impacto no desempenho. Referencie a documentação do provedor do sistema operacional para obter mais instruções


**Etapa 1: Desabilitar o hyper-threading na VM** -os clientes que executam código não confiável em uma VM do hyper-threaded serão necessário desabilitar o hyper-threading ou mover para uma VM não-hyper-threading.  Referência [este documento](https://docs.microsoft.com/azure/virtual-machines/linux/acu) para obter uma lista dos tamanhos de VM hyper-threading (em que proporção de vCPU para núcleo é 2:1). Para verificar se você estiver executando uma VM hyper-threading, execute o `lscpu` comando na VM do Linux. 

Se `Thread(s) per core = 2`, em seguida, hyper-threading foi habilitada. 

Se `Thread(s) per core = 1`, em seguida, hyper-threading foi desabilitada. 

 
Exemplo de saída para uma VM com o hyper-threading habilitado: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Se você estiver executando uma VM hyper-threading, por favor [entre em contato com o suporte do Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para obter hyperthreading desabilitado.  Depois que o hyper-threading estiver desabilitada, **o suporte exigirá uma reinicialização completa da VM**. Consulte [contagem de núcleo](#core-count) para entender por que a contagem de núcleos VM é reduzido.



**Etapa 2**: Para atenuar qualquer um do abaixo de vulnerabilidades de canal lateral de execução especulativa, consulte a documentação do provedor do sistema operacional:   
 
- [Red Hat e CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Contagem de núcleos

Quando uma VM hyper-threading é criada, o Azure aloca 2 threads por núcleo – eles são chamados de vCPUs. Quando o hyper-threading estiver desabilitada, o Azure remove um thread e superfícies de núcleos single-threaded (núcleos físicos). A proporção de vCPU para a CPU é de 2:1, assim que a hyper-threading estiver desabilitada, a contagem de CPU na VM parecerão ter diminuído pela metade. Por exemplo, uma VM D8_v3 é uma VM hyper-threading em execução em 8 vCPUs (2 threads por núcleos core x 4).  Quando o hyper-threading estiver desabilitada, CPUs descartará a 4 núcleos físicos com 1 thread por núcleo. 

## <a name="next-steps"></a>Próximas etapas

Este artigo fornece orientações para o abaixo de ataques de canal lateral de execução especulativa que afetam o número de processadores modernos:

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 - injeção de ramificação de destino (BTI)  
- CVE-2017-5754 - o isolamento de tabela de página de Kernel (kpti e)
- CVE-2018-3639 – Store especulativa Bypass (kpti e) 
 
[L1 Falha de Terminal (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 - extensões de proteção de Software do Intel (Intel SGX)
- CVE-2018-3620 - sistemas operacionais (SO) e o modo de gerenciamento do sistema (SMM)
- CVE-2018-3646 – impactos do Virtual Machine Manager (VMM)

[Amostragem de dados microarquitetura](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 - dados microarquitetura amostragem de memória Uncacheable (MDSUM)
- CVE-2018-12126 - Store microarquitetura Buffer os dados de amostragem (MSBDS)
- CVE-2018-12127 - dados de carga da porta microarquitetura amostragem (MLPDS)
- CVE-2018-12130 - microarquitetura preenchimento Buffer os dados de amostragem (MFBDS)








