---
title: Gerenciar as zonas DNS no DNS do Azure - CLI do Azure | Microsoft Docs
description: Você pode gerenciar zonas DNS usando a CLI do Azure. Este artigo mostra como atualizar, excluir e criar zonas DNS no DNS do Azure.
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: victorh
ms.openlocfilehash: df741b34e1268c547723af87401760197d395780
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293822"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>Como gerenciar Zonas DNS no DNS do Azure usando a CLI do Azure

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [CLI do Azure](dns-operations-dnszones-cli.md)


Este guia mostra como gerenciar as zonas DNS usando a CLI do Azure entre plataformas, que está disponível para Windows, Mac e Linux. Você também pode gerenciar seus registros DNS usando o [Azure PowerShell](dns-operations-dnszones.md) ou o Portal do Azure.

Este guia trata especificamente das zonas DNS públicas. Para obter informações sobre como usar a CLI do Azure para gerenciar Zonas Privadas no DNS do Azure, consulte [Introdução às Zonas Privadas do DNS do Azure usando a CLI do Azure](private-dns-getstarted-cli.md).

## <a name="introduction"></a>Introdução

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-for-azure-dns"></a>Configurar a CLI do Azure para DNS do Azure

### <a name="before-you-begin"></a>Antes de começar

Antes de começar a configurar, verifique se você tem os itens a seguir.

* Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

* Instale a versão mais recente da CLI do Azure, disponível para Windows, Linux ou Mac. Mais informações estão disponíveis em [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Entre na sua conta do Azure

Abra uma janela do console e autentique com suas credenciais. Para obter mais informações, confira [Conectar-se ao Azure a partir da CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

```
az login
```

### <a name="select-the-subscription"></a>Selecionar a assinatura

Verificar as assinaturas da conta.

```
az account list
```

Escolha quais das suas assinaturas do Azure deseja usar.

```azurecli
az account set --subscription "subscription name"
```

### <a name="optional-to-installuse-azure-dns-private-zones-feature-public-preview"></a>Opcional: Para instalar/usar o recurso de Zonas Privadas do DNS do Azure (Visualização Pública)
O recurso de Zonas Privadas do DNS do Azure é liberado em Visualização Pública por meio de uma extensão da CLI do Azure. Instalar o “dns” da extensão da CLI do Azure 
```
az extension add --name dns
``` 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O Azure Resource Manager requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. No entanto, como todos os recursos de DNS são globais, não regionais, a escolha do local do grupo de recursos não afeta o DNS do Azure.

Você pode ignorar esta etapa se está usando um grupo de recursos existente.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Obtendo ajuda

Todos os comandos da CLI do Azure relacionados ao DNS do Azure começam com `az network dns`. A ajuda está disponível para cada comando usando a opção `--help` (forma abreviada `-h`).  Por exemplo: 

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada usando o comando `az network dns zone create` . Para obter ajuda, consulte `az network dns zone create -h`.

O exemplo a seguir cria uma zona DNS chamada *contoso.com* no grupo de recursos chamado *MyResourceGroup*:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Para criar uma zona DNS com marcas

O exemplo a seguir mostra como criar uma zona DNS com duas [marcas do Azure Resource Manager](dns-zones-records.md#tags), *project = demo* e *env = test*, usando o parâmetro `--tags` (forma abreviada `-t`):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>Obter uma zona DNS

Para recuperar uma zona DNS, use `az network dns zone show`. Para obter ajuda, consulte `az network dns zone show --help`.

O exemplo a seguir retorna a zona DNS *contoso.com* e seus dados associados do grupo de recursos *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

O exemplo a seguir é a resposta.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Observe que registros DNS não são retornados por `az network dns zone show`. Para listar registros DNS, use `az network dns record-set list`.


## <a name="list-dns-zones"></a>Listar as zonas DNS

Para enumerar zonas DNS, use `az network dns zone list`. Para obter ajuda, consulte `az network dns zone list --help`.

Especificar o grupo de recursos lista apenas as zonas dentro do grupo de recursos:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Omitir o grupo de recursos lista todas as zonas na assinatura:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Atualizar uma zona DNS

As alterações a um recurso da zona DNS podem ser feitas usando o `az network dns zone update`. Para obter ajuda, consulte `az network dns zone update --help`.

Esse comando não atualiza nenhum dos conjuntos de registros DNS dentro da zona (consulte [Como gerenciar registros DNS](dns-operations-recordsets-cli.md)). Ele só é usado para atualizar as propriedades do recurso da zona em si. Atualmente, essas propriedades são limitadas às [“marcas” do Azure Resource Manager](dns-zones-records.md#tags) para o recurso de zona.

O exemplo a seguir mostra como atualizar as marcas em uma zona DNS. As marcas existentes são substituídas pelo valor especificado.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>Excluir uma zona DNS

As zonas DNS podem ser excluídas por meio do `az network dns zone delete`. Para obter ajuda, consulte `az network dns zone delete --help`.

> [!NOTE]
> Excluir uma zona DNS também excluirá todos os registros DNS na zona. Essa operação não pode ser desfeita. Se a zona DNS estiver em uso, serviços que usam a zona falharão quando a zona for excluída.
>
>Para se proteger contra a exclusão acidental da zona, consulte [Proteger registros e zonas DNS](dns-protect-zones-recordsets.md).

Esse comando solicita uma confirmação. A opção `--yes` opcional suprime esse prompt.

O exemplo a seguir mostra como excluir a zona *contoso.com* de grupo de recursos *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Próximas etapas

Saiba como [gerenciar conjuntos de registros e registros](dns-getstarted-create-recordset-cli.md) em sua zona DNS.

Saiba como [delegar seu domínio ao DNS do Azure](dns-domain-delegation.md).

