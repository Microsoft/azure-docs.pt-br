---
title: Configurar a recuperação de desastre do DNS/Active Directory com Azure Site Recovery
description: Este artigo descreve como implementar uma solução de recuperação de desastre para o Active Directory e DNS usando o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mayg
ms.openlocfilehash: 528a24bb64aa8d323b5d63a27af0a52ccdf1abb6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86132329"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Configurar a recuperação de desastres para Active Directory e DNS

Aplicativos empresariais como o SharePoint, o Dynamics AX e o SAP dependem do Active Directory e de uma infraestrutura de DNS para funcionar corretamente. Quando você configura a recuperação de desastre para aplicativos, geralmente precisa recuperar Active Directory e o DNS (sistema de nomes de domínio) antes de recuperar outros componentes de aplicativo, para garantir a funcionalidade correta do aplicativo.

É possível usar o [Site Recovery](site-recovery-overview.md) para criar um plano de recuperação de desastre para o Active Directory. Quando ocorrer uma interrupção, você poderá iniciar um failover. Você pode colocar o Active Directory em funcionamento em alguns minutos. Se tiver implantado o Active Directory para vários aplicativos no site primário, por exemplo, para o SharePoint e o SAP, você provavelmente desejará o failover do site completo. Você pode fazer failover primeiramente do Active Directory usando o Site Recovery. Em seguida, faça failover dos outros aplicativos usando planos de recuperação específicos do aplicativo.

Este artigo explica como criar uma solução de recuperação de desastre para o Active Directory. Ele inclui os pré-requisitos e as instruções de failover. Antes de iniciar, é necessários que você esteja familiarizado com o Active Directory e o Azure Site Recovery.

## <a name="prerequisites"></a>Pré-requisitos

- Se você estiver replicando para o Azure, [prepare os recursos do Azure](tutorial-prepare-azure.md), incluindo uma assinatura, uma Rede Virtual do Microsoft Azure, uma conta de armazenamento e um cofre dos Serviços de Recuperação.
- Examine os [requisitos de suporte](./vmware-physical-azure-support-matrix.md) de todos os componentes.

## <a name="replicate-the-domain-controller"></a>Replicar o controlador de domínio

- Você deve configurar a replicação do Site Recovery, em pelo menos uma VM (máquina virtual) que hospede um controlador de domínio ou DNS.
- Se tiver vários controladores de domínio no ambiente, você também deverá configurar um controlador de domínio adicional no site de destino. O controlador de domínio adicional pode estar no Azure, ou em um datacenter secundário local.
- Se tiver apenas alguns aplicativos e um controlador de domínio, você provavelmente desejará fazer failover de todo o site junto. Nesse caso, é recomendável usar Site Recovery para replicar o controlador de domínio para o site de destino, seja no Azure ou em um datacenter local secundário. É possível usar o mesmo controlador de domínio replicado ou a máquina virtual DNS para [failover de teste](#test-failover-considerations).
- Se você tiver muitos aplicativos e mais de um controlador de domínio no ambiente, ou se pretender fazer failover de alguns aplicativos por vez, além de replicar a máquina virtual do controlador de domínio com o Site Recovery, será recomendável configurar um controlador de domínio adicional no site de destino (no Azure ou em um datacenter local secundário). Para o [failover de teste](#test-failover-considerations), você pode usar um controlador de domínio que é replicado pelo site Recovery. Para failover, você pode usar o controlador de domínio adicional no site de destino.

## <a name="enable-protection-with-site-recovery"></a>Habilitar a proteção com o Site Recovery

Você pode usar o Site Recovery para proteger a máquina virtual que hospeda o controlador de domínio ou o DNS.

### <a name="protect-the-vm"></a>Proteger a VM

O controlador de domínio replicado usando o Site Recovery é usado para [failover de teste](#test-failover-considerations). Verifique se ele atende aos seguintes requisitos:

1. O controlador de domínio é um servidor de catálogo global.
1. O controlador de domínio deve ser o proprietário da função FSMO (operações de mestre único) flexível para funções que são necessárias durante um failover de teste. Do contrário, essas funções precisarão ser [dimensionadas](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) depois do failover.

### <a name="configure-vm-network-settings"></a>Definir configurações de rede de VMs

Para a máquina virtual que hospeda o controlador de domínio ou o DNS, no Site Recovery, defina configurações de rede nas configurações **Computação e Rede** da máquina virtual replicada. Isso garante que a máquina virtual seja anexada à rede correta após o failover.

## <a name="protect-active-directory"></a>Proteger o Active Directory

### <a name="site-to-site-protection"></a>Proteção site a site

Crie um controlador de domínio no site secundário. Ao promover o servidor a uma função de controlador de domínio, especifique o nome do mesmo domínio que está sendo usado no site primário. Você pode usar o snap-in dos **Sites e Serviços do Active Directory** para definir as configurações no objeto de link de site ao qual os sites serão adicionados. Ao definir as configurações em um link de site, você pode controlar quando a replicação ocorre entre dois ou mais sites e com que frequência isso ocorre. Para obter mais informações, consulte [agendando a replicação entre sites](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731862(v=ws.11)).

### <a name="site-to-azure-protection"></a>Proteção Site ao Azure

Primeiro, crie um controlador de domínio em uma rede virtual do Azure. Ao promover o servidor para uma função de controlador de domínio, especifique o mesmo nome de domínio usado no site primário.

Depois disso, reconfigure o servidor DNS para a rede virtual para usar o servidor DNS no Azure.

:::image type="content" source="./media/site-recovery-active-directory/azure-network.png" alt-text="Rede do Azure":::

### <a name="azure-to-azure-protection"></a>Proteção Azure ao Azure

Primeiro, crie um controlador de domínio em uma rede virtual do Azure. Ao promover o servidor para uma função de controlador de domínio, especifique o mesmo nome de domínio usado no site primário.

Depois disso, reconfigure o servidor DNS para a rede virtual para usar o servidor DNS no Azure.

## <a name="test-failover-considerations"></a>considerações sobre failover de teste

Para evitar o impacto nas cargas de trabalho de produção, o failover de teste ocorre em uma rede isolada da rede de produção.

A maioria dos aplicativos exige a presença de um controlador de domínio ou de um servidor DNS. Portanto, antes do failover do aplicativo, você deve criar um controlador de domínio na rede isolada para ser usado para failover de teste. A maneira mais fácil de fazer isso é usar o Site Recovery para replicar uma máquina virtual que hospede um controlador de domínio ou um DNS. Em seguida, execute um failover de teste da máquina virtual do controlador de domínio antes de executar um failover de teste do plano de recuperação para o aplicativo.

1. Você pode usar o Site Recovery para [replicar](vmware-azure-tutorial.md) a máquina virtual que hospeda o controlador de domínio ou o DNS.
1. Crie uma rede isolada. Qualquer rede virtual criada por você no Azure é isolada de outras redes, por padrão. Recomendamos que você use o mesmo intervalo de endereços IP para essa rede de sua rede de produção. Não habilite a conectividade site a site nessa rede.
1. Forneça um endereço IP DNS na rede isolada. Use o endereço IP que você espera que a máquina virtual DNS obtenha. Se você estiver replicando para o Azure, forneça o endereço IP da máquina virtual usada no failover. Para digitar o endereço IP, na máquina virtual replicada, nas configurações **Computação e Rede**, selecione as configurações **IP de Destino**.

   :::image type="content" source="./media/site-recovery-active-directory/azure-test-network.png" alt-text="Rede de teste do Azure":::

   > [!TIP]
   > O Site Recovery tenta criar máquinas virtuais de teste em uma sub-rede de mesmo nome e usando o mesmo endereço IP fornecido nas configurações de **Computação e Rede** da máquina virtual. Se uma sub-rede do mesmo nome não estiver disponível na rede virtual do Azure fornecida para failover de teste, a máquina virtual de teste será criada na primeira sub-rede em ordem alfabética.
   >
   > Se o endereço IP de destino fizer parte da sub-rede selecionada, o Site Recovery tentará criar a máquina virtual do failover de teste usando o endereço IP de destino. Se o IP de destino não fizer parte da sub-rede selecionada, a máquina virtual do failover de teste será criada usando o próximo IP disponível na sub-rede selecionada.

### <a name="test-failover-to-a-secondary-site"></a>Failover de teste para um site secundário

1. Se você estiver replicando para outro site local e utiliza DHCP, [configure o DNS e DHCP para failover de teste](hyper-v-vmm-test-failover.md#prepare-dhcp).
1. Execute um failover de teste na máquina virtual do controlador de domínio na rede isolada. Use o ponto de recuperação _consistente com o aplicativo_ mais recente disponível da máquina virtual do controlador de domínio para fazer o failover de teste.
1. Execute um failover de teste para o plano de recuperação que contém máquinas virtuais nas quais o aplicativo é executado.
1. Quando o teste for concluído, _limpe o failover de teste_ na máquina virtual do controlador de domínio. Essa etapa exclui o controlador de domínio que foi criado para o failover de teste.

### <a name="remove-references-to-other-domain-controllers"></a>Remover referências a outros controladores de domínio

Ao iniciar um failover de teste, não inclua todos os controladores de domínio na rede de teste. Para remover referências a outros controladores de domínio no ambiente de produção, talvez seja necessário [aproveitar as funções FSMO do Active Directory](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) e fazer a [limpeza dos metadados](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816907(v=ws.10)) de controladores de domínio ausentes.

### <a name="issues-caused-by-virtualization-safeguards"></a>Problemas causados por defesas da virtualização

> [!IMPORTANT]
> Algumas das configurações descritas nesta seção não são as configurações padrão do controlador de domínio. Se não quiser fazer essas alterações em um controlador de domínio de produção, você poderá criar um controlador de domínio dedicado para o Site Recovery usar no failover de teste. Faça essas alterações somente nesse controlador de domínio.

Começando com o Windows Server 2012, [defesas adicionais foram inseridas no Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Essas proteções ajudam a proteger controladores de domínio virtualizados contra reversões de número de sequência de atualização (USN) se a plataforma de hipervisor subjacente der suporte a **VM-generationid**. O Azure dá suporte a **VM-GenerationID**. Por isso, os controladores de domínio que executam o Windows Server 2012 ou posterior nas máquinas virtuais do Azure têm essas proteções adicionais.

Quando **VM-GenerationID** é redefinido, o valor **InvocationID** do banco de dados AD DS também é redefinido. Além disso, o pool de ID relativa (RID) é Descartado e a `SYSVOL` pasta é marcada como não autoritativa. Para obter mais informações, consulte [introdução à virtualização de Active Directory Domain Services](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) e [virtualizar com segurança sistema de arquivos distribuído a replicação (DFSR)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/safely-virtualizing-dfsr/ba-p/424671).

O failover do Azure pode causar a redefinição de **VM-GenerationID**. A redefinição de **VM-GenerationID** dispara garantias adicionais quando a máquina virtual do controlador de domínio é iniciada no Azure. Isso pode resultar em um atraso significativo na capacidade de entrar na máquina virtual do controlador de domínio.

Como esse controlador de domínio é usado apenas um failover de teste, as defesas da virtualização não são necessárias. Para garantir que o valor de **generationid de VM** para a máquina virtual do controlador de domínio não seja alterado, você pode alterar o valor de seguinte `DWORD` para **4** no controlador de domínio local:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`

#### <a name="symptoms-of-virtualization-safeguards"></a>Sintomas das defesas da virtualização

Se as defesas da virtualização forem disparadas após um failover de teste, você poderá ver um ou mais dos seguintes sintomas:

- O valor de **generationid** muda:

  :::image type="content" source="./media/site-recovery-active-directory/Event2170.png" alt-text="Alteração da ID de Geração":::

- O valor de **inrevocationid** é alterado:

  :::image type="content" source="./media/site-recovery-active-directory/Event1109.png" alt-text="Alteração de ID de invocação":::

- `SYSVOL` a pasta e os `NETLOGON` compartilhamentos não estão disponíveis.

  :::image type="content" source="./media/site-recovery-active-directory/sysvolshare.png" alt-text="Compartilhamento de pasta SYSVOL":::

  :::image type="content" source="./media/site-recovery-active-directory/Event13565.png" alt-text="Pasta do SYSVOL do NtFrs":::

- Os bancos de dados DFSR são excluídos.

  :::image type="content" source="./media/site-recovery-active-directory/Event2208.png" alt-text="Os bancos de dados DFSR são excluídos":::

### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Solucionar problemas do controlador de domínio durante o failover de teste

> [!IMPORTANT]
> Algumas das configurações descritas nesta seção não são as configurações padrão do controlador de domínio. Se não quiser fazer essas alterações em um controlador de domínio de produção, você poderá criar um controlador de domínio dedicado para o failover de teste do Site Recovery. Faça essas alterações somente nesse controlador de domínio dedicado.

1. No prompt de comando, execute o seguinte comando para verificar se a `SYSVOL` pasta e a `NETLOGON` pasta são compartilhadas:

    `NET SHARE`

1. No prompt de comando, execute o comando a seguir para garantir que o controlador de domínio esteja funcionando corretamente:

    `dcdiag /v > dcdiag.txt`

1. No log de saída, procure o texto a seguir. O texto confirma que o controlador de domínio está funcionando corretamente.

    - `passed test Connectivity`
    - `passed test Advertising`
    - `passed test MachineAccount`

Se as condições anteriores forem atendidas, é provável que o controlador de domínio esteja funcionando corretamente. Do contrário, conclua as seguintes etapas:

1. Faça uma restauração autoritativa do controlador de domínio. Lembre-se das informações a seguir:

    - Embora não recomendemos a replicação usando o [FRS (serviço de replicação de arquivo)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-end-is-nigh-for-frs-8211-updated-for-ws2016/ba-p/425379), se você usar a replicação do FRS, siga as etapas para uma restauração autoritativa. O processo é descrito em [Como usar a chave do Registro BurFlags para reinicializar o serviço de replicação de arquivos](https://support.microsoft.com/kb/290762).

      Para obter mais informações sobre BurFlags, consulte a postagem no blog [D2 e D4: para que servem?](/archive/blogs/janelewis/d2-and-d4-what-is-it-for).

    - Se você usar a replicação DFSR, conclua as etapas de uma restauração autoritativa. O processo é descrito em [forçar uma sincronização autoritativa e não autoritativa para a pasta SYSVOL replicada pelo DFSR (como "D4/D2" para o FRS)](https://support.microsoft.com/kb/2218556).

      Você também pode usar as funções do PowerShell. Para obter mais informações, consulte [Funções do PowerShell de restauração autoritativa/não autoritativa de DFSR-SYSVOL](/archive/blogs/thbouche/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions).

1. Ignore o requisito de sincronização inicial definindo a chave do Registro a seguir como **0** no controlador de domínio local. Se o `DWORD` não existir, você poderá criá-lo no nó **parâmetros** .

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

   Para obter mais informações, consulte [Solucionar problemas da ID do evento 4013 do DNS: O servidor DNS não conseguiu carregar zonas DNS integradas ao AD](https://support.microsoft.com/kb/2001093).

1. Desabilite o requisito de ter um servidor de catálogo global disponível para validar o logon do usuário. Para isso, no controlador de domínio local, defina a seguinte chave do Registro como **1**. Se o `DWORD` não existir, você poderá criá-lo no nó **LSA** .

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

   Para obter mais informações, consulte [como o catálogo global funciona](/previous-versions/windows/it-pro/windows-server-2003/cc737410(v=ws.10)).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS e controlador de domínio em computadores diferentes

Se você estiver executando o controlador de domínio e os DNs na mesma VM, poderá ignorar este procedimento.

Se o DNS não estiver na mesma VM que o controlador de domínio, você precisará criar uma VM DNS para o failover de teste. Você pode usar um servidor DNS atualizado e criar todas as zonas necessárias. Por exemplo, se seu domínio de Active Directory for `contoso.com` , você poderá criar uma zona DNS com o nome `contoso.com` . As entradas correspondentes ao Active Directory devem ser atualizadas no DNS da seguinte maneira:

1. Assegure-se de que essas configurações estejam definidas antes que outra máquina virtual no plano de recuperação seja iniciada:

   - A zona deve ser nomeada depois do nome raiz da floresta.
   - A zona deve ter backup em arquivo.
   - A zona deve ser habilitada para atualizações seguras e não seguras.
   - O resolvedor da máquina virtual que hospeda o controlador de domínio deve apontar para o endereço IP da máquina virtual do DNS.

1. Execute o seguinte comando na VM que hospeda o controlador de domínio:

   `nltest /dsregdns`

1. Execute os seguintes comandos para adicionar uma zona no servidor DNS, permitir atualizações seguras e adicionar uma entrada da zona ao DNS:

   ```cmd
   dnscmd /zoneadd contoso.com  /Primary

   dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1

   dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>

   dnscmd /config contoso.com /allowupdate 1
   ```

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-workload.md) sobre como proteger as cargas de trabalho corporativas com o Azure Site Recovery.
