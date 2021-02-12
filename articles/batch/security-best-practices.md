---
title: Práticas recomendadas de segurança e conformidade do lote
description: Conheça as práticas recomendadas e dicas úteis para aprimorar a segurança com suas soluções de lote do Azure.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 6ec4a1d89ebaa9318986fc0d51e832652ba51683
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98723805"
---
# <a name="batch-security-and-compliance-best-practices"></a>Práticas recomendadas de segurança e conformidade do lote

Este artigo fornece orientações e práticas recomendadas para aumentar a segurança ao usar o lote do Azure.

Por padrão, as contas do lote do Azure têm um ponto de extremidade público e podem ser acessadas publicamente. Quando um pool do lote do Azure é criado, o pool é provisionado em uma sub-rede especificada de uma rede virtual do Azure. As máquinas virtuais no pool do lote são acessadas por meio de endereços IP públicos criados pelo lote. Os nós de computação em um pool podem se comunicar entre si quando necessário, como para executar tarefas de várias instâncias, mas os nós em um pool não podem se comunicar com máquinas virtuais fora do pool.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="Diagrama mostrando um ambiente de lote típico.":::

Muitos recursos estão disponíveis para ajudá-lo a criar uma implantação de lote do Azure mais segura. Você pode restringir o acesso a nós e reduzir a capacidade de descoberta dos nós da Internet [Provisionando o pool sem endereços IP públicos](batch-pool-no-public-ip-address.md). Os nós de computação podem se comunicar com segurança com outras máquinas virtuais ou com uma rede local [Provisionando o pool em uma sub-rede de uma rede virtual do Azure](batch-virtual-network.md). E você pode habilitar o [acesso privado de redes virtuais](private-connectivity.md) de um serviço da plataforma Azure link privado.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="Diagrama mostrando um ambiente de lote mais seguro.":::

## <a name="general-security-related-best-practices"></a>Práticas recomendadas relacionadas à segurança geral

### <a name="pool-configuration"></a>Configuração de pool

Muitos recursos de segurança só estão disponíveis para pools configurados usando a [configuração de máquina virtual](nodes-and-pools.md#configurations), e não para pools com a configuração de serviços de nuvem. É recomendável usar pools de configuração de máquina virtual, que utilizam [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/overview.md), sempre que possível.

### <a name="batch-account-authentication"></a>Autenticação da conta do lote

O acesso à conta do lote dá suporte a dois métodos de autenticação: chave compartilhada e [Azure Active Directory (AD do Azure)](batch-aad-auth.md).

É altamente recomendável usar o Azure AD para autenticação de conta do lote. Alguns recursos do lote exigem esse método de autenticação, incluindo muitos dos recursos relacionados à segurança discutidos aqui.

### <a name="batch-account-pool-allocation-mode"></a>Modo de alocação do pool de contas do lote

Ao criar uma conta do lote, você pode escolher entre dois [modos de alocação de pool](accounts.md#batch-accounts):

- **Serviço de lote**: a opção padrão, em que os recursos de serviço de nuvem subjacente ou conjunto de dimensionamento de máquinas virtuais usados para alocar e gerenciar nós de pool são criados em assinaturas internas e não são diretamente visíveis no portal do Azure. Somente os pools e os nós do lote estão visíveis. 
- **Assinatura do usuário**: os recursos de serviço de nuvem subjacente ou conjunto de dimensionamento de máquinas virtuais são criados na mesma assinatura que a conta do lote. Portanto, esses recursos são visíveis na assinatura, além dos recursos do lote correspondentes.

Com o modo de assinatura do usuário, as VMs do lote e outros recursos são criados diretamente na sua assinatura quando um pool é criado. O modo de assinatura do usuário será necessário se você quiser criar pools do lote usando instâncias de VM reservadas do Azure, usar Azure Policy em recursos do conjunto de dimensionamento de máquinas virtuais e/ou gerenciar a cota de núcleo na assinatura (compartilhada entre todas as contas do lote na assinatura). Para criar uma conta do Lote no modo de assinatura do usuário, você também deverá registrar sua assinatura com o Lote do Azure e associar a conta com um Azure Key Vault.

## <a name="restrict-network-endpoint-access"></a>Restringir acesso de ponto de extremidade de rede

### <a name="batch-network-endpoints"></a>Pontos de extremidade de rede do lote

Lembre-se de que, por padrão, os pontos de extremidade com endereços IP públicos são usados para se comunicar com contas do lote, pools do lote e nós de pool.

### <a name="batch-account-api"></a>API da conta do lote

 Quando uma conta do lote é criada, é criado um ponto de extremidade público que é usado para invocar a maioria das operações da conta usando uma [API REST](/rest/api/batchservice/). O ponto de extremidade da conta tem uma URL base usando o formato `https://{account-name}.{region-id}.batch.azure.com` . O acesso à conta do lote é protegido, com a comunicação com o ponto de extremidade da conta que está sendo criptografado usando HTTPS e cada solicitação autenticada usando a autenticação de chave compartilhada ou Azure Active Directory (AD do Azure).

### <a name="azure-resource-manager"></a>Azure Resource Manager

Além das operações específicas de uma conta do lote, [as operações de gerenciamento](/rest/api/batchmanagement/) se aplicam a uma única e a várias contas do lote. Essas operações de gerenciamento são acessadas via Azure Resource Manager.

As operações de gerenciamento de lote via Azure Resource Manager são criptografadas usando HTTPS, e cada solicitação é autenticada usando a autenticação do Azure AD.

### <a name="batch-pool-nodes"></a>Nós do pool do lote

O serviço de lote se comunica com um agente de nó de lote que é executado em cada nó no pool. Por exemplo, o serviço instrui o agente do nó a executar uma tarefa, interromper uma tarefa ou obter os arquivos para uma tarefa. A comunicação com o agente de nó é habilitada por um ou mais balanceadores de carga, o número de que depende do número de nós em um pool. O balanceador de carga encaminha a comunicação para o nó desejado, com cada nó sendo endereçado por um número de porta exclusivo. Por padrão, os balanceadores de carga têm endereços IP públicos associados a eles. Você também pode acessar remotamente nós de pool via RDP ou SSH (esse acesso é habilitado por padrão, com a comunicação por meio de balanceadores de carga).

### <a name="restricting-access-to-batch-endpoints"></a>Restringindo o acesso a pontos de extremidade de lote 

Vários recursos estão disponíveis para limitar o acesso aos vários pontos de extremidade do lote, especialmente quando a solução usa uma rede virtual. 

#### <a name="use-private-endpoints"></a>Usar pontos de extremidade privados

O [link privado do Azure](../private-link/private-link-overview.md) permite o acesso aos serviços de PaaS do Azure e aos serviços hospedados do cliente/parceiro do Azure por meio de um ponto de extremidade privado em sua rede virtual. Você pode usar o link privado para restringir o acesso a uma conta do lote de dentro da rede virtual ou de qualquer rede virtual emparelhada. Os recursos mapeados para o Link Privado também podem ser acessados localmente no emparelhamento privado por meio de VPN ou do Azure ExpressRoute.

Para usar pontos de extremidade privados, uma conta do lote precisa ser configurada adequadamente quando criada; a configuração de acesso à rede pública deve ser desabilitada. Depois de criados, os pontos de extremidade privados podem ser criados e associados à conta do lote. Para obter mais informações, consulte [usar pontos de extremidade privados com contas do lote do Azure](private-connectivity.md).

#### <a name="create-pools-in-virtual-networks"></a>Criar pools em redes virtuais

Os nós de computação em um pool do lote podem se comunicar entre si, como executar tarefas de várias instâncias, sem a necessidade de uma rede virtual (VNET). No entanto, por padrão, os nós em um pool não podem se comunicar com máquinas virtuais que estão fora do pool em uma rede virtual e têm endereços IP privados, como servidores de licença ou servidores de arquivos.

Para permitir que os nós de computação se comuniquem com segurança com outras máquinas virtuais, ou com uma rede local, você pode configurar um pool para estar em uma sub-rede de uma VNET do Azure.

Quando os pools têm pontos de extremidade de IP público, a sub-rede deve permitir que a comunicação de entrada do serviço de lote seja capaz de agendar tarefas e realizar outras operações nos nós de computação e a comunicação de saída para se comunicar com o armazenamento do Azure ou outros recursos conforme a necessidade de sua carga de trabalho. Para pools na configuração de máquina virtual, o lote adiciona NSGs (grupos de segurança de rede) no nível de interface de rede anexado a nós de computação. Esses NSGs têm regras para habilitar:

- Tráfego TCP de entrada de endereços IP do serviço de lote
- Tráfego TCP de entrada para acesso remoto
- Tráfego de saída em qualquer porta para a rede virtual (pode ser alterado por regras de NSG no nível de sub-rede)
- Tráfego de saída em qualquer porta para a Internet (pode ser alterado por regras de NSG no nível de sub-rede)

Você não precisa especificar NSGs no nível de sub-rede da rede virtual, pois o lote configura seu próprio NSGs. Se você tiver um NSG associado à sub-rede em que os nós de computação do lote são implantados ou se quiser aplicar regras de NSG personalizadas para substituir os padrões aplicados, você deve configurar esse NSG com pelo menos as regras de segurança de entrada e saída para permitir a comunicação do serviço de lote com os nós do pool e a comunicação do nó do pool no armazenamento do Azure.

Para obter mais informações, consulte [criar um pool do lote do Azure em uma rede virtual](batch-virtual-network.md).

#### <a name="create-pools-with-static-public-ip-addresses"></a>Criar pools com endereços IP públicos estáticos

Por padrão, os endereços IP públicos associados aos pools são dinâmicos; Eles são criados quando um pool é criado e os endereços IP podem ser adicionados ou removidos quando um pool é redimensionado. Quando os aplicativos de tarefa em execução em nós de pool precisam acessar serviços externos, o acesso a esses serviços pode precisar ser restrito a IPs específicos.  Nesse caso, ter endereços IP dinâmicos não poderá ser gerenciado.

Você pode criar recursos de endereço IP público estático na mesma assinatura que a conta do lote antes da criação do pool. Em seguida, você pode especificar esses endereços ao criar o pool.

Para obter mais informações, consulte [criar um pool do lote do Azure com endereços IP públicos especificados](create-pool-public-ip.md).

#### <a name="create-pools-without-public-ip-addresses"></a>Criar pools sem endereços IP públicos

Por padrão, todos os nós de computação em um pool de configuração de máquina virtual do lote do Azure recebem um ou mais endereços IP públicos. Esses pontos de extremidade são usados pelo serviço de lote para agendar tarefas e para comunicação com nós de computação, incluindo acesso de saída à Internet.

Para restringir o acesso a esses nós e reduzir a capacidade de descoberta desses nós da Internet, você pode provisionar o pool sem endereços IP públicos.

Para obter mais informações, consulte [criar um pool sem endereços IP públicos](batch-pool-no-public-ip-address.md).

#### <a name="limit-remote-access-to-pool-nodes"></a>Limitar o acesso remoto a nós de pool

Por padrão, o lote permite que um usuário do nó com conectividade de rede se conecte externamente a um nó de computação em um pool do lote usando RDP ou SSH.

Para limitar o acesso remoto a nós, use um dos seguintes métodos:

- Configure o [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) para negar acesso. O grupo de segurança de rede (NSG) apropriado será associado ao pool.
- Crie seu pool [sem endereços IP públicos](batch-pool-no-public-ip-address.md). Por padrão, esses pools não podem ser acessados fora da VNet.
- Associe um NSG à VNet para negar acesso às portas RDP ou SSH.
- Não crie nenhum usuário no nó. Sem nenhum usuário de nó, o acesso remoto não será possível.

## <a name="encrypt-data"></a>Criptografar dados

### <a name="encrypt-data-in-transit"></a>Criptografar dados em trânsito

Toda a comunicação com o ponto de extremidade da conta do lote (ou via Azure Resource Manager) deve usar HTTPS. Você deve usar `https://` nas URLs de conta do lote especificadas em APIs ao se conectar ao serviço de lote.

Os clientes que se comunicam com o serviço de lote devem ser configurados para usar o protocolo TLS 1,2.

### <a name="encrypt-batch-data-at-rest"></a>Criptografar dados em lotes em repouso

Algumas das informações especificadas em APIs do lote, como certificados de conta, metadados de tarefa e de trabalho e linhas de comando de tarefa, são criptografadas automaticamente quando armazenadas pelo serviço de lote. Por padrão, esses dados são criptografados usando chaves gerenciadas pela plataforma do lote do Azure exclusivas para cada conta do lote.

Você também pode criptografar esses dados usando [chaves gerenciadas pelo cliente](batch-customer-managed-key.md). [Azure Key Vault](../key-vault/general/overview.md) é usado para gerar e armazenar a chave, com o identificador de chave registrado com sua conta do lote.

### <a name="encrypt-compute-node-disks"></a>Criptografar discos do nó de computação

Os nós de computação do lote têm dois discos por padrão: um disco do sistema operacional e o SSD temporário local. Os [arquivos e diretórios](files-and-directories.md) gerenciados pelo lote estão localizados no SSD temporário, que é o local padrão para arquivos como arquivos de saída de tarefas. Os aplicativos de tarefa em lotes podem usar o local padrão no SSD ou no disco do sistema operacional.

Para segurança extra, criptografe esses discos usando um destes recursos de criptografia de disco do Azure:

- [Criptografia de disco gerenciado em repouso com chaves gerenciadas pela plataforma](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Criptografia no host usando uma chave gerenciada por plataforma](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Criptografia de Disco do Azure](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>Acesse os serviços de nós de computação com segurança

Os nós do lote podem [acessar com segurança as credenciais e os segredos](credential-access-key-vault.md) armazenados no [Azure Key Vault](../key-vault/general/overview.md), que podem ser usados por aplicativos de tarefas para acessar outros serviços. Um certificado é usado para conceder aos nós do pool o acesso a Key Vault.

## <a name="governance-and-compliance"></a>Governança e conformidade

### <a name="compliance"></a>Conformidade

Para ajudar os clientes a atender suas próprias obrigações de conformidade entre os setores regulamentados e os mercados em todo o mundo, o Azure mantém um [grande portfólio de ofertas de conformidade](https://azure.microsoft.com/overview/trusted-cloud/compliance). 

Essas ofertas se baseiam em vários tipos de garantias, incluindo certificações formais, atestado, validações, autorizações e avaliações produzidas por empresas de auditoria de terceiros independentes, bem como emendas contratuais, autoavaliações e documentos de orientação do cliente produzidos pela Microsoft. Examine a [visão geral abrangente das ofertas de conformidade](https://aka.ms/AzureCompliance) para determinar quais delas podem ser relevantes para suas soluções de lote.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) ajuda a impor padrões organizacionais e a avaliar a conformidade em escala. Casos de uso comuns do Azure Policy incluem implementar a governança para consistência de recursos, conformidade regulatória, segurança, custo e gerenciamento.

Dependendo do modo de alocação do pool e dos recursos aos quais uma política deve ser aplicada, use Azure Policy com o lote de uma das seguintes maneiras:

- Diretamente, usando o recurso Microsoft.Batch/batchAccounts. Um subconjunto das propriedades de uma conta do lote pode ser usado. Por exemplo, sua política pode incluir regiões de conta do lote válidas, modo de alocação de pool permitido e se uma rede pública está habilitada para contas.
- Indiretamente, usando o recurso Microsoft. Compute/virtualMachineScaleSets. Contas do lote com o modo de alocação do pool de assinatura do usuário podem ter a política definida nos recursos do conjunto de dimensionamento de máquinas virtuais que são criadas na assinatura da conta do lote. Por exemplo, tamanhos de VM permitidos e garantir que certas extensões sejam executadas em cada nó de pool.

## <a name="next-steps"></a>Próximas etapas

- Examine a [linha de base de segurança do Azure para o lote](security-baseline.md).
- Leia mais [práticas recomendadas para o lote do Azure](best-practices.md).