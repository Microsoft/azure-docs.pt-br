---
title: Clusters MATLAB em máquinas virtuais | Microsoft Docs
description: Use máquinas virtuais do Microsoft Azure para criar clusters de Servidor de Computação Distribuída MATLAB para executar suas cargas de trabalho MATLAB paralelas de computação intensiva.
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: gwallace
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: 857382c09192e71d9727a91274993091f2f903b2
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718681"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Criar clusters de Servidor de Computação Distribuída MATLAB em VMs do Azure
Use máquinas virtuais do Microsoft Azure para criar um ou mais clusters de Servidor de Computação Distribuída MATLAB para executar suas cargas de trabalho MATLAB paralelas de computação intensiva. Instale o software de Servidor de Computação Distribuída MATLAB em uma VM para usar como uma imagem de base e usar um modelo de início rápido do Azure ou um script do Azure PowerShell (disponível no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) para implantar e gerenciar o cluster. Após a implantação, conecte-se ao cluster para executar as cargas de trabalho.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Sobre a MATLAB e o Servidor de Computação Distribuída MATLAB
A plataforma [MATLAB](https://www.mathworks.com/products/matlab/) é otimizada para solucionar problemas de engenharia e científicos. Os usuários da MATLAB com simulações em grande escala e tarefas de processamento de dados podem usar produtos de computação paralela da MathWorks para acelerar suas cargas de trabalho de computação intensiva, aproveitando os clusters de computadores e serviços de grade. [Caixa de Ferramentas de Computação Paralela](https://www.mathworks.com/products/parallel-computing/) permite que os usuários da MATLAB paralelizem aplicativos e aproveitem os processadores de vários núcleos, GPUs e clusters de cálculo. [Servidor de Computação Distribuída MATLAB](https://www.mathworks.com/products/distriben/) permite que os usuários da MATLAB utilizem diversos computadores em um cluster de cálculo.

Usando máquinas virtuais do Azure, você pode criar clusters de Servidor de Computação Distribuída MATLAB que têm os mesmos mecanismos disponíveis para enviar o trabalho paralelo como clusters locais, como trabalhos interativos, trabalhos em lote, tarefas independentes e tarefas de comunicação. O uso do Azure em conjunto com a plataforma MATLAB traz muitos benefícios em comparação ao provisionamento e o uso de hardware local tradicional: uma gama de tamanhos de máquinas virtuais, criação de clusters sob demanda para que você pague apenas pelos recursos de computação você usa e a capacidade de testar modelos em escala.  

## <a name="prerequisites"></a>Pré-requisitos
* **Computador cliente** : você precisará de um computador cliente baseado no Windows para se comunicar com o Azure e o cluster de Servidor de Computação Distribuída MATLAB após a implantação.
* **Azure PowerShell** : consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para instalá-lo no computador cliente.
* **Assinatura do Azure** : se você não tiver uma assinatura, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos. Para clusters maiores, considere uma assinatura pré-paga ou outras opções de compra.
* **Cota de vCPUs** – talvez seja necessário aumentar a cota de vCPUs para implantar um cluster grande ou mais de um cluster de Servidor de Computação Distribuída MATLAB. Para aumentar a cota, [abra uma solicitação de atendimento ao cliente online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.
* **Licenças da MATLAB, Caixa de Ferramentas de Computação Paralela e Servidor de Computação Distribuída MATLAB** : os scripts assumem que o [Gerenciador de Licença Hospedada MathWorks](https://www.mathworks.com/help/install/license-management.html) é usado para todas as licenças.  
* **Software de Servidor de Computação Distribuída MATLAB** : será instalado em uma VM que será usada como a imagem de VM de base para as VMs do cluster.

## <a name="high-level-steps"></a>Etapas de alto nível
Para usar máquinas virtuais do Azure para os clusters de Servidor de Computação Distribuída MATLAB, são necessárias as seguintes etapas de alto nível. As instruções detalhadas estão na documentação que acompanha o modelo de início rápido e os scripts no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Criar uma imagem de VM de base**  

   * Baixe e instale o software de Servidor de Computação Distribuída MATLAB nessa VM.

     > [!NOTE]
     > Esse processo pode levar algumas horas, mas você só precisa fazê-lo uma vez para cada versão do MATLAB que usar.   
     >
     >
2. **Criar um ou mais clusters**  

   * Use o script PowerShell fornecido ou use o modelo de início rápido para criar um cluster da imagem VM de base.   
   * Gerencie os clusters usando o script PowerShell fornecido que permite que você liste, pause, retome e exclua clusters.

## <a name="cluster-configurations"></a>Configurações de cluster
No momento, o script de criação de cluster e o modelo permitem que você crie uma topologia de Servidor de Computação Distribuída MATLAB única. Se desejar, crie um ou mais clusters adicionais, com cada cluster com um número diferente de VMs de trabalho, usando diferentes tamanhos de VM e assim por diante.

### <a name="matlab-client-and-cluster-in-azure"></a>Cluster e cliente MATLAB no Azure
O nó do cliente MATLAB, nó do Agendador de Trabalhos da MATLAB e os nós de “trabalho” do Servidor de Computação Distribuída MATLAB são todos configurados como VMs do Azure em uma rede virtual, como mostrado na imagem a seguir.


* Para usar o cluster, conecte-se pela Área de Trabalho Remota para o nó do cliente. O nó do cliente executa o cliente MATLAB.
* O nó do cliente tem um compartilhamento de arquivos que pode ser acessado por todos os funcionários.
* O Gerenciador de Licença Hospedada MathWorks é usado para as verificações de licença para todo o software da MATLAB.
* Por padrão, é criado um trabalho de Servidor de Computação Distribuída MATLAB por vCPU nas VMs de trabalho, mas você pode especificar qualquer número.

## <a name="use-an-azure-based-cluster"></a>Usar um Cluster baseado no Azure
Como com outros tipos de clusters de Servidor de Computação Distribuída MATLAB, você precisa usar o Gerenciador de Perfis de Cluster no cliente MATLAB (na VM cliente) para criar um perfil de cluster do Agendador de Trabalhos MATLAB.

![Gerenciador de perfis de cluster](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Próximas etapas
* Para obter instruções detalhadas implantar e gerenciar clusters de Servidor de Computação Distribuída MATLAB no Azure, consulte o repositório do [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) que contém os modelos e scripts.
* Acesse o site da [MathWorks](https://www.mathworks.com/) para obter a documentação detalhada para MATLAB e Servidor de Computação Distribuída MATLAB.
