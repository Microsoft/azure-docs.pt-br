---
title: Configurar um ouvinte do grupo de disponibilidade para SQL Server em máquinas virtuais RHEL no Azure – máquinas virtuais do Linux | Microsoft Docs
description: Aprenda a configurar um ouvinte do grupo de disponibilidade no SQL Server em máquinas virtuais do RHEL no Azure
ms.service: virtual-machines-sql
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 5f0b300be2f1cec4ee456065455832a2dc3598be
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449496"
---
# <a name="tutorial-configure-an-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Tutorial: Configurar um ouvinte do grupo de disponibilidade para o SQL Server em máquinas virtuais do RHEL no Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> O tutorial apresentado está em **versão prévia pública**. 
>
> Usamos o SQL Server 2017 com RHEL 7.6 neste tutorial, mas é possível usar o SQL Server 2019 no RHEL 7 ou RHEL 8 para configurar a alta disponibilidade. Os comandos para configurar os recursos do grupo de disponibilidade foram alterados no RHEL 8 e será conveniente examinar o artigo [Criar recurso do grupo de disponibilidade](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) e recursos do RHEL 8 para obter mais informações sobre os comandos corretos.

Este tutorial abordará etapas sobre como criar um ouvinte do grupo de disponibilidade para seus SQL Servers em máquinas virtuais (VMs) RHEL no Azure. Você saberá como:

> [!div class="checklist"]
> - Criar um balanceador de carga no portal do Azure
> - Configurar o pool de back-end para o balanceador de carga
> - Criar uma investigação para o balanceador de carga
> - Definir as regras de balanceamento de carga
> - Criar o recurso do balanceador de carga no cluster
> - Crie o ouvinte do grupo de disponibilidade
> - Testar a conexão com o ouvinte
> - Testar um failover

## <a name="prerequisite"></a>Pré-requisito

[Tutorial: Configurar grupos de disponibilidade para o SQL Server em máquinas virtuais RHEL no Azure](rhel-high-availability-stonith-tutorial.md) concluído

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Criar o balanceador de carga no portal do Azure

As instruções a seguir percorrem as etapas 1 a 4 da seção [Criar e configurar o balanceador de carga no portal do Azure](../windows/availability-group-load-balancer-portal-configure.md#create--configure-load-balancer) do artigo [Balanceador de carga – portal do Azure](../windows/availability-group-load-balancer-portal-configure.md).

### <a name="create-the-load-balancer"></a>Criar o balanceador de carga

1. No Portal do Azure, abra o grupo de recursos que contém as máquinas virtuais do SQL Server. 

2. No grupo de recursos, clique em **Adicionar**.

3. Pesquise por **balanceador de carga** e, em seguida, nos resultados da pesquisa, selecione **Balanceador de Carga**, que é publicado pela **Microsoft**.

4. Na folha **Balanceador de Carga**, clique em **Criar**.

5. Na caixa de diálogo **Criar balanceador de carga**, configure o balanceador de carga da seguinte maneira:

   | Configuração | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa o balanceador de carga. Por exemplo, **sqlLB**. |
   | **Tipo** |**Interna** |
   | **Rede virtual** |A rede virtual padrão que foi criada deve ter o nome **VM1VNET**. |
   | **Sub-rede** |Selecione a sub-rede na qual estão as instâncias do SQL Server. O padrão deve ser **VM1Subnet**.|
   | **Atribuição de endereço IP** |**Estático** |
   | **Endereço IP privado** |Use o endereço IP `virtualip` que foi criado no cluster. |
   | **Assinatura** |Use a assinatura que foi usada para seu grupo de recursos. |
   | **Grupo de recursos** |Selecione o grupo de recursos no qual estão as instâncias do SQL Server. |
   | **Localidade** |Selecione o local do Azure no qual estão as instâncias do SQL Server. |

### <a name="configure-the-back-end-pool"></a>Configure o pool de back-end
O Azure chama o *pool de back-ends* do pool de endereços back-end. Nesse caso, o pool de back-ends é composto por endereços das três instâncias do SQL Server em seu grupo de disponibilidade. 

1. No grupo de recursos, clique no balanceador de carga que você criou. 

2. Em **Configurações**, clique em **Pools de back-end**.

3. Em **Pools de back-end**, clique em **Adicionar** para criar um pool de endereços de back-end. 

4. Em **Adicionar pool de back-ends**, sob **Nome**, digite um nome para o pool de back-ends.

5. Em **Associado a**, selecione **Máquina virtual**. 

6. Selecione cada máquina virtual no ambiente e associe o endereço IP apropriado a cada seleção.

    :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-backend-pool.png" alt-text="Adicionar pool de back-end":::

7. Clique em **Adicionar**. 

### <a name="create-a-probe"></a>Criar uma investigação

A investigação define como o Azure verifica qual das instâncias do SQL Server é proprietária atual do ouvinte do grupo de disponibilidade. O Azure investiga o serviço com base no endereço IP em uma porta que você define quando cria o teste.

1. Na folha **Configurações** do balanceador de carga, clique em **Investigações de integridade**. 

2. Na folha **Investigações de integridade**, clique em **Adicionar**.

3. Configure a investigação na folha **Adicionar investigação** . Use os valores a seguir para configurar a investigação:

   | Configuração | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa a investigação. Por exemplo, **SQLAlwaysOnEndPointProbe**. |
   | **Protocolo** |**TCP** |
   | **Porta** |Você pode usar qualquer porta disponível. Por exemplo, *59999*. |
   | **Intervalo** |*5* |
   | **Limite não íntegro** |*2* |

4.  Clique em **OK**. 

5. Faça logon em todas as suas máquinas virtuais e abra a porta de investigação usando os seguintes comandos:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

O Azure cria a investigação e a usa para testar qual instância do SQL Server tem o ouvinte do grupo de disponibilidade.

### <a name="set-the-load-balancing-rules"></a>Definir as regras de balanceamento de carga

As regras de balanceamento de carga configuram como o balanceador de carga encaminha o tráfego para as instâncias do SQL Server. Para este balanceador de carga, habilite o retorno de servidor direto, pois somente uma das três instâncias do SQL Server é proprietária do recurso de ouvinte do grupo de disponibilidade por vez.

1. Na folha **Configurações** do balanceador de carga, clique em **Regras de balanceamento de carga**. 

2. Na folha **Regras de balanceamento de carga**, clique em **Adicionar**.

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

   :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-load-balancing-rule.png" alt-text="Adicionar regra de balanceamento de carga":::

4. Clique em **OK**. 
5. O Azure configura a regra de balanceamento de carga. Agora, o balanceador de carga está configurado para rotear o tráfego para a instância do SQL Server que hospeda o ouvinte para o grupo de disponibilidade. 

Neste ponto, o grupo de recursos tem um balanceador de carga que se conecta em todas as máquinas do SQL Server. O balanceador de carga também contém um endereço IP para o ouvinte do grupo de disponibilidade Always On do SQL Server para que qualquer computador possa responder às solicitações para os grupos de disponibilidade.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>Criar o recurso do balanceador de carga no cluster

1. Faça logon na máquina virtual primária. Precisamos criar o recurso para habilitar a porta de investigação do Azure Load Balancer (59999 é usado em nosso exemplo). Execute o comando a seguir:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Crie um grupo que contém o recurso `virtualip` e `azure_load_balancer`:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Adicionar restrições

1. Uma restrição de colocalização deve ser configurada para verificar se o endereço IP do balanceador de carga do Azure e o recurso do AG estão em execução no mesmo nó. Execute o comando a seguir:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Crie uma restrição de ordenação para garantir que o recurso AG esteja em funcionamento antes do endereço IP do balanceador de carga do Azure. Enquanto a restrição de colocalização implica uma restrição de ordenação, isso a impõe.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. Para verificar as restrições, execute o seguinte comando:

    ```bash
    sudo pcs constraint list --full
    ```

    Você deve ver o seguinte resultado:

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>Crie o ouvinte do grupo de disponibilidade

1. No nó primário, execute os seguintes comandos no SQLCMD ou no SSMS:

    - Substitua o endereço IP usado abaixo pelo endereço IP `virtualip`.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Faça logon em cada nó de VM. Use o comando a seguir para abrir o arquivo de hosts e configurar a resolução do `ag1-listener` em cada computador.

    ```
    sudo vi /etc/hosts
    ```

    No editor **vi**, digite `i` para inserir texto e, em uma linha em branco, adicione o IP do `ag1-listener`. Em seguida, adicione `ag1-listener` após um espaço ao lado do IP.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    Para sair do editor **vi**, primeiro pressione a tecla **Esc** e, em seguida, insira o comando `:wq` para gravar o arquivo e encerrar. Faça isso em cada nó.

## <a name="test-the-listener-and-a-failover"></a>Testar o ouvinte e um failover

### <a name="test-logging-in-to-sql-server-using-the-availability-group-listener"></a>Testar o logon no SQL Server usando o ouvinte do grupo de disponibilidade

1. Use o SQLCMD para fazer logon no nó primário do SQL Server usando o nome do ouvinte do grupo de disponibilidade:

    - Use um logon criado anteriormente e substitua `<YourPassword>` pela senha correta. O exemplo abaixo usa o logon `sa` que foi criado com o SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Verifique o nome do servidor ao qual você está conectado. Execute o seguinte comando no SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    A saída deve mostrar o nó primário atual. Isso deverá ser `VM1` se você nunca tiver testado um failover.

    Saia da sessão do SQL Server digitando o comando `exit`.

### <a name="test-a-failover"></a>Testar um failover

1. Execute o comando a seguir para fazer failover manual da réplica primária para `<VM2>` ou outra réplica. Substitua `<VM2>` pelo valor do nome do servidor.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Se você verificar suas restrições, verá que outra restrição foi adicionada devido ao failover manual:

    ```bash
    sudo pcs constraint list --full
    ```

    Você verá que uma restrição com a ID `cli-prefer-ag_cluster-master` foi adicionada.

1. Remova a restrição com a ID `cli-prefer-ag_cluster-master` usando o seguinte comando:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Verifique os recursos de cluster usando o comando `sudo pcs resource`; você verá que a instância primária agora é `<VM2>`.

    > [!NOTE]
    > Este artigo contém referências ao termo "servidor subordinado", um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.


    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Use o SQLCMD para fazer logon em sua réplica primária usando o nome do ouvinte:

    - Use um logon criado anteriormente e substitua `<YourPassword>` pela senha correta. O exemplo abaixo usa o logon `sa` que foi criado com o SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
     ```

1. Verifique o servidor ao qual você está conectado. Execute o seguinte comando no SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Você verá que agora está conectado à VM para a qual você fez failover.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre balanceadores de carga no Azure, confira:

> [!div class="nextstepaction"]
> [Configurar um balanceador de carga para um grupo de disponibilidade no SQL Server em VMs do Azure](../windows/availability-group-load-balancer-portal-configure.md)
