---
title: Acesso JIT à máquina virtual na Central de Segurança do Azure | Microsoft Docs
description: Este documento demonstra como o acesso just-in-time à VM (JIT) na central de segurança do Azure ajuda você a controlar o acesso às suas máquinas virtuais do Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 60ae36d80e34f27ed68c679f47edacf3e402417c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916143"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Proteja suas portas de gerenciamento com acesso just-in-time

Bloqueie o tráfego de entrada para suas máquinas virtuais do Azure com o recurso de acesso à VM (máquina virtual) JIT (just-in-time) da central de segurança do Azure. Isso reduz a exposição a ataques e, ao mesmo tempo, fornece acesso fácil quando você precisa se conectar a uma VM.

Para obter uma explicação completa sobre como o JIT funciona e a lógica subjacente, consulte o [just-in-time explicado](just-in-time-explained.md).

Esta página ensina como incluir o JIT em seu programa de segurança. Você aprenderá a: 

- **Habilitar o JIT em suas VMs** – você pode habilitar o JIT com suas próprias opções personalizadas para uma ou mais VMs usando a central de segurança, o PowerShell ou a API REST. Como alternativa, você pode habilitar o JIT com parâmetros padrão, embutidos em código, de máquinas virtuais do Azure. Quando habilitado, o JIT bloqueia o tráfego de entrada para suas VMs do Azure criando uma regra no seu grupo de segurança de rede.
- **Solicitar acesso a uma VM que tenha o JIT habilitado** -o objetivo do JIT é garantir que, embora o tráfego de entrada seja bloqueado, a central de segurança ainda fornece acesso fácil para se conectar às VMs quando necessário. Você pode solicitar acesso a uma VM habilitada para JIT da central de segurança, máquinas virtuais do Azure, PowerShell ou API REST.
- **Auditar a atividade** -para garantir que suas VMs sejam protegidas adequadamente, examine os acessos às suas VMs habilitadas para JIT como parte de suas verificações de segurança regulares.   



## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|Requer [Azure Defender para Servidores](defender-for-servers-introduction.md)|
|VMs com suporte:|![Sim, ](./media/icons/yes-icon.png) VMs implantadas por meio de Azure Resource Manager.<br>![Nenhuma ](./media/icons/no-icon.png) VM implantada com modelos de implantação clássicos. [Saiba mais sobre esses modelos de implantação](../azure-resource-manager/management/deployment-models.md).<br>![Não há ](./media/icons/no-icon.png) VMs protegidas pelos firewalls do Azure controlados pelo [Gerenciador de firewall do Azure](../firewall-manager/overview.md)|
|Funções e permissões necessárias:|As funções **leitor** e **SecurityReader** podem exibir o status e os parâmetros do JIT.<br>Para criar funções personalizadas que podem funcionar com o JIT, consulte [quais permissões são necessárias para configurar e usar o JIT?](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit).<br>Para criar uma função com privilégios mínimos para usuários que precisam solicitar acesso JIT a uma VM e não executar outras operações JIT, use o [script set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) das páginas da Comunidade do GitHub da central de segurança.|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||


## <a name="enable-jit-vm-access"></a>Habilitar o acesso à VM JIT <a name="jit-configure"></a>

Você pode habilitar o acesso à VM JIT com suas próprias opções personalizadas para uma ou mais VMs usando a central de segurança ou programaticamente. 

Como alternativa, você pode habilitar o JIT com parâmetros padrão, embutidos em código, de máquinas virtuais do Azure.

Cada uma dessas opções é explicada em uma guia separada abaixo.

### <a name="azure-security-center"></a>[**Central de segurança do Azure**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>Habilitar o JIT em suas VMs na central de segurança do Azure <a name="jit-asc"></a>

:::image type="content" source="./media/security-center-just-in-time/jit-config-security-center.gif" alt-text="Configurando o acesso à VM JIT na central de segurança do Azure":::

Na central de segurança, você pode habilitar e configurar o acesso à VM JIT.

1. Abra o painel do Azure defender e, na área proteção avançada, selecione **acesso à VM just-in-time**.

    A página de **acesso da VM just-in-time** é aberta com suas VMs agrupadas nas seguintes guias:

    - **Configurado** -VMs que já foram configuradas para dar suporte ao acesso de VM just-in-time. Para cada VM, a guia configurada mostra:
        - o número de solicitações de JIT aprovadas nos últimos sete dias
        - a data e a hora do último acesso
        - os detalhes de conexão configurados
        - o último usuário
    - **Não configurado** -VMs sem o JIT habilitado, mas que podem dar suporte a JIT. Recomendamos que você habilite o JIT para essas VMs.
    - Não **há suporte** para VMs sem o JIT habilitado e que não dão suporte ao recurso. Sua VM pode estar nessa guia pelos seguintes motivos:
      - NSG (grupo de segurança de rede) ausente-o JIT requer que um NSG seja configurado
      - VM clássica-JIT dá suporte a VMs implantadas por meio de Azure Resource Manager, não ' implantação clássica '. [Saiba mais sobre os modelos de implantação clássicas do vs Azure Resource Manager](../azure-resource-manager/management/deployment-models.md).
      - Outros-sua VM pode estar nessa guia se a solução JIT estiver desabilitada na política de segurança da assinatura ou do grupo de recursos.

1. Na guia **não configurada** , marque as VMs a serem protegidas com JIT e selecione **habilitar JIT em VMs**. 

    A página acesso à VM JIT é aberta listando as portas que a central de segurança recomenda proteger:
    - 22: SSH
    - 3389: RDP
    - 5985: WinRM 
    - 5986: WinRM

    Para aceitar as configurações padrão, selecione **salvar**.

1. Para personalizar as opções de JIT:

    - Adicione portas personalizadas com o botão **Adicionar** . 
    - Modifique uma das portas padrão, selecionando-a na lista.

    Para cada porta (personalizada e padrão), o painel **Adicionar configuração de porta** oferece as seguintes opções:

    - **Protocolo**-o protocolo que é permitido nesta porta quando uma solicitação é aprovada
    - **IPS de origem permitidos**-os intervalos de IP que são permitidos nesta porta quando uma solicitação é aprovada
    - **Tempo máximo de solicitação**-a janela de tempo máxima durante a qual uma porta específica pode ser aberta

     1. Defina a segurança de porta para suas necessidades.

     1. Selecione **OK**.

1. Selecione **Salvar**.



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>Editar a configuração de JIT em uma VM habilitada para JIT usando a central de segurança <a name="jit-modify"></a>

Você pode modificar a configuração Just-in-time de uma VM adicionando e configurando uma nova porta para proteger essa VM ou alterando qualquer outra configuração relacionada a uma porta já protegida.

Para editar as regras de JIT existentes para uma VM:

1. Abra o painel do Azure defender e, na área proteção avançada, selecione **controles de aplicativo adaptáveis**.

1. Na guia **configurada** , clique com o botão direito do mouse na VM à qual você deseja adicionar uma porta e selecione Editar. 

    ![Editando uma configuração de acesso à VM JIT na central de segurança do Azure](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. Em **Configuração de acesso JIT à VM**, você pode editar as configurações existentes de uma porta já protegida ou pode adicionar uma nova porta personalizada.

1. Quando terminar de editar as portas, selecione **salvar**.
 


### <a name="azure-virtual-machines"></a>[**Máquinas virtuais do Azure**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>Habilitar o JIT em suas VMs de máquinas virtuais do Azure

Você pode habilitar o JIT em uma VM nas páginas de máquinas virtuais do Azure da portal do Azure.

![Configurando o acesso à VM JIT em máquinas virtuais do Azure](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> Se uma VM já tiver o just-in-time habilitado, quando você acessar sua página de configuração, verá que o just-in-time está habilitado e você poderá usar o link para abrir a página de acesso da VM just-in-time na central de segurança e exibir e alterar as configurações.

1. No [portal do Azure](https://ms.portal.azure.com), procure e selecione **máquinas virtuais**. 

1. Selecione a máquina virtual que você deseja proteger com o JIT.

1. No menu, selecione **configuração**.

1. Em **acesso just-in-time**, selecione **habilitar just-in-time**. 

    Isso habilita o acesso just-in-time para a VM usando as seguintes configurações padrão:

    - Computadores Windows:
        - RDP porta 3389
        - Três horas de acesso máximo permitido
        - Os endereços IP de origem permitidos estão definidos como qualquer
    - Computadores Linux:
        - SSH porta 22
        - Três horas de acesso máximo permitido
        - Os endereços IP de origem permitidos estão definidos como qualquer

1. Para editar qualquer um desses valores ou adicionar mais portas à sua configuração de JIT, use a página just-in-time da central de segurança do Azure:

    1. No menu da central de segurança, selecione **acesso à VM just-in-time**.

    1. Na guia **configurada** , clique com o botão direito do mouse na VM à qual você deseja adicionar uma porta e selecione Editar. 

        ![Editando uma configuração de acesso à VM JIT na central de segurança do Azure](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. Em **Configuração de acesso JIT à VM**, você pode editar as configurações existentes de uma porta já protegida ou pode adicionar uma nova porta personalizada.

    1. Quando terminar de editar as portas, selecione **salvar**.


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>Ativar JIT nas suas VMs usando PowerShell

Para habilitar o acesso just-in-time à VM do PowerShell, use o cmdlet oficial do PowerShell da central de segurança do Azure `Set-AzJitNetworkAccessPolicy` .

**Exemplo** – habilitar o acesso just-in-time à VM em uma VM específica com as seguintes regras:

* Fechar as portas 22 e 3389
* Defina uma janela de tempo máxima de 3 horas para cada para que elas possam ser abertas por solicitação aprovada
* Permitir que o usuário que está solicitando acesso controle os endereços IP de origem
* Permitir que o usuário que está solicitando acesso estabeleça uma sessão bem-sucedida após uma solicitação de acesso just-in-time aprovada

Os comandos do PowerShell a seguir criam essa configuração de JIT:

1. Atribua uma variável que mantém as regras de acesso da VM just-in-time para uma VM:

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})
    ```

1. Insira as regras de acesso à VM just-in-time da VM em uma matriz:
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. Configure as regras de acesso da VM just-in-time na VM selecionada:
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    Use o parâmetro-Name para especificar uma VM. Por exemplo, para estabelecer a configuração de JIT para duas VMs diferentes, VM1 e VM2, use: ```Set-AzJitNetworkAccessPolicy -Name VM1``` e ```Set-AzJitNetworkAccessPolicy -Name VM2``` .


### <a name="rest-api"></a>[**REST API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>Habilitar o JIT em suas VMs usando a API REST

O recurso de acesso Just-In-Time à VM pode ser usado por meio da API da Central de Segurança do Azure. Use essa API para obter informações sobre VMs configuradas, adicionar novas, solicitar acesso a uma VM e muito mais. 

Saiba mais em [políticas de acesso à rede JIT](/rest/api/securitycenter/jitnetworkaccesspolicies).


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>Solicitar acesso a uma VM habilitada para JIT

Você pode solicitar acesso a uma VM habilitada para JIT do portal do Azure (na central de segurança ou em máquinas virtuais do Azure) ou programaticamente.

Cada uma dessas opções é explicada em uma guia separada abaixo.

### <a name="azure-security-center"></a>[**Central de segurança do Azure**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>Solicitar acesso a uma VM habilitada para JIT da central de segurança do Azure 

Quando uma VM tem um JIT habilitado, você precisa solicitar acesso para se conectar a ele. Você pode solicitar acesso em qualquer uma das maneiras com suporte, independentemente de como você habilitou o JIT.

:::image type="content" source="./media/security-center-just-in-time/jit-request-security-center.gif" alt-text="Solicitando acesso JIT na central de segurança":::

1. Na página **acesso da VM just-in-time** , selecione a guia **configurada** .

1. Marque as VMs que você deseja acessar.

    - O ícone na coluna **detalhes da conexão** indica se o JIT está habilitado no grupo de segurança de rede ou no firewall. Se ele estiver habilitado em ambos, somente o ícone de firewall será exibido.

    - A coluna **detalhes da conexão** fornece as informações necessárias para conectar a VM e suas portas abertas.

1. Selecione **Solicitar acesso**. A janela **solicitar acesso** é aberta.

1. Em **Solicitar acesso**, configure para cada VM as portas a abrir, os endereços IP de origem para os quais a porta fica aberta e o intervalo de tempo durante o qual a porta fica aberta. Só será possível solicitar acesso às portas configuradas. Cada porta tem um tempo máximo permitido derivado da configuração de JIT que você criou.

1. Selecione **Abrir portas**.

> [!NOTE]
> Se um usuário que está solicitando acesso estiver atrás de um proxy, a opção **Meu IP** pode não funcionar. Pode ser que você precise definir o intervalo completo de endereços IP da organização.



### <a name="azure-virtual-machines"></a>[**Máquinas virtuais do Azure**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Solicitar acesso a uma VM habilitada para JIT na página conectar da máquina virtual do Azure

Quando uma VM tem um JIT habilitado, você precisa solicitar acesso para se conectar a ele. Você pode solicitar acesso em qualquer uma das maneiras com suporte, independentemente de como você habilitou o JIT.

  >![solicitação JIT just-in-time](./media/security-center-just-in-time/jit-request-vm.png)


Para solicitar acesso de máquinas virtuais do Azure:

1. No portal do Azure, abra as páginas de máquinas virtuais.

1. Selecione a VM à qual você deseja se conectar e abra a página **conectar** .

    O Azure verifica se o JIT está habilitado nessa VM.

    - Se o JIT não estiver habilitado para a VM, você será solicitado a habilitá-lo.

    - Se o JIT estiver habilitado, selecione **solicitar acesso** para passar uma solicitação de acesso com o IP solicitante, o intervalo de tempo e as portas que foram configuradas para essa VM.

> [!NOTE]
> Depois que uma solicitação for aprovada para uma VM protegida pelo firewall do Azure, a central de segurança fornecerá ao usuário os detalhes de conexão apropriados (o mapeamento de porta da tabela DNAT) a ser usada para se conectar à VM.



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>Solicitar acesso a uma VM habilitada para JIT usando PowerShell

No exemplo a seguir, é possível ver uma solicitação de acesso Just-In-Time à VM para uma VM específica, na qual é solicitada a abertura da porta 22 para um endereço IP específico e por um período de tempo específico:

Execute o seguinte no PowerShell:

1. Configure as propriedades de acesso à solicitação da VM:

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. Insira os parâmetros de solicitação de acesso da VM em uma matriz:

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. Enviar o acesso de solicitação (use a ID de recurso da etapa 1)

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

Saiba mais na [documentação do cmdlet do PowerShell](/powershell/scripting/developer/cmdlet/cmdlet-overview).



### <a name="rest-api"></a>[**REST API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>Solicitar acesso a VMs habilitadas para JIT usando a API REST

O recurso de acesso Just-In-Time à VM pode ser usado por meio da API da Central de Segurança do Azure. Use essa API para obter informações sobre VMs configuradas, adicionar novas, solicitar acesso a uma VM e muito mais. 

Saiba mais em [políticas de acesso à rede JIT](/rest/api/securitycenter/jitnetworkaccesspolicies).

---








## <a name="audit-jit-access-activity-in-security-center"></a>Auditar atividade de acesso JIT na central de segurança

Você pode obter informações sobre as atividades de VM usando a pesquisa de logs. Para exibir os logs:

1. Em **acesso à VM just-in-time**, selecione a guia **configurada** .

1. Para a VM que você deseja auditar, abra o menu de reticências no final da linha.
 
1. Selecione **log de atividades** no menu.

   ![Selecione o log de atividades JIT just-in-time](./media/security-center-just-in-time/jit-select-activity-log.png)

   O log de atividades fornece uma exibição filtrada das operações anteriores para essa VM junto com a hora, a data e a assinatura.

1. Para baixar as informações de log, selecione **baixar como CSV**.








## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a configurar e usar o acesso de VM just-in-time. Para saber por que o JIT deve ser usado, leia o artigo conceito que explica as ameaças com as quais está se defendendo:

> [!div class="nextstepaction"]
> [Explicou o JIT](just-in-time-explained.md)