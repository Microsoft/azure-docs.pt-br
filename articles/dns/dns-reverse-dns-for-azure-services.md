---
title: DNS reverso para serviços do Azure-DNS do Azure
description: Com este roteiro de aprendizagem, comece Configurando pesquisas reversas de DNS para serviços hospedados no Azure.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: c72e17e601ebf87b0f344a4723159ae22abc81d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919860"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Configurar DNS reverso para serviços hospedados no Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este artigo explica como configurar pesquisas inversas de DNS para serviços hospedados no Azure.

Os serviços no Azure usam endereços IP atribuídos pelo Azure e de propriedade da Microsoft. Esses registros DNS reversos (registros PTR) devem ser criados nas zonas de pesquisa inversa de DNS de propriedade da Microsoft correspondentes. Esse artigo explica como fazer isso.

Este cenário não deve ser confundido com a capacidade de [hospedar as zonas de pesquisa inversa de DNS para seus intervalos de IP atribuídos no DNS do Azure](dns-reverse-dns-hosting.md). Nesse caso, os intervalos de IP representados pela zona de pesquisa inversa devem ser atribuídos à sua organização, normalmente por seu ISP.

Antes de ler este artigo, você deve estar familiarizado com essa [Visão geral de DNS reverso e suporte no Azure](dns-reverse-dns-overview.md).

No DNS do Azure, os recursos de computação (como máquinas virtuais, conjuntos de escala de máquina virtual ou clusters do Service Fabric) são expostos por meio de um recurso PublicIpAddress. As pesquisas inversas de DNS são configuradas usando a propriedade 'ReverseFqdn' de PublicIpAddress.


O DNS reverso não tem suporte no momento para o serviço de Azure App e o gateway de aplicativo.

## <a name="validation-of-reverse-dns-records"></a>Validação de registros DNS reversos

Uma terceira parte não deve ser capaz de criar registros DNS reversos para seu mapeamento de serviço do Azure para os domínios DNS. Para evitar isso, o Azure só permite a criação de um registro DNS reverso em que o nome de domínio especificado no registro de DNS reverso seja igual ou resolva para o nome DNS ou endereço IP de um PublicIpAddress ou um serviço de nuvem na mesma assinatura do Azure.

Essa validação é realizada somente quando o registro de DNS reverso é definido ou modificado. Uma nova validação periódica não é executada.

Por exemplo: suponha que o recurso PublicIpAddress tenha o endereço IP 23.96.52.53 e contosoapp1.northus.cloudapp.azure.com de nome DNS. O ReverseFqdn para o PublicIpAddress pode ser especificado como:
* O nome DNS para o PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* O nome DNS para um PublicIpAddress diferente na mesma assinatura, como contosoapp2.westus.cloudapp.azure.com
* Um nome DNS personalizado, como app1.contoso.com, desde que esse nome seja *primeiro* configurado como um CNAME para contosoapp1.northus.cloudapp.azure.com ou um PublicIpAddress diferente na mesma assinatura.
* Um nome DNS personalizado, como app1.contoso.com, desde que esse nome seja *primeiro* configurado como um registro para o endereço IP 23.96.52.53 ou o endereço IP de um PublicIpAddress diferente na mesma assinatura.

As mesmas restrições aplicam-se a DNS reverso para Serviços de Nuvem.


## <a name="reverse-dns-for-publicipaddress-resources"></a>DNS reverso para recursos de PublicIpAddress

Esta seção fornece instruções detalhadas sobre como configurar o DNS reverso para recursos de PublicIpAddress no modelo de implantação do Resource Manager, usando o Azure PowerShell, a CLI Clássica do Azure ou a CLI do Azure. Configurar o DNS reverso para recursos de PublicIpAddress não é suportado no momento por meio do Portal do Azure.

O Azure atualmente oferece suporte a DNS reverso somente para recursos PublicIpAddress de IPv4. Não há suporte para IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Adicionar o DNS reverso a PublicIpAddresses existentes

#### <a name="powershell"></a>PowerShell

Para atualizar o DNS reverso para um PublicIpAddress existente:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Para adicionar o DNS reverso a um PublicIpAddress existente que ainda não tenha um nome DNS, também deverá especificar um nome DNS:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

Para adicionar o DNS reverso ao PublicIpAddress existente:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Para adicionar o DNS reverso a um PublicIpAddress existente que ainda não tenha um nome DNS, também deverá especificar um nome DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>CLI do Azure

Para adicionar o DNS reverso ao PublicIpAddress existente:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Para adicionar o DNS reverso a um PublicIpAddress existente que ainda não tenha um nome DNS, também deverá especificar um nome DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Criar um Endereço IP Público com DNS reverso

Para criar um novo PublicIpAddress com a propriedade DNS reversa já especificada:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Exibir o DNS reverso para um PublicIpAddress existente

Para exibir o valor configurado para um PublicIpAddress existente:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Remover o DNS reverso de Endereços IP Públicos existentes

Para remover uma propriedade de DNS reverso de um PublicIpAddress existente:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Configurar o DNS reverso para Serviços de Nuvem

Esta seção fornece instruções detalhadas sobre como configurar o DNS reverso para Serviços de Nuvem no modelo de implantação clássico, usando o Azure PowerShell. A ação de configurar o DNS reverso para Serviços de Nuvem não é compatível por meio do portal do Azure, da CLI Clássica do Azure ou da CLI do Azure.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Adicionar DNS reverso aos Serviços de Nuvem existentes

Para adicionar um registro DNS reverso a um Serviço de Nuvem existente:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Criar um Serviço de Nuvem com DNS reverso

Para criar um novo Serviço de Nuvem com a propriedade DNS reversa já especificada:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Exibir o DNS reverso dos Serviços de Nuvem existentes

Para exibir uma propriedade de DNS reverso para um Serviço de Nuvem existente:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Remover o DNS reverso dos Serviços de Nuvem existentes

Para remover uma propriedade de DNS reverso de um Serviço de Nuvem existente:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>Perguntas frequentes

### <a name="how-much-do-reverse-dns-records-cost"></a>Qual é o custo dos registros DNS reversos?

Eles são gratuitos!  Não há nenhum custo adicional para registros DNS reversos ou consultas.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Meus registros DNS reversos serão resolvidos na Internet?

Sim. Depois de definir a propriedade de DNS reverso para seu Serviço do Azure, o Azure gerenciará todas as delegações de DNS e as zonas DNS necessárias para garantir que o registro DNS reverso seja resolvido para todos os usuários da Internet.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Os registros DNS reversos padrão serão criados para meus serviços do Azure?

Não. O DNS reverso é um recurso opcional. Nenhum registro DNS reverso padrão é criado se você optar por não configurar um.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Qual é o formato do FQDN (nome de domínio totalmente qualificado)?

Os FQDNs são especificados em ordem crescente e devem terminar com um ponto (por exemplo, “app1.contoso.com.”).

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>O que acontecerá se ocorrer uma falha nas verificações de validação do DNS reverso especificado?

Quando a verificação de validação de DNS reverso falhar, a operação para configurar o registro DNS reverso falhará. Corrija o valor DNS reverso conforme necessário e tente novamente.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Posso configurar o DNS reverso para o Serviço de Aplicativo do Azure?

Não. O DNS reverso não é suportado para o Serviço de Aplicativo do Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Posso configurar vários registros DNS reversos para meu serviço do Azure?

Não. O Azure dá suporte a um único registro DNS reverso por Serviço de Nuvem do Azure ou PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Posso configurar o DNS reverso para recursos de PublicIpAddress do IPv6?

Não. O Azure atualmente oferece suporte a DNS reverso somente para recursos PublicIpAddress de IPv4 e Serviços de Nuvem.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Posso enviar emails a domínios externos de meus Serviços de Computação do Azure?

A capacidade técnica de enviar email diretamente de uma implantação do Azure depende do tipo de assinatura. Independentemente do tipo de assinatura, a Microsoft recomenda o uso de serviços de retransmissão de email confiáveis para enviar emails de saída. Para mais detalhes, consulte [Segurança reforçada do Azure para envio de Emails - atualização de novembro de 2017](../virtual-network/troubleshoot-outbound-smtp-connectivity.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre DNS reverso, confira [Pesquisa de DNS reverso na Wikipédia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como [hospedar a zona de pesquisa inversa para o intervalo de IP atribuído pelo ISP no DNS do Azure](dns-reverse-dns-for-azure-services.md).
