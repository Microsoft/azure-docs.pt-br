---
title: Configurar recuperação de desastre para uma implantação do Dynamics AX de várias camada usando o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar a recuperação de desastres para o Dynamics AX com o Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: b97bf56c23dfa96acf7cb5af5ac28b4270de117d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61281380"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Configurar a recuperação de desastre para um aplicativo do Dynamics AX de várias camadas   




 O Dynamics AX é uma das soluções de ERP mais populares usadas pelas empresas para padronizar processos entre locais, gerenciar recursos e simplificar a conformidade. Como o aplicativo é crítico para uma organização, em caso de desastre, ele deve estar em execução em tempo mínimo.

Hoje, o Dynamics AX não fornece nenhuma funcionalidade integrada de recuperação de desastre. O Dynamics AX consiste em vários componentes de servidor, como o Servidor de Objetos de Aplicativo do Windows, Azure Active Directory, Banco de Dados SQL do Azure, SharePoint Server e Reporting Services. Gerenciar a recuperação de desastre de cada um desses componentes manualmente não é apenas caro, mas também propenso a erros.

Este artigo explica como você pode criar uma solução de recuperação de desastre para o aplicativo Dynamics AX usando o [Azure Site Recovery](site-recovery-overview.md). Também aborda failovers de teste planejados/não planejados usando o plano de recuperação com um único clique, configurações com suporte e pré-requisitos.



## <a name="prerequisites"></a>Pré-requisitos

A implementação de recuperação de desastre para aplicativos do Dynamics AX usando o Site Recovery exige os seguintes pré-requisitos:

• Configurar uma implantação local do Dynamics AX.

• Criar um cofre do Site Recovery em uma assinatura do Azure.

• Se o Azure for o site de recuperação, execute a ferramenta de Avaliação de Prontidão de Máquina Virtual do Azure nas VMs. Elas devem ser compatíveis com as Máquinas Virtuais do Azure e os serviços do Site Recovery.

## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para a finalidade de criação deste artigo, usamos as máquinas virtuais do VMware com o Dynamics AX 2012 R3 no Windows Server 2012 R2 Enterprise. Já que a replicação do Site Recovery é independente do aplicativo, esperamos que as recomendações fornecidas aqui sirvam para os cenários a seguir.

### <a name="source-and-target"></a>Origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Habilitar a recuperação de desastre do aplicativo Dynamics AX usando o Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Proteger o aplicativo Dynamics AX
Para habilitar a replicação e a recuperação completas do aplicativo, cada componente do Dynamics AX deve ser protegido.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Configurar o Active Directory e a replicação do DNS

O Active Directory é necessário no site de recuperação de desastre para que o aplicativo Dynamics AX funcione. Recomendamos as duas opções a seguir, de acordo com a complexidade do ambiente local do cliente.

**Opção 1**

O cliente tem um número pequeno de aplicativos e um único controlador de domínio para todo o site local e pretende fazer falilover de todo o site em conjunto. Recomendamos que você use a replicação do Site Recovery para replicar o computador do controlador de domínio em um site secundário (aplicável para cenários de site a site e site para o Azure).

**Opção 2**

O cliente tem um grande número de aplicativos e executa uma floresta do Active Directory e pretende fazer failover de alguns aplicativos por vez. Recomendamos que você configure um controlador de domínio adicional no site de recuperação de desastre (um site secundário ou no Azure).

 Para obter mais informações, consulte [Disponibilizar um controlador de domínio em um site de recuperação de desastre](site-recovery-active-directory.md). Para o restante deste documento, vamos pressupor que um controlador de domínio esteja disponível no site de recuperação de desastre.

### <a name="2-set-up-sql-server-replication"></a>2. Configurar a replicação do SQL Server
Para obter diretrizes técnicas sobre a opção recomendada para proteger a camada do SQL, consulte [Replicar aplicativos com o SQL Server e o Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Habilitar a proteção para o cliente do Dynamics AX e as VMs do Servidor de Objetos de Aplicativo
Execute a configuração relevante do Site Recovery de acordo com se as VMs são implantadas no [Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou no [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Recomendamos configurar a frequência consistente com falha para 15 minutos.
>

O instantâneo a seguir mostra o status de proteção de VMs de componentes do Dynamics em um cenário de proteção site do VMware para o Azure.

![Itens protegidos](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Configurar a rede
**Definir as configurações de rede e de computação da VM**

Para o cliente do Dynamics AX e as VMs do Servidor de Objetos de Aplicativo, defina as configurações de rede no Site Recovery, de modo que as redes VM sejam anexadas à rede de recuperação de desastre correta após o failover. Garanta que a rede de recuperação de desastre para essas camadas seja roteável para a camada do SQL.

Você pode selecionar a VM nos itens replicados para definir as configurações de rede, conforme mostrado no seguinte instantâneo:

* Para servidores do Servidor de Objetos de Aplicativo, selecione o conjunto de disponibilidade correto.

* Se estiver usando um IP estático, especifique o IP que você deseja que seja usado pela VM na caixa de texto **IP de Destino**.

    ![Configurações de rede](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. Criar um plano de recuperação

Crie um plano de recuperação no Site Recovery para automatizar o processo de failover. Adicione uma camada de aplicativos e uma camada da Web ao plano de recuperação. Ordene-as em grupos diferentes, de modo que o front-end seja desligado antes da camada de aplicativos.

1. Selecione o cofre do Site Recovery em sua assinatura e selecione o bloco **Planos de Recuperação**.

2. Selecione **+ Plano de recuperação** e especifique um nome.

3. Selecione a **Origem** e o **Destino**. O destino pode ser o Azure ou um site secundário. Caso você escolha o Azure, deverá especificar o modelo de implantação.

    ![Criar Plano de Recuperação](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Selecione o Servidor de Objetos de Aplicativo e as VMs do cliente para o plano de recuperação e selecione o ✓.

    ![Selecionar os itens](./media/site-recovery-dynamics-ax/selectvms.png)

    Exemplo de plano de recuperação:

    ![Detalhes do plano de recuperação](./media/site-recovery-dynamics-ax/recoveryplan.png)

Personalize o plano de recuperação para o aplicativo Dynamics AX adicionando as etapas a seguir. O instantâneo anterior mostra o plano de recuperação completo depois de adicionar todas as etapas.


* **Etapas de failover do SQL Server**: Para obter informações sobre as etapas de recuperação específicas para SQL server, consulte [Aplicativos de replicação com SQL Server e Azure Site Recovery](site-recovery-sql.md).

* **Grupo de failover 1**: Fazer failover de VMs do Servidor de Objetos de Aplicativo.
Verifique se o ponto de recuperação selecionado está tão próximo quanto possível do PIT do banco de dados, mas não à frente dele.

* **Script**: Adicione balanceador de carga (somente E-A).
Adicione um script (por meio da Automação do Azure) depois que o grupo de VMs do Servidor de Objetos de Aplicativo for exibido para adicionar um balanceador de carga a ele. Você pode usar um script para realizar essa tarefa. Para obter mais informações, consulte [Como adicionar um balanceador de carga para a recuperação de desastre de aplicativos de várias camadas](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Grupo de failover 2**: Fazer failover de VMs do cliente do Dynamics AX. Faça failover das VMs da camada da Web como parte do plano de recuperação.


### <a name="perform-a-test-failover"></a>Executar um failover de teste

Para obter mais informações específicas ao Active Directory durante o failover de teste, consulte o guia complementar “Solução de recuperação de desastre do Active Directory”.

Para obter mais informações específicas ao SQL Server durante o failover de teste, consulte [Replicar aplicativos com o SQL Server e o Azure Site Recovery](site-recovery-sql.md).

1. Acesse o portal do Azure e selecione seu cofre do Site Recovery.

2. Selecione no plano de recuperação criado para o Dynamics AX.

3. Selecione **Failover de Teste**.

4. Selecione a rede virtual para iniciar o processo de failover de teste.

5. Depois que o ambiente secundário estiver funcionando, você poderá executar as validações.

6. Após a conclusão das validações, selecione **Validações concluídas** e o ambiente do failover de teste será limpo.

Para obter mais informações sobre como executar um failover de teste, consulte [Failover de teste para o Azure no Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Executar um failover

1. Acesse o portal do Azure e selecione seu cofre do Site Recovery.

2. Selecione no plano de recuperação criado para o Dynamics AX.

3. Selecione **Failover** e, em seguida, **Failover**.

4. Selecione a rede de destino e selecione **✓** para iniciar o processo de failover.

Para obter mais informações sobre como fazer um failover, consulte [Failover no Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Executar um failback

Para obter considerações específicas ao SQL Server durante o failback, consulte [Replicar aplicativos com o SQL Server e o Azure Site Recovery](site-recovery-sql.md).

1. Acesse o portal do Azure e selecione seu cofre do Site Recovery.

2. Selecione no plano de recuperação criado para o Dynamics AX.

3. Selecione **Failover** e, em seguida, **Failover**.

4. Selecione **Alterar Direção**.

5. Selecione as opções apropriadas: sincronização de dados e criação de VM.

6. Selecione **✓** para iniciar o processo de failback.


Para obter mais informações sobre como fazer um failback, consulte [Failback de VMs do VMware do Azure para o local](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Resumo
Usando o Site Recovery, você pode criar um plano totalmente automatizado de recuperação de desastre para seu aplicativo Dynamics AX. Caso haja uma interrupção, você poderá iniciar o failover em alguns segundos em qualquer lugar e fazer com que o aplicativo funcione em questão de minutos.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como proteger cargas de trabalho corporativas com o Site Recovery, consulte [Quais cargas de trabalho posso proteger?](site-recovery-workload.md).
