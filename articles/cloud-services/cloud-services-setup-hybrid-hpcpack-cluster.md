---
title: "Configurar um cluster do HPC Pack híbrido no Azure | Microsoft Docs"
description: "Aprenda a usar o Microsoft HPC Pack e o Azure para configurar um cluster de computação de alto desempenho (HPC) híbrido e pequeno"
services: cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: hpc-pack
ms.assetid: 
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: danlep
ms.openlocfilehash: f6dc9657e64160be1e68a7356863b53131e9b3c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>Configurar um cluster HPC (computação de alto desempenho) híbrido com nós de computação do Azure sob demanda e o Microsoft HPC Pack
Use o Microsoft HPC Pack 2012 R2 e o Azure para configurar um cluster HPC (computação de alto desempenho) híbrido e pequeno. O cluster mostrado neste artigo é composto por um nó de cabeçalho do HPC Pack local e alguns nós de computação que você implanta sob demanda em um serviço de nuvem do Azure. É possível executar trabalhos de computação no cluster híbrido.

![Cluster híbrido de HPC][Overview] 

Este tutorial mostra uma abordagem, às vezes chamada de cluster "de estouro para a nuvem", para usar os recursos de computação dimensionáveis e sob demanda do Azure para executar aplicativos com uso intensivo de computação.

Este tutorial não pressupõe nenhuma experiência anterior com clusters de cálculo ou com o HPC Pack. Ele destina-se somente para ajudá-lo a implantar um cluster híbrido de cálculo de forma rápida para fins de demonstração. Para obter as considerações e as etapas para implantar um cluster híbrido do HPC Pack em uma escala maior em um ambiente de produção, ou para usar o HCP Pack 2016, confira as [diretrizes detalhadas](http://go.microsoft.com/fwlink/p/?LinkID=200493). Para ver outros cenários com o HPC Pack, incluindo a implantação de cluster automatizada nas máquinas virtuais do Azure, confira [Opções de cluster HPC com o Microsoft HPC Pack no Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura do Azure** – Se não tiver uma, você poderá criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.
* **Um computador local executando o Windows Server 2012 R2 ou o Windows Server 2012** – use computador como o nó de cabeçalho do cluster HPC. Se você ainda não estiver executando o Windows Server, poderá baixar e instalar uma [versão de avaliação](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * O computador deve estar ingressado a um domínio do Active Directory. Para fins de teste, você pode configurar o computador do nó de cabeçalho como um controlador de domínio. Para adicionar a função de servidor dos Active Directory Domain Services e promover o computador do nó de cabeçalho como um controlador de domínio, consulte a documentação do Windows Server.
  * Para oferecer suporte ao HPC Pack, o sistema operacional deve ser instalado em um desses idiomas: inglês, japonês ou chinês (simplificado).
  * Verifique se as atualizações importantes e críticas foram instaladas.
* **HPC Pack 2012 R2** - [Baixe](http://go.microsoft.com/fwlink/p/?linkid=328024) o pacote de instalação correspondente à versão gratuita mais recente e copie os arquivos para o computador do nó de cabeçalho. Escolha os arquivos de instalação no mesmo idioma da sua instalação do Windows Server.

    >[!NOTE]
    > Se você quiser usar o HPC Pack 2016 em vez do HPC Pack 2012 R2, será necessária uma configuração adicional. Confira as [orientações detalhadas](http://go.microsoft.com/fwlink/p/?LinkID=200493).
    > 
* **Conta de domínio** - Esta conta deve ser configurada com as permissões de Administrador local no nó de cabeçalho para instalar o HPC Pack.
* **Conectividade TCP na porta 443** do nó de cabeçalho para o Azure.

## <a name="install-hpc-pack-on-the-head-node"></a>Instalar o HPC Pack no nó de cabeça
Primeiro, instale o Microsoft HPC Pack em seu computador local executando o Windows Server. Esse computador se tornará o nó de cabeçalho do cluster.

1. Faça login no nó de cabeça usando uma conta de domínio que tenha permissões de Administrador local.

2. Inicie o Assistente de Instalação do HPC Pack executando Setup.exe nos arquivos de instalação do HPC Pack.

3. Na tela **Instalação do HPC Pack 2012 R2**, clique em **Nova instalação ou adicionar novos recursos a uma instalação existente**.

    ![Instalação do HPC Pack 2012][install_hpc1]

4. Na **página do Contrato de Usuário do Software da Microsoft**, clique em **Avançar**.

5. Na página **Selecionar Tipo de Instalação**, clique em **Criar um novo cluster de HPC, criando um nó de cabeça** e, em seguida, clique em **Avançar**.

6. O assistente executa vários testes de pré-instalação. Clique em **Avançar** on the **Regras de instalação** se todos os testes forem aprovados. Caso contrário, examine as informações fornecidas e faça as alterações necessárias em seu ambiente. Execute os testes novamente ou se necessário inicie o Assistente de Instalação novamente.
7. Na página **Configuração do BD de HPC**, certifique-se de que **Nó de cabeça** esteja selecionado para todos os bancos de dados de HPC e, em seguida, clique em **Avançar**. 

    ![Configuração do BD][install_hpc4]

8. Aceite as seleções padrão nas páginas restantes do assistente. Na página **Instalar Componentes necessários**, clique em **Instalar**.
   
    ![Instalar][install_hpc6]

9. Após a conclusão da instalação, desmarque **Iniciar o Gerenciador de Cluster de HPC** e, em seguida, clique em **Concluir**. (Você iniciará o Gerenciador de Cluster de HPC em uma etapa posterior.)
   
    ![Concluir][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Preparar a assinatura do Azure
Realize as etapas a seguir no [portal do Azure](https://portal.azure.com) com sua assinatura do Azure. Após concluir essas etapas, você pode implantar os nós do Azure no nó de cabeçalho local. 
  
  > [!NOTE]
  > Além disso, tome nota de sua ID de assinatura do Azure, que será necessária posteriormente. Encontre a ID em **Assinaturas** no portal.
  > 

### <a name="upload-the-default-management-certificate"></a>Carregue o certificado de gerenciamento padrão
O HPC Pack instala um certificado autoassinado no nó de cabeça, chamado de Certificado de gerenciamento padrão de HPC no Azure da Microsoft, que pode ser carregado como um certificado de gerenciamento do Azure. Esse certificado é fornecido para implantações de teste e de prova de conceito para proteger a conexão entre o nó de cabeçalho e o Azure.

1. No computador do nó de cabeçalho, entre no [portal do Azure](https://portal.azure.com).

2. Clique em **Assinaturas** > *nome_da_assinatura*.

3. Clique em **Certificados de gerenciamento** > **Carregar**.4. Procure no nó de cabeçalho pelo arquivo C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer. Em seguida, clique em **Carregar**.

   
O certificado de **Gerenciamento padrão de HPC no Azure** aparece na lista de certificados de gerenciamento.

### <a name="create-an-azure-cloud-service"></a>Criar um serviço de nuvem do Azure
> [!NOTE]
> Para melhor desempenho, crie o serviço de nuvem e a conta de armazenamento (em uma etapa posterior) na mesma região geográfica.
> 
> 

1. No portal, clique em **Serviços de nuvem (clássico)** > **+Adicionar**.

2.  Digite um nome DNS para o serviço, escolha um grupo de recursos e um local e, em seguida, clique em **Criar**.


### <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure
1. No portal, clique em **Contas de armazenamento (clássico)** > **+Adicionar**.

2. Digite um nome para a conta e, em seguida, selecione o modelo de implantação **Clássico**.

3. Escolha um grupo de recursos e um local e deixe as outras configurações com os valores padrão. Em seguida, clique em **Criar**.

## <a name="configure-the-head-node"></a>Configurar o nó de cabeça
Para usar o Gerenciador de Cluster de HPC para implantar nós do Azure e para enviar trabalhos, primeiro realize algumas etapas de configuração necessárias do cluster.

1. No nó de cabeça, inicie o Gerenciador de Cluster de HPC. Se a caixa de diálogo **Selecionar Nó de Cabeça** for exibida, clique em **Computador Local**. A **Lista de tarefas de implantação** é exibida.

2. Em **Tarefas de implantação necessárias**, clique em **Configurar sua rede**.
   
    ![Configurar Rede][config_hpc2]

3. No Assistente de Configuração de Rede, selecione **Todos os nós somente em uma rede corporativa** (Topologia 5). Essa configuração de rede é a mais simples para fins de demonstração.
   
    ![Topologia 5][config_hpc3]
   
4. Clique em **Avançar** para aceitar os valores padrão nas páginas restantes do assistente. Em seguida, na guia **Examinar**, clique em **Configurar** para concluir a configuração de rede.

5. Na **Lista de tarefas de implantação**, clique em **Fornecer credenciais de instalação**.

6. Na caixa de diálogo **Credenciais de instalação** , digite as credenciais da conta de domínio usada para instalar o HPC Pack. Em seguida, clique em **OK**. 
   
    ![Credenciais de instalação][config_hpc6]
   
7. Na **Lista de tarefas de implantação**, clique em **Configurar a nomeação dos novos nós**.

8. Na caixa de diálogo **Especificar Série de Nomeação de Nós**, aceite a série de nomeação padrão e clique em **OK**. Conclua esta etapa mesmo que os nós do Azure que você adicionar neste tutorial sejam nomeados automaticamente.
   
    ![Nomeação de nós][config_hpc8]
   
9. Na **Lista de tarefas de implantação**, clique em **Criar um modelo de nó**. Posteriormente no tutorial, você usará o modelo do nó para adicionar nós do Azure ao cluster.

10. Em Criar Assistente do modelo de nó, faça o seguinte:
    
    a. Na página **Escolher Tipo de Modelo de Nó**, clique em **Modelo de nó do Microsoft Azure** e, em seguida, clique em **Avançar**.
    
    ![Modelo do nó][config_hpc10]
    
    b. Clique em **Avançar** para aceitar o nome do modelo padrão.
    
    c. Na página **Fornecer Informações sobre Assinatura**, insira sua ID de Assinatura do Azure (disponível em suas informações de conta do Azure). Em seguida, em **Certificado de gerenciamento**, procure por **Gerenciamento do Azure Padrão no Microsoft HPC.** Em seguida, clique em **Próximo**.
    
    ![Modelo do nó][config_hpc12]
    
    d. Na página **Fornecer informações sobre serviço** , selecione o serviço de nuvem e a conta de armazenamento criados na etapa anterior. Em seguida, clique em **Próximo**.
    
    ![Modelo do nó][config_hpc13]
    
    e. Clique em **Avançar** para aceitar os valores padrão nas páginas restantes do assistente. Em seguida, na guia **Examinar**, clique em **Criar** para criar o modelo de nó.
    
    > [!NOTE]
    > Por padrão, o modelo de nó do Azure inclui configurações para iniciar (provisionar) e interromper os nós manualmente, usando o Gerenciador de Cluster HPC. Como opção, você também pode configurar uma agenda para iniciar e interromper os nós do Azure automaticamente.
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>Adicionar nós do Azure ao cluster
Agora, use o modelo do nó para adicionar nós do Azure ao cluster. Adicionar os nós ao cluster armazena suas informações de configuração para que você possa iniciá-los (provisioná-los) a qualquer momento no serviço de nuvem. Sua assinatura é cobrada pelos nós do Azure somente depois que as instâncias estiverem sendo executadas no serviço de nuvem.

Siga estas etapas para adicionar dois nós pequenos.

1. No Gerenciador de Cluster HPC, clique em **Gerenciamento de Nós** (chamado de **Gerenciamento de Recursos** em versões atuais do HPC Pack) > **Adicionar Nó**.
   
    ![Adicionar Nó][add_node1]

2. No Assistente para Adicionar Nós, na página **Selecionar Método de Implantação**, clique em **Adicionar nós do Windows Azure** e em **Avançar**.
   
    ![Adicionar nó do Azure][add_node1_1]

3. Na página **Especificar Novos Nós**, selecione o modelo do nó do Azure criado anteriormente (chamado por padrão de **Modelo de Nós do Azure Padrão**). Em seguida, especifique **2** nós de tamanho **Pequeno** e clique em **Avançar**.
   
    ![Especificar nós][add_node2]
   
4. Na página de **Conclusão do Assistente de Adição de Nós**, clique em **Concluir**.
    
     Dois nós do Azure, chamados **AzureCN-0001** e **AzureCN-0002**, agora são exibidos no Gerenciador de Cluster de HPC. Ambos estão no estado **Não Implantado** .
   
    ![Nós adicionados][add_node3]

## <a name="start-the-azure-nodes"></a>Iniciar os nós do Azure
Quando desejar usar os recursos de cluster no Azure, use o Gerenciador de Cluster de HPC para iniciar (provisionar) os nós no Azure e colocá-los on-line.

1. No Gerenciador de Cluster HPC, clique em **Gerenciamento de Nós** (chamado de **Gerenciamento de Recursos** em versões atuais do HPC Pack) e selecione os nós do Azure.

2. Clique em **Iniciar** e clique em **OK**.
   
   ![Iniciar nós][add_node4]
   
    Os nós farão a transição para o **Provisionamento** . Exiba o log de provisionamento para controlar o progresso do provisionamento.
   
    ![Provisionar nós][add_node6]

3. Depois de alguns minutos, os nós do Azure concluirão o provisionamento e estarão no estado **Off-line** . Nesse estado, as instâncias de função estão em execução, mas ainda não podem aceitar trabalhos de cluster.

4. Para confirmar se as instâncias de função estão sendo executadas, no portal do Azure, clique em **Serviços de Nuvem (clássico)** > *nome_do_serviço_de_nuvem*.
   
   Você deverá ver duas instâncias (nós) de **HpcWorkerRole** em execução no serviço. O HPC Pack implanta automaticamente duas instâncias **HpcProxy** (de tamanho Médio) para manipular as comunicações entre o nó de cabeça e o Azure.

   ![Executando instâncias][view_instances1]

5. Para colocar os nós do Azure on-line para executar trabalhos de cluster, selecione os nós, clique com o botão direito e clique em **Colocar on-line**.
   
    ![Nós off-line][add_node7]
   
    O Gerenciador de Cluster de HPC indica que os nós estão no estado **On-line** .

## <a name="run-a-command-across-the-cluster"></a>Executar um comando no cluster
Para verificar a instalação, use o comando **clusrun** do HPC Pack para executar um comando ou aplicativo em um ou mais nós do cluster. Como um exemplo simples, use **clusrun** para obter a configuração de IP dos nós do Azure.

1. No nó de cabeçalho, abra um prompt de comando como administrador.

2. Digite o seguinte comando: 
   
    `clusrun /nodes:azurecn* ipconfig`

3. Se solicitado, insira sua senha de administrador de cluster. Você deverá ver uma saída de comando semelhante à seguinte.
   
    ![clusrun][clusrun1]

## <a name="run-a-test-job"></a>Executar um trabalho de teste
Você pode enviar um trabalho de teste que é executado no cluster híbrido. Este exemplo é um trabalho simples de limpeza paramétrica (um tipo de computação intrinsecamente paralela). Este exemplo executa as subtarefas que adicionam um número inteiro a si mesmo usando o comando **set /a** . Todos os nós no cluster contribuem para concluir as subtarefas para números inteiros de 1 a 100.

1. No Gerenciador de Cluster HPC, clique em **Gerenciamento de Trabalho** > **Novo Trabalho de Limpeza Paramétrica**.
   
    ![Novo trabalho][test_job1]

2. Na caixa de diálogo **Novo Trabalho de Limpeza Paramétrica** em **Linha de Comando**, digite `set /a *+*` (substituindo a linha de comando padrão que é exibida). Deixe os valores padrão para o restante das configurações e clique em **Enviar** para enviar o trabalho.
   
    ![Limpeza paramétrica][param_sweep1]

3. Quando o trabalho estiver concluído, clique duas vezes no trabalho **Minhas tarefas de limpeza** .

4. Clique em **Exibir tarefas**e em uma subtarefa para exibir a saída calculada dessa subtarefa.
   
    ![Resultados da tarefa][view_job361]

5. Para visualizar qual nó executou o cálculo para dessa subtarefa, clique em **Nós alocados**. (Seu cluster pode mostrar um nome de nó diferente).
   
    ![Resultados da tarefa][view_job362]

## <a name="stop-the-azure-nodes"></a>Parar os nós do Azure
Depois de testar o cluster, interrompa os nós do Azure para evitar cobranças desnecessárias em sua conta. Essa etapa interromperá o serviço de nuvem e removerá as instâncias de função do Azure.

1. No Gerenciador de Cluster HPC, em **Gerenciamento de Nós** (chamado de **Gerenciamento de Recursos** em versões anteriores do HPC Pack), selecione ambos os nós do Azure. Em seguida, clique em **Parar**.
   
    ![Parar os nós][stop_node1]

2. Na caixa de diálogo **Parar nós do Windows Azure**, clique em **Parar**. 
   
3. Os nós farão a transição para a **Parada** . Depois de alguns minutos, o Gerenciador de Cluster de HPC mostra o estado dos nós como **Não implantados**.
   
    ![Nós não implantados][stop_node4]

4. Para confirmar que as instâncias de função não estão mais sendo executadas no Azure, no portal do Azure, clique em **Serviços de nuvem (clássico)** > *nome_do_serviço_de_nuvem*. Nenhuma instância é implantada no ambiente de produção. 
   
    Assim, concluímos o tutorial.

## <a name="next-steps"></a>Próximas etapas
* Explore a documentação do [HPC Pack](https://technet.microsoft.com/library/cc514029).
* Para configurar uma implantação de cluster de HPC Pack hibrida em uma escala maior, consulte [Intermitência para Instâncias de Função de Trabalho do Azure com Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493).
* Para outras maneiras de criar um cluster de HPC Pack no Azure, inclusive usando modelos do Azure Resource Manager, veja [Opções de cluster HPC com Microsoft HPC Pack no Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png
