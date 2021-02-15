---
title: Executar um failover de teste (análise de recuperação de desastre) no Azure no Azure Site Recovery
description: Aprenda sobre a execução de um failover de teste do local para o Azure usando o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 815a35c167bbcd3ac03dfdaaf6d699e58a791f33
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369379"
---
# <a name="run-a-test-failover-disaster-recovery-drill-to-azure"></a>Executar um failover de teste (análise de recuperação de desastre) no Azure 


Este artigo descreve como executar uma simulação de recuperação de desastre no Azure, usando um failover de teste do Site Recovery.  

Execute um failover de teste para validar sua estratégia de replicação e recuperação de desastre sem nenhuma perda de dados ou tempo de inatividade. Um failover de teste não afeta a replicação em andamento nem o ambiente de produção. Você pode executar um failover de teste em uma VM (máquina virtual) específica ou em um [plano de recuperação](site-recovery-create-recovery-plans.md) contendo várias VMs.


## <a name="run-a-test-failover"></a>Execute um teste de failover
Este procedimento descreve como executar um failover de teste para um plano de recuperação. Se você quiser executar um failover de teste para uma única VM, siga as etapas descritas [aqui](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)

![Captura de tela da página failover de teste no portal do Azure.](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Em site Recovery no portal do Azure, clique em **planos de recuperação**  >  *recoveryplan_name*  >  **failover de teste**.
2. Selecione um **ponto de recuperação** para o qual fazer o failover. Você pode usar uma das seguintes opções:
    - **Últimos processados**: essa opção executa failover de todas as VM no plano para o último ponto de recuperação processado pelo Site Recovery. Para ver o último ponto de recuperação de uma VM específica, verifique os **Pontos de Recuperação Mais Recentes** nas configurações da VM. Essa opção fornece um RTO (Objetivo do Tempo de Recuperação) baixo porque não há tempo gasto para processar dados não processados.
    - **Consistente com o aplicativo mais recente**: essa opção executa failover de todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente pelo Site Recovery. Para ver o último ponto de recuperação de uma VM específica, verifique os **Pontos de Recuperação Mais Recentes** nas configurações da VM.
    - **Mais recente**: essa opção primeiro processa todos os dados que foram enviados ao serviço do Site Recovery para criar um ponto de recuperação para cada VM antes de fazer failover para ele. Essa opção fornece o RPO (objetivo de ponto de recuperação) mais baixo porque a VM criada após o failover tem todos os dados replicados para o Site Recovery quando o failover for disparado.
    - **Várias VMs processadas mais recentemente**: essa opção está disponível para os planos de recuperação com uma ou mais VMs que tenham consistência de várias VMs ativada. As VMs com a configuração habilitaram o failover para o último ponto de recuperação consistente com várias VMs em comum. Outras VMs fazem failover para o último ponto de recuperação processado.  
    - **Várias VMs consistentes com o aplicativo mais recentes**: essa opção está disponível para planos de recuperação com uma ou mais VMs que tenham consistência com várias VMs ativada. As VMs que fazem parte de um failover do grupo de replicação para o ponto de recuperação comum mais recente de várias VMs consistente com aplicativo. Outras VMs fazem failover para seus últimos pontos de recuperação consistentes com aplicativo.
    - **Personalizado**: use essa opção para fazer failover de uma VM específica para um ponto de recuperação específico.
3. Selecione uma rede virtual do Azure onde as máquinas virtuais de teste serão criadas.

    - O Site Recovery tenta criar VMs de teste em uma sub-rede com o mesmo nome e mesmo endereço IP fornecido nas configurações de **Computação e Rede** da VM.
    - Se uma sub-rede com o mesmo nome não estiver disponível na rede virtual do Azure usada para failover de teste, a VM de teste será criada na primeira sub-rede em ordem alfabética.
    - Se o mesmo endereço IP não estiver disponível na sub-rede, a VM receberá outro endereço IP disponível na sub-rede. [Saiba mais](#create-a-network-for-test-failover).
4. Se estiver fazendo failover no Azure e a criptografia de dados estiver habilitada, em **Chave de Criptografia**, selecione o certificado que foi emitido quando você habilitou a criptografia durante a instalação do provedor. Você poderá ignorar essa etapa se a criptografia não estiver habilitada.
5. Acompanhe o progresso do failover na guia **trabalhos** . Você deve ser capaz de ver o computador de réplica de teste no portal do Azure.
6. Para iniciar uma conexão de RDP com a VM do Azure, você precisa [adicionar um endereço IP público](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) no adaptador de rede da VM em que o failover foi feito.
7. Quando tudo está funcionando conforme o esperado, clique em **Limpar failover de teste**. Isso exclui as VMs que foram criadas durante o failover de teste.
8. Em **Observações**, registre e salve todas as observações associadas ao failover de teste.


![Captura de tela da guia trabalhos de failover de teste.](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Quando um failover de teste é disparado, ocorre o seguinte:

1. **Pré-requisitos**: uma verificação de pré-requisitos será executada para conferir se todas as condições exigidas para o failover foram cumpridas.
2. **Failover**: o failover processa e prepara os dados, para que uma VM do Azure possa ser criada a partir dela.
3. **Mais recente**: se você tiver escolhido o ponto de recuperação mais recente, um ponto de recuperação será criado com os dados que foram enviados para o serviço.
4. **Início**: esta etapa cria uma máquina virtual do Azure usando os dados processados na etapa anterior.

### <a name="failover-timing"></a>Tempo de failover

Nos cenários a seguir, o failover exige uma etapa intermediária extra que geralmente leva cerca de 8 a 10 minutos para ser concluída:

* VMs VMware executando uma versão do serviço Mobility anterior à versão 9.8
* Servidores físicos
* VMs VMware Linux
* VM Hyper-V protegida como servidores físicos
* VM VMware em que os drivers abaixo não sejam drivers de inicialização:
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* VM VMware que não tem o DHCP ativado, independentemente de usar DHCP ou endereços IP estáticos.

Em todos os outros casos, nenhuma etapa intermediária é necessária e o failover leva significativamente menos tempo.


## <a name="create-a-network-for-test-failover"></a>Criar uma rede para failover de teste

Recomendamos que para o failover de teste você escolha uma rede isolada da rede do site de recuperação de produção especificada nas configurações de **Computação e Rede** para cada VM. Por padrão, quando você cria uma rede virtual do Azure, ela é isolada de outras redes. Essa rede de teste deve imitar a sua rede de produção:

- A rede de teste deve ter o mesmo número de sub-redes da rede de produção. As sub-redes devem ter os mesmos nomes.
- A rede de teste deve usar o mesmo intervalo de endereços IP.
- Atualize o DNS da rede de teste com o endereço IP especificado para a VM de DNS nas configurações de **Computação e Rede**. Leia as [considerações sobre o failover de teste para o Active Directory](site-recovery-active-directory.md#test-failover-considerations) para obter mais detalhes.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Failover de teste para uma rede de produção no site de recuperação

Embora recomendemos que você use uma rede de teste separada da sua rede de produção, se você quiser testar uma simulação de recuperação de desastre na rede de produção, observe o seguinte:

- Verifique se a máquina virtual primária está desligada quando você executa o failover de teste. Caso contrário, duas VMs com a mesma identidade estarão em execução na mesma rede, ao mesmo tempo. Isso pode gerar consequências inesperadas.
- As alterações em VMs criadas para failover de teste serão perdidas quando você limpa o failover. Essas alterações não são replicadas de volta na VM primária.
- O teste no ambiente de produção resulta em um tempo de inatividade do seu aplicativo de produção. Os usuários não devem usar aplicativos executados em VMs quando o failover de teste está em andamento.  



## <a name="prepare-active-directory-and-dns"></a>Preparar o Active Directory e o DNS

Para executar um failover de teste em um teste de aplicativo, você precisará de uma cópia do ambiente de produção do Active Directory no ambiente de teste. Leia as [considerações sobre failover de teste para o Active Directory](site-recovery-active-directory.md#test-failover-considerations) para saber mais.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Se você quiser se conectar às VMs do Azure usando RDP/SSH após o failover, siga os requisitos resumidos na tabela.

**Failover** | **Local** | **Ações**
--- | --- | ---
**VM do Azure executando o Windows** | Computador local antes do failover | Para acessar a VM do Azure pela Internet, habilite o RDP e fazer com que as regras de TCP e UDP sejam adicionadas para o **Público** e que o RDP foi permitido para todos os perfis no **Firewall do Windows** > **Aplicativos Permitidos**.<br/><br/> Para acessar a VM do Azure em uma conexão site a site, habilite o RDP no computador e verifique se o RDP é permitido no **Firewall do Windows**  ->  **aplicativos e recursos permitidos**para redes **privadas** e de domínio.<br/><br/>  Verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Verifique se não existem Windows atualizações pendentes na VM virtual quando disparar um failover. A atualização do Windows poderá iniciar quando o failover for feito, e você não poderá fazer logon na VM até que a atualização seja concluída.
**VM do Azure executando o Windows** | VM do Azure após o failover |  [Adicione um endereço IP público](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) para a VM.<br/><br/> As regras de grupo de segurança de rede na VM com failover e a sub-rede do Azure à qual ela está conectada precisam permitir conexões de entrada para a porta RDP.<br/><br/> Verifique o **Diagnóstico de inicialização** para examinar uma captura de tela da VM.<br/><br/> Se você não conseguir se conectar, verifique se a VM está em execução e examine essas [dicas de solução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**VM do Azure executando Linux** | Computador local antes do failover | Verifique se o serviço Secure Shell na VM está definido para iniciar automaticamente na inicialização do sistema.<br/><br/> Verifique se as regras de firewall permitem uma conexão SSH com ele.
**VM do Azure executando Linux** | VM do Azure após o failover | As regras de grupo de segurança de rede na VM com failover (e a sub-rede do Azure à qual ela está conectada) precisam permitir conexões de entrada para a porta SSH.<br/><br/> [Adicione um endereço IP público](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) para a VM.<br/><br/> Verifique os **Diagnósticos de inicialização** para obter uma captura de tela da VM.<br/><br/>

Siga as etapas descritas [aqui](site-recovery-failover-to-azure-troubleshoot.md) para solucionar quaisquer problemas de conectividade após o failover.

## <a name="next-steps"></a>Próximas etapas
Depois de concluir uma simulação de recuperação de desastre, saiba mais sobre outros tipos de [failover](site-recovery-failover.md).