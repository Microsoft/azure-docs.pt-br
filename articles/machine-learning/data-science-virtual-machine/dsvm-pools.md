---
title: Pools compartilhados
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como criar & implantar um pool compartilhado de máquinas virtuais de ciência de dados (DSVMs) como um recurso compartilhado para uma equipe.
keywords: aprendizado profundo, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados da equipe
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: a118d5a3e716a80bda21ffe82a8cefd1da1202f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519704"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Criar um pool compartilhado de Máquinas Virtuais de Ciência de Dados

Neste artigo, você aprenderá a criar um pool compartilhado de máquinas virtuais de ciência de dados (DSVMs) para uma equipe. Os benefícios de usar um pool compartilhado incluem melhor utilização de recursos, colaboração e compartilhamento mais fáceis e gerenciamento mais eficaz de recursos de DSVM.

Você pode usar vários métodos e tecnologias para criar um pool de DSVMs. Este artigo se concentra em pools de VMs (máquinas virtuais) interativas. Uma infraestrutura de computação gerenciada alternativa é a Computação do Azure Machine Learning. Para obter mais informações, consulte [criar cluster de computação](../how-to-create-attach-compute-cluster.md).

## <a name="interactive-vm-pool"></a>Pool VM interativo

Um pool de VMs interativas compartilhadas por toda a equipe de IA/ciência de dados permite que os usuários façam logon em uma instância disponível da DSVM em vez de ter uma instância dedicada para cada conjunto de usuários. Essa configuração permite uma melhor disponibilidade e uma utilização mais eficaz dos recursos.

Você usa a tecnologia de [conjuntos de dimensionamento de máquinas virtuais do Azure](../../virtual-machine-scale-sets/index.yml) para criar um pool de VMs interativo. Você pode usar os conjuntos de dimensionamento para criar e gerenciar um grupo de VMs idênticas, com balanceamento de carga e dimensionamento automático.

O usuário faz logon no endereço IP ou DNS do pool principal. O conjunto de dimensionamento roteia automaticamente a sessão para uma DSVM disponível no conjunto de dimensionamento. Como os usuários desejam um ambiente consistente e familiar, independentemente da VM em que estão fazendo logon, todas as instâncias da VM no conjunto de dimensionamento montam uma unidade de rede compartilhada, como um compartilhamento de arquivos do Azure ou um compartilhamento NFS (sistema de arquivos de rede). O workspace compartilhado do usuário normalmente é mantido no armazenamento de arquivos compartilhado montado em cada uma das instâncias.

Encontre uma amostra de modelo do Azure Resource Manager que cria um conjunto de dimensionamento com instâncias de DSVM do Ubuntu no [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Você encontrará um exemplo do [arquivo de parâmetro](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) para o modelo de Azure Resource Manager no mesmo local.

Você pode criar o conjunto de dimensionamento do modelo de Azure Resource Manager especificando valores para o arquivo de parâmetro no CLI do Azure:

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az deployment group create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

Os comandos anteriores consideram que você tenha:

* Uma cópia do arquivo de parâmetro com os valores especificados para a instância do conjunto de dimensionamento.
* O número de instâncias de VM.
* Ponteiros para o compartilhamento de Arquivos do Azure.
* Credenciais da conta de armazenamento que será montada em cada VM.

O arquivo de parâmetro é referenciado localmente nos comandos. Você também pode passar parâmetros embutidos ou solicitá-los no script.  

O modelo anterior habilita o SSH e a porta do JupyterHub do conjunto de dimensionamento de front-end para o pool de back-end de DSVMs do Ubuntu. Como usuário, você faz logon na VM em um Secure Shell (SSH) ou no JupyterHub da maneira normal. Como as instâncias de VM podem ser aumentadas ou reduzidas dinamicamente, qualquer Estado deve ser salvo no compartilhamento de arquivos do Azure montado. A mesma abordagem pode ser usada para criar um pool de DSVMs do Windows.

O [script que monta o compartilhamento de Arquivos do Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) também está disponível no repositório DataScienceVM do Azure no GitHub. O script monta o compartilhamento de Arquivos do Azure no ponto de montagem especificado no arquivo de parâmetro. O script também cria links flexíveis com a unidade montada no diretório base do usuário inicial. Um diretório de bloco de anotações específico do usuário no compartilhamento de arquivos do Azure é vinculado de forma flexível ao `$HOME/notebooks/remote` diretório para que os usuários possam acessar, executar e salvar seus notebooks Jupyter. A mesma convenção pode ser usada ao criar usuários adicionais na VM para apontar o workspace do Jupyter de cada usuário para o compartilhamento de Arquivos do Azure.

Os Conjuntos de Dimensionamento de Máquinas Virtuais são compatíveis com o dimensionamento automático. Você pode definir regras sobre quando criar instâncias adicionais e quando reduzir instâncias. Por exemplo, você pode reduzir verticalmente até zero instâncias para economizar em custos de uso de hardware de nuvem quando as VMs não estão sendo usadas. As páginas de documentação dos Conjuntos de Dimensionamento de Máquinas Virtuais fornecem as etapas detalhadas para realizar o [dimensionamento automático](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

## <a name="next-steps"></a>Próximas etapas

* [Configurar uma identidade comum](dsvm-common-identity.md)
* [Armazenar com segurança as credenciais para acessar recursos de nuvem](dsvm-secure-access-keys.md)