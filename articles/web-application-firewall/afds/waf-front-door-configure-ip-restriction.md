---
title: Configurar a regra WAF de restrição de IP para a porta frontal do Azure
description: Saiba como configurar uma regra de firewall de aplicativo Web para restringir os endereços IP para um ponto de extremidade de porta de recepção do Azure existente.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 12/22/2020
ms.author: tyao
ms.openlocfilehash: 65e378c0380804c13e4b42d855aede7781b93592
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211661"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Configurar uma regra de restrição de IP com um firewall do aplicativo Web para a porta frontal do Azure

Este artigo mostra como configurar regras de restrição de IP em um WAF (firewall do aplicativo Web) para a porta frontal do Azure usando o portal do Azure, CLI do Azure, Azure PowerShell ou um modelo Azure Resource Manager.

Uma regra de controle de acesso baseada em endereço IP é uma regra de WAF personalizada que permite controlar o acesso aos seus aplicativos Web. Ele faz isso especificando uma lista de endereços IP ou intervalos de endereços IP no formato CIDR (roteamento sem classe Inter-Domain). Há dois tipos de variáveis de correspondência na correspondência de endereço IP, **RemoteAddr** e **SocketAddr**. RemoteAddr é o IP do cliente original que geralmente é enviado via cabeçalho X-Forwardd-for Request. SocketAddr é o endereço IP de origem que o WAF vê. Se o usuário estiver protegido por um proxy, o SocketAddr geralmente será o endereço do servidor proxy.

Por padrão, seu aplicativo Web pode ser acessado pela Internet. Se você quiser limitar o acesso a clientes de uma lista de endereços IP conhecidos ou intervalos de endereços IP, você poderá criar uma regra de correspondência de IP que contenha a lista de endereços IP como valores correspondentes e definir operador como "Not" (negar é verdadeiro) e a ação a ser **bloqueada**. Depois que uma regra de restrição de IP é aplicada, as solicitações originadas de endereços fora dessa lista de permissão recebem uma resposta de 403 Proibido.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Configurar uma política de WAF com o portal do Azure

### <a name="prerequisites"></a>Pré-requisitos

Crie um perfil de porta frontal do Azure seguindo as instruções descritas em [início rápido: criar uma porta frontal para um aplicativo Web global altamente disponível](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Criar uma política de WAF

1. Na portal do Azure, selecione **criar um recurso**, digite  **Firewall do aplicativo Web** na caixa de pesquisa e, em seguida, selecione **Firewall do aplicativo Web (WAF)**.
2. Selecione **Criar**.
3. Na página **criar uma política de WAF** , use os seguintes valores para concluir a guia **noções básicas** :
   
   |Configuração  |Valor  |
   |---------|---------|
   |Política para     |WAF global (porta frontal)|
   |Subscription     |Selecionar sua assinatura|
   |Resource group     |Selecione o grupo de recursos onde está a sua porta frontal.|
   |Nome de política     |Digite um nome para a política|
   |Estado da política     |habilitado|

   Selecione **Avançar: configurações de política**

1. Na guia **configurações de política** , selecione **prevenção**. Para o **corpo da resposta de bloco**, digite *você foi bloqueado!* Você pode ver que sua regra personalizada está em vigor.
2. Selecione **Avançar: regras gerenciadas**.
3. Selecione **Avançar: regras personalizadas**.
4. Selecione **Adicionar regra personalizada**.
5. Na página **Adicionar regra personalizada** , use os seguintes valores de teste para criar uma regra personalizada:

   |Configuração  |Valor  |
   |---------|---------|
   |Nome da regra personalizada     |FdWafCustRule|
   |Status     |habilitado|
   |Tipo de regra     |Corresponder a|
   |Prioridade    |100|
   |Tipo de correspondência     |Endereço IP|
   |Variável de correspondência|RemoteAddr|
   |Operação|Não contém|
   |Endereço IP ou intervalo|rede 10.10.10.0/24|
   |Então|Negar tráfego|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Regra personalizada":::

   Selecione **Adicionar**.
6. Selecione **Avançar: Associação**.
7. Selecione **Adicionar host de front-end**.
8. Para **host de front-end**, selecione seu host de front-end e selecione **Adicionar**.
9. Selecione **Examinar + criar**.
10. Depois que a validação da política passar, selecione **criar**.

### <a name="test-your-waf-policy"></a>Testar sua política do WAF

1. Depois que a implantação da política do WAF for concluída, navegue até o nome do host de front-end da porta frontal.
2. Você deve ver sua mensagem de bloco personalizada.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="Teste de regra WAF":::

   > [!NOTE]
   > Um endereço IP privado foi usado intencionalmente na regra personalizada para garantir que a regra seja disparada. Em uma implantação real, crie regras de *permissão* e *negação* usando endereços IP para sua situação específica.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Configurar uma política de WAF com o CLI do Azure

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de restrição de IP, configure o ambiente da CLI e crie um perfil de porta frontal do Azure.

#### <a name="set-up-the-azure-cli-environment"></a>Configurar o ambiente de CLI do Azure
1. Instale o [CLI do Azure](/cli/azure/install-azure-cli)ou use Azure cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Selecione o botão **experimentar** nos comandos da CLI que seguem e, em seguida, entre em sua conta do Azure na sessão de Cloud shell que é aberta. Depois que a sessão for iniciada, insira `az extension add --name front-door` para adicionar a extensão de porta frontal do Azure.
 2. Se você estiver usando a CLI localmente no bash, entre no Azure usando o `az login` .

#### <a name="create-an-azure-front-door-profile"></a>Criar um perfil de porta de recepção do Azure
Crie um perfil de porta frontal do Azure seguindo as instruções descritas em [início rápido: criar uma porta frontal para um aplicativo Web global altamente disponível](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Criar uma política de WAF

Crie uma política de WAF usando o comando [AZ Network front-door WAF-Policy Create](/cli/azure/ext/front-door/network/front-door/waf-policy#ext-front-door-az-network-front-door-waf-policy-create) . No exemplo a seguir, substitua o nome da política *IPAllowPolicyExampleCLI* por um nome de política exclusivo.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Adicionar uma regra de controle de acesso de IP personalizada

Use o comando [AZ Network front-door WAF-Policy personalizada-Rule Create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule#ext-front-door-az-network-front-door-waf-policy-rule-create) para adicionar uma regra de controle de acesso de IP personalizada para a política de WAF que você acabou de criar.

Nos exemplos a seguir:
-  Substitua *IPAllowPolicyExampleCLI* pela sua política exclusiva criada anteriormente.
-  Substitua *IP-address-Range-1*, *IP-address-Range-2* por seu próprio intervalo.

Primeiro, crie uma regra de permissão de IP para a política criada na etapa anterior. 
> [!NOTE]
> **--Defer** é necessário porque uma regra deve ter uma condição de correspondência para ser adicionada na próxima etapa.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Em seguida, adicione a condição de correspondência à regra:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Localizar a ID de uma política de WAF 
Localize a ID de uma política de WAF usando o comando [AZ Network front-door WAF-Policy show](/cli/azure/ext/front-door/network/front-door/waf-policy#ext-front-door-az-network-front-door-waf-policy-show) . Substitua *IPAllowPolicyExampleCLI* no exemplo a seguir pela sua política exclusiva que você criou anteriormente.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Vincular uma política do WAF a um host front-end da porta do Azure

Defina a ID de *WebApplicationFirewallPolicyLink* da porta frontal do Azure para a ID da política usando o comando [AZ Network front-door Update](/cli/azure/ext/front-door/network/front-door#ext-front-door-az-network-front-door-update) . Substitua *IPAllowPolicyExampleCLI* pela sua política exclusiva que você criou anteriormente.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Neste exemplo, a política WAF é aplicada a **FrontendEndpoints [0]**. Você pode vincular a política WAF a qualquer um dos seus front-ends.
> [!Note]
> Você precisa definir a propriedade **WebApplicationFirewallPolicyLink** apenas uma vez para vincular uma política de WAF a um front-end da porta do Azure. As atualizações de política subsequentes são aplicadas automaticamente ao front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Configurar uma política de WAF com Azure PowerShell

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de restrição de IP, configure o ambiente do PowerShell e crie um perfil de porta frontal do Azure.

#### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
Azure PowerShell fornece um conjunto de cmdlets que usam o modelo de [Azure Resource Manager](../../azure-resource-manager/management/overview.md) para gerenciar recursos do Azure.

Você pode instalar o [Azure PowerShell](/powershell/azure/) no computador local e usá-lo em qualquer sessão do PowerShell. Siga as instruções na página para entrar no PowerShell usando suas credenciais do Azure e, em seguida, instale o módulo AZ.

1. Conecte-se ao Azure usando o comando a seguir e, em seguida, use uma caixa de diálogo interativa para entrar.
    ```
    Connect-AzAccount
    ```
 2. Antes de instalar um módulo de porta frontal do Azure, verifique se você tem a versão atual do módulo PowerShellGet instalada. Execute o comando a seguir e reabra o PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Instale o módulo AZ. FrontDoor usando o comando a seguir. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Criar um perfil de porta de recepção do Azure
Crie um perfil de porta frontal do Azure seguindo as instruções descritas em [início rápido: criar uma porta frontal para um aplicativo Web global altamente disponível](../../frontdoor/quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Definir uma condição de correspondência de IP
Use o comando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) para definir uma condição de correspondência de IP.
No exemplo a seguir, substitua *IP-address-Range-1*, *IP-address-Range-2* por seu próprio intervalo.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Criar uma regra de permissão de IP personalizada

Use o comando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) para definir uma ação e definir uma prioridade. No exemplo a seguir, as solicitações não dos IPs de cliente que correspondem à lista serão bloqueadas.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Configurar uma política de WAF
Localize o nome do grupo de recursos que contém o perfil de porta frontal do Azure usando `Get-AzResourceGroup` . Em seguida, configure uma política de WAF com a regra de IP usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Vincular uma política do WAF a um host front-end da porta do Azure

Vincule um objeto de política WAF a um host front-end existente e atualize as propriedades da porta frontal do Azure. Primeiro, recupere o objeto de porta frontal do Azure usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Em seguida, defina a propriedade **WebApplicationFirewallPolicyLink** para a ID de recurso de *$IPAllowPolicyExamplePS*, criada na etapa anterior, usando o comando [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) .

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Neste exemplo, a política WAF é aplicada a **FrontendEndpoints [0]**. Você pode vincular uma política de WAF a qualquer um de seus front-ends. Você precisa definir a propriedade **WebApplicationFirewallPolicyLink** apenas uma vez para vincular uma política de WAF a um front-end da porta do Azure. As atualizações de política subsequentes são aplicadas automaticamente ao front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Configurar uma política de WAF com um modelo do Resource Manager
Para exibir o modelo que cria uma política de porta frontal do Azure e uma política de WAF com regras de restrição de IP personalizadas, vá para [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um perfil de porta de recepção do Azure](../../frontdoor/quickstart-create-front-door.md).
