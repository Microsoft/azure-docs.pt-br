---
title: Instalar o MongoDB em uma VM Linux com a CLI do Azure | Microsoft Docs
description: Aprenda a instalar e configurar o MongoDB em uma máquina virtual Linux usando a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: d75b00acac75b3b9680f862952e5c224336ca57a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671370"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Como instalar e configurar o MongoDB em uma VM Linux

[O MongoDB](https://www.mongodb.org) é um popular banco de dados NoSQL de código-fonte aberto e de alto desempenho. Este artigo mostra como instalar e configurar o MongoDB em uma VM Linux com a CLI do Azure. São mostrados exemplos que explicam em detalhes como:

* [Instalar e configurar uma instância básica do MongoDB manualmente](#manually-install-and-configure-mongodb-on-a-vm)
* [Criar uma instância básica do MongoDB usando um Modelo do Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Criar um cluster fragmentado complexo MongoDB com conjuntos de réplicas usando um modelo do Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Instalar e configurar manualmente o MongoDB em uma VM
O MongoDB [fornece instruções de instalação](https://docs.mongodb.com/manual/administration/install-on-linux/) para distribuições de Linux incluindo Red Hat/CentOS, SUSE, Ubuntu e Debian. O exemplo a seguir cria uma VM chamada *CentOS*. Para criar esse ambiente, você precisa da última [Azure CLI](/cli/azure/install-az-cli2) instalado e conectado para uma conta Azure usando de [login az](/cli/azure/reference-index).

Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo a seguir cria uma VM nomeada *myVM* com um usuário nomeado *azureuser* utilizando autenticação de chave pública SSH

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para a VM utilizando seu próprio nome de usuário e `publicIpAddress` listado na saída da etapa anterior:

```bash
ssh azureuser@<publicIpAddress>
```

Para adicionar fontes de instalação para o MongoDB, crie um arquivo de repositório **yum** da seguinte maneira:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Abra o arquivo de repositório do MongoDB para edição, com `vi` ou `nano`. Adicione as linhas a seguir:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Instale o MongoDB usando o **yum** da seguinte maneira:

```bash
sudo yum install -y mongodb-org
```

Por padrão, SELinux é imposto nas imagens do CentOS que impedem que você acesse o MongoDB. Instale as ferramentas de gerenciamento de política e configure o SELinux para permitir que o MongoDB opere na porta TCP padrão 27017, conforme descrito a seguir:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Inicie o serviço MongoDB da seguinte maneira:

```bash
sudo service mongod start
```

Verifique a instalação do MongoDB ao se conectar usando o cliente `mongo` local:

```bash
mongo
```

Agora teste a instância do MongoDB adicionando alguns dados e, em seguida, pesquisando por:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Se desejar, configure o MongoDB para iniciar automaticamente durante uma reinicialização do sistema:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Criar uma instância básica do MongoDB em CentOS usando um modelo
Você pode criar uma instância básica do MongoDB em uma única VM CentOS usando o modelo de início rápido do Azure a seguir no GitHub. Este modelo usa a extensão de Script Personalizado para Linux para adicionar um repositório **yum** à sua VM CentOS recém-criada e então instalar o MongoDB.

* [Instância básica do MongoDB no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Para criar esse ambiente, você precisa da última [Azure CLI](/cli/azure/install-az-cli2) instalado e conectado para uma conta Azure usando de [login az](/cli/azure/reference-index). Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implante o modelo do MongoDB com [az group deployment create](/cli/azure/group/deployment). Mediante solicitação, insira seus próprios valores exclusivos para *newStorageAccountName*, *dnsNameForPublicIP* e nome de usuário e senha de administrador:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Faça logon na VM usando o endereço DNS público de sua VM. Você pode exibir o endereço DNS público com [az vm show](/cli/azure/vm):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH para sua VM usando seu próprio nome de usuário e endereço DNS público:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Verifique a instalação do MongoDB ao se conectar usando o cliente `mongo` local conforme demonstrado a seguir:

```bash
mongo
```

Agora teste a instância adicionando alguns dados e, em seguida, pesquisando conforme demonstrado a seguir:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Criar um Cluster Fragmentado MongoDB complexo no CentOS usando um modelo
Você pode criar um cluster fragmentado complexo MongoDB usando o modelo de início rápido do Azure a seguir no GitHub. Esse modelo segue as [melhores práticas do cluster fragmentado MongoDB](https://docs.mongodb.com/manual/core/sharded-cluster-components/) para fornecer alta disponibilidade e redundância. O modelo cria dois fragmentos, com três nós em cada conjunto de réplicas. Um conjunto de réplicas de servidor de configuração com três nós também é criado, mais dois servidores do roteador **mongos** para fornecer consistência a aplicativos entre os fragmentos.

* [Cluster de fragmentação do MongoDB no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Implantar este cluster fragmentado MongoDB complexo requer mais de 20 núcleos, que é normalmente a contagem padrão de núcleos por região para uma assinatura. Abra uma solicitação de suporte do Azure para aumentar a contagem de núcleos.

Para criar esse ambiente, você precisa da última [Azure CLI](/cli/azure/install-az-cli2) instalado e conectado para uma conta Azure usando de [login az](/cli/azure/reference-index). Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implante o modelo do MongoDB com [az group deployment create](/cli/azure/group/deployment). Defina seus próprios nomes e tamanhos de recurso quando necessário, como para *mongoAdminUsername*, *sizeOfDataDiskInGB* e *configNodeVmSize*:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

Essa implantação pode demorar mais de uma hora para implantar e configurar todas as instâncias da VM. O sinalizador `--no-wait` é usado no final do comando anterior para retornar o controle ao prompt de comando após a implantação de modelo ter sido aceita pela plataforma do Azure. Você pode exibir o status de implantação com [az group deployment show](/cli/azure/group/deployment). O exemplo a seguir exibe o status da implantação do *myMongoDBCluster* no grupo de recursos *myResourceGroup*:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Próximas etapas
Nesses exemplos, você se conecta à instância do MongoDB localmente da VM. Se você desejar conectar-se à instância do MongoDB de outra VM ou de rede, certifique-se de que as devidas [regras de Grupo de Segurança de Rede tenham sido criadas](nsg-quickstart.md).

Esses exemplos implantam o ambiente do MongoDB principal para fins de desenvolvimento. Aplique as opções de configuração de segurança necessárias para o seu ambiente. Para obter mais informações, consulte os [documentos de segurança do MongoDB](https://docs.mongodb.com/manual/security/).

Para obter mais informações sobre a criação de modelos, veja a [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Os modelos do Azure Resource Manager usam a Extensão de Script Personalizado para baixar e executar scripts em suas VMs. Para obter mais informações, veja [Uso da extensão de script personalizado do Azure com máquinas virtuais do Linux](extensions-customscript.md).

