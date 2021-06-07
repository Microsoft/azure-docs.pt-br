---
title: Configurar ouvintes do grupo de disponibilidade e o balanceador de carga (portal do Azure)
description: Instruções passo a passo de como criar um ouvinte para um grupo de disponibilidade Always On para SQL Server em máquinas virtuais do Azure
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e53a6a4875b3dde55d1822daa342d6cde536d1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096422"
---
# <a name="configure-a-load-balancer-for-a-sql-server-always-on-availability-group-in-azure-virtual-machines"></a>Configurar um balanceador de carga para um grupo de disponibilidade Always On do SQL Server em Máquinas Virtuais do Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Este artigo explica como criar um balanceador de carga para um grupo de disponibilidade Always On do SQL Server em Máquinas Virtuais do Azure em execução com o Azure Resource Manager. Um grupo de disponibilidade exige um balanceador de carga quando as instâncias do SQL Server estão em Máquinas Virtuais do Azure. O balanceador de carga armazena o endereço IP do ouvinte do grupo de disponibilidade. Se um grupo de disponibilidade abranger várias regiões, cada região precisará de um balanceador de carga.

Para concluir esta tarefa, você precisará ter um grupo de disponibilidade Always On do SQL Server implantado em VMs do Azure que estejam sendo executadas com o Resource Manager. As máquinas virtuais do SQL Server devem pertencer ao mesmo conjunto de disponibilidade. Você pode usar o [modelo da Microsoft](./availability-group-quickstart-template-configure.md) para criar automaticamente o grupo de disponibilidade no Resource Manager. Este modelo cria automaticamente um balanceador de carga interno para você. 

Se preferir, você poderá [configurar manualmente um grupo de disponibilidade](availability-group-manually-configure-tutorial.md).

Este artigo exige que os grupos de disponibilidade já estejam configurados.  

Veja artigos relacionados:

* [Configurar os grupos de disponibilidade Always On na VM do Azure (GUI)](availability-group-manually-configure-tutorial.md)   
* [Configurar uma conexão de rede virtual com rede virtual usando o PowerShell e o Azure Resource Manager](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Ao seguir este artigo, você cria e configura um balanceador de carga no Portal do Azure. Após a conclusão desse processo, você configura o cluster para usar o endereço IP do balanceador de carga no ouvinte do grupo de disponibilidade.

## <a name="create--configure-load-balancer"></a>Criar e configurar o balanceador de carga 

Nesta parte da tarefa, execute as seguintes etapas:

1. No Portal do Azure, crie o balanceador de carga e configure o endereço IP.
2. Configure o pool de back-end.
3. Crie a investigação. 
4. Defina as regras de balanceamento de carga.

> [!NOTE]
> Se as instâncias do SQL Server estiverem em vários grupos de recursos e regiões, execute cada etapa duas vezes, uma vez em cada grupo de recursos.
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Etapa 1: Criar o balanceador de carga e configurar o endereço IP

Primeiro, crie o balanceador de carga. 

1. No Portal do Azure, abra o grupo de recursos que contém as máquinas virtuais do SQL Server. 

2. No grupo de recursos, selecione **Adicionar**.

3. Pesquise pelo **balanceador de carga**. Escolha **Load Balancer** (publicado pela **Microsoft**) nos resultados da pesquisa.

4. Na folha **Balanceador de carga**, selecione **Criar**.

5. Na caixa de diálogo **Criar balanceador de carga**, configure o balanceador de carga da seguinte maneira:

   | Configuração | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa o balanceador de carga. Por exemplo, **sqlLB**. |
   | **Tipo** |**Interna**: a maioria das implementações usa um balanceador de carga interno que permite a conexão dos aplicativos na mesma rede virtual ao grupo de disponibilidade.  </br> **Externa**: permite que os aplicativos se conectem ao grupo de disponibilidade por meio de uma conexão à Internet pública. |
   | **SKU** |**Básico**: opção padrão. Válido somente se as instâncias do SQL Server estiverem no mesmo conjunto de disponibilidade. </br> **Standard**: preferencial. Válido se as instâncias do SQL Server estiverem no mesmo conjunto de disponibilidade. Necessário se as instâncias do SQL Server estiverem em zonas de disponibilidade diferentes. |
   | **Rede virtual** |Selecione a rede virtual na qual estão as instâncias do SQL Server. |
   | **Sub-rede** |Selecione a sub-rede na qual estão as instâncias do SQL Server. |
   | **Atribuição de endereço IP** |**Estático** |
   | **Endereço IP privado** |Especifique um endereço IP disponível na sub-rede. Você usa esse endereço IP ao criar um ouvinte no cluster. Em um script do PowerShell posterior neste artigo, use esse endereço para a variável `$ListenerILBIP`. |
   | **Assinatura** |Se você tiver várias assinaturas, este campo poderá aparecer. Selecione a assinatura que você deseja associar a esse recurso. Normalmente, trata-se da mesma assinatura de todos os recursos do grupo de disponibilidade. |
   | **Grupo de recursos** |Selecione o grupo de recursos no qual estão as instâncias do SQL Server. |
   | **Localidade** |Selecione o local do Azure no qual estão as instâncias do SQL Server. |

6. Selecione **Criar**. 

O Azure cria o balanceador de carga. O balanceador de carga pertence a uma rede, sub-rede, grupo de recursos e local específicos. Após o Azure concluir a tarefa, verifique as configurações do balanceador de carga no Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Etapa 2: Configure o pool de back-end

O Azure chama o *pool de back-ends* do pool de endereços back-end. Nesse caso, o pool de back-ends é composto por endereços das duas instâncias do SQL Server em seu grupo de disponibilidade. 

1. No grupo de recursos, selecione o balanceador de carga criado. 

2. Em **Configurações**, selecione **Pools de back-end**.

3. Em **Pools de back-end**, selecione **Adicionar** para criar um pool de endereços de back-end. 

4. Em **Adicionar pool de back-ends**, sob **Nome**, digite um nome para o pool de back-ends.

5. Em **Máquinas virtuais**, selecione **Adicionar uma máquina virtual**. 

6. Em **Escolher máquinas virtuais**, selecione **Escolher um conjunto de disponibilidade** e especifique o conjunto de disponibilidade ao qual as máquinas virtuais do SQL Server pertencem.

7. Depois de escolher o conjunto de disponibilidade, selecione **Escolher as máquinas virtuais**, escolha as duas máquinas virtuais que hospedam as instâncias do SQL Server no grupo de disponibilidade e escolha **Selecionar**. 

8. Selecione **OK** para fechar as folhas **Escolher máquinas virtuais** e **Adicionar pool de back-end**. 

O Azure atualiza as configurações para o pool de endereços de back-end. Agora seu conjunto de disponibilidade tem um pool de duas instâncias do SQL Server.

### <a name="step-3-create-a-probe"></a>Etapa 3: Criar uma investigação

A investigação define como o Azure verifica qual das instâncias do SQL Server é proprietária atual do ouvinte do grupo de disponibilidade. O Azure investiga o serviço com base no endereço IP em uma porta que você define quando cria o teste.

1. Na folha **Configurações** do balanceador de carga, selecione **Investigações de integridade**. 

2. Na folha **Investigações de integridade**, selecione **Adicionar**.

3. Configure a investigação na folha **Adicionar investigação** . Use os valores a seguir para configurar a investigação:

   | Configuração | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa a investigação. Por exemplo, **SQLAlwaysOnEndPointProbe**. |
   | **Protocolo** |**TCP** |
   | **Porta** |Você pode usar qualquer porta disponível. Por exemplo, *59999*. |
   | **Intervalo** |*5* |
   | **Limite não íntegro** |*2* |

4.  Selecione **OK**. 

> [!NOTE]
> Verifique se a porta especificada está aberta no firewall das duas instâncias do SQL Server. As duas instâncias exigem uma regra de entrada para a porta TCP que você usa. Para saber mais, confira [Adicionar ou Editar Regra de Firewall](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)). 
> 

O Azure cria a investigação e a usa para testar qual instância do SQL Server tem o ouvinte do grupo de disponibilidade.

### <a name="step-4-set-the-load-balancing-rules"></a>Etapa 4: Definir as regras de balanceamento de carga

As regras de balanceamento de carga configuram como o balanceador de carga encaminha o tráfego para as instâncias do SQL Server. Para este balanceador de carga, habilite o retorno de servidor direto, pois somente uma das duas instâncias do SQL Servers é proprietária do recurso de ouvinte do grupo de disponibilidade por vez.

1. Na folha **Configurações** do balanceador de carga, selecione **Regras de balanceamento de carga**. 

2. Na folha **Regras de balanceamento de carga**, selecione **Adicionar**.

3. Na folha **Adicionar regras de balanceamento de carga**, configure a regra de balanceamento de carga. Use as configurações a seguir: 

   | Configuração | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa as regras de balanceamento de carga. Por exemplo, **SQLAlwaysOnEndPointListener**. |
   | **Protocolo** |**TCP** |
   | **Porta** |*1433* |
   | **Porta de back-end** |*1433*. Esse valor é ignorado porque essa regra usa **IP flutuante (retorno de servidor direto)** . |
   | **Investigação** |Use o nome da investigação que você criou para este balanceador de carga. |
   | **Persistência de sessão** |**Nenhuma** |
   | **Tempo limite de ociosidade (minutos)** |*4* |
   | **IP flutuante (retorno de servidor direto)** |**Enabled** |

   > [!NOTE]
   > Talvez você precise rolar a folha para baixo para ver todas as configurações.
   > 

4. Selecione **OK**. 

5. O Azure configura a regra de balanceamento de carga. Agora, o balanceador de carga está configurado para rotear o tráfego para a instância do SQL Server que hospeda o ouvinte para o grupo de disponibilidade. 

Neste ponto, o grupo de recursos tem um balanceador de carga que se conecta em ambas as máquinas do SQL Server. O balanceador de carga também contém um endereço IP para o ouvinte do grupo de disponibilidade AlwaysOn do SQL Server para que o computador possa responder às solicitações para os grupos de disponibilidade.

> [!NOTE]
> Se suas instâncias do SQL Server estiverem em duas regiões separadas, repita as etapas na outra região. Cada região exige um balanceador de carga. 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configure o cluster para usar o endereço IP do balanceador de carga

A próxima etapa é configurar o ouvinte no cluster e colocar o ouvinte online. Execute as seguintes etapas: 

1. Crie o ouvinte do grupo de disponibilidade no cluster de failover. 

2. Faça o ouvinte ficar online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Etapa 5: Crie o ouvinte do grupo de disponibilidade no cluster de failover

Nesta etapa, você cria manualmente o ouvinte do grupo de disponibilidade no Gerenciador de Cluster de Failover e no Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Verifique a configuração do ouvinte

Se os recursos e as dependências do cluster forem configurados corretamente, você poderá ver o ouvinte no SQL Server Management Studio. Para definir a porta do ouvinte, execute as seguintes etapas:

1. Inicie o SQL Server Management Studio e conecte-se à réplica principal.

2. Vá até **Alta Disponibilidade do AlwaysOn** > **Grupos de Disponibilidade** > **Ouvintes do Grupo de Disponibilidade**.  

    Agora você deve ver o nome do ouvinte que você criou no Gerenciador de Cluster de Failover. 

3. Clique com o botão direito do mouse no nome do ouvinte e selecione **Propriedades**.

4. Na caixa **Porta**, especifique o número da porta para o ouvinte do grupo de disponibilidade usando o $EndpointPort usado anteriormente (1433 era o padrão) e selecione **OK**.

Agora você tem um grupo de disponibilidade nas máquinas virtuais do Azure em execução no modo Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Testar a conexão com o ouvinte

Teste a conexão executando as seguintes etapas:

1. Use o protocolo RDP para se conectar a uma instância do SQL Server que esteja na mesma rede virtual, mas não seja proprietário da réplica. Esse servidor pode ser a outra instância do SQL Server no cluster.

2. Use o utilitário **sqlcmd** para testar a conexão. Por exemplo, o script a seguir estabelece uma conexão de **sqlcmd** com a réplica primária por meio do ouvinte com autenticação do Windows:

    ```console
    sqlcmd -S <listenerName> -E
    ```

A conexão SQLCMD se conecta automaticamente a qualquer instância do SQL Server que hospede a réplica primária. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Criar um endereço IP para um grupo de disponibilidade adicional

Cada grupo de disponibilidade usa um ouvinte separado. Cada ouvinte tem seu próprio endereço IP. Use o mesmo balanceador de carga para manter o endereço IP para ouvintes adicionais. Depois de criar um grupo de disponibilidade, adicione o endereço IP ao balanceador de carga e, em seguida, configure o ouvinte.

Para adicionar um endereço IP a um balanceador de carga com o portal do Azure, execute as seguintes etapas:

1. No portal do Azure, abra o grupo de recursos que contém o balanceador de carga e selecione o balanceador de carga. 

2. Em **CONFIGURAÇÕES**, selecione **Pool de IP de front-end** e **Adicionar**. 

3. Em **Adicionar endereço IP de front-end**, atribua um nome para o front-end. 

4. Verifique se a **Rede virtual** e a **Sub-rede** são as mesmas das instâncias do SQL Server.

5. Defina o endereço IP para o ouvinte. 
   
   >[!TIP]
   >Você pode definir o endereço IP como estático e digitar um endereço que atualmente não está sendo usado na sub-rede. Como alternativa, é possível definir o endereço IP como dinâmico e salvar o novo pool IP de front-ends. Quando você faz isso, o Portal do Azure atribui automaticamente um endereço IP disponível ao pool. Em seguida, é possível reabrir o pool IP de front-end e alterar a atribuição para estática. 

6. Salve o endereço IP para o ouvinte. 

7. Adicione uma investigação de integridade usando as seguintes configurações:

   |Configuração |Valor
   |:-----|:----
   |**Nome** |Um nome para identificar a investigação.
   |**Protocolo** |TCP
   |**Porta** |Uma porta TCP não usada, que deve estar disponível em todas as máquinas virtuais. Não pode ser usada para qualquer outra finalidade. Dois ouvintes não podem usar a mesma porta de investigação. 
   |**Intervalo** |O tempo entre as tentativas de investigação. Use o (5) padrão.
   |**Limite não íntegro** |O número de limites consecutivos que devem falhar antes que uma máquina virtual seja considerada não íntegra.

8. Selecione **OK** para salvar a investigação. 

9. Crie uma regra de balanceamento de carga. Selecione **Regras de balanceamento de carga** e **Adicionar**.

10. Defina a nova regra de balanceamento de carga usando as seguintes configurações:

    |Configuração |Valor
    |:-----|:----
    |**Nome** |Um nome para identificar a regra de balanceamento de carga. 
    |**Endereço IP de front-end** |Selecione o endereço IP que você criou. 
    |**Protocolo** |TCP
    |**Porta** |Use a porta que as instâncias do SQL Server estão usando. Uma instância padrão usa a porta 1433, a menos que você tenha alterado. 
    |**Porta de back-end** |Use o mesmo valor de **porta**.
    |**Pool de back-end** |O pool que contém as máquinas virtuais com instâncias do SQL Server. 
    |**Investigação de integridade** |Escolha a investigação que você criou.
    |**Persistência de sessão** |Nenhum
    |**Tempo limite de ociosidade (minutos)** |(4) padrão
    |**IP flutuante (retorno de servidor direto)** | habilitado

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Configurar o grupo de disponibilidade para usar o novo endereço IP

Para concluir a configuração do cluster, repita as etapas que seguiu quando criou o primeiro grupo de disponibilidade. Ou seja, configure o [cluster para usar o novo endereço IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Depois de adicionar um endereço IP ao ouvinte, configure o grupo de disponibilidade adicional executando as seguintes etapas: 

1. Verifique se a porta de investigação para o novo endereço IP está aberta em ambas as máquinas virtuais do SQL Server. 

2. [No Gerenciador de Clusters, adicione o ponto de acesso do cliente](#addcap).

3. [Configurar o recurso de IP do grupo de disponibilidade](#congroup).

   >[!IMPORTANT]
   >Quando você cria o endereço IP, use o endereço IP que adicionou ao balanceador de carga.  

4. [Torne o recurso de grupo de disponibilidade do SQL Server dependente do ponto de acesso para cliente](#dependencyGroup).

5. [Torne o recurso de ponto de acesso de cliente dependente do endereço IP](#listname).
 
6. [Definir os parâmetros do cluster no PowerShell](#setparam).

Depois de configurar o grupo de disponibilidade para usar o novo endereço IP, configure a conexão para o ouvinte. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Adicionar uma regra de balanceamento de carga ao grupo de disponibilidade distribuído

Se um grupo de disponibilidade participar de um grupo de disponibilidade distribuído, o balanceador de carga precisará de uma regra adicional. Essa regra armazena a porta utilizada pelo ouvinte do grupo de disponibilidade distribuído.

>[!IMPORTANT]
>Esta etapa será aplicável somente se o grupo de disponibilidade participar de um [ grupo de disponibilidade distribuído](/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Em cada servidor que participa no grupo de disponibilidade distribuído, crie uma regra de entrada na porta TCP do ouvinte do grupo de disponibilidade distribuído. Em muitos exemplos, a documentação usa 5022. 

1. No portal do Azure, selecione o balanceador de carga e **Regras de balanceamento de carga** e escolha **+Adicionar**. 

1. Crie a regra de balanceamento de carga com as configurações a seguir:

   |Configuração |Valor
   |:-----|:----
   |**Nome** |Um nome para identificar a regra de balanceamento de carga para o grupo de disponibilidade distribuído. 
   |**Endereço IP de front-end** |Use o mesmo endereço IP de front-end como o grupo de disponibilidade.
   |**Protocolo** |TCP
   |**Porta** |5022 - A porta para o [ouvinte de ponto de extremidade do grupo de disponibilidade distribuído](/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Pode ser qualquer porta disponível.  
   |**Porta de back-end** | 5022 - Use o mesmo valor da **Porta**.
   |**Pool de back-end** |O pool que contém as máquinas virtuais com instâncias do SQL Server. 
   |**Investigação de integridade** |Escolha a investigação que você criou.
   |**Persistência de sessão** |Nenhum
   |**Tempo limite de ociosidade (minutos)** |(4) padrão
   |**IP flutuante (retorno de servidor direto)** | habilitado

Repita essas etapas para o balanceador de carga nos outros grupos de disponibilidade que participam dos grupos de disponibilidade distribuído.

Se você tiver um grupo de segurança de rede do Azure para restringir o acesso, verifique se as regras de permissão incluem:
- Os endereços IP da VM do SQL Server de back-end
- Os endereços IP flutuantes do balanceador de carga para o ouvinte do AG
- O endereço IP do núcleo do cluster, se aplicável.

## <a name="next-steps"></a>Próximas etapas

- [Configurar um Grupo de Disponibilidade Always On do SQL Server em Máquinas Virtuais do Azure em diferentes regiões](availability-group-manually-configure-multiple-regions.md)
