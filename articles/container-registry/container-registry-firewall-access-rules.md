---
title: Regras de acesso de firewall
description: Configure regras para acessar um registro de contêiner do Azure por trás de um firewall, permitindo o acesso a API REST e nomes de domínio de ponto de extremidade de dados ou intervalos de endereços IP específicos do serviço.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 548d64632c1d726111770dfb49f705d31f5ca714
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935981"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Configurar regras para acessar um registro de contêiner do Azure atrás de um firewall

Este artigo explica como configurar regras no firewall para permitir o acesso a um registro de contêiner do Azure. Por exemplo, um dispositivo Azure IoT Edge por trás de um firewall ou servidor proxy pode precisar acessar um registro de contêiner para efetuar pull de uma imagem de contêiner. Ou um servidor bloqueado em uma rede local pode precisar de acesso para enviar por push uma imagem.

Se, em vez disso, você quiser configurar o acesso à rede de entrada para um registro de contêiner somente dentro de uma rede virtual do Azure, consulte [Configurar o Link Privado do Azure para um registro de contêiner do Azure](container-registry-private-link.md).

## <a name="about-registry-endpoints"></a>Sobre os pontos de extremidade do registro

Para efetuar pull ou enviar imagens ou outros artefatos por push para um registro de contêiner do Azure, um cliente como um daemon do Docker precisa interagir por HTTPS com dois pontos de extremidade distintos. Para clientes que acessam um registro por trás de um firewall, é preciso configurar regras de acesso para ambos os pontos de extremidade. Ambos os pontos de extremidade são alcançados pela porta 443.

* **Ponto de extremidade da API REST do registro**: as operações de gerenciamento de autenticação e registro são manipuladas por meio do ponto de extremidade da API REST pública do registro. Esse ponto de extremidade é o nome do servidor de logon do registro. Exemplo: `myregistry.azurecr.io`

* **Ponto de extremidade de armazenamento (dados)** : o Azure [aloca armazenamento de blobs](container-registry-storage.md) em contas de Armazenamento do Microsoft Azure em nome de cada registro para gerenciar os dados de imagens de contêiner e outros artefatos. Quando um cliente acessa camadas de imagem em um registro de contêiner do Azure, ele faz solicitações usando um ponto de extremidade de conta de armazenamento fornecido pelo registro.

Se o registro estiver [replicado geograficamente](container-registry-geo-replication.md), um cliente poderá precisar interagir com o ponto de extremidade de dados em uma região específica ou em várias regiões replicadas.

## <a name="allow-access-to-rest-and-data-endpoints"></a>Permitir acesso a pontos de extremidade de dados e REST

* **Ponto de extremidade REST**: permitir acesso ao nome do servidor de logon totalmente qualificado `<registry-name>.azurecr.io`ou um intervalo de endereços IP associado
* **Ponto de extremidade de armazenamento (dados)** : permitir acesso a todas as contas de armazenamento de blobs do Azure usando o caractere curinga `*.blob.core.windows.net`ou um intervalo de endereços IP associado.
> [!NOTE]
> O Registro de Contêiner do Azure está apresentando [pontos de extremidade de dados dedicados](#enable-dedicated-data-endpoints), permitindo que você atenda rigorosamente às regras de firewall do cliente para o armazenamento do registro. Como alternativa, habilite os pontos de extremidade de dados em todas as regiões em que o registro está localizado ou replicado, usando o formulário `<registry-name>.<region>.data.azurecr.io`.

## <a name="allow-access-by-ip-address-range"></a>Permitir o acesso por intervalo de endereços IP

Se a sua organização tiver políticas para permitir o acesso somente a endereços IP ou intervalos de endereços específicos, baixe os [intervalos de IP do Azure e marcas de serviço – nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519).

Para encontrar os intervalos IP do ponto de extremidade REST do Azure Container Registry para o qual você precisa permitir o acesso, procure **AzureContainerRegistry** no arquivo JSON.

> [!IMPORTANT]
> Os intervalos de endereços IP para os serviços do Azure podem ser alterados, e as atualizações são publicadas semanalmente. Baixe o arquivo JSON regularmente e faça as atualizações necessárias em suas regras de acesso. Se o seu cenário envolve a configuração de regras de grupo de segurança de rede em uma rede virtual do Azure ou se você usa o Firewall do Azure, use a [marca de serviço](#allow-access-by-service-tag) **AzureContainerRegistry**.
>

### <a name="rest-ip-addresses-for-all-regions"></a>Endereços IP REST para todas as regiões

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>Endereços IP REST para uma região específica

Procure a região específica, como **AzureContainerRegistry. AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Endereços IP de armazenamento para todas as regiões

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Endereços IP de armazenamento para uma região específica

Procure a região específica, como **Storage.AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Permitir acesso por marca de serviço

Em uma rede virtual do Azure, use as regras de segurança de rede para filtrar o tráfego de um recurso, como uma máquina virtual, para um registro de contêiner. Para simplificar a criação das regras de rede do Azure, use a [marca de serviço](../virtual-network/network-security-groups-overview.md#service-tags) **AzureContainerRegistry**. Uma marca de serviço representa um grupo de prefixos de endereço IP para acessar um serviço do Azure globalmente ou por região do Azure. A marca é atualizada automaticamente quando os endereços são alterados. 

Por exemplo, crie uma regra de grupo de segurança de rede de saída com destino **AzureContainerRegistry** para permitir o tráfego para um registro de contêiner do Azure. Para permitir o acesso à marca de serviço somente em uma região específica, especifique a região no seguinte formato: **AzureContainerRegistry**.[*nome da região*].

## <a name="enable-dedicated-data-endpoints"></a>Habilitar pontos de extremidade dos dados dedicados 

> [!WARNING]
> Se você configurou anteriormente o acesso de firewall do cliente aos pontos de extremidade atuais do `*.blob.core.windows.net`, alternar para pontos de extremidade de dados dedicados afetará a conectividade do cliente, causando falhas de pull. Para garantir que os clientes tenham acesso consistente, adicione as novas regras de ponto de extremidade de dados às regras de firewall do cliente. Depois de concluído, habilite os pontos de extremidade de dados dedicados para seus registros usando a CLI do Azure ou outras ferramentas.

Os pontos de extremidade de dados dedicados são um recurso opcional da camada de serviço do registro de contêiner **Premium**. Para obter informações sobre os limites e as camadas de serviço do Registro, confira [Camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md). 

É possível habilitar pontos de extremidade de dados dedicados usando o portal do Azure ou a CLI do Azure. Os pontos de extremidade de dados seguem um padrão regional, `<registry-name>.<region>.data.azurecr.io`. Em um registro replicado geograficamente, a habilitação dos pontos de extremidade de dados permite pontos de extremidade em todas as regiões de réplica.

### <a name="portal"></a>Portal

Para habilitar os pontos de extremidade de dados usando o portal:

1. Navegue até seu registro de contêiner.
1. Escolha **Rede** > **Acesso público**.
1. Marque a caixa de seleção **Habilitar ponto de extremidade de dados dedicados**.
1. Clique em **Salvar**.

O ponto de extremidade ou os pontos de extremidades de dados aparecem no Portal.

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="Pontos de extremidade de dados dedicados no portal":::

### <a name="azure-cli"></a>CLI do Azure

Para habilitar os pontos de extremidade de dados usando a CLI do Azure, use a versão 2.4.0 ou superior. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

O comando [az acr update][az-acr-update] a seguir permite pontos de extremidade de dados dedicados em um registro *myregistry*. 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Para exibir os pontos de extremidade de dados, use o comando [az acr show-endpoints][az-acr-show-endpoints]:

```azurecli
az acr show-endpoints --name myregistry
```

A saída para fins de demonstração mostra dois pontos de extremidade regionais

```
{
    "loginServer": "myregistry.azurecr.io",
    "dataEndpoints": [
        {
            "region": "eastus",
            "endpoint": "myregistry.eastus.data.azurecr.io",
        },
        {
            "region": "westus",
            "endpoint": "myregistry.westus.data.azurecr.io",
        }
    ]
}
```

Depois de configurar pontos de extremidade de dados dedicados para o registro, é possível habilitar as regras de acesso de firewall do cliente para os pontos de extremidade de dados. Habilite regras de acesso de ponto de extremidade de dados para todas as regiões de registro necessárias.

## <a name="configure-client-firewall-rules-for-mcr"></a>Configurar regras de firewall do cliente para MCR

Se precisar acessar o MCR (Registro de Contêiner do Azure) por trás de um firewall, consulte as diretrizes para configurar [regras de firewall de cliente do MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). O MCR é o registro principal para todas as imagens do Docker publicadas pela Microsoft, como imagens do Windows Server.

## <a name="next-steps"></a>Próximas etapas

* Conheça as [melhores práticas do Azure de segurança de rede](../security/fundamentals/network-best-practices.md)

* Saiba mais sobre [grupos de segurança](../virtual-network/network-security-groups-overview.md) em uma rede virtual do Azure

* Saiba mais sobre como configurar um [Link Privado](container-registry-private-link.md) para um registro de contêiner

* Saiba mais sobre [pontos de extremidade de dados dedicados](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) para o Registro de Contêiner do Azure



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints