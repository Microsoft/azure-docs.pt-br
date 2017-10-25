---
title: "Criar um nó principal do Pacote HPC em uma VM do Azure | Microsoft Docs"
description: "Saiba como usar o Portal do Azure e o modelo de implantação do Resource Manager para criar um nó de cabeçalho do Microsoft HPC Pack 2012 R2 em uma VM do Azure."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,hpc-pack
ms.assetid: e6a13eaf-9124-47b4-8d75-2bc4672b8f21
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: b2bb9caf82a580dc5f67ea0b0b1c2e9a46363e9c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Criar o nó principal de um cluster de Pacote HPC em uma VM do Azure com uma imagem do Marketplace
Use uma [imagem de máquina virtual do Microsoft HPC Pack 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) do Azure Marketplace e o Portal do Azure para criar o nó de cabeçalho de um cluster HPC. Esta imagem da VM do Pacote HPC baseia-se no Windows Server 2012 R2 Datacenter com Pacote HPC 2012 R2 Atualização 3 pré-instalado. Use esse nó principal para uma implantação de prova de conceito do Pacote HPC no Azure. Você pode adicionar nós de computação ao cluster para executar cargas de trabalho HPC.

> [!TIP]
> Para implantar um cluster HPC Pack 2012 R2 completo no Azure que inclua o nó de cabeçalho e os nós de computação, é recomendável usar um método automatizado. As opções incluem o [script de implantação IaaS do HPC Pack](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) e o modelo do Resource Manager, como o [cluster HPC Pack para cargas de trabalho do Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Os modelos do Resource Manager também estão disponíveis para [clusters Microsoft HPC Pack 2016](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates). 
> 
> 

## <a name="planning-considerations"></a>Considerações sobre planejamento
Conforme mostrado na figura a seguir, implante o nó principal do HPC Pack em um domínio do Active Directory em uma rede virtual do Azure.

![Nó principal do Pacote HPC][headnode]

* **Domínio do Active Directory**: o nó de cabeçalho do HPC Pack 2012 R2 deve ser adicionado a um domínio do Active Directory no Azure antes de iniciar os serviços do HPC na VM. Como mostrado neste artigo, para obter uma implantação prova de conceito, você pode promover a VM criada para o nó principal como controlador de domínio antes de iniciar os serviços do HPC. Outra opção é implantar um controlador de domínio e floresta separados no Azure aos quais você pode adicionar a VM de nó principal.

* **Modelo de implantação**: para a maioria das novas implantações, a Microsoft recomenda usar o modelo de implantação do Resource Manager. Este artigo pressupõe que você use esse modelo de implantação.

* **Rede virtual do Azure**: ao usar o modelo de implantação do Resource Manager para implantar o nó de cabeçalho, você especifica ou cria uma rede virtual do Azure. Use a rede virtual se você precisar ingressar o nó principal em um domínio existente do Active Directory. Você também precisará dele posteriormente para adicionar VMs do nó de computação ao cluster.

## <a name="steps-to-create-the-head-node"></a>Etapas para criar o nó principal
Veja a seguir as etapas de nível superior para usar o Portal do Azure para criar uma VM do Azure para o nó principal do HPC Pack usando o modelo de implantação do Resource Manager. 

1. Se você quiser criar uma nova floresta do Active Directory no Azure com VMs do controlador de domínio separadas, uma opção será usar um [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc). Para uma prova de conceito simples da implantação, é suficiente omitir essa etapa e configurar a própria VM do nó principal como um controlador de domínio. Essa opções será descrita posteriormente neste artigo.
2. Na [página HPC Pack 2012 R2 no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) no Azure Marketplace, clique em **Criar Máquina Virtual**. 
3. No portal, na página do **HPC Pack 2012 R2 no Windows Server 2012 R2**, selecione o modelo de implantação do **Resource Manager** e clique em **Criar**.
   
    ![Imagem do Pacote HPC][marketplace]
4. Use o portal para definir as configurações e criar a VM. Se você ainda estiver conhecendo o Azure, siga o tutorial [Criar uma máquina virtual do Windows no Portal do Azure](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para uma implantação de prova de conceito, você geralmente pode aceitar as configurações padrão ou recomendadas.
   
   > [!NOTE]
   > Se você quiser ingressar o nó principal em um domínio do Active Directory existente no Azure, verifique se você especificou a rede virtual para esse domínio ao criar a VM.
   > 
   > 
5. Depois de criar a VM e quando ela estiver em execução, [conecte-se a ela](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) por meio da Área de Trabalho Remota. 
6. Adicione a VM a uma floresta de domínio do Active Directory escolhendo uma das seguintes opções:
   
   * Se você criou a VM em uma rede virtual do Azure com uma floresta de domínio existente, use o Gerenciador do servidor padrão ou as ferramentas do Windows PowerShell para ingressá-la na floresta. Em seguida, reinicie.
   * Se você tiver criado a VM em uma nova rede virtual (sem uma floresta de domínio existente), promova a VM a controlador de domínio. Use as etapas padrão para instalar e configurar a função Serviços de Domínio do Active Directory no nó principal. Para obter etapas detalhadas, consulte [Instalar uma nova floresta do Active Directory no Windows Server 2012](https://technet.microsoft.com/library/jj574166.aspx).
7. Depois que a VM estiver em execução e fizer parte de uma floresta do Active Directory, inicie os serviços do HPC Pack da seguinte maneira:
   
    a. Conecte-se à VM do nó principal com uma conta de domínio que seja membro do grupo Administradores local. Por exemplo, use a conta de administrador que foi configurada quando você criou a VM do nó principal.
   
    b. Para uma configuração de nó principal padrão, inicie o Windows PowerShell como administrador e digite o seguinte:
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    Pode levar vários minutos para que os serviços do Pacote HPC iniciem.
   
    Para opções de configuração adicionais de nó principal, digite `get-help HPCHNPrepare.ps1`.

## <a name="next-steps"></a>Próximas etapas
* Agora, você pode trabalhar com o nó principal do cluster do Pacote HPC. Por exemplo, inicie o Gerenciador de Cluster do HPC e conclua a [Lista de Tarefas Pendentes de Implantação](https://technet.microsoft.com/library/jj884141.aspx).
* Se você quiser aumentar a capacidade de computação do cluster sob demanda, adicione [nós de disparo contínuo do Azure](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) em um serviço de nuvem. 
* Tente executar uma carga de trabalho de teste no cluster. Para obter um exemplo, consulte o [guia de Introdução](https://technet.microsoft.com/library/jj884144)do Pacote HPC.

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png
