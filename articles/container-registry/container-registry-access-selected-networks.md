---
title: Configurar o acesso ao Registro público
description: Configure as regras de IP para habilitar o acesso a um registro de contêiner do Azure de endereços IP públicos selecionados ou intervalos de endereços.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 4e4ee817e2534bcca09cba89daafd379ff3f03f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89488755"
---
# <a name="configure-public-ip-network-rules"></a>Configurar regras de rede IP pública

Por padrão, um registro de contêiner do Azure aceita conexões pela Internet de hosts em qualquer rede. Este artigo mostra como configurar seu registro de contêiner para permitir o acesso somente de endereços IP públicos ou intervalos de endereços específicos. São fornecidas etapas equivalentes usando a CLI do Azure e o portal do Azure.

As regras de rede IP são configuradas no ponto de extremidade do registro público. As regras de rede IP não se aplicam a pontos de extremidade privados configurados com [Link Privado](container-registry-private-link.md)

A configuração das regras de acesso IP está disponível na camada de serviço **Premium** do registro de contêiner. Para obter informações sobre os limites e as camadas de serviço do registro, confira [Camadas do Registro de Contêiner do Azure](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="access-from-selected-public-network---cli"></a>Acesso da rede pública selecionada – CLI

### <a name="change-default-network-access-to-registry"></a>Alterar o acesso de rede padrão para o registro

Para limitar o acesso a uma rede pública selecionada, primeiro altere a ação padrão para negar acesso. Substitua o nome do registro pelo seguinte comando [az acr update][az-acr-update]:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registro

Use o comando [az acr network-rule add][az-acr-network-rule-add] para adicionar uma regra de rede ao registro que permite o acesso de um intervalo ou endereço IP público. Por exemplo, substitua o nome do registro de contêiner e o endereço IP público de uma VM em uma rede virtual.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Após a adição de uma regra, levará alguns minutos para que ela entre em vigor.

## <a name="access-from-selected-public-network---portal"></a>Acesso da rede pública selecionada – portal

1. No portal, navegue até o registro de contêiner.
1. Em **Configurações**, selecione **Rede**.
1. Na guia **Acesso público**, selecione a opção para permitir o acesso público de **Redes selecionadas**.
1. Em **Firewall**, insira um endereço IP público, como o endereço IP público de uma VM em uma rede virtual. Ou insira um intervalo de endereços na notação CIDR que contém o endereço IP da VM.
1. Clique em **Salvar**.

![Configurar regra de firewall para registro de contêiner][acr-access-selected-networks]

> [!NOTE]
> Após a adição de uma regra, levará alguns minutos para que ela entre em vigor.

> [!TIP]
> Opcionalmente, habilite o acesso ao registro de um computador cliente local ou intervalo de endereços IP. Para permitir esse acesso, você precisa do endereço IPv4 público do computador. Você pode encontrar esse endereço pesquisando "qual é meu endereço IP" em um navegador da Internet. O endereço IPv4 do cliente atual também aparece automaticamente quando você define as configurações de firewall na página **Rede** no portal.

## <a name="disable-public-network-access"></a>Desabilitar o acesso de redes públicas

Opcionalmente, desabilite o ponto de extremidade público no registro. Desabilitar o ponto de extremidade público substitui todas as configurações do firewall. Por exemplo, talvez você queira desabilitar o acesso público a um registro protegido em uma rede virtual usando [Link Privado](container-registry-private-link.md).

> [!NOTE]
> Se o registro estiver configurado em uma rede virtual com um [ponto de extremidade de serviço](container-registry-vnet.md), desabilitar o acesso ao ponto de extremidade público do registro também desabilita o acesso ao registro na rede virtual.

### <a name="disable-public-access---cli"></a>Desabilitar o acesso público – CLI

Para desabilitar o acesso público usando ao CLI do Azure, execute [az acr update][az-acr-update] e defina `--public-network-enabled` como `false`. O argumento `public-network-enabled` requer a CLI do Azure 2.6.0 ou posterior. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>Desabilitar o acesso público – portal

1. No portal, navegue até o registro de contêiner e selecione **Configurações > Rede**.
1. Na guia **Acesso público**, em **Permitir acesso da rede pública**, selecione **Desabilitado**. Em seguida, selecione **Salvar**.

![Desabilitar o acesso público][acr-access-disabled]


## <a name="restore-public-network-access"></a>Restaurar o acesso de rede pública

Para reabilitar o ponto de extremidade público, atualize as configurações de rede para permitir o acesso público. A habilitação do ponto de extremidade público substitui todas as configurações do firewall. 

### <a name="restore-public-access---cli"></a>Restaurar o acesso público – CLI

Execute [az acr update][az-acr-update] e defina `--public-network-enabled` como `true`. 

> [!NOTE]
> O argumento `public-network-enabled` requer a CLI do Azure 2.6.0 ou posterior. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>Restaurar o acesso público – portal

1. No portal, navegue até o registro de contêiner e selecione **Configurações > Rede**.
1. Na guia **Acesso público**, em **Permitir acesso da rede pública**, selecione **Todas as redes**. Em seguida, selecione **Salvar**.

![Acesso público de todas as redes][acr-access-all-networks]

## <a name="troubleshoot"></a>Solucionar problemas

Se uma regra de rede pública estiver definida ou o acesso público ao registro for negado, as tentativas de logon no registro de uma rede pública não permitida falharão. O acesso de cliente por trás de um proxy HTTPS também falhará se uma regra de acesso para o proxy não estiver definida. Você verá uma mensagem de erro semelhante a `Error response from daemon: login attempt failed with status: 403 Forbidden` ou `Looks like you don't have access to registry` .

Esses erros também podem ocorrer se você usar um proxy HTTPS permitido por uma regra de acesso à rede, mas o proxy não estiver configurado corretamente no ambiente do cliente. Verifique se o cliente do Docker e o daemon do Docker estão configurados para o comportamento do proxy. Para obter detalhes, consulte [proxy http/https](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) na documentação do Docker.


## <a name="next-steps"></a>Próximas etapas

* Para restringir o acesso a um registro usando um ponto de extremidade privado em uma rede virtual, confira [Configurar o Link Privado do Azure para um registro de contêiner do Azure](container-registry-private-link.md).
* Se você precisar configurar regras de acesso do registro por trás de um firewall do cliente, confira [Configurar regras para acessar um registro de contêiner do Azure atrás de um firewall](container-registry-firewall-access-rules.md).

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
