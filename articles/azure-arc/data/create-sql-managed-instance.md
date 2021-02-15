---
title: Criar uma instância gerenciada do SQL do Azure no arco do Azure
description: Criar uma instância gerenciada do SQL do Azure no arco do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0d1d6c7349adc65270e13d09d771963cb9cacea3
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280402"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Criar uma instância gerenciada do SQL do Azure no arco do Azure

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="login-to-the-azure-arc-data-controller"></a>Fazer logon no controlador de dados de arco do Azure

Antes de criar uma instância, faça logon no controlador de dados de arco do Azure se você ainda não estiver conectado.

```console
azdata login
```

Em seguida, você será solicitado a fornecer o nome de usuário, a senha e o namespace do sistema.  

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-sql-managed-instance"></a>Criar uma Instância Gerenciada do SQL Azure

Para exibir as opções de criação disponíveis forSQL Instância Gerenciada, use o seguinte comando:
```console
azdata arc sql mi create --help
```

Para criar um Instância Gerenciada SQL, use o seguinte comando:

```console
azdata arc sql mi create -n <instanceName> --storage-class-data <storage class> --storage-class-logs <storage class>
```

Exemplo:

```console
azdata arc sql mi create -n sqldemo --storage-class-data managed-premium --storage-class-logs managed-premium
```
> [!NOTE]
>  Os nomes devem ter menos de 13 caracteres de comprimento e estar em conformidade com as [convenções de nomenclatura de DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)
>
>  Ao especificar a alocação de memória e a alocação vCore, use esta fórmula para garantir que a criação seja bem-sucedida-para cada 1 vCore, você precisará de, pelo menos, 4 GB de RAM de capacidade disponível no nó kubernetes em que o Pod da instância gerenciada do SQL será executado.
>
>  Ao criar uma instância SQL, não use letras maiúsculas no nome, se estiver provisionando no Azure
>
>  Para listar as classes de armazenamento disponíveis em sua execução do cluster kubernetes `kubectl get storageclass` 


> [!NOTE]
> Se você quiser automatizar a criação de instâncias SQL e evitar o prompt interativo para a senha de administrador, poderá definir as `AZDATA_USERNAME` variáveis de `AZDATA_PASSWORD` ambiente e para o nome de usuário e a senha desejados antes de executar o `azdata arc sql mi create` comando.
> 
>  Se você criou o controlador de dados usando AZDATA_USERNAME e AZDATA_PASSWORD na mesma sessão de terminal, os valores de AZDATA_USERNAME e AZDATA_PASSWORD serão usados para criar a instância gerenciada do SQL também.

> [!NOTE]
> A criação do Azure SQL Instância Gerenciada não registrará os recursos no Azure. As etapas para registrar o recurso estão nos seguintes artigos: 
> - [Exibir logs e métricas usando Kibana e Grafana](monitor-grafana-kibana.md)
> - [Carregar dados de cobrança no Azure e exibi-los no portal do Azure](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Exibir instância no arco do Azure

Para exibir a instância, use o seguinte comando:

```console
azdata arc sql mi list
```

A saída deve ser semelhante a esta:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Se você estiver usando AKS ou `kubeadm` OpenShift, etc., poderá copiar o IP externo e o número da porta aqui e conectar-se a ele usando sua ferramenta favorita para se conectar a uma instância do SQL Server/SQL do Azure, como Azure Data Studio ou SQL Server Management Studio. No entanto, se você estiver usando a VM de início rápido, consulte o artigo [conectar-se ao Azure Arc habilitado do SQL instância gerenciada](connect-managed-instance.md) para obter instruções especiais.


## <a name="next-steps"></a>Próximas etapas
- [Conectar-se ao SQL Instância Gerenciada habilitado para Arc do Azure](connect-managed-instance.md)
- [Registrar sua instância com o Azure e carregar métricas e logs sobre sua instância](upload-metrics-and-logs-to-azure-monitor.md)
- [Implantar a instância gerenciada do SQL do Azure usando Azure Data Studio](create-sql-managed-instance-azure-data-studio.md)
