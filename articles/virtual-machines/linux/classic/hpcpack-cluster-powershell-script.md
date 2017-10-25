---
title: Script do PowerShell para implantar o cluster HPC do Linux | Microsoft Docs
description: "Executar um script do PowerShell para implantar um cluster HPC Pack 2012 R2 do Linux nas máquinas virtuais do Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 73041960-58d3-4ecf-9540-d7e1a612c467
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: c15dc66718a855e22f8109448cb8c8a23787b9bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Criar um cluster HPC (computação de alto desempenho) do Linux usando o script de implantação IaaS do HPC Pack
Execute o script do PowerShell de implantação IaaS do HPC Pack para implantar um cluster HPC Pack 2012 R2 completo para cargas de trabalho do Linux nas máquinas virtuais do Azure. O cluster consiste em um nó principal associado do Active Directory que executa o Windows Server e o Pacote HPC da Microsoft, e nós de computação que executam uma das distribuições do Linux com suporte do Pacote HPC. Se você desejar implantar um cluster de HPC Pack no Azure para cargas de trabalho do Linux, consulte [Criar um cluster de HPC Windows com o script de implantação do HPC Pack IaaS](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Você também pode usar um modelo do Gerenciador de Recursos do Azure para implantar um cluster Pacote HPC. Para obter um exemplo, consulte [Criar um cluster HPC com nós de computação Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

> [!IMPORTANT] 
> O script do PowerShell descrito neste artigo cria um cluster Microsoft HPC Pack 2012 R2 no Azure usando o modelo de implantação clássico. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.
> Além disso, o script descrito neste artigo não é compatível com HPC Pack 2016.

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Exemplo de arquivo de configuração
O arquivo de configuração a seguir cria um controlador de domínio e uma floresta de domínio, além de implantar um cluster HPC Pack que tem um nó de cabeçalho com bancos de dados locais e 10 nós de computação Linux. Todos os serviços de nuvem são criados diretamente no local na Ásia Oriental. Os nós de computação Linux são criados em dois serviços de nuvem e em duas contas de armazenamento (ou seja, *MyLnxCN-0001* a *MyLnxCN-0005* em *MyLnxCNService01* e *mylnxstorage01*, e *MyLnxCN-0006* a *MyLnxCN-0010* em *MyLnxCNService02* e *mylnxstorage02*). Os nós de computação são criados de uma imagem do Linux OpenLogic CentOS versão 7.0. 

Substitua seus próprios valores para o nome da assinatura e os nomes da conta e do serviço.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Solucionar problemas
* **Erro "VNet não existe"**. Se você executar o script de implantação IaaS do HPC Pack para implantar vários clusters no Azure simultaneamente em uma assinatura, uma ou mais implantações poderão falhar com o erro "VNet *Nome\_VNet* não existe".
  Se esse erro ocorrer, execute novamente o script para a implantação com falha.
* **Problema ao acessar a Internet usando a rede virtual do Azure**. Se você criar um cluster HPC Pack com um novo controlador de domínio usando o script de implantação ou promover manualmente uma VM do nó de cabeçalho a controlador de domínio, é possível que haja problemas ao conectar as VMs na rede virtual do Azure com a Internet. Isso pode ocorrer se um servidor DNS encaminhador for configurado automaticamente no controlador de domínio e não resolver corretamente.
  
    Para contornar esse problema, faça logon no controlador de domínio e remova a configuração do encaminhador ou configure um servidor DNS encaminhador válido. Para fazer isso, no Gerenciador do Servidor, clique em **Ferramentas** > **DNS** para abrir o Gerenciador DNS e clique duas vezes em **Encaminhadores**.

## <a name="next-steps"></a>Próximas etapas
* Confira [Introdução aos nós de computação do Linux em um cluster do Pacote HPC no Azure](hpcpack-cluster.md) para obter informações sobre distribuições do Linux com suporte, movimentação de dados e envio de trabalhos para um cluster do Pacote HPC com nós de computação do Linux.
* Para obter tutoriais que usam o script para criar um cluster e executar uma carga de trabalho do HPC Linux, confira:
  * [Executar o NAMD com o Microsoft HPC Pack em nós de computação do Linux no Azure](hpcpack-cluster-namd.md)
  * [Executar o OpenFOAM com o Pacote HPC da Microsoft em nós de computação do Linux no Azure](hpcpack-cluster-openfoam.md)
  * [Executar o STAR-CCM+ com o Pacote HPC da Microsoft em nós de computação do Linux no Azure](hpcpack-cluster-starccm.md)

