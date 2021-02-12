---
title: Criar um grupo de servidores de hiperescala do banco de dados do Azure para PostgreSQL no arco do Azure
description: Criar um grupo de servidores de hiperescala do banco de dados do Azure para PostgreSQL no arco do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 45bb045e7bad2d5f8a56b71787b3abb5921cb7d5
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985879"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Criar um grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc

Este documento descreve as etapas para criar um grupo de servidores de hiperescala PostgreSQL no arco do Azure.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Introdução
Se você já estiver familiarizado com os tópicos abaixo, poderá ignorar este parágrafo.
Há tópicos importantes que você pode querer ler antes de prosseguir com a criação:
- [Visão geral dos serviços de dados habilitados para Arc do Azure](overview.md)
- [Requisitos e modos de conectividade](connectivity.md)
- [Configuração de armazenamento e conceitos de armazenamento kubernetes](storage-configuration.md)
- [Modelo de recurso kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Se você preferir experimentar coisas sem provisionar um ambiente completo, comece rapidamente com o [Azure Arc JumpStart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) no Azure kubernetes Service (AKs), AWS elástico kubernetes Service (EKS), Google Cloud kubernetes Engine (GKE) ou em uma VM do Azure.


## <a name="login-to-the-azure-arc-data-controller"></a>Fazer logon no controlador de dados de arco do Azure

Antes de criar uma instância, você deve primeiro fazer logon no controlador de dados de arco do Azure. Se você já estiver conectado ao controlador de dados, poderá ignorar esta etapa.

```console
azdata login
```

Em seguida, você será solicitado a fornecer o nome de usuário, a senha e o namespace do sistema.  

> Se você usou o script para criar o controlador de dados, o namespace deve ser **Arc**

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Etapa preliminar e temporária somente para usuários OpenShift

Implemente esta etapa antes de passar para a próxima etapa. Para implantar o grupo de servidores de hiperescala PostgreSQL no Red Hat OpenShift em um projeto diferente do padrão, você precisa executar os seguintes comandos em seu cluster para atualizar as restrições de segurança. Esse comando concede os privilégios necessários para as contas de serviço que executarão o grupo de servidores de hiperescala PostgreSQL. A SCC (restrição de contexto de segurança) **_Arc-data-SCC_** é aquela que você adicionou quando implantou o controlador de dados de arco do Azure.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-Group-Name** é o nome do grupo de servidores que será criado durante a próxima etapa._
   
Para obter mais detalhes sobre o SCCs no OpenShift, consulte a [documentação do OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Agora você pode implementar a próxima etapa.

## <a name="create-an-azure-database-for-postgresql-hyperscale-server-group"></a>Criar um grupo de servidores de hiperescala do banco de dados do Azure para PostgreSQL

Para criar um grupo de servidores de hiperescala do banco de dados do Azure para PostgreSQL no arco do Azure, use o seguinte comando:

```console
azdata arc postgres server create -n <name> --workers <# worker nodes with #>=2> --storage-class-data <storage class name> --storage-class-logs <storage class name> --storage-class-backups <storage class name>

#Example
#azdata arc postgres server create -n postgres01 --workers 2
```

> [!IMPORTANT]
> - A classe de armazenamento usada para backups (_--Storage-Class-backups-SCB_) usa como padrão a classe de armazenamento de dados do controlador de dados se ela não for fornecida.
> - Para restaurar um grupo de servidores para um grupo de servidores separado (como a restauração pontual), você deve configurar o grupo de servidores para usar PVCs com o modo de acesso ReadWriteMany. É necessário fazer isso na criação do grupo de servidores. Ele não pode ser alterado depois de ser criado. Para obter mais detalhes, leia:
>    - [Esta seção sobre backup e restauração](https://docs.microsoft.com/azure/azure-arc/data/backup-restore-postgresql-hyperscale#create-a-server-group-that-is-ready-for-backups-and-restores)
>    - [Esta seção sobre as limitações da hiperescala do PostgreSQL habilitada para Arc do Azure](https://docs.microsoft.com/azure/azure-arc/data/limitations-postgresql-hyperscale)


> [!NOTE]
> - **Há outros parâmetros de linha de comando disponíveis.  Consulte a lista completa de opções executando `azdata arc postgres server create --help` .**

> - A unidade aceita pelos parâmetros--volume-Size-* é uma quantidade de recursos kubernetes (um número inteiro seguido por um desses valores é suficiente (T, G, M, K, m) ou seus equivalentes de potência de dois (ti, GI, mi, Ki)).
> - Os nomes devem ter 12 caracteres ou menos de comprimento e estar em conformidade com as convenções de nomenclatura de DNS.
> - Você será solicitado a inserir a senha para o usuário administrativo do _postgres_ Standard.  Você pode ignorar o prompt interativo definindo a `AZDATA_PASSWORD` variável de ambiente de sessão antes de executar o comando Create.
> - Se você implantou o controlador de dados usando as variáveis de ambiente de sessão AZDATA_USERNAME e AZDATA_PASSWORD na mesma sessão de terminal, os valores de AZDATA_PASSWORD serão usados para implantar o grupo de servidores de hiperescala PostgreSQL também. Se você preferir usar outra senha, (1) atualize o valor para AZDATA_PASSWORD ou (2) exclua a variável de ambiente AZDATA_PASSWORD ou exclua seu valor para inserir uma senha interativamente ao criar um grupo de servidores.
> - O nome do usuário administrador padrão do mecanismo de banco de dados de hiperescala PostgreSQL é _postgres_ e não pode ser alterado neste ponto.
> - Criar um grupo de servidores de hiperescala PostgreSQL não registrará imediatamente os recursos no Azure. Como parte do processo de carregar dados de [inventário de recursos](upload-metrics-and-logs-to-azure-monitor.md)  ou de [uso](view-billing-data-in-azure.md) no Azure, os recursos serão criados no Azure e você poderá ver seus recursos no portal do Azure.



## <a name="list-your-azure-database-for-postgresql-server-groups-created-in-your-arc-setup"></a>Listar os grupos de servidores do banco de dados do Azure para PostgreSQL criados na configuração do seu Arc

Para exibir os grupos de servidores de hiperescala do PostgreSQL no Arc do Azure, use o seguinte comando:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-database-for-postgresql-server-groups"></a>Obter os pontos de extremidade para se conectar ao banco de dados do Azure para grupos de servidores PostgreSQL

Para exibir os pontos de extremidade de uma instância do PostgreSQL, execute o seguinte comando:

```console
azdata arc postgres endpoint list -n <server group name>
```
Por exemplo:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

Você pode usar o ponto de extremidade da instância PostgreSQL para se conectar ao grupo de servidores de hiperescala PostgreSQL de sua ferramenta favorita:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [Pgcli](https://www.pgcli.com/) psql, pgAdmin, etc.

Se você estiver usando uma VM do Azure para testar, siga as instruções abaixo:

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Observação especial sobre implantações de máquinas virtuais do Azure

Quando você estiver usando uma máquina virtual do Azure, o endereço IP do ponto de extremidade não mostrará o endereço IP _público_ . Para localizar o endereço IP público, use o seguinte comando:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Em seguida, você pode combinar o endereço IP público com a porta para estabelecer a conexão.

Talvez você também precise expor a porta do grupo de servidores de hiperescala PostgreSQL por meio do NSG (gateway de segurança de rede). Para permitir o tráfego por meio do (NSG), você precisará adicionar uma regra que você pode fazer usando o seguinte comando:

Para definir uma regra, você precisará saber o nome do seu NSG. Você determina o NSG usando o comando a seguir:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Quando tiver o nome do NSG, você poderá adicionar uma regra de firewall usando o comando a seguir. Os valores de exemplo aqui criam uma regra NSG para a porta 30655 e permitem a conexão de **qualquer** endereço IP de origem.  Essa não é uma prática recomendada de segurança!  Você pode bloquear as coisas melhor especificando um valor-origem-endereço-prefixos que é específico para o endereço IP do cliente ou um intervalo de endereços IP que abrange os endereços IP da sua equipe ou da organização.

Substitua o valor do parâmetro--Destination-Port-ranges abaixo pelo número da porta obtido do comando ' azdata Arc postgres Server List ' acima.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Conectar-se com o Azure Data Studio

Abra Azure Data Studio e conecte-se à sua instância com o endereço IP do ponto de extremidade externo e o número da porta acima e a senha que você especificou no momento em que criou a instância.  Se o PostgreSQL não estiver disponível no menu suspenso *tipo de conexão* , você poderá instalar a extensão PostgreSQL pesquisando PostgreSQL na guia extensões.

> [!NOTE]
> Você precisará clicar no botão [avançado] no painel conexão para inserir o número da porta.

Lembre-se de que, se você estiver usando uma VM do Azure, será necessário o endereço IP _público_ que é acessível por meio do seguinte comando:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Conectar-se ao psql

Para acessar o grupo de servidores de hiperescala do PostgreSQL, passe o ponto de extremidade externo do grupo de servidores de hiperescala do PostgreSQL que você recuperou acima:

Agora você pode conectar o psql:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Próximas etapas

- Leia os conceitos e os guias de instruções do banco de dados do Azure para PostgreSQL de hiperescala para distribuir seus dados em vários nós de hiperescala do PostgreSQL e para se beneficiar de toda a potência do banco de dado do Azure para PostgreSQL em hiperescala. :
    * [Nós e tabelas](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinar o tipo de aplicativo](../../postgresql/concepts-hyperscale-app-type.md)
    * [Escolher uma coluna de distribuição](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocação de tabela](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuir e modificar tabelas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Criar um banco de dados de vários locatários](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Criar um painel de análise em tempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* Nos documentos acima, ignore as seções **entrar no portal do Azure**, & **criar um banco de dados do Azure para PostgreSQL-Citus (hiperescala)**. Implemente as etapas restantes em sua implantação do Azure Arc. Essas seções são específicas para o banco de dados do Azure para PostgreSQL Citus (hiperescala) oferecida como um serviço de PaaS na nuvem do Azure, mas as outras partes dos documentos são diretamente aplicáveis à sua hiperescala PostgreSQL habilitada para o Arc do Azure.

- [Escalar horizontalmente o grupo de servidores de Hiperescala do Banco de Dados do Azure para PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)
- [Configuração de armazenamento e conceitos de armazenamento kubernetes](storage-configuration.md)
- [Expandindo declarações de volume persistentes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Modelo de recurso kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)