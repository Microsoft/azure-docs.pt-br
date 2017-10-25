---
title: "Adicionar nós de intermitência a um cluster de HPC Pack | Microsoft Docs"
description: "Saiba como expandir um cluster HPC Pack no Azure sob demanda adicionando instâncias de função de trabalho em execução em um serviço de nuvem"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 24b79a8a-24ad-4002-ae76-75abc9b28c83
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 10/14/2016
ms.author: danlep
ms.openlocfilehash: 9336743b92130e37b1df2992aab806696f8276aa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Adicionar nós de “intermitência” sob demanda a um cluster HPC Pack no Azure
Se você configurar um cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) no Azure, poderá preferir uma maneira de dimensionar rapidamente a capacidade de cluster para mais ou menos, sem manter um conjunto de VMs do nó de computação pré-configuradas. Este artigo mostra como adicionar os nós de “disparo contínuo” sob demanda (instâncias de função de trabalho em execução em um serviço de nuvem) como recursos de computação para um nó de cabeçalho no Azure. 

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

![Nós de disparo contínuo][burst]

As etapas neste artigo ajudam você a adicionar nós do Azure rapidamente a uma VM de nó de cabeçalho do HPC Pack baseado em nuvem para uma implantação de teste ou de prova de conceito. As etapas de alto nível são as mesmas que aquelas usadas para o “disparo contínuo para o Azure” para adicionar a capacidade de computação em nuvem a um cluster HPC Pack local. Para obter um tutorial, veja [Configurar um cluster de cálculo híbrido com o Microsoft HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Para obter diretrizes e considerações detalhadas sobre implantações de produção, veja [Disparo contínuo para o Azure com o Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

## <a name="prerequisites"></a>Pré-requisitos
* **Nó de cabeçalho do HPC Pack implantado em uma VM do Azure** – Você pode usar uma VM de nó de cabeçalho autônomo ou uma que faça parte de um cluster maior. Para criar um nó de cabeçalho autônomo, veja [Criar o nó principal de um cluster de Pacote HPC em uma VM do Azure com uma imagem do Marketplace](../../virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para obter opções de implantação do cluster HPC Pack, veja [Opções para criar e gerenciar um cluster HPC (computação de alto desempenho) do Windows no Azure com o Microsoft HPC Pack](../../virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
  > [!TIP]
  > Se você usar o [script de implantação de IaaS do HPC Pack](hpcpack-cluster-powershell-script.md) para criar o cluster no Azure, é possível incluir os nós de disparo contínuo do Azure em sua implantação automatizada. Consulte os exemplos nesse artigo.
  > 
  > 
* **Assinatura do Azure** – Para adicionar nós do Azure, você pode escolher a mesma assinatura usada para implantar a VM de nó de cabeçalho ou uma assinatura (ou assinaturas) diferente.
* **Cota de núcleos** - Talvez seja necessário aumentar a cota de núcleos, especialmente se você optar por implantar vários nós do Azure com tamanhos de vários núcleos. Para aumentar a cota, [abra uma solicitação de atendimento ao cliente online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Etapa 1: criar um serviço de nuvem e uma conta de armazenamento para os nós do Azure
Use o portal clássico do Azure ou ferramentas equivalentes para configurar os recursos a seguir, que são necessários para implantar seus nós do Azure:

* Um novo serviço de nuvem do Azure
* Uma nova conta de armazenamento do Azure

> [!NOTE]
> Não reutilize um serviço de nuvem existente em sua assinatura. 
> 
> 

**Considerações**

* Configure um serviço de nuvem separado para cada modelo de nó do Azure que você pretende criar. No entanto, é possível usar a mesma conta de armazenamento para vários modelos de nó.
* Recomendamos que você localize o serviço de nuvem e a conta de armazenamento para a implantação na mesma região do Azure.

## <a name="step-2-configure-an-azure-management-certificate"></a>Etapa 2: configurar um certificado de gerenciamento do Azure
Para adicionar nós do Azure como recursos de computação, você precisa ter um certificado de gerenciamento no nó de cabeçalho e carregar um certificado correspondente na assinatura do Azure usada para a implantação.

Para este cenário, é possível escolher o **Certificado padrão de gerenciamento do HPC Azure** que o HPC Pack instala e configura automaticamente no nó de cabeçalho. Este certificado é útil para implantações de prova de conceito e fins de teste. Para usar esse certificado, carregue o arquivo C:\Arquivos de Programas\Microsoft HPC Pack 2012\Bin\hpccert.cer da VM de nó de cabeçalho na assinatura. Para carregar o certificado no [Portal clássico do Azure](https://manage.windowsazure.com), clique em **Configurações** > **Certificados de Gerenciamento**.

Para obter opções adicionais para configurar o certificado de gerenciamento, veja [Cenários para configurar o certificado de gerenciamento do Azure para implantações de disparo contínuo do Azure](http://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Etapa 3: implantar nós do Azure ao cluster
As etapas para adicionar e iniciar os nós do Azure neste cenário geralmente são as mesmas usadas com um nó de cabeçalho local. Para obter mais informações, veja as seguintes seções em [Etapas para implantar nós do Azure com o Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Criar um modelo de nó do Azure
* Adicionar nós do Azure ao cluster Windows HPC
* Iniciar (provisionar) os nós do Azure

Depois de adicionar e iniciar os nós, eles estarão prontos para uso para a execução de trabalhos de cluster.

Se tiver problemas ao implantar nós do Azure, veja [Troubleshoot Deployments of Azure Nodes with Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097.aspx)(Solucionar problemas de nós do Azure com o Microsoft HPC Pack).

## <a name="next-steps"></a>Próximas etapas
* Para usar um tamanho de instância com uso intenso de computação para os nós de disparo, consulte as considerações em [Tamanhos de VM de computação de alto desempenho](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Caso deseje aumentar ou reduzir automaticamente os recursos de computação do Azure de acordo com a carga de trabalho do cluster, veja [Aumentar e reduzir automaticamente os recursos do cluster HPC Pack no Azure conforme a carga de trabalho do cluster](hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/hpcpack-cluster-node-burst/burst.png
