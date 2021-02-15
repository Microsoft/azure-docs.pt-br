---
title: Conectar-se ao SQL Instância Gerenciada habilitado para Arc do Azure
description: Conectar-se ao SQL Instância Gerenciada habilitado para Arc do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: abd27e15ccf5b421e69e78b2b726d192ffdecacb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372354"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>Conectar-se ao SQL Instância Gerenciada habilitado para Arc do Azure

Este artigo explica como você pode se conectar à sua Instância Gerenciada SQL habilitada para o Arc do Azure. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-azure-arc-enabled-sql-managed-instances"></a>Exibir instâncias gerenciadas SQL do Azure Arc habilitadas

Para exibir o SQL Instância Gerenciada habilitado para o arco do Azure e os pontos de extremidade externos, use o seguinte comando:

```console
azdata arc sql mi list
```

A saída deve ser semelhante a esta:

```console
Name    Replicas    ExternalEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Se você estiver usando AKS ou kubeadm ou OpenShift, etc., poderá copiar o IP externo e o número da porta aqui e conectar-se a ele usando sua ferramenta favorita para se conectar a uma instância do SQL Server/SQL do Azure, como Azure Data Studio ou SQL Server Management Studio.  No entanto, se você estiver usando a VM de início rápido, consulte abaixo informações especiais sobre como se conectar a essa VM de fora do Azure. 

> [!NOTE]
> Suas políticas corporativas podem bloquear o acesso ao IP e à porta, especialmente se isso for criado na nuvem pública.

## <a name="connect"></a>Conectar 

Conectar-se com Azure Data Studio, SQL Server Management Studio ou SQLCMD

Abra Azure Data Studio e conecte-se à sua instância com o endereço IP do ponto de extremidade externo e o número da porta acima. Se você estiver usando uma VM do Azure, precisará do endereço IP _público_ , que é identificável usando a [Observação especial sobre implantações de máquina virtual do Azure](#special-note-about-azure-virtual-machine-deployments).

Por exemplo:

- Servidor: 52.229.9.30, 30913
- Nome de usuário: SA
- Senha: sua senha SQL especificada no momento do provisionamento

> [!NOTE]
> Você pode usar Azure Data Studio [exibir os painéis de instância gerenciada do SQL](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards).

Para se conectar usando o SQLCMD ou o Linux ou o Windows, você pode usar um comando como este. Digite a senha do SQL quando solicitado:

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Observação especial sobre implantações de máquinas virtuais do Azure

Se você estiver usando uma máquina virtual do Azure, o endereço IP do ponto de extremidade não mostrará o endereço IP público. Para localizar o endereço IP externo, use o seguinte comando:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Em seguida, você pode combinar o endereço IP público com a porta para estabelecer a conexão.

Talvez você também precise expor a porta da instância do SQL por meio do NSG (gateway de segurança de rede). Para permitir o tráfego por meio do (NSG), você precisará adicionar uma regra que você pode fazer usando o comando a seguir.

Para definir uma regra, você precisará saber o nome do seu NSG, que pode ser encontrado usando o comando a seguir:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Quando tiver o nome do NSG, você poderá adicionar uma regra de firewall usando o comando a seguir. Os valores de exemplo aqui criam uma regra NSG para a porta 30913 e permitem a conexão de **qualquer** endereço IP de origem.  Essa não é uma prática recomendada de segurança!  Você pode obter um bloqueio melhor definindo um valor -source-address-prefixes específico para o endereço IP do cliente ou um intervalo de endereços IP que cubra os endereços IP da equipe ou organização.

Substitua o valor do `--destination-port-ranges` parâmetro abaixo pelo número da porta que você obteve do `azdata sql instance list` comando F acima.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>Próximas etapas

- [Exibir os painéis da instância gerenciada do SQL](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [Exibir Instância Gerenciada SQL no portal do Azure](view-arc-data-services-inventory-in-azure-portal.md)
