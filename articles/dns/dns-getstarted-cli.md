---
title: 'Início Rápido: Criar uma zona DNS do Azure e o registro usando a CLI do Azure'
description: Início Rápido - Saiba como criar uma zona e registro DNS no DNS do Azure. Este é uma guia passo a passo para criar e gerenciar sua primeira zona e registro DNS usando a CLI do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 7a2c300e30050e7e46a2b2c724258539df85e410
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66111345"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Início Rápido: Criar uma zona DNS do Azure e o registro usando a CLI do Azure

Este artigo explica as etapas de criação de sua primeira zona e registro DNS usando a CLI do Azure, que está disponível para Windows, Mac e Linux. Você também pode executar essas etapas usando o [portal do Azure](dns-getstarted-portal.md) ou o [Azure PowerShell](dns-getstarted-powershell.md).

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem do seu domínio no DNS do Azure, você precisará criar uma zona DNS para esse nome de domínio. Cada registro DNS para seu domínio é criado dentro dessa zona DNS. Por fim, para publicar sua zona DNS na Internet, você precisa configurar os servidores de nome para o domínio. Cada uma dessas etapas é descrita abaixo.

O DNS do Azure agora também oferece suporte a zonas de DNS privado (atualmente em visualização pública). Para saber mais sobre as zonas DNS privadas, consulte [Usar o Azure DNS para domínios privados](private-dns-overview.md). Para obter um exemplo de como criar uma zona DNS privado, confira [Introdução às Zonas Privadas do DNS do Azure usando a CLI](./private-dns-getstarted-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar a zona DNS, crie um grupo de recursos para conter a zona DNS:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada usando o comando `az network dns zone create` . Para ver a ajuda desse comando, digite `az network dns zone create -h`.

O exemplo a seguir cria uma zona DNS chamada *contoso.xyz* no grupo de recursos *MyResourceGroup*. Use o exemplo para criar uma zona DNS, substituindo os valores pelos seus próprios.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>Criar um registro DNS

Para criar um registro DNS, use o comando `az network dns record-set [record type] add-record`. Para obter ajuda sobre registros A, veja `azure network dns record-set A add-record -h`.

O exemplo a seguir cria um registro com o nome relativo "www" na Zona DNS "contoso.xyz", no grupo de recursos "MyResourceGroup". O nome totalmente qualificado do conjunto de registros é "www.contoso.xyz". O tipo de registro é "A", com o endereço IP "10.10.10.10" e um TTL padrão de 3.600 segundos (1 hora).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>Exibir registros

Para listar os registros DNS em sua zona, execute:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>Testar a resolução de nome

Agora que você tem uma zona DNS de teste com um registro 'A' de teste, é possível testar a resolução de nome com uma ferramenta chamada *nslookup*. 

**Para testar a resolução de nomes DNS:**

1. Execute o seguinte cmdlet para obter a lista de servidores de nomes da sua zona:

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
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

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

O nome do host **www\.contoso.xyz** resolve para **10.10.10.10**, conforme você o configurou. Esse resultado verifica se a resolução do nome está funcionando corretamente.

## <a name="delete-all-resources"></a>Excluir todos os recursos

Quando não forem mais necessários, você poderá excluir todos os recursos criados neste início rápido ao excluir o grupo de recursos:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou sua primeira zona e registro DNS usando a CLI do Azure, pode criar registros para um aplicativo Web em um domínio personalizado.

> [!div class="nextstepaction"]
> [Criar registros DNS para um aplicativo Web em um domínio personalizado](./dns-web-sites-custom-domain.md)
