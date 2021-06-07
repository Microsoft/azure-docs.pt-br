---
title: Criar um grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc
description: Criar um grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 046f9d80c034e1ac1f2e7ffe144b4f389861b043
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687933"
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
Implemente esta etapa antes de passar para a próxima etapa. Para implantar o grupo de servidores de hiperescala PostgreSQL no Red Hat OpenShift em um projeto diferente do padrão, você precisa executar os seguintes comandos em seu cluster para atualizar as restrições de segurança. Esse comando concede os privilégios necessários para as contas de serviço que executarão o grupo de servidores de hiperescala PostgreSQL. A SCC (restrição de contexto de segurança) Arc-data-SCC é aquela que você adicionou quando implantou o controlador de dados de arco do Azure.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Server-Group-Name é o nome do grupo de servidores que será criado durante a próxima etapa.**

Para obter mais detalhes sobre o SCCs no OpenShift, consulte a [documentação do OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html). Agora você pode implementar a próxima etapa.


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Criar um grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc

Para criar um grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc no controlador de dados Arc, você usará o comando `azdata arc postgres server create` para o qual passará vários parâmetros.

Para obter detalhes sobre todos os parâmetros que podem ser definidos no momento da criação, examine a saída do comando:
```console
azdata arc postgres server create --help
```

Os principais parâmetros devem ser considerados:
- **o nome do grupo de servidores** que você deseja implantar. Indique `--name` `-n` um ou seguido por um nome cujo comprimento não deve exceder 11 caracteres.

- **a versão do mecanismo PostgreSQL** que você deseja implantar: por padrão, é a versão 12. Para implantar a versão 12, você pode omitir esse parâmetro ou passar um dos seguintes parâmetros: `--engine-version 12` ou `-ev 12` . Para implantar a versão 11, indique `--engine-version 11` ou `-ev 11` .

- **o número de nós de trabalho** que você deseja implantar para expandir e potencialmente alcançar um desempenho melhor. Antes de continuar aqui, leia os [conceitos sobre a hiperescala postgres](concepts-distributed-postgres-hyperscale.md). Para indicar o número de nós de trabalho a serem implantados, use o parâmetro `--workers` ou `-w` seguido por um inteiro maior ou igual a 2. Por exemplo, se você quiser implantar um grupo de servidores com 2 nós de trabalho, indique `--workers 2` ou `-w 2` . Isso criará três pods, um para o nó/instância de coordenador e dois para os nós/instâncias de trabalho (um para cada um dos trabalhadores).

- **as classes de armazenamento** que você deseja que seu grupo de servidores use. É importante que você defina a classe de armazenamento no momento em que implanta um grupo de servidores, pois isso não pode ser alterado após a implantação. Se você alterar a classe de armazenamento após a implantação, precisará extrair os dados, excluir o grupo de servidores, criar um novo grupo de servidores e importar os dados. Você pode especificar as classes de armazenamento a serem usadas para os dados, os logs e os backups. Por padrão, se você não indicar classes de armazenamento, as classes de armazenamento do controlador de dados serão usadas.
    - para definir a classe de armazenamento para os dados, indique o parâmetro `--storage-class-data` ou `-scd` seguido pelo nome da classe de armazenamento.
    - para definir a classe de armazenamento para os logs, indique o parâmetro `--storage-class-logs` ou `-scl` seguido pelo nome da classe de armazenamento.
    - para definir a classe de armazenamento para os backups: nesta visualização da hiperescala PostgreSQL habilitada para o Arc do Azure, há duas maneiras de definir classes de armazenamento, dependendo de quais tipos de operações de backup/restauração você deseja fazer. Estamos trabalhando para simplificar essa experiência. Você irá indicar uma classe de armazenamento ou uma montagem de declaração de volume. Uma montagem de declaração de volume é um par de uma declaração de volume persistente existente (no mesmo namespace) e tipo de volume (e metadados opcionais, dependendo do tipo de volume) separados por dois-pontos. O volume persistente será montado em cada pod para o grupo de servidores PostgreSQL.
        - Se você quiser que o plano faça apenas restaurações completas de banco de dados, defina o parâmetro `--storage-class-backups` ou `-scb` seguido pelo nome da classe de armazenamento.
        - Se você planeja fazer restaurações completas de bancos de dados e restaurações pontuais, defina o parâmetro `--volume-claim-mounts` ou `-vcm` seguido pelo nome de uma declaração de volume e um tipo de volume.

Observe que, ao executar o comando Create, você será solicitado a inserir a senha do `postgres` usuário administrativo padrão. O nome desse usuário não pode ser alterado nesta visualização. Você pode ignorar o prompt interativo definindo a `AZDATA_PASSWORD` variável de ambiente de sessão antes de executar o comando Create.

### <a name="examples"></a>Exemplos

**Para implantar um grupo de servidores do postgres versão 12 chamado postgres01 com 2 nós de trabalho que usam as mesmas classes de armazenamento que o controlador de dados, execute o seguinte comando:**
```console
azdata arc postgres server create -n postgres01 --workers 2
```

**Para implantar um grupo de servidores do postgres versão 12 chamado postgres01 com 2 nós de trabalho que usa as mesmas classes de armazenamento que o controlador de dados para dados e logs, mas sua classe de armazenamento específica para fazer restaurações completas e restaurações pontuais, use as seguintes etapas:**

 Este exemplo pressupõe que o grupo de servidores está hospedado em um cluster do AKS (serviço kubernetes do Azure). Este exemplo usa o azurefile-Premium como o nome da classe de armazenamento. Você pode ajustar o exemplo abaixo para corresponder ao seu ambiente. Observe que **AccessModes ReadWriteMany é necessário** para essa configuração.  

Primeiro, crie um arquivo YAML que contenha a descrição abaixo do PVC de backup e nomeie-o CreateBackupPVC. yml, por exemplo:
```console
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: arc
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: azurefile-premium
```

Em seguida, crie um PVC usando a definição armazenada no arquivo YAML:

```console
kubectl create -f e:\CreateBackupPVC.yml -n arc
``` 

Em seguida, crie o grupo de servidores:

```console
azdata arc postgres server create -n postgres01 --workers 2 -vcm backup-pvc:backup
```

> [!IMPORTANT]
> - Leia as [limitações atuais relacionadas ao backup/restauração](limitations-postgresql-hyperscale.md#backup-and-restore).


> [!NOTE]  
> - Se você implantou o controlador de dados usando `AZDATA_USERNAME` `AZDATA_PASSWORD` variáveis de ambiente de sessão na mesma sessão de terminal, os valores para `AZDATA_PASSWORD` serão usados para implantar o grupo de servidores de hiperescala PostgreSQL também. Se você preferir usar outra senha, (1) atualize o valor de `AZDATA_PASSWORD` ou (2) exclua a `AZDATA_PASSWORD` variável de ambiente ou (3) exclua seu valor para ser solicitado a inserir uma senha interativamente ao criar um grupo de servidores.
> - Criar um grupo de servidores de hiperescala PostgreSQL não registrará imediatamente os recursos no Azure. Como parte do processo de carregar dados de [inventário de recursos](upload-metrics-and-logs-to-azure-monitor.md)  ou de [uso](view-billing-data-in-azure.md) no Azure, os recursos serão criados no Azure e você poderá ver seus recursos no portal do Azure.



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>Listar os grupos de servidores de hiperescala PostgreSQL implantados em seu controlador de dados de arco

Para listar os grupos de servidores de hiperescala PostgreSQL implantados no controlador de dados Arc, execute o seguinte comando:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Obter os pontos de extremidade para se conectar aos grupos de servidores de hiperescala PostgreSQL habilitados para o Azure Arc

Para exibir os pontos de extremidade de um grupo de servidores PostgreSQL, execute o seguinte comando:

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

Você pode usar o ponto de extremidade da instância PostgreSQL para se conectar ao grupo de servidores de hiperescala PostgreSQL de sua ferramenta favorita:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [Pgcli](https://www.pgcli.com/) psql, pgAdmin, etc. Ao fazer isso, você se conecta ao nó/instância do coordenador, que cuida do roteamento da consulta para os nós/instâncias apropriados se você tiver criado tabelas distribuídas. Para obter mais detalhes, leia os [conceitos de hiperescala do PostgreSQL habilitado para Arc do Azure](concepts-distributed-postgres-hyperscale.md).

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

- Leia os conceitos e os guias de instruções do banco de dados do Azure para PostgreSQL de hiperescala para distribuir seus dados entre vários nós de hiperescala PostgreSQL e para se beneficiar de um desempenho melhor possível:
    * [Nós e tabelas](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinar o tipo de aplicativo](../../postgresql/concepts-hyperscale-app-type.md)
    * [Escolher uma coluna de distribuição](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocação de tabela](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuir e modificar tabelas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Criar um banco de dados de vários locatários](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Criar um painel de análise em tempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* Nos documentos acima, ignore as seções **entrar no portal do Azure**, & **criar um banco de dados do Azure para PostgreSQL-Citus (hiperescala)**. Implemente as etapas restantes em sua implantação do Azure Arc. Essas seções são específicas para o banco de dados do Azure para PostgreSQL Citus (hiperescala) oferecida como um serviço de PaaS na nuvem do Azure, mas as outras partes dos documentos são diretamente aplicáveis à sua hiperescala PostgreSQL habilitada para o Arc do Azure.

- [Escalar horizontalmente seu arco do Azure habilitado para o grupo de servidores de hiperescala PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)
- [Configuração de armazenamento e conceitos de armazenamento kubernetes](storage-configuration.md)
- [Expandindo declarações de volume persistentes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Modelo de recurso kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
