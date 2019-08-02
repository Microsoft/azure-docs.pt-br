---
title: Acesso JIT à máquina virtual na Central de Segurança do Azure | Microsoft Docs
description: Este documento demonstra como o acesso Just-In-Time à VM na Central de Segurança do Azure ajuda a controlar o acesso às suas máquinas virtuais do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 671930b1-fc84-4ae2-bf7c-d34ea37ec5c7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/17/2019
ms.author: v-mohabe
ms.openlocfilehash: eb9366acf82c94bdf99c4d4f0c7c6bdf4f51e06d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294984"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Gerenciar o acesso à máquina virtual usando JIT

O acesso Just-In-Time (JIT) à VM (máquina virtual) pode ser usado para bloquear o tráfego de entrada às suas VMs do Azure, reduzindo a exposição a ataques enquanto fornece acesso fácil para conectar às VMs quando necessário.

> [!NOTE]
> O recurso Just-In-Time está disponível na camada Standard da Central de Segurança.  Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança.


> [!NOTE]
> O acesso JIT à VM da Central de Segurança atualmente oferece suporte somente a VMs implantadas por meio do Azure Resource Manager. Para saber mais sobre os modelos de implantação clássica e do Resource Manager, consulte [Azure Resource Manager vs implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="attack-scenario"></a>Cenário de ataque

Ataques de força bruta geralmente se destinam às portas de gerenciamento como um meio para obter acesso a uma VM. Se for bem-sucedido, um invasor poderá assumir o controle sobre a VM e estabelecer uma base em seu ambiente.

Uma maneira de reduzir a exposição a um ataque de força bruta é limitar a quantidade de tempo que uma porta fica aberta. As portas de gerenciamento não precisam ficar abertas o tempo todo. Elas só precisam ser abertas enquanto você estiver conectado à VM, por exemplo, para realizar tarefas de manutenção ou gerenciamento. Quando o just-in-time estiver habilitada, a Central de segurança usa [grupo de segurança de rede](../virtual-network/security-overview.md#security-rules) (NSG) e as regras de Firewall do Azure, que restringem o acesso às portas de gerenciamento para que eles não podem ser alvo de invasores.

![Cenário JIT](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Como funciona o acesso JIT?

Quando o JIT está habilitado, a Central de Segurança bloqueia o tráfego de entrada às suas VMs do Azure, criando uma regra de NSG. Você seleciona as portas na VM para as quais o tráfego de entrada será bloqueado. Essas portas são controladas pela solução Just-In-Time.

Quando um usuário solicita acesso a uma VM, a Central de Segurança verifica se o usuário tem permissões de [RBAC (Controle de acesso baseado em função)](../role-based-access-control/role-assignments-portal.md), que permitem que eles solicitem com sucesso o acesso à VM. Se a solicitação for aprovada, a Central de segurança configura automaticamente os grupos de segurança de rede (NSGs) e o Firewall do Azure para permitir o tráfego de entrada para as portas selecionadas e endereços IP de origem solicitada ou intervalos de, pelo período de tempo que foi especificado. Depois que o tempo expirar, a Central de Segurança restaura os NSGs aos seus estados anteriores. No entanto, as conexões já estabelecidas não estão sendo interrompidas.

 > [!NOTE]
 > Se uma solicitação de acesso JIT for aprovada para uma VM por trás de um Firewall do Azure, a Central de segurança automaticamente altera as regras de política NSG e de firewall. Para a quantidade de tempo em que foi especificado, as regras permitem tráfego de entrada para as portas selecionadas e endereços IP de origem solicitada ou intervalos. Após o tempo, a Central de segurança restaura o firewall e regras de NSG para seus estados anteriores.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Permissões necessárias para configurar e usar o JIT

| Para permitir que um usuário: | Permissões para definir|
| --- | --- |
| Configurar ou editar uma política JIT para uma VM | *Atribua essas ações para a função:*  No escopo de uma assinatura ou Grupo de recursos que esteja associado à VM: ```Microsoft.Security/locations/jitNetworkAccessPolicies/write``` No escopo de uma assinatura ou grupo de recursos ou VM: ```Microsoft.Compute/virtualMachines/write``` | 
| ||
|Solicitar acesso JIT a uma VM | *Atribua essas ações para o usuário:*  No escopo de uma assinatura ou Grupo de recursos que esteja associado à VM:  ```Microsoft.Security/locations/{the_location_of_the_VM}/jitNetworkAccessPolicies/initiate/action``` No escopo de uma assinatura ou grupo de recursos ou VM: ```Microsoft.Compute/virtualMachines/read``` |


## <a name="configure-jit-on-a-vm"></a>Configurar o JIT em uma VM

Há 3 maneiras de configurar uma política JIT em uma VM:

- [Configurar o acesso JIT na Central de segurança do Azure](#jit-asc)
- [Configurar o acesso JIT em uma folha de VM do Azure](#jit-vm)
- [Configurar uma política JIT em uma VM por meio de programação](#jit-program)

## <a name="configure-jit-in-asc"></a>Configurar o JIT no ASC

ASC, você pode configurar um JIT política e solicitar acesso a uma VM usando uma política JIT


### Configurar o acesso JIT em uma VM no ASC <a name="jit-asc"></a>

1. Abra o painel **Central de Segurança**.

2. No painel esquerdo, selecione **Just-in-time acesso à VM**.

    ![Bloco de acesso JIT à VM](./media/security-center-just-in-time/just-in-time.png)

    A janela **Acesso à VM Just-In-Time** é aberta.

      ![Habilitar acesso Just-In-Time](./media/security-center-just-in-time/enable-just-in-time.png)

    O **Acesso à VM Just-In-Time** fornece informações sobre o estado das suas VMs:

    - **Configurado**: VMs que foram configuradas para dar suporte ao acesso JIT à VM. Os dados apresentados são da última semana e incluem, para cada VM, o número de solicitações aprovadas, a data e a hora do último acesso e o último usuário.
    - **Recomendado**: VMs que podem oferecer suporte ao acesso JIT à VM, mas não foram configuradas para isso. Recomenda-se que você habilite para essas VMs o controle de acesso JIT à VM. 
    - **Nenhuma recomendação** – as razões que podem fazer com que uma VM não seja recomendada são:
      - NSG ausente: a solução Just-In-Time exige que um NSG esteja em vigor.
      - VM Clássica: o acesso Just-In-Time à VM pela Central de Segurança atualmente oferece suporte apenas às VMs implantadas por meio do Azure Resource Manager. Não há suporte para uma implantação clássica na solução Just-In-Time. 
      - Outros – uma VM é nessa categoria se a solução just-in-time estiver desativada na política de segurança da assinatura ou grupo de recursos, ou se a VM está faltando um IP público e não tiver um NSG em vigor.

3. Selecione a guia **Recomendado**.

4. Sob **máquina VIRTUAL**, clique em VMs que você deseja habilitar. Isso coloca uma marca de seleção ao lado de uma VM.

5. Clique em **habilitar JIT nas VMs**.
   -. Esta folha exibe as portas padrão recomendadas pela Central de Segurança do Azure:
      - 22: SSH
      - 3389: RDP
      - 5985: WinRM 
      - 5986: WinRM
6. Você também pode configurar portas personalizadas:

      1. Clique em **Adicionar**. O **Adicionar configuração de porta** janela é aberta.
      2. Para cada porta que você optar por configurar ambos padrão e personalizadas, você pode personalizar as configurações a seguir:

    - **Tipo de protocolo**: o protocolo permitido nesta porta quando uma solicitação for aprovada.
    - **Endereços IP de origem permitida**: os intervalos de IP permitidos nesta porta quando uma solicitação for aprovada.
    - **Tempo máximo de solicitação**: o intervalo de tempo máximo durante o qual uma porta específica pode ficar aberta.

     3. Clique em **OK**.

1. Clique em **Salvar**.

> [!NOTE]
>Quando o acesso à VM de JIT está habilitado para uma VM, a Central de segurança do Azure cria regras "Negar todo o tráfego de entrada" para as portas selecionadas no Firewall do Azure e grupos de segurança de rede associados com ele. Se outras regras tinham sido criadas para as portas selecionadas, em seguida, as regras existentes têm prioridade sobre as novas regras "Negar todo o tráfego de entrada". Se não houver nenhuma regra existente nas portas selecionadas, as novas regras "Negar todo o tráfego de entrada" levam prioridade nos grupos de segurança de rede e Firewall do Azure.


## <a name="request-jit-access-via-asc"></a>Solicitar acesso JIT por meio do ASC

Para solicitar acesso a uma VM por meio de ASC:

1. Em **Acesso à VM just in time**, selecione a guia **Configurada**.

2. Sob **Máquina Virtual**, clique nas VMs que você deseja solicitar acesso. Isso coloca uma marca de seleção ao lado da VM.


    - O ícone na **detalhes de Conexão** coluna indica se o JIT está habilitado no firmware ou NSG. Se ele estiver habilitado em ambos, apenas o ícone de Firewall é exibido.

    - O **detalhes de Conexão** coluna fornece as informações corretas necessárias para se conectar a VM, como também indica as portas abertas.

      ![Solicitar acesso just-in-time](./media/security-center-just-in-time/request-just-in-time-access.png)

3. Clique em **solicitar acesso**. O **solicitar acesso** janela é aberta.

      ![Detalhes JIT](./media/security-center-just-in-time/just-in-time-details.png)

4. Em **Solicitar acesso**, configure para cada VM as portas a abrir, os endereços IP de origem para os quais a porta fica aberta e o intervalo de tempo durante o qual a porta fica aberta. É possível solicitar acesso somente para as portas que estão configuradas na política Just-In-Time. Cada porta tem um tempo máximo permitido derivado da política Just-In-Time.

5. Clique em **abrir portas**.

> [!NOTE]
> Se um usuário que está solicitando acesso estiver atrás de um proxy, a opção **Meu IP** pode não funcionar. Pode ser que você precise definir o intervalo completo de endereços IP da organização.

## <a name="edit-a-jit-access-policy-via-asc"></a>Editar uma política de acesso JIT por meio do ASC

Você pode alterar a política Just-In-Time existente de uma VM adicionando e configurando uma nova porta a ser aberta para essa VM ou alterando qualquer outro parâmetro relacionado a uma porta já protegida.

Para editar a política Just-In-Time existente de uma VM:
1. Em **Configurado**, sob **VMs**, selecione a VM à qual será adicionada uma porta clicando nos três pontos dentro da linha dessa VM. 

1. Selecione **Editar**.
1. Em **Configuração de acesso JIT à VM**, você pode editar as configurações existentes de uma porta já protegida ou pode adicionar uma nova porta personalizada. 
  ![Acesso JIT à VM](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-asc"></a>Auditar a atividade de acesso JIT no ASC

Você pode obter informações sobre as atividades de VM usando a pesquisa de logs. Para exibir os logs:

1. Em **Acesso à VM Just-In-Time**, selecione a guia **Configurado**.
2. Sob **VMs**, selecione uma VM para exibir informações sobre clicando nos três pontos dentro da linha para a VM e selecione **Log de atividades** no menu. O **log de atividades** é aberta.

   ![Selecionar o log de atividades](./media/security-center-just-in-time/select-activity-log.png)

   **Log de atividades** fornece uma exibição filtrada das operações anteriores dessa VM junto com a hora, a data e a assinatura.

Você pode baixar as informações de log selecionando **Clique aqui para baixar todos os itens como CSV**.

Modificar os filtros e clique em **aplicar** para criar uma pesquisa e log.



## Configurar o acesso JIT em uma folha de VM do Azure <a name="jit-vm"></a>

Para sua conveniência, é possível se conectar a uma VM usando JIT diretamente de dentro da folha da VM no Azure.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-blade"></a>Configurar o acesso JIT em uma VM por meio da folha de VM do Azure

Para tornar mais fácil distribuir acesso Just-In-Time entre suas VMs, você pode definir uma VM para permitir apenas o acesso Just-In-Time diretamente de dentro da VM.

1. No portal do Azure, selecione **Máquinas Virtuais**.
2. Clique na máquina virtual que você deseja limitar o acesso Just-In-Time.
3. No menu, clique em **Configuração**.
4. Em **Acesso Just-In-Time** clique em **Habilitar política Just-In-Time**. 

Isso permite o acesso Just-In-Time para a VM usando as seguintes configurações:

- Servidores Windows:
    - RDP porta 3389
    - 3 horas de acesso máximo permitido
    - Endereços IP de origem permitidos é definida como qualquer
- Servidores Linux:
    - SSH porta 22
    - 3 horas de acesso máximo permitido
    - Endereços IP de origem permitidos é definida como qualquer
     
Se uma VM já tiver o Just-In-Time habilitado quando você acessar a página de configuração, você poderá ver que o Just-In-Time está habilitado e você pode usar o link para abrir a política na Central de Segurança do Azure para exibir e alterar as configurações.

![configuração de JIT na vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>Solicitar acesso JIT a uma VM por meio da folha de VM do Azure

No portal do Azure, ao tentar se conectar a uma VM, o Azure verifica se você configurou uma política de acesso Just-In-Time naquela VM.

- Se houver uma política JIT configurada na VM, é possível clicar em **Solicitar acesso** para poder ter acesso de acordo com a política JIT definida para a VM. 

  >![solicitação de JIT](./media/security-center-just-in-time/jit-request.png)

  O acesso é solicitado com os seguintes parâmetros de padrão:

  - **IP de origem**: 'Any' (*) (não pode ser alterado)
  - **intervalo de tempo**: 3 horas (não pode ser alterado)  <!--Isn't this set in the policy-->
  - **número da porta** RDP da porta 3389 para Windows / porta 22 para Linux (pode ser alterado)

    > [!NOTE]
    > Depois que uma solicitação for aprovada para uma máquina virtual protegida pelo Firewall do Azure, a Central de segurança fornece ao usuário com os detalhes de conexão apropriadas (o mapeamento de porta da tabela DNAT) para usar para se conectar à VM.

- Se não tiver configurado o JIT em uma VM, receberá uma solicitação para configurar a política JIT.

  ![Solicitação de JIT](./media/security-center-just-in-time/jit-prompt.png)

## Configurar uma política JIT em uma VM por meio de programação  <a name="jit-program"></a>

Você pode configurar e usar Just-In-Time por meio de APIs REST e do PowerShell.

## <a name="jit-vm-access-via-rest-apis"></a>Acesso de VM JIT por meio de APIs REST

O recurso de acesso Just-In-Time à VM pode ser usado por meio da API da Central de Segurança do Azure. Você pode obter informações sobre VMs configuradas, adicionar novos, solicitar o acesso a uma VM e mais, por meio dessa API. Consulte [Políticas de acesso à rede JIT](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies) para saber mais sobre a API REST de Just-In-Time.

## <a name="jit-vm-access-via-powershell"></a>Acesso de VM JIT por meio do PowerShell

Para usar a solução de acesso Just-In-Time à VM por meio do PowerShell, use os cmdlets oficiais do PowerShell da Central de Segurança do Azure e, especificamente, `Set-AzJitNetworkAccessPolicy`.

O exemplo a seguir define uma política de acesso Just-In-Time à VM para uma VM específica e define o seguinte:

1.  Fechar as portas 22 e 3389.

2.  Definir uma janela de tempo máximo de 3 horas para cada, de modo que possam ser abertas por solicitação aprovada.
3.  Permitir que o usuário que está solicitando acesso controle os endereços IP de origem e permitir que o usuário estabeleça uma sessão com êxito mediante uma solicitação de acesso Just-In-Time aprovada.

Para fazer isso, execute o seguinte no PowerShell:

1.  Atribuir uma variável que contenha a política de acesso Just-In-Time à VM para uma VM:

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
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

2.  Inserir a política de acesso Just-In-Time à VM em uma matriz:
    
        $JitPolicyArr=@($JitPolicy)

3.  Configurar a política de acesso Just-In-Time à VM na VM selecionada:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="request-access-to-a-vm-via-powershell"></a>Solicitar acesso a uma VM por meio do PowerShell

No exemplo a seguir, é possível ver uma solicitação de acesso Just-In-Time à VM para uma VM específica, na qual é solicitada a abertura da porta 22 para um endereço IP específico e por um período de tempo específico:

Execute o seguinte no PowerShell:
1.  Configurar as propriedades de acesso de solicitação da VM

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Insira os parâmetros de solicitação de acesso da VM em uma matriz:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Enviar o acesso da solicitação (use a ID do recurso que você recebeu na etapa 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Para obter mais informações, consulte a documentação do cmdlet do PowerShell.

## <a name="next-steps"></a>Próximas etapas
Nesse artigo você aprendeu como o acesso Just-In-Time à VM na Central de Segurança o ajuda a controlar o acesso às suas máquinas virtuais do Azure.

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança](tutorial-security-policy.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciar recomendações de segurança](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger seus recursos do Azure.
- [Monitoramento da integridade da segurança](security-center-monitoring.md) – saiba como monitorar a integridade dos seus recursos do Azure.
- [Gerenciar e responder aos alertas de segurança](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e responder aos alertas de segurança.
- [Monitorar as soluções de parceiros](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes da Central de Segurança](security-center-faq.md) - encontre as perguntas frequentes sobre como usar o serviço.
- [Blog de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) : encontre postagens no blog sobre conformidade e segurança do Azure.

