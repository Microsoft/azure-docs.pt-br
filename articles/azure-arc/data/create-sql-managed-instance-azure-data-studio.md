---
title: Criar instância gerenciada do SQL do Azure usando Azure Data Studio
description: Criar instância gerenciada do SQL do Azure usando Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 651811384a8e831c41c48ca8c4849e5ee3852054
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93280472"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>Criar instância gerenciada do SQL-Arc do Azure usando Azure Data Studio

Este documento orienta você pelas etapas de instalação da instância gerenciada do SQL do Azure-Azure ARC usando Azure Data Studio

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>Faça logon no controlador de dados de arco do Azure

Antes de criar uma instância, faça logon no controlador de dados de arco do Azure se você ainda não estiver conectado.

```console
azdata login
```

Em seguida, você será solicitado a fornecer o namespace no qual o controlador de dados é criado, o nome de usuário e a senha para fazer logon no controlador.  

> Se você precisar validar o namespace, poderá executar ```kubectl get pods -A``` para obter uma lista de todos os namespaces no cluster.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Criar instância gerenciada do SQL do Azure no arco do Azure

- Iniciar Azure Data Studio
- Na guia conexões, clique nos três pontos na parte superior esquerda e escolha "nova implantação"
- Nas opções de implantação, selecione **instância gerenciada do SQL do Azure-arco do Azure** 
  > [!NOTE]
  > Você pode ser solicitado a instalar o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] aqui se ele não estiver instalado no momento.
- Aceite os termos de privacidade e licença e clique em **selecionar** na parte inferior



- Na folha implantar instância gerenciada do SQL do Azure – arco do Azure, insira as seguintes informações:
  - Insira um nome para a instância de SQL Server
  - Insira e confirme uma senha para a instância de SQL Server
  - Selecione a classe de armazenamento conforme apropriado para os dados
  - Selecione a classe de armazenamento conforme apropriado para os logs

- Clique no botão **implantar**

- Isso deve iniciar a criação da instância gerenciada do SQL do Azure – arco do Azure no controlador de dados.

- Em alguns minutos, sua criação deve ser concluída com êxito

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Conectar-se à instância gerenciada do SQL do Azure-Azure Arc do Azure Data Studio

- Faça logon no controlador de dados Arc do Azure, fornecendo o namespace, o nome de usuário e a senha para o controlador de dados: 
```console
azdata login
```

- Exiba todas as instâncias gerenciadas do Azure SQL provisionadas, usando os seguintes comandos:

```console
azdata arc sql mi list
```

A saída deve ser semelhante a esta, copie o ServerEndpoint (incluindo o número da porta) aqui.

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- Em Azure Data Studio, na guia **conexões** , clique na **nova conexão** no modo de exibição **servidores**
- Na folha **conexão** , Cole o ServerEndpoint na caixa de texto servidor
- Selecione **logon do SQL** como o tipo de autenticação
- Digite *SA* como o nome de usuário
- Insira a senha da `sa` conta
- Opcionalmente, insira o nome do banco de dados específico ao qual se conectar
- Opcionalmente, selecione/adicione um novo grupo de servidores conforme apropriado
- Selecione **conectar** para conectar-se à instância gerenciada do SQL do Azure-Arc do Azure




## <a name="next-steps"></a>Próximas etapas

Agora, tente [monitorar sua instância do SQL](monitor-grafana-kibana.md)
