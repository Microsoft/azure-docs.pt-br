---
title: "Introdução ao DNS do Azure usando a CLI do Azure 2.0 | Microsoft Docs"
description: "Saiba como criar uma zona e registro DNS no DNS do Azure. Este é uma guia passo a passo para criar e gerenciar sua primeira zona e registro DNS usando a CLI do Azure 2.0."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
ms.openlocfilehash: 6958d61b29961f59cb22f62bec55f2d467e7e7cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-dns-using-azure-cli-20"></a>Introdução ao DNS do Azure usando a CLI do Azure 2.0

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [CLI 1.0 do Azure](dns-getstarted-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-getstarted-cli.md)

Este artigo explica as etapas de criação de sua primeira zona e registro DNS usando a CLI do Azure 2.0 entre plataformas, que está disponível para Windows, Mac e Linux. Você também pode executar essas etapas usando o Portal do Azure ou o Azure PowerShell.

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem do seu domínio no DNS do Azure, você precisará criar uma zona DNS para esse nome de domínio. Cada registro DNS para seu domínio é criado dentro dessa zona DNS. Por fim, para publicar sua zona DNS na Internet, você precisa configurar os servidores de nome para o domínio. Cada uma dessas etapas é descrita abaixo.

Essas instruções pressupõem que você já instalou e entrou na CLI do Azure 2.0. Para obter ajuda, confira [Como gerenciar as zonas DNS usando a CLI do Azure 2.0](dns-operations-dnszones-cli.md).

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar a zona DNS, um grupo de recursos é criado para conter a zona DNS. O código a seguir mostra o comando.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada usando o comando `az network dns zone create` . Para ver a ajuda desse comando, digite `az network dns zone create -h`.

O exemplo a seguir cria uma zona DNS chamada *contoso.com* no grupo de recursos *MyResourceGroup*. Use o exemplo para criar uma zona DNS, substituindo os valores pelos seus próprios.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```


## <a name="create-a-dns-record"></a>Criar um registro DNS

Para criar um registro DNS, use o comando `az network dns record-set [record type] add-record`. Para obter ajuda, por exemplo, com os registros A, veja `azure network dns record-set A add-record -h`.

O exemplo a seguir cria um registro com o nome relativo "www" na Zona DNS "contoso.com", no grupo de recursos "MyResourceGroup". O nome totalmente qualificado do conjunto de registros é “www.contoso.com”. O tipo de registro é "A", com o endereço IP "1.2.3.4", e um TTL padrão de 3600 segundos (1 hora) é usado.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

Para outros tipos de registros, para conjuntos de registros com mais de um registro, para valores de TTL alternativos e para modificar registros existentes, veja [Gerenciar registros DNS e conjuntos de registros usando a CLI do Azure 2.0](dns-operations-recordsets-cli.md).


## <a name="view-records"></a>Exibir registros

Para listar os registros DNS em sua zona, use:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```


## <a name="update-name-servers"></a>Atualizar servidores de nome

Quando você estiver satisfeito com a configuração de sua zona e registros DNS, configure seu nome de domínio para usar os servidores de nome DNS do Azure. Isso permite que outros usuários na Internet encontrem os registros DNS.

Os servidores de nomes de sua zona são fornecidos pelo comando `az network dns zone show`. Para ver os nomes do servidor de nome, use a saída em JSON, conforme mostra o exemplo a seguir.

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Esses servidores de nome devem ser configurados com o registrador de nome de domínio (onde você adquiriu o nome de domínio). Seu registrador oferecerá a opção de configurar os servidores de nome do domínio. Para saber mais, confira [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Excluir todos os recursos
 
Para excluir todos os recursos criados neste artigo, execute as seguintes etapas:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o DNS do Azure, veja [Visão geral do DNS do Azure](dns-overview.md).

Para saber mais sobre como gerenciar as zonas DNS no DNS do Azure, consulte [Gerenciar zonas DNS no DNS do Azure usando a CLI do Azure 2.0](dns-operations-dnszones-cli.md).

Para saber mais sobre como gerenciar os registros DNS no DNS do Azure, veja [Gerenciar registros DNS e conjuntos de registros no DNS do Azure usando a CLI do Azure 2.0](dns-operations-recordsets-cli.md).
