---
title: Criar, alterar ou excluir um Grupo de Segurança de Rede do Azure
titlesuffix: Azure Virtual Network
description: Saiba onde encontrar informações sobre regras de segurança e como criar, alterar ou excluir um grupo de segurança de rede.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: d42e0f5594fdde55f1b4183a806e388658e86dc3
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222931"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Criar, alterar ou excluir um Grupo de Segurança de Rede

As regras de segurança em grupos de segurança de rede permitem filtrar o tipo de tráfego de rede que pode fluir para dentro e fora das sub-redes da rede virtual e dos adaptadores de rede. Para saber mais sobre grupos de segurança de rede, confira [Visão geral do grupo de segurança de rede](./network-security-groups-overview.md). Em seguida, conclua o tutorial [Filtrar tráfego de rede](tutorial-filter-network-traffic.md) para obter alguma experiência com grupos de segurança de rede.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se você não tiver uma, configure uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Conclua uma dessas tarefas antes de iniciar o restante deste artigo:

- **Usuários do portal**: entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

- **Usuários do PowerShell**: execute os comandos no [Azure cloud Shell](https://shell.azure.com/powershell)ou execute o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Na guia Azure Cloud Shell navegador, localize a lista suspensa **selecionar ambiente** e escolha **PowerShell** se ela ainda não estiver selecionada.

    Se você estiver executando o PowerShell localmente, use Azure PowerShell módulo versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az.Network` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Execute `Connect-AzAccount` para criar uma conexão com o Azure.

- **Usuários da CLI (interface de linha de comando) do Azure**: execute os comandos no [Azure cloud Shell](https://shell.azure.com/bash)ou execute a CLI do seu computador. Use CLI do Azure versão 2.0.28 ou posterior se você estiver executando o CLI do Azure localmente. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Execute `az login` para criar uma conexão com o Azure.

A conta que você faz logon ou conecta-se ao Azure com deve ser atribuída à [função de colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

## <a name="work-with-network-security-groups"></a>Trabalhar com grupos de segurança de rede

Você pode criar, [exibir todos](#view-all-network-security-groups), [exibir os detalhes](#view-details-of-a-network-security-group), [alterar](#change-a-network-security-group) e [excluir](#delete-a-network-security-group) um Grupo de Segurança de Rede. Você também pode [associar ou desassociar](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) um Grupo de Segurança de Rede de um adaptador de rede ou sub-rede.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Há um limite para quantos grupos de segurança de rede você pode criar para cada local e assinatura do Azure. Para saber mais, confira [assinatura e limites de serviço, cotas e restrições do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. No menu do [portal do Azure](https://portal.azure.com) ou na **Página Inicial**, selecione **Criar um recurso**.

2. Selecione **rede** e, em seguida, selecione **grupo de segurança de rede**.

3. Na página **Criar grupo de segurança de rede** , na guia **noções básicas** , defina valores para as seguintes configurações:

    | Configuração | Ação |
    | --- | --- |
    | **Assinatura** | Escolha sua assinatura. |
    | **Grupo de recursos** | Escolha um grupo de recursos existente ou selecione **criar novo** para criar um novo grupo de recursos. |
    | **Nome** | Insira uma cadeia de texto exclusiva em um grupo de recursos. |
    | **Região** | Escolha o local desejado. |

4. Selecione **Examinar + criar**.

5. Depois de ver a mensagem **validação aprovada** , selecione **criar**.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Exibir todos os grupos de segurança de rede

Vá para a [portal do Azure](https://portal.azure.com) para exibir os grupos de segurança de rede. Procure e selecione **grupos de segurança de rede**. A lista de grupos de segurança de rede é exibida para sua assinatura.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network NSG List](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Exibir os detalhes de um Grupo de Segurança de Rede

1. Vá para a [portal do Azure](https://portal.azure.com) para exibir os grupos de segurança de rede. Procure e selecione **grupos de segurança de rede**.

2. Selecione o nome do seu grupo de segurança de rede.

Na barra de menus do grupo de segurança de rede, em **configurações**, você pode exibir as **regras de segurança de entrada**, **as regras de segurança de saída, as** interfaces de **rede** e as **sub-redes** às quais o grupo de segurança de rede está associado.

Em **monitoramento**, você pode habilitar ou desabilitar **as configurações de diagnóstico**. Em **suporte + solução de problemas**, você pode exibir **regras de segurança em vigor**. Para saber mais, consulte [log de diagnóstico para um grupo de segurança de rede](virtual-network-nsg-manage-log.md) e [diagnosticar um problema de filtro de tráfego de rede VM](diagnose-network-traffic-filter-problem.md).

Para saber mais sobre as configurações comuns do Azure listadas, veja as seguintes informações:

- [Log de atividades](../azure-monitor/platform/platform-logs-overview.md)
- [Controle de acesso (IAM)](../role-based-access-control/overview.md)
- [Marcas](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Bloqueios](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Script de automação](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network NSG show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Criar um Grupo de Segurança de Rede

1. Vá para a [portal do Azure](https://portal.azure.com) para exibir os grupos de segurança de rede. Procure e selecione **grupos de segurança de rede**.

2. Selecione o nome do grupo de segurança de rede que você deseja alterar.

As alterações mais comuns são [Adicionar uma regra de segurança](#create-a-security-rule), [remover uma regra](#delete-a-security-rule)e [associar ou dissociar um grupo de segurança de rede de ou para uma sub-rede ou interface de rede](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network NSG Update](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Associar ou desassociar um Grupo de Segurança de Rede de uma sub-rede ou adaptador de rede

Para associar ou desassociar um Grupo de Segurança de Rede de um adaptador de rede, consulte [Associar ou desassociar um Grupo de Segurança de Rede de um adaptador de rede](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Para associar ou desassociar um Grupo de Segurança de Rede de uma sub-rede, consulte [Alterar as configurações de sub-rede](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Excluir um Grupo de Segurança de Rede

Se um grupo de segurança de rede estiver associado a quaisquer sub-redes ou interfaces de rede, ele não poderá ser excluído. Desassocie um grupo de segurança de rede de todas as sub-redes e adaptadores de rede antes de tentar excluí-lo.

1. Vá para a [portal do Azure](https://portal.azure.com) para exibir os grupos de segurança de rede. Procure e selecione **grupos de segurança de rede**.

2. Selecione o nome do grupo de segurança de rede que você deseja excluir.

3. Na barra de ferramentas do grupo de segurança de rede, selecione **excluir**. Em seguida, selecione **Sim** na caixa de diálogo de confirmação.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network NSG Delete](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Trabalhar com regras de segurança

Um Grupo de Segurança de Rede contém zero ou mais regras de segurança. Você pode criar, [exibir todas](#view-all-security-rules), [exibir os detalhes](#view-details-of-a-security-rule), [alterar](#change-a-security-rule) e [excluir](#delete-a-security-rule) uma regra de segurança.

### <a name="create-a-security-rule"></a>Criar uma regra de segurança

Há um limite para quantas regras por grupo de segurança de rede você pode criar para cada local e assinatura do Azure. Para saber mais, confira [assinatura e limites de serviço, cotas e restrições do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Vá para a [portal do Azure](https://portal.azure.com) para exibir os grupos de segurança de rede. Procure e selecione **grupos de segurança de rede**.

2. Selecione o nome do grupo de segurança de rede ao qual você deseja adicionar uma regra de segurança.

3. Na barra de menus do grupo de segurança de rede, escolha **regras de segurança de entrada** ou regras de segurança de **saída**.

    Várias regras existentes são listadas, incluindo algumas que você pode não ter adicionado. Quando você cria um grupo de segurança de rede, várias regras de segurança padrão são criadas nele. Para saber mais, consulte [regras de segurança padrão](./network-security-groups-overview.md#default-security-rules).  Não é possível excluir as regras de segurança padrão, porém você pode substituí-las por regras com prioridade mais alta.

4. <a name="security-rule-settings"></a>Selecione **Adicionar**. Selecione ou adicione valores para as seguintes configurações e, em seguida, selecione **OK**:

    | Configuração | Valor | Detalhes |
    | ------- | ----- | ------- |
    | **Origem** | Um destes:<ul><li>**Qualquer**</li><li>**Endereços IP**</li><li>**Marca de serviço** (regra de segurança de entrada) ou **VirtualNetwork** (regra de segurança de saída)</li><li>**&nbsp;Grupo de segurança de aplicativo &nbsp;**</li></ul> | <p>Se você escolher **endereços IP**, também deverá especificar os **endereços IP de origem/intervalos de CIDR**.</p><p>Se você escolher a **marca de serviço**, também poderá escolher uma marca de serviço de **origem**.</p><p>Se você escolher **grupo de segurança de aplicativo**, também deverá escolher um grupo de segurança de aplicativo existente. Se você escolher o **grupo de segurança de aplicativo** para a **origem** e o **destino**, as interfaces de rede dentro dos dois grupos de segurança de aplicativo deverão estar na mesma rede virtual.</p> |
    | **Intervalos de CIDR/endereços IP de origem** | Uma lista delimitada por vírgulas de endereços IP e intervalos CIDR (roteamento entre domínios sem classificação) | <p>Essa configuração será exibida se você alterar a **origem** para **endereços IP**. Você deve especificar um único valor ou uma lista separada por vírgulas de vários valores. Um exemplo de vários valores é `10.0.0.0/16, 192.188.1.1` . Há limites para o número de valores que podem ser especificados. Para obter mais detalhes, consulte [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Se o endereço IP especificado for atribuído a uma VM do Azure, especifique seu endereço IP privado, não seu endereço IP público. O Azure processa as regras de segurança depois de converter o endereço IP público em um endereço IP privado para regras de segurança de entrada, mas antes de converter um endereço IP privado em um endereço IP público para regras de saída. Para saber mais sobre endereços IP públicos e privados no Azure, consulte [Tipos de endereços IP](./public-ip-addresses.md).</p> |
    | **Marca de serviço de origem** | Uma marca de serviço da lista suspensa | Essa configuração opcional será exibida se você definir a **origem** como a **marca de serviço** para uma regra de segurança de entrada. Uma marcação de serviço é um identificador predefinido para uma categoria de endereços IP. Para saber mais sobre as marcas de serviço disponíveis e o que cada marca representa, consulte [marcas de serviço](./network-security-groups-overview.md#service-tags). |
    | **Grupo de segurança do aplicativo de origem** | Um grupo de segurança de aplicativo existente | Essa configuração será exibida se você definir **origem** como **grupo de segurança de aplicativo**. Selecione um grupo de segurança de aplicativo que exista na mesma região que a interface de rede. Saiba como [criar um grupo de segurança do aplicativo](#create-an-application-security-group). |
    | **Intervalos de portas de origem** | Um destes:<ul><li>Uma única porta, como `80`</li><li>Um intervalo de portas, como `1024-65535`</li><li>Uma lista separada por vírgulas de portas únicas e/ou intervalos de porta, como `80, 1024-65535`</li><li>Um asterisco ( `*` ) para permitir o tráfego em qualquer porta</li></ul> | Essa configuração especifica as portas nas quais a regra permite ou nega o tráfego. Há limites para o número de portas que podem ser especificadas. Para obter mais detalhes, consulte [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Destino** | Um destes:<ul><li>**Qualquer**</li><li>**Endereços IP**</li><li>**Marca de serviço** (regra de segurança de saída) ou **VirtualNetwork** (regra de segurança de entrada)</li><li>**&nbsp;Grupo de segurança de aplicativo &nbsp;**</li></ul> | <p>Se você escolher **endereços IP**, especifique também os **endereços IP de destino/intervalos de CIDR**.</p><p>Se você escolher o **VirtualNetwork**, o tráfego será permitido para todos os endereços IP no espaço de endereço da rede virtual. O **VirtualNetwork** é uma marca de serviço.</p><p>Se você selecionar **grupo de segurança de aplicativo**, deverá selecionar um grupo de segurança de aplicativo existente. Saiba como [criar um grupo de segurança do aplicativo](#create-an-application-security-group).</p> |
    | **Intervalos de CIDR/endereços IP de destino** | Uma lista delimitada por vírgulas de endereços IP e intervalos CIDR | <p>Essa configuração será exibida se você alterar o **destino** para **endereços IP**. Semelhante aos intervalos de **CIDR/endereços IP** **de origem e** de origem, você pode especificar um ou vários endereços ou intervalos. Há limites para o número que você pode especificar. Para obter mais detalhes, consulte [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Se o endereço IP especificado for atribuído a uma VM do Azure, certifique-se de especificar seu IP privado, não seu endereço IP público. O Azure processa as regras de segurança depois de converter o endereço IP público em um endereço IP privado para regras de segurança de entrada, mas antes que o Azure traduza um endereço IP privado para um endereço IP público para as regras de saída. Para saber mais sobre endereços IP públicos e privados no Azure, consulte [Tipos de endereços IP](./public-ip-addresses.md).</p> |
    | **Marca de serviço de destino** | Uma marca de serviço da lista suspensa | Essa configuração opcional será exibida se você alterar o **destino** para a **marca de serviço** de uma regra de segurança de saída. Uma marcação de serviço é um identificador predefinido para uma categoria de endereços IP. Para saber mais sobre as marcas de serviço disponíveis e o que cada marca representa, consulte [marcas de serviço](./network-security-groups-overview.md#service-tags). |
    | **Grupo de segurança do aplicativo de destino** | Um grupo de segurança de aplicativo existente | Essa configuração será exibida se você definir **destino** como **grupo de segurança de aplicativo**. Selecione um grupo de segurança de aplicativo que exista na mesma região que a interface de rede. Saiba como [criar um grupo de segurança do aplicativo](#create-an-application-security-group). |
    | **Intervalos de portas de destino** | Um destes:<ul><li>Uma única porta, como `80`</li><li>Um intervalo de portas, como `1024-65535`</li><li>Uma lista separada por vírgulas de portas únicas e/ou intervalos de porta, como `80, 1024-65535`</li><li>Um asterisco ( `*` ) para permitir o tráfego em qualquer porta</li></ul> | Assim como acontece com os **intervalos de porta de origem**, você pode especificar uma ou várias portas e intervalos. Há limites para o número que você pode especificar. Para obter mais detalhes, consulte [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Protocolo** | **Qualquer**, **TCP**, **UDP** ou **ICMP** | Você pode restringir a regra ao protocolo TCP, UDP (User Datagram Protocol) ou ICMP (protocolo de mensagens de controle da Internet). O padrão é que a regra seja aplicada a todos os protocolos. |
    | **Ação** | **Permitir** ou **negar** | Essa configuração especifica se essa regra permite ou nega o acesso para a configuração de origem e destino fornecida. |
    | **Prioridade** | Um valor entre 100 e 4096 que é exclusivo para todas as regras de segurança no grupo de segurança de rede | O Azure processa as regras de segurança em ordem de prioridade. Quanto menor o número, maior a prioridade. É recomendável deixar uma lacuna entre os números de prioridade ao criar regras, como 100, 200 e 300. Deixar as lacunas facilita a adição de regras no futuro, para que você possa dar a elas uma prioridade maior ou menor do que as regras existentes. |
    | **Nome** | Um nome exclusivo para a regra dentro do grupo de segurança de rede | O nome pode ter até 80 caracteres. Ele deve começar com uma letra ou número e deve terminar com uma letra, número ou sublinhado. O nome pode conter apenas letras, números, sublinhados, pontos ou hifens. |
    | **Descrição** | Uma descrição de texto | Opcionalmente, você pode especificar uma descrição de texto para a regra de segurança. A descrição não pode ter mais de 140 caracteres. |

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Exibir todas as regras de segurança

Um grupo de segurança de rede contém zero ou mais regras. Para saber mais sobre as informações listadas ao exibir regras, consulte a [Visão geral de Grupo de Segurança de Rede](./network-security-groups-overview.md).

1. Vá para a [portal do Azure](https://portal.azure.com) para exibir as regras de um grupo de segurança de rede. Procure e selecione **grupos de segurança de rede**.

2. Selecione o nome do grupo de segurança de rede para o qual você deseja exibir as regras.

3. Na barra de menus do grupo de segurança de rede, escolha **regras de segurança de entrada** ou regras de segurança de **saída**.

A lista contém todas as regras que você criou e as [regras de segurança padrão](./network-security-groups-overview.md#default-security-rules)do grupo de segurança de rede.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Exibir os detalhes de uma regra de segurança

1. Vá para a [portal do Azure](https://portal.azure.com) para exibir as regras de um grupo de segurança de rede. Procure e selecione **grupos de segurança de rede**.

2. Selecione o nome do grupo de segurança de rede para o qual você deseja exibir os detalhes de uma regra.

3. Na barra de menus do grupo de segurança de rede, escolha **regras de segurança de entrada** ou regras de segurança de **saída**.

4. Selecione a regra cujos detalhes você deseja exibir. Para obter uma explicação de todas as configurações, consulte [configurações de regra de segurança](#security-rule-settings).

    > [!NOTE]
    > Este procedimento se aplica somente a uma regra de segurança personalizada. Ele não funcionará se você escolher uma regra de segurança padrão.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network NSG Rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Alterar uma regra de segurança

1. Conclua as etapas em [Exibir detalhes de uma regra de segurança](#view-details-of-a-security-rule).

2. Altere as configurações conforme necessário e, em seguida, selecione **salvar**. Para obter uma explicação de todas as configurações, consulte [configurações de regra de segurança](#security-rule-settings).

    > [!NOTE]
    > Este procedimento se aplica somente a uma regra de segurança personalizada. Você não tem permissão para alterar uma regra de segurança padrão.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Excluir uma regra de segurança

1. Conclua as etapas em [Exibir detalhes de uma regra de segurança](#view-details-of-a-security-rule).

2. Selecione **Excluir** e, em seguida, selecione **Sim**.

    > [!NOTE]
    > Este procedimento se aplica somente a uma regra de segurança personalizada. Você não tem permissão para excluir uma regra de segurança padrão.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network NSG Rule Delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Trabalhar com grupos de segurança de aplicativo

Um grupo de segurança de aplicativo contém zero ou mais adaptadores de rede. Para saber mais, consulte [grupos de segurança de aplicativo](./network-security-groups-overview.md#application-security-groups). Todos os adaptadores de rede em um grupo de segurança do aplicativo precisam existir na mesma rede virtual. Para saber como adicionar um adaptador de rede a um grupo de segurança de aplicativo, consulte [Adicionar um adaptador de rede a um grupo de segurança de aplicativo](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Criar um grupo de segurança de aplicativo

1. No menu do [portal do Azure](https://portal.azure.com) ou na **Página Inicial**, selecione **Criar um recurso**.

2. Na caixa de pesquisa, insira *grupo de segurança de aplicativo*.

3. Na página **grupo de segurança de aplicativo** , selecione **criar**.

4. Na página **criar um grupo de segurança de aplicativo** , na guia **noções básicas** , defina valores para as seguintes configurações:

    | Configuração | Ação |
    | --- | --- |
    | **Assinatura** | Escolha sua assinatura. |
    | **Grupo de recursos** | Escolha um grupo de recursos existente ou selecione **criar novo** para criar um novo grupo de recursos. |
    | **Nome** | Insira uma cadeia de texto exclusiva em um grupo de recursos. |
    | **Região** | Escolha o local desejado. |

5. Selecione **Examinar + criar**.

6. Na guia **revisar + criar** , depois de ver a mensagem **validação aprovada** , selecione **criar**.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network ASG Create](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Exibir todos os grupos de segurança de aplicativo

Vá para a [portal do Azure](https://portal.azure.com) para exibir os grupos de segurança do aplicativo. Procure e selecione **grupos de segurança de aplicativo**. O portal do Azure exibe uma lista de seus grupos de segurança de aplicativo.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network ASG List](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Exibir detalhes de um grupo de segurança de aplicativo específico

1. Vá para a [portal do Azure](https://portal.azure.com) para exibir um grupo de segurança de aplicativo. Procure e selecione **grupos de segurança de aplicativo**.

2. Selecione o nome do grupo de segurança de aplicativo para o qual você deseja exibir os detalhes.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network ASG show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Alterar um grupo de segurança de aplicativo

1. Vá para a [portal do Azure](https://portal.azure.com) para exibir um grupo de segurança de aplicativo. Procure e selecione **grupos de segurança de aplicativo**.

2. Selecione o nome do grupo de segurança do aplicativo que você deseja alterar.

3. Selecione **alterar** ao lado da configuração que você deseja modificar. Por exemplo, você pode adicionar ou remover **marcas** ou pode alterar o grupo de **recursos** ou a **assinatura**.

    > [!NOTE]
    > Não é possível alterar o local.

    Na barra de menus, você também pode selecionar **iam (controle de acesso)**. Na página **controle de acesso (iam)** , você pode atribuir ou remover permissões para o grupo de segurança de aplicativo.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network ASG Update](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Nenhum cmdlet do PowerShell |

### <a name="delete-an-application-security-group"></a>Excluir um grupo de segurança de aplicativo

Você não poderá excluir um grupo de segurança de aplicativo se ele contiver qualquer interface de rede. Para remover todas as interfaces de rede do grupo de segurança de aplicativo, altere as configurações de interface de rede ou exclua as interfaces de rede. Para saber mais, confira [Adicionar ou remover de grupos de segurança de aplicativo](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) ou [excluir um adaptador de rede](virtual-network-network-interface.md#delete-a-network-interface).

1. Vá para o [portal do Azure](https://portal.azure.com) para gerenciar os grupos de segurança do aplicativo. Procure e selecione **grupos de segurança de aplicativo**.

2. Selecione o nome do grupo de segurança do aplicativo que você deseja excluir.

3. Selecione **Excluir** e, em seguida, selecione **Sim** para excluir o grupo de segurança do aplicativo.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network ASG Delete](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Permissões

Para realizar tarefas em grupos de segurança de rede, regras de segurança e grupos de segurança de aplicativo, sua conta deve ser atribuída à função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuída às permissões apropriadas, conforme listado nas tabelas a seguir:

### <a name="network-security-group"></a>Grupo de segurança de rede

| Ação                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Obter Grupo de Segurança de Rede                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Criar ou atualizar um Grupo de Segurança de Rede                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Excluir Grupo de Segurança de Rede                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Associar um grupo de segurança de rede a uma interface de sub-rede ou rede 

### <a name="network-security-group-rule"></a>Regra do grupo de segurança de rede

| Ação                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/securityRules/read            |   Obter regra                                                            |
| Microsoft.Network/networkSecurityGroups/securityRules/write           |   Criar ou atualizar regra                                               |
| Microsoft.Network/networkSecurityGroups/securityRules/delete          |   Excluir regra                                                         |

### <a name="application-security-group"></a>Grupo de segurança do aplicativo

| Ação                                                                     | Name                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Entrar uma configuração IP a um grupo de segurança do aplicativo|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Entrar em uma regra de segurança aos grupos de segurança de aplicativo    |
| Microsoft.Network/applicationSecurityGroups/read                           | Obter um grupo de segurança de aplicativo                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Criar ou atualizar um grupo de segurança de aplicativo           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Excluir um grupo de segurança de aplicativo                     |

## <a name="next-steps"></a>Próximas etapas

- Criar um grupo de segurança de aplicativo ou de rede usando o [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts de exemplo ou modelos do Azure [Resource Manager](template-samples.md)
- Criar e atribuir [definições de Azure Policy](./policy-reference.md) para redes virtuais