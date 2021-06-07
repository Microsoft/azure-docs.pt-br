---
title: 'Início Rápido: Criar uma zona DNS do Azure e o registro – Azure PowerShell'
titleSuffix: Azure DNS
description: Saiba como criar uma zona e registro DNS no DNS do Azure. Este é um início rápido passo a passo para criar e gerenciar sua primeira zona e registro DNS usando o Azure PowerShell.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 10/20/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9c457b435ca17ce9dc86e1a83a4d1a56d9623b17
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537881"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Início Rápido: Criar uma zona DNS do Azure e o registro usando o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Neste início rápido, você pode criar sua primeira zona e registro DNS usando o Azure PowerShell. Você também pode executar essas etapas usando o [portal do Azure](dns-getstarted-portal.md) ou a [CLI do Azure](dns-getstarted-cli.md). 

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem do seu domínio no DNS do Azure, você precisará criar uma zona DNS para esse nome de domínio. Cada registro DNS para seu domínio é criado dentro dessa zona DNS. Por fim, para publicar sua zona DNS na Internet, você precisa configurar os servidores de nome para o domínio. Cada uma dessas etapas é descrita abaixo.

O DNS do Azure também oferece suporte à criação de domínios privados. Para obter instruções passo a passo sobre como criar sua primeira zona DNS privada e o registro, confira [Introdução às zonas privadas do Azure DNS usando o PowerShell](private-dns-getstarted-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell instalado localmente ou Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar a zona DNS, crie um grupo de recursos para conter a zona DNS:

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada usando o cmdlet `New-AzDnsZone` . O exemplo a seguir cria uma zona DNS chamada *contoso.xyz* no grupo de recursos chamado *MyResourceGroup*. Use o exemplo para criar uma zona DNS, substituindo os valores pelos seus próprios.

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Criar um registro DNS

Você cria conjuntos de registros usando o cmdlet `New-AzDnsRecordSet`. O exemplo a seguir cria um registro com o nome relativo "www" na Zona DNS "contoso.xyz", no grupo de recursos "MyResourceGroup". O nome totalmente qualificado do conjunto de registros é "www.contoso.xyz". O tipo de registro é "A", com o endereço IP "10.10.10.10" e a TTL de 3.600 segundos.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>Exibir registros

Para listar os registros DNS em sua zona, use:

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>Testar a resolução de nome

Agora que você tem uma zona DNS de teste com um registro 'A' de teste, é possível testar a resolução de nome com uma ferramenta chamada *nslookup*. 

**Para testar a resolução de nomes DNS:**

1. Execute o seguinte cmdlet para obter a lista de servidores de nomes da sua zona:

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
   ```

1. Copie um dos nomes de servidor de nomes da saída da etapa anterior.

1. Abra um prompt de comando e execute o seguinte:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Por exemplo:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Você deve ver algo semelhante à tela a seguir:

   ![A captura de tela mostra uma janela do prompt de comando com um comando de pesquisa de NS e valores para Servidor, Endereço, Nome e Endereço.](media/dns-getstarted-portal/nslookup.PNG)

O nome do host **www\.contoso.xyz** resolve para **10.10.10.10**, conforme você o configurou. Esse resultado verifica se a resolução do nome está funcionando corretamente.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, você poderá excluir todos os recursos criados neste início rápido ao excluir o grupo de recursos:

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou sua primeira zona e registro DNS usando o Azure PowerShell, pode criar registros para um aplicativo Web em um domínio personalizado.

> [!div class="nextstepaction"]
> [Criar registros DNS para um aplicativo Web em um domínio personalizado](./dns-web-sites-custom-domain.md)
