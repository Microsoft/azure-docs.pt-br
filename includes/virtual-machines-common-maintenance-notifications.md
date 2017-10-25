
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>Exibir VMs agendadas para manutenção no portal

Quando uma onda de manutenção planejada estiver agendada, e as notificações forem enviadas, você poderá observar a lista de máquinas virtuais que serão afetados pela próxima onda de manutenção. 

É possível usar o portal do Azure e procurar as VMs agendadas para manutenção.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Na navegação à esquerda, clique em **Máquinas virtuais**.

3. No painel de máquinas virtuais, clique no botão **Colunas**para abrir a lista de colunas disponíveis.

4. Selecione e adicione as seguintes colunas:

   **Manutenção** -mostra o status de manutenção para a máquina virtual. Estes são os valores possíveis:
      
      | Valor | Descrição |
      |-------|-------------|
      | Comece agora | A VM está na janela de manutenção de autoatendimento que permite a você iniciar a manutenção por conta própria. Veja abaixo sobre como iniciar a manutenção em sua VM | 
      | Agendado | A VM está programada para manutenção sem a opção para iniciar a manutenção. Você pode saber da janela de manutenção, selecionando a janela Agendado Automaticamente neste modo de exibição ou clicando na VM | 
      | Concluído | Você iniciou e concluiu com êxito a manutenção em sua VM. | 
      | Ignorado| Você optou por iniciar a manutenção sem sucesso. O Azure cancelou a manutenção da sua VM e irá reagendá-la em um momento posterior | 
      | Tente novamente mais tarde| Você optou por iniciar a manutenção e o Azure não pôde atender à sua solicitação. Nesse caso, você pode tentar novamente em um momento posterior. | 
   
   **Manutenção proativa** -mostra a janela de tempo quando você pode iniciar automaticamente a manutenção em suas VMs.
   
   **Manutenção agendada** -mostra a janela de tempo quando o Azure reiniciará sua VM para concluir a manutenção. 




## <a name="notification-and-alerts-in-the-portal"></a>Notificação e alertas no portal

O Azure comunica uma agenda para manutenção planejada, enviando um email para o grupo de proprietário e os coadministradores de assinatura. Você pode adicionar outros destinatários e canais para essa comunicação com a criação de alertas de log de atividades do Azure. Para saber mais, veja [Monitorar a atividade de assinatura com o Log de Atividades do Azure] (../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione **Monitor**. 
3. No painel **Monitor - Log de atividades**, selecione **Alertas**.
4. No painel **Monitor - Alertas**, clique em **+ Adicionar alerta do log de atividade**.
5. Complete as informações na página **Adicionar alerta do log de atividade** e defina o seguinte em **Critérios**: **Tipo**: Manutenção - **Status**: Todos (não defina o status como Ativo ou Resolvido) **Nível**: Todos
    
Para saber mais sobre como configurar alertas de Log de Atividade, veja [Criar alertas de log de atividade](../articles/monitoring-and-diagnostics/monitoring-activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Iniciar Manutenção na sua VM do portal

Ao examinar os detalhes da VM, você poderá ver mais detalhes relacionados à manutenção.  
Na parte superior do modo de exibição de detalhes VM, uma nova faixa de opções de notificação será adicionada se sua VM for incluída em uma onda de manutenção planejada. Além disso, uma nova opção é adicionada ao iniciar manutenção quando possível. 


Clique na notificação de manutenção para ver a página de manutenção com mais detalhes sobre a manutenção planejada. A partir daí, você será capaz de **iniciar a manutenção** na sua VM.

Quando você começar a manutenção, sua máquina virtual será reiniciada e o status de manutenção será atualizado para refletir o resultado dentro de alguns minutos.

Se você tiver perdido a janela onde pode iniciar a manutenção, ainda poderá ver a janela quando a VM for reiniciada pelo Azure. 
