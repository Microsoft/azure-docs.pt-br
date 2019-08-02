---
title: Criar um ambiente com um Cluster Service Fabric no Azure DevTest Labs | Microsoft Docs
description: Saiba como criar um ambiente com um cluster de Service Fabric autônomo e iniciar e parar o cluster usando agendamentos.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 1e192a2b27c9d617e43a56766431a0f40e87a752
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325246"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Criar um ambiente com cluster de Service Fabric autônomo no Azure DevTest Labs
Este artigo fornece informações sobre como criar um ambiente com um cluster de Service Fabric autônomo no Azure DevTest Labs. 

## <a name="overview"></a>Visão geral
O DevTest Labs pode criar ambientes de teste independentes, conforme definido pelos modelos de gerenciamento de recursos do Azure. Esses ambientes contêm recursos de IaaS, como máquinas virtuais e recursos de PaaS, como Service Fabric. O DevTest Labs permite que você gerencie máquinas virtuais em um ambiente fornecendo comandos para controlar as máquinas virtuais. Esses comandos oferecem a capacidade de iniciar ou parar uma máquina virtual em um agendamento. Da mesma forma, os DevTest Labs também podem ajudá-lo a gerenciar clusters Service Fabric em um ambiente. Você pode iniciar ou parar um Cluster Service Fabric em um ambiente manualmente ou por meio de uma agenda.

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric
Service Fabric clusters são criados usando ambientes no DevTest Labs. Cada ambiente é definido por um modelo de Azure Resource Manager em um repositório git. O [repositório do git público](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) para DevTest Labs contém o modelo do Resource Manager para criar um Cluster Service Fabric na pasta do [cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) do Outfabric. 

1. Primeiro, crie um laboratório no Azure DevTest Labs usando as instruções no seguinte artigo: [Crie um laboratório](devtest-lab-create-lab.md). Observe que a opção **ambientes públicos** está **ativada** por padrão. 
2. Confirme se o provedor de Service Fabric está registrado para sua assinatura seguindo estas etapas:
    1. Selecione **assinaturas** no menu de navegação esquerdo e selecione sua **assinatura**
    2. Na página **assinatura** , selecione **provedores de recursos** na seção **configurações** no menu à esquerda. 
    3. Se **Microsoft. ServiecFabric** não estiver registrado, selecione **registrar**. 
3. Na página **DevTest Lab** para seu laboratório, selecione **+Adicionar** na barra de ferramentas. 
    
    ![Botão Adicionar na barra de ferramentas](./media/create-environment-service-fabric-cluster/add-button.png)
3. Na página **escolher uma base** , selecione **Service Fabric cluster de laboratório** na lista. 

    ![Selecione Service Fabric cluster de laboratório na lista](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Na página **definir configurações** , execute as seguintes etapas: 
    1. Especifique um **nome** para o seu **ambiente**de cluster. Esse é o nome do grupo de recursos no Azure no qual o cluster de Service Fabric será criado. 
    2. Selecione o **sistema operacional (SO)** para as máquinas virtuais do cluster. O valor padrão é: **Windows**.
    3. Especifique um nome para o **administrador** do cluster. 
    4. Especifique uma **senha** para o administrador. 
    5. Para o **certificado**, insira suas informações de certificado como uma cadeia de caracteres codificada em base64. Para criar um certificado, execute as seguintes etapas:
        1. Baixe o arquivo **Create-ClusterCertificate. ps1** do [repositório git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Como alternativa, clone o repositório em seu computador. 
        2. Inicie o **PowerShell**. 
        3. Execute o arquivo **ps1** usando o comando `.\Create-ClusterCertificate.ps1`. Você verá um arquivo de texto aberto no bloco de notas com as informações necessárias para preencher os campos relacionados ao certificado nesta página. . 
    6. Insira a **senha para o certificado**.
    7. Especifique a **impressão digital** para seu certificado.
    8. Selecione **Adicionar** na página **definir configurações** . 

        ![Definir configurações de cluster](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Depois que o cluster for criado, você verá um grupo de recursos com o nome do ambiente fornecido na etapa anterior. Ao expandir, você verá o Cluster Service Fabric nele. Se o status do grupo de recursos estiver preso na **criação**, selecione **Atualizar** na barra de ferramentas. O ambiente de **cluster Service Fabric** cria um cluster de um nó de 5 nós no Linux ou no Windows.

    No exemplo a seguir, **mysfabricclusterrg** é o nome do grupo de recursos que é criado especificamente para o Cluster Service Fabric. É importante observar que os ambientes de laboratório são independentes no grupo de recursos em que são criados. Isso significa que o modelo que define o ambiente, que só pode acessar recursos dentro do grupo de recursos recém-criado ou das [redes virtuais configuradas para ser usado pelo laboratório](devtest-lab-configure-vnet.md). Este exemplo acima cria todos os recursos necessários no mesmo grupo de recursos.

    ![Cluster criado](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Iniciar ou parar o cluster
Você pode iniciar ou parar o cluster da própria página do laboratório do DevTest ou da página de Cluster Service Fabric fornecida pelo DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Na página do laboratório do DevTest
Você pode iniciar ou parar o cluster na página do laboratório do DevTest para seu laboratório. 

1. Selecione **três pontos (...)** para o Cluster Service Fabric, conforme mostrado na imagem a seguir: 

    ![Comandos Iniciar e parar para o cluster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Você vê dois comandos no menu de contexto para **Iniciar** e **parar** o cluster. O comando Iniciar inicia todos os nós em um cluster. O comando Stop interrompe todos os nós em um cluster. Depois que um cluster é interrompido, o próprio cluster de Service Fabric permanece em um estado pronto, mas nenhum nó está disponível até que o comando Iniciar seja reemitido no cluster no laboratório.

    Há algumas considerações a serem observadas ao usar um Cluster Service Fabric em um ambiente de teste. Pode levar algum tempo para que o cluster de Service Fabric reidratar completamente após a reinicialização dos nós. Para manter os dados do desligamento para a inicialização, os dados devem ser salvos em um disco gerenciado anexado à máquina virtual. Há implicações de desempenho ao usar um disco gerenciado anexado, portanto, ele é recomendado apenas para ambientes de teste. Se o disco usado para armazenamento de dados não for apoiado, os dados serão perdidos quando o comando de parada for emitido no cluster.

### <a name="from-the-service-fabric-cluster-page"></a>Na página do Cluster Service Fabric 
Há outra maneira de iniciar ou parar o cluster. 

1. Selecione o Cluster Service Fabric no modo de exibição de árvore na página do laboratório do DevTest. 

    ![Selecione o cluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Na página **cluster Service Fabric** para o cluster, você verá comandos na barra de ferramentas para iniciar ou parar o cluster. 

    ![Iniciar ou parar comandos na página do Cluster Service Fabric](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Configurar a inicialização automática e a agenda de desligamento automático
Service Fabric clusters também podem ser iniciados ou interrompidos em um agendamento. Essa experiência é semelhante à experiência para máquinas virtuais em um laboratório. Para economizar dinheiro, por padrão, cada cluster criado em um laboratório é desligado automaticamente no momento definido pela [política](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)de desligamento do laboratório. Você pode substituir especificando se o cluster deve ser desligado ou especificando a hora em que o cluster é desligado. 

![Agendas existentes para início automático e desligamento automático](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Aceitar a agenda de início automático
Para aceitar o agendamento de inicialização, execute as seguintes etapas:

1. Selecione **início automático** no menu à esquerda
2. Selecione **ativado** para **permitir que este cluster do Service Fabric seja agendado para início automático**. Esta página só será habilitada se o proprietário do laboratório tiver permitido que os usuários iniciem as máquinas virtuais ou os clusters de Service Fabric.
3. Selecione **Salvar** na barra de ferramentas. 

    ![Página de estrela automática](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Definir configurações de desligamento automático 
Para alterar as configurações de desligamento, execute as seguintes etapas:

1. Selecione **desligamento automático** no menu à esquerda. 
2. Nessa página, você pode recusar o desligamento automático selecionando **desativado** para **habilitado**. 
3. Se você selecionou **ativado** para **habilitado**, siga estas etapas:
    1. Especifique a **hora** de desligamento.
    2. Especifique o **fuso horário** para a hora. 
    3. Especifique se você deseja que o DevTest Labs envie **notificações** antes do desligamento automático. 
    4. Se você selecionou **Sim** para a opção de notificação, ESPECIFIQUE a **URL** do webhook e/ou o **endereço de email** para enviar notificações. 
    5. Selecione **Salvar** na barra de ferramentas.

        ![Página de desligamento automático](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Para exibir nós no cluster de Service Fabric
A página de Cluster Service Fabric que você viu nas etapas anteriores é específica para a página do DevTest Labs. Ele não mostra os nós no cluster. Para ver mais informações sobre o cluster, siga estas etapas:

1. Na página do **laboratório do DevTest** para seu laboratório, selecione o grupo de **recursos** no modo de exibição de árvore na seção **minhas máquinas virtuais** .

    ![Escolha o grupo de recursos](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Na página **grupo de recursos** , você verá todos os recursos no grupo de recursos em uma lista. Selecione o **cluster Service Fabric** na lista. 

    ![Selecione o cluster na lista](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Você verá a página de **cluster Service Fabric** para o cluster. Essa é a página que o Service Fabric fornece. Você verá todas as informações sobre os clusters, como nós, tipos de nós, etc.

    ![home page de Cluster Service Fabric](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos para obter detalhes sobre ambientes: 

- [Crie ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurar e usar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
