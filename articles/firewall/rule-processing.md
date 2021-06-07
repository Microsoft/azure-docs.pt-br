---
title: Lógica de processamento de regra do Firewall do Azure
description: O firewall do Azure tem regras NAT, regras de rede e regras de aplicativos. As regras são processadas de acordo com o tipo de regra.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/01/2021
ms.author: victorh
ms.openlocfilehash: bbf838cfa2a6addc665df4b62e2322d056778b49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741354"
---
# <a name="configure-azure-firewall-rules"></a>Configurar regras de firewall do Azure
Você pode configurar regras de NAT, regras de rede e regras de aplicativos no firewall do Azure. As coleções de regras são processadas de acordo com o tipo de regra em ordem de prioridade, números menores para números mais altos de 100 a 65.000. Um nome de coleção de regras pode ter apenas letras, números, sublinhados, pontos ou hifens. Ele deve começar com uma letra ou número e terminar com uma letra, número ou sublinhado. O comprimento máximo do nome é de 80 caracteres.

É melhor espaçar inicialmente os números de prioridade da coleção de regras em incrementos de 100 (100, 200, 300 e assim por diante) para que você tenha espaço para adicionar mais coleções de regras, se necessário.

> [!NOTE]
> Se você habilitar a filtragem baseada em inteligência contra ameaças, essas regras terão prioridade mais alta e sempre serão processadas primeiro. A filtragem de inteligência de ameaças pode negar o tráfego antes que qualquer regra configurada seja processada. Para obter mais informações, consulte [filtragem baseada em inteligência contra ameaças do firewall do Azure](threat-intel.md).

## <a name="outbound-connectivity"></a>Conectividade de saída

### <a name="network-rules-and-applications-rules"></a>Regras de rede e regras de aplicativos

Se você configurar regras de rede e regras de aplicativo, as regras de rede serão aplicadas em ordem de prioridade antes das regras de aplicativo. As regras estão sendo encerradas. Portanto, se uma correspondência for encontrada em uma regra de rede, não serão processadas outras regras.  Se não houver nenhuma correspondência de regra de rede, e se o protocolo for HTTP, HTTPS ou MSSQL, o pacote será então avaliado pelas regras de aplicativo em ordem de prioridade. Se ainda não for encontrada nenhuma correspondência, o pacote será avaliado em relação à [coleção de regras de infraestrutura](infrastructure-fqdns.md). Se ainda não houver correspondência, o pacote será negado por padrão.

#### <a name="network-rule-protocol"></a>Protocolo de regra de rede

As regras de rede podem ser configuradas para **TCP**, **UDP**, **ICMP** ou **qualquer** protocolo IP. Qualquer protocolo IP inclui todos os protocolos IP, conforme definido no documento [números de protocolo da autoridade de números de Internet atribuídos (IANA)](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) . Se uma porta de destino estiver configurada explicitamente, a regra será convertida em uma regra TCP + UDP.

Antes de 9 de novembro de 2020, **qualquer** significava **TCP**, **UDP** ou **ICMP**. Portanto, você pode ter configurado uma regra antes dessa data com protocolo = qualquer e portas de destino = ' * '. Se você não pretende realmente permitir qualquer protocolo IP como definido atualmente, modifique a regra para configurar explicitamente os protocolos desejados (TCP, UDP ou ICMP).

## <a name="inbound-connectivity"></a>Conexões de entrada

### <a name="nat-rules"></a>Regras de NAT

A conectividade de Internet de entrada pode ser habilitada Configurando DNAT (conversão de endereços de rede de destino) conforme descrito em [tutorial: filtrar o tráfego de entrada com o Firewall do Azure DNAT usando o portal do Azure](tutorial-firewall-dnat.md). As regras de NAT são aplicadas em prioridade antes das regras de rede. Se uma correspondência for encontrada, será adicionada uma regra de rede correspondente implícita para permitir o tráfego convertido. Por motivos de segurança, a abordagem recomendada é adicionar uma fonte de Internet específica para permitir o acesso DNAT à rede e evitar o uso de caracteres curinga.

As regras de aplicativo não são aplicadas a conexões de entrada. Portanto, se você quiser filtrar o tráfego HTTP/S de entrada, deverá usar o WAF (firewall do aplicativo Web). Para obter mais informações, consulte [o que é o Firewall do aplicativo Web do Azure?](../web-application-firewall/overview.md)

## <a name="examples"></a>Exemplos

Os exemplos a seguir mostram os resultados de algumas dessas combinações de regras.

### <a name="example-1"></a>Exemplo 1

A conexão com o google.com é permitida devido a uma regra de rede correspondente.

**Regra de rede**

- Ação: Permitir


|name  |Protocolo  |Tipo de origem  |Fonte  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-web     |TCP|Endereço IP|*|Endereço IP|*|80.443

**Regra de aplicativo**

- Ação: Negar

|name  |Tipo de origem  |Fonte  |Protocolo:Porta|FQDNs de destino|
|---------|---------|---------|---------|----------|----------|
|Negar-Google     |Endereço IP|*|http:80,https:443|google.com

**Resultado**

A conexão com google.com é permitida porque o pacote corresponde à regra de rede *Allow-Web* . O processamento da regra é interrompido neste ponto.

### <a name="example-2"></a>Exemplo 2

O tráfego SSH é negado porque uma coleção de regras de rede de *negação* de prioridade mais alta o bloqueia.

**Coleção de regras de rede 1**

- Nome: Allow-collection
- Prioridade: 200
- Ação: Permitir

|name  |Protocolo  |Tipo de origem  |Fonte  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-SSH     |TCP|Endereço IP|*|Endereço IP|*|22

**Coleção de regras de rede 2**

- Nome: Deny-collection
- Prioridade: 100
- Ação: Negar

|name  |Protocolo  |Tipo de origem  |Fonte  |Tipo de destino  |Endereço de destino  |Portas de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH     |TCP|Endereço IP|*|Endereço IP|*|22

**Resultado**

As conexões SSH são negadas porque uma coleção de regras de rede de prioridade mais alta a bloqueia. O processamento da regra é interrompido neste ponto.

## <a name="rule-changes"></a>Alterações de regra

Se você alterar uma regra para negar tráfego anteriormente permitido, todas as sessões existentes relevantes serão descartadas.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [implantar e configurar um firewall do Azure](tutorial-firewall-deploy-portal.md).
