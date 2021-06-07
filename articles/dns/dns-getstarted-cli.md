---
title: 'Início Rápido: Criar uma zona DNS do Azure e o registro – CLI do Azure'
titleSuffix: Azure DNS
description: Início Rápido - Saiba como criar uma zona e registro DNS no DNS do Azure. Este é uma guia passo a passo para criar e gerenciar sua primeira zona e registro DNS usando a CLI do Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1929cd512d18d7fd234aff1f55814c423455e63b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94561345"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Início Rápido: Criar uma zona DNS do Azure e o registro usando a CLI do Azure

Este artigo explica as etapas de criação de sua primeira zona e registro DNS usando a CLI do Azure, que está disponível para Windows, Mac e Linux. Você também pode executar essas etapas usando o [portal do Azure](dns-getstarted-portal.md) ou o [Azure PowerShell](dns-getstarted-powershell.md).

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem do seu domínio no DNS do Azure, você precisará criar uma zona DNS para esse nome de domínio. Cada registro DNS para seu domínio é criado dentro dessa zona DNS. Por fim, para publicar sua zona DNS na Internet, você precisa configurar os servidores de nome para o domínio. Cada uma dessas etapas é descrita abaixo.

O DNS do Azure também dá suporte a zonas DNS privadas. Para saber mais sobre as zonas DNS privadas, consulte [Usar o Azure DNS para domínios privados](private-dns-overview.md). Para obter um exemplo de como criar uma zona DNS privado, confira [Introdução às Zonas Privadas do DNS do Azure usando a CLI](./private-dns-getstarted-cli.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.4 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

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

   ![A captura de tela mostra uma janela do prompt de comando com um comando de pesquisa de NS e valores para Servidor, Endereço, Nome e Endereço.](media/dns-getstarted-portal/nslookup.PNG)

O nome do host **www\.contoso.xyz** resolve para **10.10.10.10**, conforme você o configurou. Esse resultado verifica se a resolução do nome está funcionando corretamente.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, você poderá excluir todos os recursos criados neste início rápido ao excluir o grupo de recursos:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou sua primeira zona e registro DNS usando a CLI do Azure, pode criar registros para um aplicativo Web em um domínio personalizado.

> [!div class="nextstepaction"]
> [Criar registros DNS para um aplicativo Web em um domínio personalizado](./dns-web-sites-custom-domain.md)
