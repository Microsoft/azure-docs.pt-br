---
title: Exibir logs e métricas usando Kibana e Grafana
description: Exibir logs e métricas usando Kibana e Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3693c30a34601512770f5d9071f5d786410fb00e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360370"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Exibir logs e métricas usando Kibana e Grafana

Os painéis da Web Kibana e Grafana são fornecidos para trazer insight e clareza para os namespaces do Kubernetes que estão sendo usados pelos serviços de dados habilitados para o Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="retrieve-the-ip-address-of-your-cluster"></a>Recuperar o endereço IP do cluster

Para acessar os painéis, você precisará recuperar o endereço IP do cluster. O método para recuperar o endereço IP correto varia dependendo de como você optou por implantar o kubernetes. Percorra as opções abaixo para encontrar a correta para você.

### <a name="azure-virtual-machine"></a>Máquina virtual do Azure

Para recuperar o endereço IP público, use o comando a seguir:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

### <a name="kubeadm-cluster"></a>Cluster Kubeadm

Para recuperar o endereço IP do cluster, use o seguinte comando:

```console
kubectl cluster-info
```


### <a name="aks-or-other-load-balanced-cluster"></a>AKS ou outro cluster com balanceamento de carga

Para monitorar seu ambiente no AKS ou em outro cluster com balanceamento de carga, você precisa obter o endereço IP do serviço de proxy de gerenciamento. Use este comando para recuperar o endereço **IP externo** :

```console
kubectl get svc mgmtproxy-svc-external -n <namespace>

#Example:
#kubectl get svc mgmtproxy-svc-external -n arc
NAME                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)           AGE
mgmtproxy-svc-external   LoadBalancer   10.0.186.28   52.152.148.25   30777:30849/TCP   19h
```

## <a name="additional-firewall-configuration"></a>Configuração de firewall adicional

Talvez você ache que precisa abrir portas em seu firewall para acessar os pontos de extremidade Kibana e Grafana.

Veja abaixo um exemplo de como fazer isso para uma VM do Azure. Você precisará fazer isso se tiver implantado kubernetes usando o script.

As etapas a seguir destacam como criar uma regra NSG para os pontos de extremidade Kibana e Grafana:

### <a name="find-the-name-of-the-nsg"></a>Localize o nome do NSG

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>Adicionar a regra NSG

Quando tiver o nome do NSG, você poderá adicionar uma regra usando o seguinte comando:

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```

## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Monitorar instâncias gerenciadas do SQL do Azure no arco do Azure

Agora que você tem o endereço IP e expôs as portas, você deve ser capaz de acessar Grafana e Kibana.

> [!NOTE]
>  Quando solicitado a inserir um nome de usuário e senha, insira o nome de usuário e a senha que você forneceu no momento em que você criou o controlador de dados de arco do Azure.

> [!NOTE]
>  Você será solicitado a fornecer um aviso de certificado porque os certificados usados na visualização são certificados autoassinados.

Use o seguinte padrão de URL para acessar os painéis de registro em log e monitoramento da instância gerenciada do SQL do Azure:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

Os painéis relevantes são:

* "Métricas da instância gerenciada do SQL do Azure"
* "Métricas do nó de host"
* "Métricas de pods de host"

## <a name="monitor-azure-database-for-postgresql-hyperscale---azure-arc"></a>Monitorar o banco de dados do Azure para PostgreSQL hiperescala-arco do Azure

Use o seguinte padrão de URL para acessar os painéis de registro em log e monitoramento de hiperescala PostgreSQL:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

Os painéis relevantes são:

* "Métricas de postgres"
* "Métricas de tabela postgres"
* "Métricas do nó de host"
* "Métricas de pods de host"

## <a name="next-steps"></a>Próximas etapas
- Tente [carregar as métricas e os logs para Azure monitor](upload-metrics-and-logs-to-azure-monitor.md)
- Leia sobre Grafana:
   - [Guia de Introdução](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Conceitos básicos do Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Tutoriais do Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Leia sobre Kibana
   - [Introdução](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Guia do Kibana](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Introdução ao Dashboard detalhamentos com visualizações de dados no Kibana](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Como criar painéis do Kibana](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

