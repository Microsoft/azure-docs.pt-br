---
title: Implantar o modelo de solução do consórcio de prova de autoridade Ethereum no Azure
description: Usar a solução do consórcio de prova de autoridade do Ethereum para implantar e configurar uma rede Ethereum do consórcio de vários membros no Azure
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: 70c9498bae9117585963e111bea4f1e127cab232
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097934"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Implantar o modelo de solução do consórcio de prova de autoridade Ethereum no Azure

Você pode usar [o modelo de solução do Ethereum de verificação do consórcio de prova de autoridade do Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) para implantar, configurar e governar uma rede Ethereum do consórcio de vários membros com conhecimento mínimo do Azure e do Ethereum.

O modelo de solução pode ser usado por cada membro do consórcio para provisionar uma superfície de rede blockchain usando os serviços de computação, rede e armazenamento do Microsoft Azure. A superfície de rede de cada membro do consórcio consiste em um conjunto de nós de validador com balanceamento de carga com os quais um aplicativo ou usuário pode interagir para enviar transações Ethereum.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Escolher uma solução de Blockchain do Azure

Antes de escolher usar o modelo de solução do consórcio de prova de autoridade Ethereum, Compare seu cenário com os casos de uso comuns das opções disponíveis do Azure Blockchain.

> [!IMPORTANT]
> Considere usar o [serviço Blockchain do Azure](../service/overview.md) em vez do Ethereum no modelo de solução do Azure. O serviço Blockchain do Azure é um serviço gerenciado do Azure com suporte. Ethereum de paridade transição para desenvolvimento e manutenção controlados pela Comunidade. Para obter mais informações, consulte [transicionating Parity Ethereum to OPENETHEREUM DAO](https://www.parity.io/parity-ethereum-openethereum-dao/).

Opção | Modelo de serviço | Caso de uso comum
-------|---------------|-----------------
Modelos de Solução | IaaS | Os modelos de solução são Azure Resource Manager modelos que você pode usar para provisionar uma topologia de rede blockchain totalmente configurada. Os modelos implantam e configuram Microsoft Azure serviços de computação, rede e armazenamento para um determinado tipo de rede blockchain. Os modelos de solução são fornecidos sem um contrato de nível de serviço. Use a [Página de P e R da Microsoft](/answers/topics/azure-blockchain-workbench.html) para obter suporte.
[Azure Blockchain Service](../service/overview.md) | PaaS | O Azure Blockchain Service Preview simplifica a formação, o gerenciamento e a governança de redes do consórcio Blockchain. Use o serviço Blockchain do Azure para soluções que exigem PaaS, gerenciamento de consórcio ou privacidade de contrato e transação.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS e PaaS | A versão prévia do Azure Blockchain Workbench é uma coleção de serviços e recursos do Azure desenvolvidos para ajudar você a criar e implantar aplicativos de blockchain para compartilhar processos empresariais e dados com outras organizações. Use o Azure Blockchain Workbench para o protótipo de uma solução Blockchain ou uma prova de conceito de aplicativo Blockchain. O Azure Blockchain Workbench é fornecido sem um Contrato de Nível de Serviço. Use a [Página de P e R da Microsoft](/answers/topics/azure-blockchain-workbench.html) para obter suporte.

## <a name="solution-architecture"></a>Arquitetura da solução

Usando o modelo de solução Ethereum, você pode implantar uma rede única ou multimembro com base em várias regiões do consórcio de Ethereum de prova de autoridade.

![arquitetura de implantação](./media/ethereum-poa-deployment/deployment-architecture.png)

Cada implantação de membro do consórcio inclui:

* Máquinas virtuais para executar os validadores de PoA
* Azure Load Balancer para distribuir solicitações de DApp de RPC, emparelhamento e governança
* Azure Key Vault para proteger as identidades do validador
* Armazenamento do Microsoft Azure para hospedar as informações de rede persistentes e leasing de coordenação
* Azure Monitor para agregar logs e estatísticas de desempenho
* Gateway de rede virtual para permitir conexões VPN entre redes virtuais privadas (opcional)

Por padrão, o RPC e pontos de extremidade de emparelhamento são acessíveis pela IP público para permitir a conectividade simplificada entre assinaturas e nuvens. Para controles de acesso de nível de aplicativo, você pode usar [contratos de permissão de paridade](https://openethereum.github.io/Permissioning.html). As redes implantadas atrás de VPNs, que aproveitam gateways de VNet para conectividade entre assinaturas, têm suporte. Como as implantações de VPN e VNet são mais complexas, talvez você queira começar com um modelo de IP público ao fazer um protótipo de uma solução.

Os contêineres do Docker são usados para confiabilidade e modularidade. O registro de contêiner do Azure é usado para hospedar e fornecer imagens com versão como parte de cada implantação. As imagens de contêiner consistem em:

* Orchestrator – gera identidades e contratos de governança. Armazena identidades em um repositório de identidades.
* Cliente de paridade – identidades de concessão do armazenamento de identidades. Descobre e se conecta a pares.
* Agente EthStats-coleta logs e estatísticas locais via RPC e envia informações para Azure Monitor.
* Governança DApp-interface da Web para interagir com contratos de governança.

### <a name="validator-nodes"></a>Nós do validador

No protocolo de prova de autoridade, os nós do validador tomam o lugar dos nós de mineração tradicional. Cada validador tem uma identidade Ethereum exclusiva, permitindo que ele participe do processo de criação de bloco. Cada membro do consórcio pode provisionar dois ou mais nós de validador em cinco regiões, para redundância geográfica. Os nós do validador se comunicam com outros nós de mineração para chegar a consenso sobre o estado da razão distribuído de forma subjacente. Para garantir a participação justa na rede, cada membro do consórcio é proibido de usar mais validadores do que o primeiro membro na rede. Por exemplo, se o primeiro membro implantar três validadores, cada membro poderá ter até três validadores.

### <a name="identity-store"></a>Armazenamento de identidade

Um repositório de identidades é implantado na assinatura de cada membro que mantém com segurança as identidades Ethereum geradas. Para cada validador, o contêiner de orquestração gera uma chave privada Ethereum e a armazena em Azure Key Vault.

## <a name="deploy-ethereum-consortium-network"></a>Implantar rede do Ethereum Consortium

Nesta passagem, vamos supor que você esteja criando uma rede Ethereum Consortium de vários participantes. O seguinte fluxo é um exemplo de uma implantação de vários participantes:

1. Três membros geram uma conta de Ethereum usando MetaMask
1. *O membro A* implanta Ethereum POA, fornecendo seu endereço público Ethereum
1. *Membro de A* fornece a URL do consórcio para o *Membro B* e *Membro C*
1. *Implantação do Membro B* e *Membro C*, Ethereum PoA, fornecendo seu En Público Ethereum e URL de consórcio do *Membro A*
1. *Membro A* vota na *Membro B* como um administrador
1. *Membro de A* e *Membro B* ambos os votam *Membro C* como um administrador

As próximas seções mostram como configurar a superfície do primeiro membro na rede.

### <a name="create-resource"></a>Criar recurso

Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso** no canto superior esquerdo.

Selecione **Blockchain**  >  **Ethereum reprovação de autoridade do consórcio (versão prévia)**.

### <a name="basics"></a>Noções básicas

Em **noções básicas**, especifique valores para parâmetros padrão para qualquer implantação.

![Noções básicas](./media/ethereum-poa-deployment/basic-blade.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Criar uma nova rede ou ingressar na rede existente | Você pode criar uma nova rede Consortium ou ingressar em uma rede consórcio pré-existente. Ingressar em uma rede existente requer parâmetros adicionais. | Criar Novo
Endereço de Email | Você receberá uma notificação por email quando sua implantação for concluída com informações sobre sua implantação. | Um endereço de email válido
Nome de usuário da VM | Nome de usuário de administrador de cada VM implantada | 1-64 caracteres alfanuméricos
Tipo de autenticação | O método para autenticar para a máquina virtual. | Senha
Senha | A senha para a conta de administrador para cada uma das máquinas virtuais implantadas. Todas as VMs têm inicialmente a mesma senha. Você pode alterar a senha após o provisionamento. | 12 a 72 caracteres 
Subscription | A assinatura para a qual deseja implantar a rede consortium |
Grupo de recursos| O grupo de recursos para o qual implantar a rede do consórcio. | myResourceGroup
Local | A região do Azure para o grupo de recursos. | Oeste dos EUA 2

Selecione **OK**.

### <a name="deployment-regions"></a>Regiões de implantação

Em *regiões de implantação*, especifique o número de regiões e locais para cada um. Você pode implantar no máximo cinco regiões. A primeira região deve corresponder à localização do grupo de recursos da seção *básico* . Para redes de desenvolvimento ou teste, você pode usar uma única região por membro. Para produção, implante em duas ou mais regiões para alta disponibilidade.

![regiões de implantação](./media/ethereum-poa-deployment/deployment-regions.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Número de regiões|Número de regiões para implantar a rede consortium| 2
Primeira região | Primeira região para implantar a rede consortium | Oeste dos EUA 2
Segunda região | Segunda região para implantar a rede Consortium. Regiões adicionais ficam visíveis quando o número de regiões é dois ou mais. | Leste dos EUA 2

Selecione **OK**.

### <a name="network-size-and-performance"></a>Tamanho e desempenho da rede

Em *tamanho da rede e desempenho*, especifique as entradas para o tamanho da rede do consórcio. O tamanho do armazenamento do nó do validador determina o tamanho potencial do blockchain. O tamanho pode ser alterado após a implantação.

![Tamanho e desempenho da rede](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Número de nós do validador balanceador de carga | O número de nós validadores para provisionar como parte da rede. | 2
Desempenho do armazenamento de nó do validador | O tipo de disco gerenciado para cada um dos nós de validador implantados. Para obter detalhes sobre preços, consulte [preços de armazenamento](https://azure.microsoft.com/pricing/details/managed-disks/) | SSD Standard
Tamanho da máquina virtual do nó de mineração | O tamanho de máquina virtual usado para nós de mineração. Para obter detalhes sobre preços, consulte [preços da máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | D2 v3 Standard

A máquina virtual e a camada de armazenamento afetam o desempenho da rede.  Use a tabela a seguir para ajudar a escolher a eficiência de custo:

SKU da máquina virtual|Camada de armazenamento|Preço|Produtividade|Latency
---|---|---|---|---
F1|SSD Standard|low|low|high
D2_v3|SSD Standard|média|média|média
F16s|SSD Premium|high|high|low

Selecione **OK**.

### <a name="ethereum-settings"></a>Configurações de Ethereum

Em *configurações de Ethereum*, especifique definições de configuração relacionadas a Ethereum.

![Configurações de Ethereum](./media/ethereum-poa-deployment/ethereum-settings.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
ID do Membro do Consortium | A ID associada a cada membro que participa da rede do consórcio. Ele é usado para configurar espaços de endereço IP para evitar colisão. Para uma rede privada, a ID de membro deve ser exclusiva em diferentes organizações na mesma rede.  É necessária uma ID de membro exclusiva mesmo quando a mesma organização implanta em várias regiões. Anote o valor desse parâmetro, pois você precisa compartilhá-lo com outros membros de ingresso para garantir que não haja colisão. O intervalo válido é de 0 a 255. | 0
ID da Rede | A ID de rede para a rede de Ethereum consortium que está sendo implantada. Cada rede Ethereum tem sua própria ID de rede, sendo que 1 indica a ID para a rede pública. O intervalo válido é de 5 a 999.999.999 | 10101010
Endereço do administrador Ethereum | O endereço da conta do Ethereum usado para participar da governança do PoA. Você pode usar a metamáscara para gerar um endereço Ethereum. |
Opções avançadas | Opções avançadas para configurações do Ethereum | Habilitar
Implantar usando o IP público | Se VNet privada estiver selecionada, a rede será implantada atrás de um gateway de VNet e removerá o acesso de emparelhamento. Para VNet privada, todos os membros devem usar um gateway de VNet para que a conexão seja compatível. | IP público
Limite de gás de bloqueio | O limite de gás de bloco inicial da rede. | 50 milhões
Período para selar o bloco novamente (s) | A frequência com que serão criados blocos vazios quando não existirem transações na rede. Uma frequência superior terá uma finalidade mais rápida, mas os custos de armazenamento serão maiores. | 15
Contrato de permissão de transação | Código de bytes para o contrato de permissão de transação. Restringe a implantação e a execução do contrato inteligente a uma lista permitida de contas do Ethereum. |

Selecione **OK**.

### <a name="monitoring"></a>Monitoramento

O monitoramento permite que você configure um recurso de log para sua rede. O agente de monitoramento coleta e superfícies de logs e métricas úteis de sua rede, fornecendo a capacidade de verificar rapidamente a integridade da rede ou os problemas de depuração.

![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Monitoramento | Opção para habilitar o monitoramento | Habilitar
Conectar-se a logs de Azure Monitor existentes | Opção para criar uma nova instância de logs de Azure Monitor ou ingressar em uma instância existente | Criar Novo
Local | A região em que a nova instância é implantada | Leste dos EUA
ID do espaço de trabalho do log Analytics existente (conectar a logs de Azure Monitor existentes = ingressar existente)|ID do espaço de trabalho da instância existente de logs de Azure Monitor||NA
Chave primária do log Analytics existente (conectar a logs de Azure Monitor existentes = ingressar existente)|A chave primária usada para se conectar à instância existente de logs de Azure Monitor||NA

Selecione **OK**.

### <a name="summary"></a>Resumo

Clique no resumo para revisar as entradas especificadas e executar a validação de pré-implantação básica. Antes de implantar o, você pode baixar o modelo e os parâmetros.

Selecione **criar** para implantar.

Se a implantação incluir gateways de VNet, a implantação poderá levar de 45 a 50 minutos.

## <a name="deployment-output"></a>Saída de implantação

Depois que a implantação for concluída, você poderá acessar os parâmetros necessários usando o portal do Azure.

### <a name="confirmation-email"></a>Email de confirmação

Se você fornecer um endereço de email ([seção noções básicas](#basics)), será enviado um email que inclui as informações de implantação e links para esta documentação.

![email de implantação](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portal

Depois que a implantação for concluída com êxito e todos os recursos tiverem sido provisionados, você poderá exibir os parâmetros de saída em seu grupo de recursos.

1. Vá para o grupo de recursos no Portal.
1. Selecione **visão geral > implantações**.

    ![Visão geral do grupo de recursos](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Selecione a implantação **Microsoft-Azure-blockchain. Azure-blockchain-ether-...** .
1. Selecione a seção **saídas** .

    ![Saídas de implantação](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Aumentando o consórcio

Para expandir seu consórcio, primeiro você deve se conectar a rede física. Se estiver implantando por trás de uma VPN, consulte a seção [conectando o gateway de VNet](#connecting-vnet-gateways) configurar a conexão de rede como parte da implantação do novo membro. Quando a implantação for concluída, use o [DApp de governança](#governance-dapp) para se tornar um administrador de rede.

### <a name="new-member-deployment"></a>Nova implantação de membro

Compartilhe as informações a seguir com o membro da junção. As informações são encontradas em seu email de pós-implantação ou na saída de implantação do Portal.

* URL de dados do Consortium
* O número de nós implantados
* ID do recurso do gateway da VNET (no caso de uso da VPN)

O membro de implantação deve usar o mesmo modelo de solução do consórcio de prova de autoridade do Ethereum ao implantar sua presença de rede usando as seguintes diretrizes:

* Selecione **Ingressar Existente**
* Escolha o mesmo número de nós de validador que o restante dos membros na rede para garantir a representação justa
* Usar o mesmo endereço de Ethereum do administrador
* Usar a *URL de dados do consórcio* fornecida nas configurações do *Ethereum*
* Se o restante da rede estiver atrás de uma VPN, selecione **VNet privada** na seção avançado

### <a name="connecting-vnet-gateways"></a>Conectar-se a gateways de rede virtual

Esta seção só será necessária se você tiver implantado usando uma VNet privada. Você pode ignorar esta seção se estiver usando endereços IP públicos.

Para uma rede privada, os diferentes membros são conectados por meio de conexões de gateway de VNet. Antes que um membro possa ingressar na rede e ver o tráfego da transação, um membro existente deve fazer uma configuração final em seu gateway de VPN para aceitar a conexão. Os nós Ethereum do membro de junção não serão executados até que uma conexão seja estabelecida. Para reduzir as chances de um único ponto de falha, crie conexões de rede redundantes no consórcio.

Depois que o novo membro implanta, o membro existente deve concluir a conexão bi-direcional, configurando uma conexão de gateway de rede virtual para o novo membro. O membro existente precisa de:

* O ResourceId do gateway de VNet do membro de conexão. Consulte [saída da implantação](#deployment-output).
* A chave de conexão compartilhada.

O membro existente deve executar o seguinte script do PowerShell para concluir a conexão. Você pode usar Azure Cloud Shell localizadas na barra de navegação superior direita no Portal.

![cloud shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="governance-dapp"></a>DApp de Governança

O cerne da prova de autoridade é descentralizado de governança. Como a prova de autoridade depende de uma lista de autoridades de rede permitidas para manter a rede íntegra, é importante fornecer um mecanismo justo para fazer modificações nessa lista de permissões. Cada implantação vem com um conjunto de contratos inteligentes e o portal para governança na cadeia dessa lista de permissão. Quando uma alteração proposta atinge um voto maioria por membros consortium, a alteração é imposta. A votação permite que novos participantes de consenso sejam adicionados ou comprometidos os participantes sejam removidos de uma maneira transparente que incentiva uma rede honesta.

O DApp de governança é um conjunto de [contratos inteligentes](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) previamente implantados e um aplicativo Web que é usado para controlar as autoridades na rede. As autoridades são divididas em identidades de administrador e nós de validador.
Os administradores têm a capacidade de delegar a participação em consenso a um conjunto de nós de validador. Os administradores também podem votar outros administradores para dentro ou fora da rede.

![DApp de Governança](./media/ethereum-poa-deployment/governance-dapp.png)

* **Governança descentralizada:** As alterações nas autoridades de rede são administradas por meio de votação na cadeia por meio de administradores selecionados.
* **Delegação de validador:** As autoridades podem gerenciar seus nós de validador que são configurados em cada implantação do PoA.
* **Histórico de alterações auditável:** Cada alteração é registrada no blockchain, fornecendo transparência e auditoria.

### <a name="getting-started-with-governance"></a>Introdução à governança

Para executar qualquer tipo de transação por meio do DApp de governança, você precisa usar uma carteira Ethereum. A abordagem mais simples é usar uma carteira no navegador, como [metamask](https://metamask.io); no entanto, como esses contratos inteligentes são implantados na rede, você também pode automatizar suas interações com o contrato de governança.

Depois de instalar o MetaMask, navegue até o DApp de Governança no navegador.  Você pode localizar a URL por meio de portal do Azure na saída de implantação.  Se você não tiver uma carteira no navegador instalada, não poderá executar nenhuma ação; no entanto, você pode exibir o estado do administrador.  

### <a name="becoming-an-admin"></a>Tornando-se um administrador

Se você for o primeiro membro implantado na rede, você se tornará automaticamente um administrador e seus nós de paridade serão listados como validadores. Se você estiver ingressando na rede, precisará entrar como administrador por uma maioria (maior que 50%) do conjunto de administradores existente. Se você optar por não se tornar um administrador, seus nós ainda serão sincronizados e validará o blockchain; no entanto, eles não participam do processo de criação de blocos. Para iniciar o processo de votação para se tornar um administrador, selecione **indicado** e insira seu endereço Ethereum e alias.

![Nomear](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Candidatos

A seleção da guia **candidatos** mostra o conjunto atual de administradores candidatos.  Quando um candidato atinge um voto de maioria dos administradores atuais, o candidato é promovido a um administrador.  Para votar em um candidato, selecione a linha e selecione **votar em**. Se você mudar de ideia em um voto, selecione o candidato e selecione **rescindir voto**.

![Candidatos](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Administradores

A guia **Administradores** mostra o conjunto atual de administradores e fornece a capacidade de votar.  Quando um administrador perde mais de 50% de suporte, eles são removidos como um administrador na rede. Todos os nós de validador que o administrador possui perdem o status do validador e se tornam nós de transação na rede. Um administrador pode ser removido por vários motivos; no entanto, cabe ao consórcio concordar sobre uma política com antecedência.

![Administradores](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Validadores

A seleção da guia **validadores** exibe os nós de paridade atualmente implantados para a instância e seu status atual (tipo de nó). Cada membro do consórcio tem um conjunto diferente de validadores nesta lista, já que essa exibição representa o membro do consórcio implantado atual. Se a instância for implantada recentemente e você não tiver adicionado seus validadores, você obterá a opção de **Adicionar validadores**. A adição de validadores escolhe automaticamente um conjunto de nós de paridade com balanceamento de região e os atribui ao seu conjunto de validadores. Se você tiver implantado mais nós do que a capacidade permitida, os nós restantes se tornarão nós de transação na rede.

O endereço de cada validador é atribuído automaticamente por meio do [repositório de identidades](#identity-store) no Azure.  Se um nó falhar, ele cederá sua identidade, permitindo que outro nó em sua implantação assuma seu lugar. Esse processo garante que sua participação no consenso esteja altamente disponível.

![Validadores](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Nome do consórcio

Qualquer administrador pode atualizar o nome do consórcio.  Selecione o ícone de engrenagem na parte superior esquerda para atualizar o nome do consórcio.

### <a name="account-menu"></a>Menu da conta

No canto superior direito, está o alias da sua conta do Ethereum e Identicon.  Se você for um administrador, terá a capacidade de atualizar seu alias.

![Conta](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="support-and-feedback"></a>Suporte e comentários<a id="tutorials"></a>

Para novidades do Azure Blockchain, acesse o [blog do Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/) para se manter atualizado sobre as ofertas de serviço de blockchain e informações da equipe de engenharia do Azure Blockchain.

Para fornecer comentários sobre o produto ou solicitar novos recursos, poste um vote em uma ideia usando o [fórum de comentários do Azure para blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Suporte da comunidade

converse com engenheiros da Microsoft e com os especialistas da comunidade do Azure Blockchain.

* [O Microsoft Q&uma página de perguntas](/answers/topics/azure-blockchain-workbench.html). O suporte de engenharia para modelos blockchain é limitado a problemas de implantação.
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Próximas etapas

Para obter mais soluções de Blockchain do Azure, consulte a [documentação do Blockchain do Azure](../index.yml).
