---
title: Tutorial - Criar registros DNS personalizados para um aplicativo Web
description: Neste tutorial, você aprenderá como criar registros DNS de domínio personalizado para o aplicativo Web usando o DNS do Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: 369c7dab174f0269797b10635882a6821ade8311
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94952838"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>Tutorial: Criar registros DNS em um domínio personalizado para um aplicativo Web 

Você pode configurar o DNS do Azure para hospedar um domínio personalizado em seus aplicativos Web. Por exemplo, você pode criar um aplicativo Web do Azure e permitir que os usuários o acessem usando www\.contoso.com ou contoso.com como um FQDN (nome de domínio totalmente qualificado).

> [!NOTE]
> Contoso.com é usado como um exemplo ao longo deste tutorial. Substitua seu próprio nome de domínio para contoso.com.

Para isso, você precisa criar dois registros:

* Um registro "A" raiz que aponta para contoso.com
* Uma raiz do Registro "TXT" para verificação
* Um registro "CNAME" para o nome www que aponta para o registro A

Tenha em mente que, se você criar um registro A para um aplicativo Web no Azure, o registro A deve ser manualmente atualizado se o endereço IP subjacente para o aplicativo Web for alterado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um registro A e TXT para seu domínio personalizado
> * Criar um registro CNAME para seu domínio personalizado
> * Testar os novos registros
> * Adicionar nomes de host personalizados a seu aplicativo Web
> * Testar os nomes de host personalizados

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Você deve ter um nome de domínio disponível com o qual seja possível testar e que você possa hospedar no DNS do Azure. Você deve ter controle total sobre esse domínio. Controle total inclui a capacidade de definir os registros NS (nomes de servidor) para o domínio.
* [Crie um aplicativo do Serviço de Aplicativo](../app-service/quickstart-html.md) ou use um aplicativo que você criou para outro tutorial.

* Crie uma zona DNS no DNS do Azure e delegue a zona no registrador para o DNS do Azure.

   1. Para criar uma zona DNS, siga as etapas em [Criar uma zona DNS](./dns-getstarted-powershell.md).
   2. Para delegar sua zona ao DNS do Azure, siga as etapas em [Delegação de domínio DNS](dns-delegate-domain-azure-dns.md).

Após criar uma zona e delegá-la ao DNS do Azure, você pode criar registros para seu domínio personalizado.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-a-record-and-txt-record"></a>Criar um registro A e um registro TXT

Um registro A é usado para mapear um nome para seu endereço IP. No exemplo a seguir, atribua “\@” como um registro A usando o endereço IPv4 do aplicativo Web. \@ normalmente representa o domínio raiz.

### <a name="get-the-ipv4-address"></a>Obter o endereço IPv4

No painel de navegação à esquerda da página dos Serviços de Aplicativos no portal do Azure, selecione **Domínios personalizados**. 

![Menu de domínio personalizado](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na página **Domínios personalizados**, copie o endereço IPv4 do aplicativo:

![Navegação no Portal para o aplicativo do Azure](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>Criar um conjunto A de registros

```azurepowershell
New-AzDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>Criar o registro TXT

Os Serviços de Aplicativos usam esse registro somente durante o tempo de configuração, para verificar se você possui seu próprio domínio personalizado. Você poderá excluir esse registro TXT depois que o domínio personalizado for validado e configurado no Serviço de Aplicativo.

> [!NOTE]
> Se você deseja verificar o nome de domínio, mas não direcionar o tráfego de produção para o aplicativo Web, basta especificar o registro TXT para a etapa de verificação.  A verificação não exige um registro CNAME ou A além do registro TXT.

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name "@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>Criar um registro CNAME

Se o domínio já é gerenciado pelo DNS do Azure (confira [Delegação de domínio ao DNS](dns-domain-delegation.md)), você pode usar o exemplo a seguir para criar um registro CNAME para contoso.azurewebsites.net.

Abra o Azure PowerShell e crie um novo registro CNAME. Este exemplo cria um tipo de conjunto de registros CNAME com uma "vida útil" de 600 segundos na zona DNS denominada "contoso.com" com o alias para o aplicativo Web contoso.azurewebsites.net.

### <a name="create-the-record"></a>Criar o registro

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -cname "contoso.azurewebsites.net")
```

O exemplo a seguir é a resposta:

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

## <a name="test-the-new-records"></a>Testar os novos registros

Você pode validar se os registros foram criados corretamente consultando “www.contoso.com” e “contoso.com” usando o nslookup, conforme mostrado abaixo:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>Adicionar nomes de host personalizados

Agora você pode adicionar os nomes de host personalizados ao seu aplicativo Web:

```azurepowershell
set-AzWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>Testar os nomes de host personalizados

Abra um navegador e navegue até `http://www.<your domainname>` e `http://<you domain name>`.

> [!NOTE]
> Inclua o prefixo `http://`, caso contrário, seu navegador poderá tentar prever uma URL para você.

Você deve ver a mesma página para ambas as URLs. Por exemplo:

![Serviço de aplicativo da Contoso](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos criados neste tutorial, você poderá excluir o grupo de recursos **myresourcegroup**.

## <a name="next-steps"></a>Próximas etapas

Saiba como criar zonas DNS no DNS do Azure.

> [!div class="nextstepaction"]
> [Introdução às zonas privadas do DNS do Azure usando o PowerShell](private-dns-getstarted-powershell.md)