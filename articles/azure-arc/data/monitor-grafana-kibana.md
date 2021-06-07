---
title: Exibir logs e métricas usando Kibana e Grafana
description: Exibir logs e métricas usando Kibana e Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/08/2020
ms.topic: how-to
ms.openlocfilehash: cb53aba300b933c78d9ac2f5fc5cf8054f3413e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669994"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Exibir logs e métricas usando Kibana e Grafana

Os painéis da Web Kibana e Grafana são fornecidos para trazer insight e clareza para os namespaces do Kubernetes que estão sendo usados pelos serviços de dados habilitados para o Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Monitorar instâncias gerenciadas do SQL do Azure no arco do Azure

Para acessar os painéis logs e monitoramento para o SQL Instância Gerenciada habilitado para Arc, execute o seguinte `azdata` comando da CLI

```bash

azdata arc sql endpoint list -n <name of SQL instance>

```
Os painéis Grafana relevantes são:

* "Métricas da instância gerenciada do SQL do Azure"
* "Métricas do nó de host"
* "Métricas de pods de host"


> [!NOTE]
>  Quando solicitado a inserir um nome de usuário e senha, insira o nome de usuário e a senha que você forneceu no momento em que você criou o controlador de dados de arco do Azure.

> [!NOTE]
>  Você será solicitado a fornecer um aviso de certificado porque os certificados usados na visualização são certificados autoassinados.


## <a name="monitor-azure-database-for-postgresql-hyperscale-on-azure-arc"></a>Monitorar o banco de dados do Azure para PostgreSQL hiperescala no arco do Azure

Para acessar os painéis logs e monitoramento de hiperescala PostgreSQL, execute o seguinte `azdata` comando da CLI

```bash

azdata arc postgres endpoint list -n <name of postgreSQL instance>

```

Os painéis do postgreSQL relevantes são:

* "Métricas de postgres"
* "Métricas de tabela postgres"
* "Métricas do nó de host"
* "Métricas de pods de host"


## <a name="additional-firewall-configuration"></a>Configuração de firewall adicional

Dependendo de onde o controlador de dados é implantado, talvez você ache que precisa abrir portas no firewall para acessar os pontos de extremidade Kibana e Grafana.

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


## <a name="next-steps"></a>Próximas etapas
- Tente [carregar as métricas e os logs para Azure monitor](upload-metrics-and-logs-to-azure-monitor.md)
- Leia sobre Grafana:
   - [Introdução](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Conceitos básicos do Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Tutoriais do Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Leia sobre Kibana
   - [Introdução](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Guia do Kibana](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Introdução ao Dashboard detalhamentos com visualizações de dados no Kibana](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Como criar painéis do Kibana](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

