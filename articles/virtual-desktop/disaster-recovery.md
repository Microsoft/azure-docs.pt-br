---
title: Configurar plano de recuperação de desastres da área de trabalho virtual do Windows – Azure
description: Como configurar um plano de continuidade de negócios e recuperação de desastre para sua implantação de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 968f82a143872fd282222539ab71a70db488a20d
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935641"
---
# <a name="set-up-a-business-continuity-and-disaster-recovery-plan"></a>Configurar um plano de continuidade de negócios e recuperação de desastres

Para manter os dados da sua organização seguros, talvez seja necessário adotar uma estratégia de BCDR (continuidade dos negócios e recuperação de desastre). Uma estratégia de BCDR de som mantém seus aplicativos e carga de trabalho em funcionamento durante o serviço planejado e não planejado ou interrupções do Azure.

A área de trabalho virtual do Windows oferece BCDR para o serviço de área de trabalho virtual do Windows para preservar os metadados do cliente durante interrupções. Quando ocorrer uma interrupção em uma região, os componentes da infraestrutura de serviço realizarão failover no local secundário e continuarão funcionando normalmente. Você ainda pode acessar metadados relacionados ao serviço e os usuários ainda podem se conectar a hosts disponíveis. As conexões do usuário final permanecerão online, desde que o ambiente de locatário ou os hosts permaneçam acessíveis.

Para garantir que os usuários ainda possam se conectar durante uma interrupção de região, você precisa replicar suas VMs (máquinas virtuais) em um local diferente. Durante as interrupções, o site primário faz failover para as VMs replicadas no local secundário. Os usuários podem continuar acessando os aplicativos do local secundário sem interrupção. Além da replicação da VM, você precisará manter as identidades de usuário acessíveis no local secundário. Se você estiver usando contêineres de perfil, também precisará replicá-los. Por fim, verifique se seus aplicativos de negócios que dependem de dados no local principal podem fazer failover com o restante dos dados.

Para resumir, para manter os usuários conectados durante uma interrupção, você precisará fazer o seguinte nesta ordem:

- Replique as VMs em um local secundário.
- Se você estiver usando contêineres de perfil, configure a replicação de dados no local secundário.
- Verifique se as identidades de usuário configuradas no local primário estão disponíveis no local secundário.
- Certifique-se de que qualquer aplicativo de linha de negócios que dependa de dados em seu local principal tenha failover para o local secundário.

## <a name="vm-replication"></a>Replicação de VM

Primeiro, você precisará replicar suas VMs para o local secundário. Suas opções para fazer isso dependem de como suas VMs são configuradas:

- Você pode configurar todas as suas VMs para pools de hosts em pool e pessoais com Azure Site Recovery. Com esse método, você só precisará configurar um pool de hosts e seus espaços de trabalho e grupos de aplicativos relacionados.
- Você pode criar um novo pool de hosts na região de failover, mantendo todos os recursos em seu local de failover desativados. Para esse método, você precisaria configurar novos grupos de aplicativos e espaços de trabalho na região de failover. Você pode usar um plano de Azure Site Recovery para ativar os pools de hosts.
- Você pode criar um pool de hosts que é preenchido por VMs criadas nas regiões primária e de failover enquanto mantém as VMs na região de failover desativadas. Nesse caso, você só precisa configurar um pool de hosts e seus espaços de trabalho e grupos de aplicativos relacionados. Você pode usar um plano de Azure Site Recovery para ligar os pools de hosts com esse método.

Recomendamos que você use [Azure site Recovery](../site-recovery/site-recovery-overview.md) para gerenciar VMs de replicação em outros locais do Azure, conforme descrito em [arquitetura de recuperação de desastre do Azure para o Azure](../site-recovery/azure-to-azure-architecture.md). É recomendável usar Azure Site Recovery para pools de hosts pessoais, porque Azure Site Recovery dá suporte a SKUs baseadas em [servidor e cliente](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

Se você usar Azure Site Recovery, não será necessário registrar essas VMs manualmente. O agente de área de trabalho virtual do Windows na VM secundária usará automaticamente o token de segurança mais recente para se conectar à instância de serviço mais próxima. A VM (host de sessão) no local secundário se tornará automaticamente parte do pool de hosts. O usuário final precisará se reconectar durante o processo, mas além disso, não há outras operações manuais.

Se houver conexões de usuário existentes durante a interrupção, antes que o administrador possa iniciar o failover para a região secundária, você precisará encerrar as conexões de usuário na região atual.

Para desconectar usuários na área de trabalho virtual do Windows (clássico), execute este cmdlet:

```powershell
Invoke-RdsUserSessionLogoff
```

Para desconectar os usuários na versão integrada do Azure da área de trabalho virtual do Windows, execute este cmdlet:

```powershell
Remove-AzWvdUserSession
```

Depois de desconectar todos os usuários na região primária, você pode fazer failover das VMs na região primária e permitir que os usuários se conectem às VMs na região secundária. Para obter mais informações sobre como esse processo funciona, consulte [replicar VMs do Azure para outra região do Azure](../site-recovery/azure-to-azure-how-to-enable-replication.md).

## <a name="virtual-network"></a>Rede virtual

Em seguida, considere a conectividade de sua rede durante a interrupção. Você precisará ter certeza de que configurou uma rede virtual (VNET) em sua região secundária. Se os usuários precisarem acessar recursos locais, você precisará configurar essa VNET para acessá-las. Você pode estabelecer conexões locais com uma VPN, ExpressRoute ou WAN virtual.

Recomendamos que você use Azure Site Recovery para configurar a VNET na região de failover porque preserva as configurações da sua rede principal e não precisa de emparelhamento.

## <a name="user-identities"></a>Identidades de usuário

Em seguida, verifique se o controlador de domínio está disponível no local secundário. 

Há três maneiras de manter o controlador de domínio disponível:

   - Ter Domínio do Active Directory controlador no local secundário
   - Usar um controlador de Domínio do Active Directory local
   - Replicar Domínio do Active Directory controlador usando [Azure site Recovery](../site-recovery/site-recovery-active-directory.md)

## <a name="user-and-app-data"></a>Dados de usuário e de aplicativo

Se você estiver usando contêineres de perfil, a próxima etapa será configurar a replicação de dados no local secundário. Você tem cinco opções para armazenar os perfis de FSLogix:

   - S2D (Espaços de Armazenamento Diretos)
   - Unidades de rede (VM com unidades extras)
   - Arquivos do Azure
   - Azure NetApp Files
   - Cache na nuvem para replicação

Para obter mais informações, confira [as opções de armazenamento para contêineres de perfil FSLogix na área de trabalho virtual do Windows](store-fslogix-profile.md).

Se você estiver configurando a recuperação de desastres para perfis, estas são suas opções:

   - Configure a replicação nativa do Azure (por exemplo, replicação de conta de armazenamento padrão de arquivos do Azure, replicação de Azure NetApp Files ou sincronização de arquivos do Azure para servidores de arquivos).
    
     >[!NOTE]
     >A replicação do NetApp é automática após você configurá-la pela primeira vez. Com os planos de Azure Site Recovery, você pode adicionar scripts e pós-scripts para fazer failover de recursos que não são da VM replicam recursos de armazenamento do Azure.

   - Configure o cache de nuvem do FSLogix para os dados do aplicativo e do usuário.
   - Configure a recuperação de desastres para dados de aplicativo somente para garantir o acesso a dados críticos para os negócios em todos os momentos. Com esse método, você pode recuperar dados do usuário após o fim da interrupção.

Vamos dar uma olhada em como configurar o FSLogix para configurar a recuperação de desastre para cada opção.

### <a name="fslogix-configuration"></a>Configuração do FSLogix

O agente FSLogix pode dar suporte a vários locais de perfil se você configurar as entradas do registro para FSLogix.

Para configurar as entradas do registro:

1. Abra o **Editor do registro**.
2. Vá para **computador**  >  **HKEY_LOCAL_MACHINE**  >  perfis de**software**  >  **FSLogix**  >  **Profiles**.
   
     > [!div class="mx-imgBorder"]
     > ![Uma captura de tela da janela perfis no editor do registro. VHDLocation está selecionado.](media/regedit-profiles.png)

3. Clique com o botão direito do mouse em **VHDLocations** e selecione **Editar Cadeia de caracteres múltipla**.

     > [!div class="mx-imgBorder"]
     > ![Uma captura de tela da janela Editar Cadeia de caracteres múltipla. Os dados de valor listam os locais Centrual dos EUA e leste dos EUA.](media/multi-string-edit.png)

4. No campo **dados do valor** , insira os locais que você deseja usar.
5. Quando terminar, selecione **OK**.

Se o primeiro local não estiver disponível, o agente do FSLogix fará failover automaticamente para o segundo e assim por diante.

Recomendamos que você configure o agente FSLogix com um caminho para o local secundário na região principal. Depois que o local primário for desligado, o agente do FLogix será replicado como parte da VM Azure Site Recovery replicação. Depois que as VMs replicadas estiverem prontas, o agente tentará automaticamente definir o caminho para a região secundária.

Por exemplo, digamos que suas VMs de host de sessão primária estejam na região de EUA Central, mas o contêiner de perfil está na região de EUA Central por motivos de desempenho.

Nesse caso, você configuraria o agente FSLogix com um caminho para o armazenamento no EUA Central. Você configuraria as VMs do host da sessão para replicar no oeste dos EUA. Depois que o caminho para EUA Central falhar, o agente tentará criar um novo caminho para armazenamento no oeste dos EUA em vez disso.

### <a name="s2d"></a>S2D

Como o S2D lida com a replicação entre regiões internamente, você não precisa configurar manualmente o caminho secundário.

### <a name="network-drives-vm-with-extra-drives"></a>Unidades de rede (VM com unidades extras)

Se você replicar as VMs de armazenamento de rede usando Azure Site Recovery como as VMs de host de sessão, a recuperação manterá o mesmo caminho, o que significa que você não precisa reconfigurar o FSlogix.

### <a name="azure-files"></a>Arquivos do Azure

Os arquivos do Azure dão suporte à replicação assíncrona entre regiões que você pode especificar ao criar a conta de armazenamento. Se a natureza assíncrona dos arquivos do Azure já abranger suas metas de recuperação de desastres, você não precisará fazer configurações adicionais.

Se você precisar de replicação síncrona para minimizar a perda de dados, é recomendável usar o cache de nuvem do FSLogix em vez disso.

>[!NOTE]
>Esta seção não aborda o mecanismo de autenticação de failover para arquivos do Azure.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Saiba mais sobre Azure NetApp Files em [criar emparelhamento de replicação para Azure NetApp files](../azure-netapp-files/cross-region-replication-create-peering.md).

## <a name="app-dependencies"></a>Dependências de aplicativo

Por fim, certifique-se de que todos os aplicativos de negócios que dependem de dados localizados na região primária possam fazer failover para o local secundário. Além disso, certifique-se de definir as configurações que os aplicativos precisam para trabalhar no novo local. Por exemplo, se um dos aplicativos for dependente do back-end do SQL, certifique-se de replicar o SQL no local secundário. Você deve configurar o aplicativo para usar o local secundário como qualquer parte do processo de failover ou como sua configuração padrão. Você pode modelar dependências de aplicativo em planos de Azure Site Recovery. Para saber mais, confira [sobre os planos de recuperação](../site-recovery/recovery-plan-overview.md).

## <a name="disaster-recovery-testing"></a>Teste de recuperação de desastre

Depois de concluir a configuração da recuperação de desastres, você desejará testar seu plano para verificar se ele funciona.

Aqui estão algumas sugestões sobre como testar seu plano:

- Se as VMs de teste tiverem acesso à Internet, elas assumirão qualquer host de sessão existente para novas conexões, mas todas as conexões existentes com o host de sessão original permanecerão ativas. Verifique se o administrador executando o teste desconecta todos os usuários ativos antes de testar o plano. 
- Você só deve fazer testes de recuperação de desastres completos durante uma janela de manutenção para não interromper os usuários. Você também pode usar um pool de hosts no ambiente de validação para o teste. 
- Verifique se o teste abrange todos os aplicativos críticos para os negócios.
- Recomendamos o failover de até 100 VMs por vez. Se você tiver mais VMs do que isso, recomendamos que faça o failover em lotes de 10 minutos de distância.

## <a name="next-steps"></a>Próximas etapas

Se você tiver dúvidas sobre como manter seus dados seguros além do planejamento de interrupções, confira nosso guia de [segurança](security-guide.md).