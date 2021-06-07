---
title: Planejar seu sistema Avere vFXT – Azure
description: Planeje um avere vFXT para o cluster do Azure que seja adequado às suas necessidades. Aprenda perguntas a serem feitas antes de ir para o Azure Marketplace ou criar máquinas virtuais.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 9f852ec056f3252005bba0bc142c05a9cfbe6c20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92342392"
---
# <a name="plan-your-avere-vfxt-system"></a>Planejar seu sistema Avere vFXT

Este artigo explica como planejar um novo avere vFXT para o cluster do Azure que está posicionado e dimensionado adequadamente para suas necessidades.

Antes de ir para o Azure Marketplace ou criar qualquer VM, considere estes detalhes:

* Como o cluster irá interagir com outros recursos do Azure?
* Onde os elementos de cluster devem estar localizados em redes privadas e sub-redes?
* Que tipo de armazenamento de back-end será usado e como o cluster o acessará?
* Quão eficientemente seus nós de cluster precisam ser para dar suporte ao seu fluxo de trabalho?

Continue lendo para saber mais.

## <a name="learn-the-components-of-the-system"></a>Conheça os componentes do sistema

Pode ser útil entender os componentes do avere vFXT para o sistema do Azure quando você inicia o planejamento.

* Nós de cluster – o cluster é composto de três ou mais VMs configuradas como nós de cluster. Mais nós oferecem a taxa de transferência mais alta do sistema e um cache maior.

* Cache-a capacidade do cache é dividida igualmente entre os nós do cluster. Definir o tamanho do cache por nó ao criar o cluster; os tamanhos de nó são adicionados para se tornarem o tamanho total do cache.

* Controlador de cluster-o controlador de cluster é uma VM adicional localizada na mesma sub-rede que os nós de cluster. O controlador é necessário para criar o cluster e para tarefas de gerenciamento em andamento.

* Armazenamento de back-end-os dados que você deseja armazenar em cache são armazenados em longo prazo em um sistema de armazenamento de hardware ou em um contêiner de blob do Azure. Você pode adicionar armazenamento depois de criar o avere vFXT para o cluster do Azure ou, se estiver usando o armazenamento de BLOB, você pode adicionar e configurar o contêiner ao criar o cluster.

* Clientes – computadores cliente que usam os arquivos armazenados em cache se conectam ao cluster usando um caminho de arquivo virtual em vez de acessar os sistemas de armazenamento diretamente. (Leia mais em [montar o cluster avere vFXT](avere-vfxt-mount-clients.md).)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Assinatura, grupo de recursos e infraestrutura de rede

Considere o local em que os elementos de seu vFXT Avere para implantação do Azure estarão. O diagrama a seguir mostra uma possível disposição para o vFXT Avere para componentes do Azure:

![Diagrama mostrando o cluster de controlador e o cluster de VMs em uma sub-rede. Em torno da sub-rede limite há um limite de rede virtual. Dentro da vnet, há um hexágono que representa o ponto de extremidade de serviço de armazenamento; ele está conectado com uma seta tracejada para um Armazenamento de Blobs fora da rede virtual.](media/avere-vfxt-components-option.png)

Siga estas diretrizes ao planejar a infraestrutura de rede do cluster do avere vFXT:

* Crie uma nova assinatura para cada avere vFXT para implantação do Azure. Gerenciar todos os componentes nesta assinatura.

  Os benefícios de usar uma nova assinatura para cada implantação incluem:
  * Acompanhamento de custo mais simples – exibir e auditar todos os custos de ciclos de recursos, infraestrutura e computação em uma assinatura de auditoria.
  * Limpeza mais fácil – você pode remover toda a assinatura quando tiver terminado o projeto.
  * Particionamento conveniente de cotas de recursos – Isole os clientes e o cluster avere vFXT em uma única assinatura para proteger outras cargas de trabalho críticas de uma possível limitação de recursos. Essa separação impede o conflito ao trazer um grande número de clientes para um fluxo de trabalho de computação de alto desempenho.

* Localize seus sistemas de computação de cliente perto do cluster vFXT. O armazenamento de back-end pode ser mais remoto.  

* Localize o cluster vFXT e a VM do controlador de cluster juntos, especificamente, eles devem ser:

  * Na mesma rede virtual
  * No mesmo grupo de recursos
  * Usando a mesma conta de armazenamento
  
  O modelo de criação de cluster trata dessa configuração para a maioria das situações.

* O cluster deve estar localizado em sua própria sub-rede para evitar conflitos de endereço IP com clientes ou outros recursos de computação.

* Use o modelo de criação de cluster para criar a maioria dos recursos de infraestrutura necessários para o cluster, incluindo grupos de recursos, redes virtuais, sub-redes e contas de armazenamento.

  Se você quiser usar recursos que já existem, verifique se eles atendem aos requisitos nesta tabela.

  | Recurso | Usar existente? | Requisitos |
  |----------|-----------|----------|
  | Resource group | Sim, se estiver vazio | Deve estar vazio|
  | Conta de armazenamento | **Sim** se estiver conectando um contêiner de blob existente após a criação do cluster <br/>  **Não** se estiver criando um novo contêiner de blob durante a criação do cluster | O contêiner de blob existente deve estar vazio <br/> &nbsp; |
  | Rede virtual | Sim | Deve incluir um ponto de extremidade de serviço de armazenamento se estiver criando um novo contêiner de blob do Azure |
  | Sub-rede | Sim | Não pode conter outros recursos |

## <a name="ip-address-requirements"></a>Requisitos de endereço IP

Certifique-se de que a sub-rede do seu cluster tenha um intervalo de endereços IP grande o suficiente para dar suporte ao cluster.

O cluster do Avere vFXT usa os seguintes endereços IP:

* Um endereço IP de gerenciamento de cluster. Esse endereço pode ser movido do nó para o nó no cluster, conforme necessário, para que ele esteja sempre disponível. Use esse endereço para se conectar à ferramenta de configuração do painel de controle do avere.
* Para cada nó de cluster:
  * Pelo menos um endereço IP voltado para o cliente. (Todos os endereços voltados para o cliente são gerenciados pelo *vserver* do cluster, que pode mover os endereços IP entre os nós, conforme necessário.)
  * Um endereço IP para comunicação de cluster
  * Endereço IP de uma instância (atribuído à VM)

Se você usar o armazenamento de BLOBs do Azure, ele também poderá exigir endereços IP da rede virtual do seu cluster:  

* Uma conta de Armazenamento de Blobs do Azure exige pelo menos cinco endereços IP. Tenha esse requisito em mente se você localizar o armazenamento de BLOBs na mesma rede virtual que o cluster.
* Se você usar o armazenamento de BLOBs do Azure que está fora da rede virtual do cluster, crie um ponto de extremidade de serviço de armazenamento dentro da rede virtual. O ponto de extremidade não usa um endereço IP.

Você tem a opção de localizar recursos de rede e Armazenamento de Blobs (se usado) em diferentes grupos de recursos do cluster.

## <a name="vfxt-node-size"></a>tamanho do nó vFXT

As VMs que atuam como nós de cluster determinam a capacidade de armazenamento e a taxa de transferência de solicitação do seu cache. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Cada nó vFXT será idêntico. Ou seja, se você criar um cluster de três nós, terá três VMs do mesmo tipo e tamanho.

| Tipo de instância | vCPUs | Memória  | Armazenamento SSD local  | Discos de dados máximos | Taxa de transferência de disco sem cache | NIC (contagem) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51.200 IOPS <br/> 768 MBps | 16.000 MBps (8)  |

O cache de disco por nó é configurável e pode variar de 1.000 GB a 8.000 GB. 4 TB por nó é o tamanho de cache recomendado para nós de Standard_E32s_v3.

Para obter informações adicionais sobre essas VMs, leia a documentação do Microsoft Azure: [tamanhos de máquina virtual com otimização de memória](../virtual-machines/sizes-memory.md)

## <a name="account-quota"></a>Cota da conta

Verifique se sua assinatura tem a capacidade para executar o cluster de do Avere vFXT, bem como todos os sistemas de computação ou cliente que estejam sendo usados. Leia [Cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para obter detalhes.

## <a name="back-end-data-storage"></a>Armazenamento de dados de back-end

Os sistemas de armazenamento de back-end fornecem arquivos ao cache do cluster e também recebem dados alterados do cache. Decida se seu conjunto de trabalho será armazenado em longo prazo em um novo contêiner de BLOB ou em um sistema de armazenamento existente (nuvem ou hardware). Esses sistemas de armazenamento de back-end são chamados de *principais Filers*.

### <a name="hardware-core-filers"></a>Principais Filers de hardware

Adicione sistemas de armazenamento de hardware ao cluster vFXT depois de criar o cluster. Você pode usar uma variedade de sistemas de hardware populares, incluindo sistemas locais, desde que o sistema de armazenamento possa ser acessado da sub-rede do cluster.

Leia [Configurar armazenamento](avere-vfxt-add-storage.md) para obter instruções detalhadas sobre como adicionar um sistema de armazenamento ao cluster do Avere vFXT.

### <a name="cloud-core-filers"></a>Filers do Cloud Core

O avere vFXT para o sistema do Azure pode usar contêineres de blob vazios para armazenamento de back-end. Os contêineres devem estar vazios quando adicionados ao cluster – o sistema vFXT deve ser capaz de gerenciar seu repositório de objetos sem a necessidade de preservar os dados existentes.

> [!TIP]
> Se você quiser usar o armazenamento de BLOBs do Azure para o back-end, crie um novo contêiner como parte da criação do cluster vFXT. O modelo de criação de cluster pode criar e configurar um novo contêiner de BLOBs para que ele esteja pronto para ser usado assim que o cluster estiver disponível. A adição de um contêiner mais tarde é mais complicada.
>
> Leia [Criar o Avere vFXT para Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) para obter detalhes.

Depois de adicionar o contêiner de armazenamento de BLOBs vazio como um Filer principal, você pode copiar dados para ele por meio do cluster. Use um mecanismo de cópia paralelo em vários threads. Leia [Movimentação de dados para o cluster do vFXT](avere-vfxt-data-ingest.md) para aprender a copiar dados para o novo contêiner do cluster com eficiência usando computadores cliente e o cache do Avere vFXT.

## <a name="cluster-access"></a>Acesso ao cluster

O cluster do Avere vFXT para Azure está localizado em uma sub-rede privada e o cluster não tem um endereço IP público. Você deve ter alguma maneira de acessar a sub-rede privada para conexões de cliente e de administração de cluster.

As opções de acesso incluem:

* Host de salto – atribua um endereço IP público a uma VM separada na rede privada e use-o para criar um túnel TLS para os nós de cluster.

  > [!TIP]
  > Se você definir um endereço IP público no controlador de cluster, poderá usá-lo como o host de atalho. Leia [Controlador de cluster como host de atalho](#cluster-controller-as-jump-host) para obter mais informações.

* VPN (rede virtual privada) – Configure uma VPN ponto a site ou site a site entre sua rede privada no Azure e nas redes corporativas.

* Azure ExpressRoute – configure uma conexão privada por meio de um parceiro do ExpressRoute.

Para obter detalhes sobre essas opções, leia a [Documentação da Rede Virtual do Azure sobre comunicação com a Internet](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Controlador de cluster como host de atalho

Se você definir um endereço IP público no controlador de cluster, poderá usá-lo como um host de atalho para contatar o cluster do Avere vFXT de fora da sub-rede privada. No entanto, como o controlador tem privilégios de acesso para modificar nós de cluster, isso cria um pequeno risco de segurança.

Para melhorar a segurança de um controlador com um endereço IP público, o script de implantação cria automaticamente um grupo de segurança de rede que restringe o acesso de entrada apenas à porta 22. Você pode proteger ainda mais o sistema bloqueando o acesso a seu intervalo de endereços IP de origem, ou seja, permitir somente conexões de computadores que você pretende usar para acesso ao cluster.

Ao criar o cluster, você pode escolher se deseja ou não criar um endereço IP público no controlador de cluster.

* Se você criar uma **nova rede virtual** ou uma **nova sub-rede**, o controlador de cluster será atribuído a um endereço IP **público** .
* Se você selecionar uma rede virtual e uma sub-rede existentes, o controlador de cluster terá apenas endereços IP **privados** .

## <a name="vm-access-roles"></a>Funções de acesso à VM

O Azure usa o Azure [RBAC (controle de acesso baseado em função)](../role-based-access-control/index.yml) para autorizar as VMs de cluster a executar determinadas tarefas. Por exemplo, o controlador de cluster precisa de autorização para criar e configurar as VMs do nó de cluster. Os nós de cluster precisam ser capazes de atribuir ou reatribuir endereços IP a outros nós de cluster.

Duas funções internas do Azure são usadas para as máquinas virtuais avere vFXT:

* O controlador de cluster usa a função interna [colaborador do avere](../role-based-access-control/built-in-roles.md#avere-contributor).
* Nós de cluster usam o operador de função interna [avere](../role-based-access-control/built-in-roles.md#avere-operator).

Se você precisar personalizar funções de acesso para componentes avere vFXT, deverá definir sua própria função e, em seguida, atribuí-la às VMs no momento em que elas forem criadas. Você não pode usar o modelo de implantação no Azure Marketplace. Consulte atendimento ao cliente da Microsoft e suporte abrindo um tíquete no portal do Azure, conforme descrito em [obter ajuda com o sistema](avere-vfxt-open-ticket.md).

## <a name="next-steps"></a>Próximas etapas

[Visão geral da implantação](avere-vfxt-deploy-overview.md) fornece a visão de visão geral das etapas necessárias para criar um avere vFXT para o sistema do Azure e colocá-lo pronto para fornecer dados.