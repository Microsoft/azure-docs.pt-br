---
title: Conectar-se ao serviço kubernetes do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba mais sobre como conectar o AKS (serviço kubernetes do Azure) com o banco de dados do Azure para PostgreSQL-servidor único
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.date: 07/14/2020
ms.topic: conceptual
ms.openlocfilehash: 9b7da2fcc1310f03f894e048089658f25be3a149
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91708842"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Conectando o serviço kubernetes do Azure e o banco de dados do Azure para PostgreSQL-servidor único

O Serviço do Kubernetes do Azure (AKS) fornece um cluster Kubernetes gerenciado que você pode usar no Azure. Abaixo estão algumas opções a considerar ao usar o AKS e o Banco de Dados do Azure para PostgreSQL juntos para criar um aplicativo.


## <a name="accelerated-networking"></a>Redes aceleradas
Use VMs subjacentes habilitadas para rede acelerada em seu cluster AKS. Quando a rede acelerada está habilitada em uma VM, há menor latência, menor oscilação e menor utilização da CPU na VM. Saiba mais sobre como funciona a rede acelerada, as versões de sistema operacional compatíveis e as instâncias de VM compatíveis para [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

A partir de novembro de 2018, o AKS oferece suporte à rede acelerada nessas instâncias de VM compatíveis. A rede acelerada é ativada por padrão nos novos clusters do AKS que usam essas VMs.

Você pode confirmar se o seu cluster AKS acelerou a rede:
1. Vá para o portal do Azure e selecione seu cluster AKS.
2. Selecione a guia Propriedades.
3. Copie o nome do **Grupo de Recursos de Infraestrutura**.
4. Use a barra de pesquisa do portal para localizar e abrir o grupo de recursos de infraestrutura.
5. Selecione uma VM nesse grupo de recursos.
6. Vá para a guia VM **Rede**.
7. Confirme se a **Rede acelerada** está "Ativada".

Ou por meio da CLI do Azure usando os dois comandos a seguir:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
A saída será o grupo de recursos gerado que o AKS cria contendo o adaptador de rede. Pegue o nome "nodeResourceGroup" e use-o no próximo comando. **EnableAcceleratedNetworking** será true ou false:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```


## <a name="connection-pooling"></a>Pool de conexões
O pooler de conexão minimiza o custo e o tempo associado criando e fechando novas conexões com o banco de dados. O pool é uma coleção de conexões que pode ser reutilizada. 

Há vários poolers de conexão, que você pode usar com PostgreSQL. Um deles é [PgBouncer](https://pgbouncer.github.io/). O Registro de Contêiner do Azure, nós fornecemos um PgBouncer em contêiner leve que pode ser usado em um sidecar para conexões de pool de AKS para o Banco de Dados do Azure para PostgreSQL. Visite a [página de hub do docker](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) para saber como acessar e usar essa imagem. 


## <a name="next-steps"></a>Próximas etapas
-  [Criar um cluster do Serviço de Kubernetes do Azure](../aks/kubernetes-walkthrough.md)
