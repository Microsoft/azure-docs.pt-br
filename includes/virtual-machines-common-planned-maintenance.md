O Azure realiza atualizações periodicamente para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host para máquinas virtuais. Essas atualizações vão desde a aplicação de patch de componentes de software no ambiente de hospedagem (como sistema operacional, hipervisor e vários agentes implantados no host), a atualização de componentes de rede até o encerramento de hardware. A maioria dessas atualizações é realizada sem nenhum impacto nas máquinas virtuais hospedadas. No entanto, há casos em que as atualizações possuem um impacto:

- Se a manutenção não exigir uma reinicialização, o Azure usa migração in-loco para pausar a máquina virtual enquanto o host está atualizado.

- Se a manutenção requer uma reinicialização, você receberá um aviso informando para quando a manutenção está planejada. Nesses casos, você também terá uma janela de tempo, onde você pode iniciar a manutenção, em um momento que mais oportuno para você.

Esta página descreve como o Microsoft Azure executa os dois tipos de manutenção. Para obter mais informações sobre eventos não planejados (interrupções), consulte Gerenciar a disponibilidade das máquinas virtuais para [Windows] (../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).

Aplicativos em execução em uma máquina virtual podem coletar informações sobre atualizações futuras por meio do Serviço de Metadados do Azure para [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) ou [Linux] (... / articles/virtual-machines/linux/instance-metadata-service.md).

Para obter instruções sobre como gerenciar a manutenção planejada, consulte “Administrando notificações de manutenção planejada” para [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="in-place-vm-migration"></a>Migração de VM local

Quando as atualizações não exigem uma reinicialização completa, uma migração ao vivo local é usada. Durante a atualização a máquina virtual será pausada por aproximadamente 30 segundos, preservando a memória RAM, enquanto o ambiente de hospedagem aplica os patches e as atualizações necessárias. A máquina virtual é reiniciada e o relógio da máquina virtual é sincronizado automaticamente.

Para VMs em conjuntos de disponibilidade, os domínios de atualização são atualizados um de cada vez. Todas as VMs em um domínio de atualização (UD) são pausadas, atualizadas e, em seguida, reiniciadas antes da manutenção planejada passar para o próximo UD.

Alguns aplicativos podem ser afetados por esses tipos de atualizações. Os aplicativos que executam processamento de eventos em tempo real, transmissão de mídia ou transcodificação, ou cenários de rede de alta produtividade, podem não ser projetados para tolerar uma pausa de 30 segundos. <!-- sooooo, what should they do? --> 


## <a name="maintenance-requiring-a-reboot"></a>Manutenção que exige uma reinicialização

Quando as VMs precisam ser reinicializadas para manutenção planejada, você será notificado com antecedência. A manutenção planejada tem duas fases: uma janela de autoatendimento e uma janela de manutenção agendada.

A **janela de autoatendimento** permite que você inicie a manutenção em suas VMs. Durante esse tempo, você pode consultar cada VM para ver seu status e verificar o resultado de sua última solicitação de manutenção.

Quando você inicia a manutenção de autoatendimento, a VM é movida para um nó que já tenha sido atualizado e liga-o novamente. Como a VM é reiniciada, o disco temporário é perdido e os endereços IP dinâmicos associados ao adaptador de rede virtual são atualizados.

Se você iniciar a manutenção de autoatendimento e ocorrer um erro durante o processo, a operação é interrompida, a VM não é atualizada e ela também é removida da iteração da manutenção planejada. Você será contatado em um momento posterior com um novo agendamento e receberá uma nova oportunidade para realizar a manutenção de autoatendimento. 

Quando a janela de autoatendimento tiver passado, a **janela de manutenção agendada** começa. Durante essa janela de tempo você ainda pode consultar a janela de manutenção, mas não será mais possível iniciar a manutenção por conta própria.

## <a name="availability-considerations-during-planned-maintenance"></a>Considerações sobre disponibilidade durante a manutenção planejada 

Se você decidir aguardar até a janela de manutenção planejada, há algumas coisas a serem consideradas para manter a disponibilidade mais alta de suas VMs. 

### <a name="paired-regions"></a>Regiões emparelhadas

Cada região do Azure é emparelhada com outra região na mesma área geográfica, formando juntas um par regional. Durante a manutenção planejada, o Azure atualizará somente as VMs em uma única região de um par de regiões. Por exemplo, ao atualizar as Máquinas Virtuais no Centro-Norte dos EUA, o Azure não atualizará qualquer Máquina Virtual no Centro-Sul dos EUA ao mesmo tempo. No entanto, outras regiões, como o Norte da Europa, podem estar em manutenção simultaneamente com Leste dos EUA. Noções básicas sobre como funcionam os pares de região podem ajudá-lo a melhor distribuir suas VMs entre regiões. Para obter mais informações, consulte [pares de regiões do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

### <a name="availability-sets-and-scale-sets"></a>Conjuntos de disponibilidade e conjuntos de dimensionamento

Ao implantar uma carga de trabalho usando VMs do Azure, é possível criar as VMs em um conjunto de disponibilidade para fornecer alta disponibilidade para o aplicativo. Isso garante que, durante uma interrupção ou eventos de manutenção, pelo menos uma máquina virtual estará disponível.

Em um conjunto de disponibilidade, VMs individuais são distribuídas em até 20 domínios de atualização (UDs). Durante a manutenção planejada, somente um único domínio de atualização é afetado em um período específico. Lembre-se de que a ordem dos domínios de atualização que são afetados não necessariamente acontece de forma sequencial. 

Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que permite implantar e gerenciar um conjunto de VMs idênticas como um único recurso. O conjunto de dimensionamento é implantado automaticamente nos domínios de atualização, como VMs em um conjunto de disponibilidade. Assim como com conjuntos de disponibilidade, com conjuntos de dimensionamento somente um domínio de atualização é afetado em um determinado momento.

Para saber mais sobre como configurar as máquinas virtuais para alta disponibilidade, consulte Gerenciar a disponibilidade de máquinas virtuais para Windows (../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).