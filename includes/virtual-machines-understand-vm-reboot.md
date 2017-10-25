As VMs (máquinas virtuais) do Azure às vezes podem reinicializar sem motivo aparente, sem prova de que você iniciou a operação de reinicialização. Este artigo lista as ações e os eventos que podem fazer com que as VMs reinicializem e forneçam informações sobre como evitar problemas de reinicialização inesperada ou reduzir o impacto desse tipo de problema.

## <a name="configure-the-vms-for-high-availability"></a>Configurar as VMs para alta disponibilidade
A melhor maneira de proteger um aplicativo em execução no Azure contra reinicializações de VM e tempo de inatividade é configurar as máquinas virtuais para alta disponibilidade.

Para oferecer esse nível de redundância ao seu aplicativo, recomendamos que agrupe uma ou mais VMs em um conjunto de disponibilidade. Essa configuração garante que durante um evento de manutenção planejada ou não planejada, pelo menos uma VM estará disponível e atenderá os 99,95% de [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Para saber mais sobre conjuntos de disponibilidade, confira os seguintes artigos:

- [Gerenciar a disponibilidade das VMs](../articles/virtual-machines/windows/manage-availability.md)
- [Configurar a disponibilidade das VMs](../articles/virtual-machines/windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Informações do Resource Health 
O Azure Resource Health é um serviço que expõe a integridade de recursos individuais do Azure e fornece diretrizes acionáveis para solucionar problemas. Em um ambiente de nuvem em que não é possível acessar diretamente os servidores ou elementos de infraestrutura, a meta do Resource Health é reduzir o tempo gasto com solução de problemas. O objetivo é, especificamente, reduzir o tempo gasto determinando se a causa do problema está no aplicativo ou em um evento da plataforma Azure. Para saber mais, confira [Compreender e usar o Resource Health](../articles/resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Ações e eventos que podem fazer a VM reiniciar

### <a name="planned-maintenance"></a>Manutenção planejada
O Microsoft Azure realiza atualizações periodicamente em todo o mundo para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host subjacente a VMs. Muitas dessas atualizações, incluindo atualizações com preservação de memória, são realizadas sem nenhum impacto sobre as VMs ou os serviços de nuvem.

No entanto, algumas atualizações requerem uma reinicialização. Nesses casos, as VMs são desligadas enquanto renovamos a infraestrutura e depois são reiniciadas.

Para compreender o que é a manutenção planejada do Azure e como ela pode afetar a disponibilidade de suas VMs Linux, confira os artigos listados aqui. Os artigos fornecem informações detalhadas sobre o processo de manutenção planejada do Azure e como agendar a manutenção planejada para reduzir ainda mais o impacto.

- [Manutenção planejada para VMs no Azure](../articles/virtual-machines/windows/planned-maintenance.md)
- [Como agendar a manutenção planejada em VMs do Azure](../articles/virtual-machines/windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Atualizações que preservam a memória   
Para essa classe de atualizações no Microsoft Azure, os usuários não têm impacto em suas VMs em execução. Muitas dessas atualizações são componentes ou serviços que podem ser atualizados sem interferir na instância em execução. Algumas são as atualizações de infraestrutura de plataforma no sistema operacional do host que podem ser aplicadas sem a reinicialização das VMs.

Essas atualizações com preservação de memória são realizadas com a tecnologia que permite a migração ao vivo local. Quando ela está sendo atualizada, a VM é colocada em um estado *pausado*. Esse estado preserva a memória na RAM enquanto o sistema operacional do host subjacente recebe os patches e as atualizações necessárias. A máquina virtual é retomada dentro de 30 segundos após a pausa. Depois que a VM é reiniciada, seu relógio é sincronizado automaticamente.

Devido à pequena pausa, a implantação de atualizações por meio desse mecanismo reduz consideravelmente o impacto sobre as VMs. No entanto, nem todas as atualizações podem ser implantadas dessa maneira. 

Atualizações de múltiplas instâncias (para VMs em um conjunto de disponibilidade) são aplicadas em um domínio de atualização por vez.

> [!NOTE]
> As máquinas Linux que têm versões antigas do kernel são afetadas por um pânico de kernel durante esse método de atualização. Para evitar esse problema, atualize para o kernel versão 3.10.0-327.10.1 ou versão posterior. Para saber mais, confira [Uma VM Linux do Azure em um kernel com base em 3.10 sofre pane após uma atualização de nó host](https://support.microsoft.com/help/3212236).     
    
### <a name="user-initiated-reboot-or-shutdown-actions"></a>Ações de reinicialização ou desligamento iniciado pelo usuário
 
Se você executar uma reinicialização no portal do Azure, no Azure PowerShell, na interface de linha de comando ou na API de redefinição, poderá encontrar o evento no [Log de atividades do Azure](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Se você executar a ação do sistema operacional da VM, poderá encontrar o evento nos logs do sistema.

Outro cenário que geralmente faz com que a VM reinicie inclui várias ações de configuração. Você normalmente verá uma mensagem de aviso que indica que a execução de determinada ação resultará em uma reinicialização da VM. Exemplos incluem operações de redimensionamento da VM, alteração da senha da conta administrativa e definição de endereço IP estático.

### <a name="azure-security-center-and-windows-update"></a>Central de Segurança do Azure e Windows Update
A Central de Segurança do Azure monitora diariamente VMs Windows e Linux para saber se faltam atualizações do sistema operacional. A Central de Segurança recupera uma lista de atualizações críticas e de segurança disponíveis no Windows Update ou no WSUS (Windows Server Update Services), dependendo de qual serviço está configurado em uma VM do Windows. A Central de Segurança também verifica as atualizações mais recentes para sistemas Linux. Se faltar uma atualização do sistema em sua VM, a Central de Segurança recomendará que você aplique as atualizações do sistema. A aplicação dessas atualizações do sistema é controlada pela Central de Segurança no portal do Azure. Depois de aplicar algumas atualizações, pode ser necessário reiniciar algumas VMs. Para saber mais, confira [Aplicar atualizações do sistema na Central de Segurança do Azure](../articles/security-center/security-center-apply-system-updates.md).

Assim como os servidores locais, o Azure não envia atualizações do Windows Update por push para VMs do Microsoft Azure, pois elas se destinam a serem gerenciadas pelos usuários. No entanto, é recomendável que os clientes deixem a configuração automática do Windows Update habilitada. A instalação automática de atualizações do Windows Update pode causar reinicializações após a aplicação. Para saber mais, confira [Perguntas frequentes sobre o Windows Update](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Outras situações que afetam a disponibilidade da VM
Há outros casos em que o Azure pode suspender ativamente o uso de uma VM. Você receberá notificações de email antes da ação para ter a oportunidade de resolver problemas subjacentes. Violações de segurança e a expiração de métodos de pagamento são exemplos de problemas que afetam a disponibilidade da VM.

### <a name="host-server-faults"></a>Falhas do servidor host 
A máquina virtual está hospedada em um servidor físico que está em execução dentro de um datacenter do Azure. O servidor físico é executado em um agente chamado o Agente Host, além de alguns outros componentes do Azure. Quando esses componentes de software do Azure no servidor físico param de responder, o sistema de monitoramento dispara uma reinicialização do servidor host para tentar recuperá-los. A VM geralmente fica disponível novamente dentro de cinco minutos e continua a residir no mesmo host de antes.

As falhas de servidor normalmente são causadas por falha de hardware, como falha de um disco rígido ou de uma unidade de estado sólido. O Azure monitora essas ocorrências continuamente, identifica os bugs subjacentes e distribui atualizações depois que a mitigação é implantada e testada.

Como algumas falhas de servidor host podem ser específicas desse servidor, uma situação de reinicialização VM repetida pode ser melhorada com a reimplantação manual da VM em outro servidor host. Essa operação pode ser acionada usando a opção **reimplantação** na página de detalhes da VM ou interrompendo e reiniciando a máquina virtual no portal do Azure.

### <a name="auto-recovery"></a>Recuperação automática
Se o servidor host não puder ser reiniciado por qualquer motivo, a plataforma do Azure iniciará uma ação de recuperação automática para remover o servidor host e investigar. 

Todas as máquinas virtuais no host são realocadas automaticamente para um servidor host diferente e íntegro. O processo é concluído normalmente em 15 minutos. Para saber mais sobre o processo de recuperação automática, confira [Recuperação automática de VMs](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Manutenção não planejada
Em raras ocasiões, a equipe de operações do Azure pode precisar executar atividades de manutenção para garantir a integridade geral da plataforma Azure. Esse comportamento pode afetar a disponibilidade da VM e normalmente resulta na mesma ação de recuperação automática descrita anteriormente.  

Manutenções não planejadas incluem o seguinte:

- Desfragmentação de nó urgente
- Atualizações de comutador de rede urgentes

### <a name="vm-crashes"></a>Falhas de VM
Máquinas virtuais podem ser reiniciadas devido a problemas de dentro da VM. A carga de trabalho ou função em execução na VM pode disparar uma verificação de bug no sistema operacional convidado. Para ajudar a determinar o motivo da falha, exiba os logs de sistema e do aplicativo para VMs Windows e logs seriais para VMs Linux.

### <a name="storage-related-forced-shutdowns"></a>Desligamentos forçados relacionados ao armazenamento
As VMs no Azure dependem de discos virtuais para o sistema operacional e o armazenamento de dados que é hospedado na infraestrutura do Armazenamento do Azure. Sempre que a disponibilidade ou a conectividade entre a VM e os discos virtuais associados é afetada por mais de 120 segundos, a plataforma Azure executa um desligamento forçado das VMs para evitar dados corrompidos. As VMs são ativadas automaticamente novamente depois que a conectividade de armazenamento é restaurada. 

A duração do desligamento pode demorar mais de cinco minutos, mas também pode ser significativamente maior. A seguir temos um dos casos específicos que está associado a desligamentos forçados relacionados ao armazenamento: 

**Ultrapassando limites de E/S**

As VMs podem ser desligadas temporariamente quando as solicitações de E/S são limitadas consistentemente porque o volume de operações de IOPS (E/S por segundo) excede os limites de E/S do disco. (O armazenamento de disco padrão é limitado a 500 IOPS.) Para atenuar esse problema, use a distribuição de disco ou o espaço de armazenamento na VM convidada, dependendo da carga de trabalho. Para obter detalhes, consulte [Configurando VMs do Azure para otimizar o desempenho de armazenamento](http://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

Limites de IOPS maiores estão disponíveis no armazenamento Premium do Azure com até 80.000 IOPS. Para obter mais informações, consulte [Armazenamento Premium de alto desempenho](../articles/storage/common/storage-premium-storage.md).

### <a name="other-incidents"></a>Outros incidentes
Em raras circunstâncias, um problema mais amplo pode afetar vários servidores em um data center do Azure. Se esse problema ocorrer, a equipe do Azure envia notificações por email para assinaturas afetadas. Você pode verificar o [painel de integridade do serviço Azure](https://azure.microsoft.com/status/) e o portal do Azure para ver o status de interrupções e incidentes passados.
