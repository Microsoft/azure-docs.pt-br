---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: cbd86571cbdcd600ef3acdea3833568a34657931
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337910"
---
**Última atualização do documento**: 14 de agosto de 2018 10 10h PST.

A divulgação de uma [nova classe de vulnerabilidades de CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) conhecida como ataques de canal paralelo de execução especulativa resultou em várias perguntas dos clientes que queriam mais esclarecimentos sobre o assunto.  

A Microsoft implantou atenuações em todos os nossos serviços de nuvem. A infraestrutura que executa o Azure e isola as cargas de trabalho do cliente entre elas está protegida. Isso significa que um invasor potencial que usa a mesma infraestrutura não pode atacar seu aplicativo usando essas vulnerabilidades.

O Azure usa a [manutenção da preservação da memória](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot) sempre que possível, para minimizar o impacto para o cliente e eliminar a necessidade de reinicializações. O Azure continuará utilizando esses métodos ao fazer atualizações em todo o sistema do host e proteger nossos clientes.

Mais informações sobre como a segurança é integrada em todos os aspectos do Azure estão disponíveis no site [Documentação de segurança do Azure](https://docs.microsoft.com/azure/security/). 

> [!NOTE] 
> Desde a primeira publicação deste documento, foram divulgadas várias variantes dessa classe de vulnerabilidade. A Microsoft continua investindo intensamente na proteção de nossos clientes e no fornecimento de diretrizes. Esta página será atualizada conforme continuamos liberando correções adicionais. 
> 
> Em 14 de agosto de 2018, o setor divulgou uma nova vulnerabilidade de canal paralelo de execução especulativa conhecida como [L1TF](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018) (Falha do Terminal L1), à qual foram atribuídos vários CVEs ([CVE-2018-3615, CVE-2018-3620 e CVE-2018-3646](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00161.html)). Essa vulnerabilidade afeta os processadores Intel® Core® e Intel® Xeon®. A Microsoft implantou mitigações em nossos serviços de nuvem que reforçam o isolamento entre os clientes. Leia abaixo as diretrizes adicionais para proteção contra a L1TF e as vulnerabilidades anteriores ([Variante de Espectro 2 CVE-2017-5715 e Variante de Meltdown 3 CVE-2017-5754](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)).
>  






## <a name="keeping-your-operating-systems-up-to-date"></a>Mantenha seus Sistemas Operacionais atualizados

Embora uma atualização do sistema operacional não seja necessária para isolar os aplicativos em execução no Azure de outros clientes do Azure, é sempre uma melhor prática manter o software atualizado. Os últimos Pacotes Cumulativos de Atualizações de Segurança para o Windows contêm mitigações de várias vulnerabilidades de canal paralelo de execução especulativa. Da mesma forma, as distribuições Linux liberaram várias atualizações para resolver essas vulnerabilidades. Estas são nossas ações recomendadas para atualizar o Sistema Operacional:

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

Habilite recursos de segurança adicionais na VM ou no Serviço de Nuvem.

### <a name="windows"></a> Windows 

O sistema operacional de destino precisa estar atualizado para habilitar esses recursos de segurança adicionais. Embora várias mitigações de ataques de canal paralelo de execução especulativa estejam habilitadas por padrão, os recursos adicionais descritos aqui precisam ser habilitados manualmente e podem causar um impacto no desempenho. 

**Etapa 1**: [Entre em contato com o suporte do Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para expor atualizado o firmware (microcódigo) em suas máquinas virtuais. 

**Etapa 2**: Habilite o suporte de Kernel virtuais endereço sombreamento (KVAS) e o sistema operacional de injeção de destino de ramificação (BTI). Siga as instruções descritas em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para habilitar proteções por meio das chaves do Registro do `Session Manager`. Uma reinicialização é necessária. 

**Etapa 3**: Para implantações que usam [virtualização aninhada](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 e E3 somente): Essas instruções se aplicam dentro da VM que você está usando como um host Hyper-V. 

1. Siga as instruções descritas em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para habilitar proteções por meio das chaves do Registro do `MinVmVersionForCpuBasedMitigations`.  
 
1. Defina o tipo de agendador do hipervisor como **Núcleo** seguindo as instruções descritas [aqui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types). 

**Etapa 4**: Siga as instruções em [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para verificar se as proteções estão habilitadas usando o [SpeculationControl](https://aka.ms/SpeculationControlPS) módulo do PowerShell. 

> [!NOTE]
> Se você já baixou este módulo, precisará instalar a versão mais recente.
>

Todas as VMs devem mostrar:

```
branch target injection mitigation is enabled: True

kernel VA shadow is enabled: True  

L1TFWindowsSupportEnabled: True
```


### <a name="linux"></a>Linux

<a name="linux"></a>A habilitação do conjunto de recursos de segurança adicionais internamente exige que o sistema operacional de destino esteja totalmente atualizado. Algumas mitigações serão habilitadas por padrão. A seção a seguir descreve os recursos que estão desativados por padrão e/ou que são dependentes de suporte de hardware (microcódigo). A habilitação desses recursos pode causar um impacto no desempenho. Referencie a documentação do provedor do sistema operacional para obter mais instruções
 
**Etapa 1**: [Entre em contato com o suporte do Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) para expor atualizado o firmware (microcódigo) em suas máquinas virtuais.
 
**Etapa 2**: Habilite o suporte de injeção de destino da ramificação (BTI) do sistema operacional atenuar a CVE-2017-5715 (Spectre Variant 2), seguindo a documentação do provedor do sistema operacional. 
 
**Etapa 3**: Habilite o Kernel página tabela isolamento (kpti) e para reduzir a CVE-2017-5754 (Meltdown Variant 3), seguindo a documentação do provedor do sistema operacional. 
 
Mais informações estão disponíveis no provedor do sistema operacional:  
 
- [Red Hat e CentOS](https://access.redhat.com/security/vulnerabilities/speculativeexecution) 
- [SUSE](https://www.suse.com/support/kb/doc/?id=7022512) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/SpectreAndMeltdown) 


## <a name="next-steps"></a>Próximos passos

Para saber mais, confira [Protegendo os clientes do Azure de vulnerabilidades de CPU](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).
