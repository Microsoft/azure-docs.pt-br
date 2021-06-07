---
title: Configuração de armazenamento
description: Explica opções de configuração de armazenamento dos serviços de dados habilitados para Arc do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 7b683029b7fd05078755d4e8cd027f55c805f991
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97107253"
---
# <a name="storage-configuration"></a>Configuração de armazenamento

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="kubernetes-storage-concepts"></a>Conceitos de armazenamento do kubernetes

O kubernetes fornece uma camada de abstração de infraestrutura sobre a pilha técnica de virtualização subjacente (opcional) e o hardware. A maneira como o kubernetes abstrai o armazenamento é por meio de **[classes de armazenamento](https://kubernetes.io/docs/concepts/storage/storage-classes/)**. No momento do provisionamento de um pod, uma classe de armazenamento pode ser especificada para ser usada para cada volume. No momento em que o pod é provisionado, o **[provisionamento](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** de classe de armazenamento é chamado para provisionar o armazenamento e, em seguida, um **[volume persistente](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** é criado no armazenamento provisionado e, em seguida, o pod é montado no volume persistente por uma **[declaração de volume persistente](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)**.

O kubernetes fornece uma maneira para os provedores de infraestrutura de armazenamento conectarem drivers (também chamados de "Complementos") que estendam o kubernetes. Os complementos de armazenamento devem estar em conformidade com o **[padrão de interface de armazenamento de contêiner](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)**. Há dezenas de complementos que podem ser encontrados nessa lista não definitiva **[de drivers do CSI](https://kubernetes-csi.github.io/docs/drivers.html)**. O driver CSI que você usa dependerá de fatores como, por exemplo, se você estiver executando em um serviço de kubernetes gerenciado hospedado na nuvem ou qual provedor OEM está usando para o hardware.

Você pode exibir quais classes de armazenamento são configuradas no cluster kubernetes executando este comando:

```console
kubectl get storageclass
```

Exemplo de saída de um cluster do serviço de kubernetes do Azure (AKS):

```console
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

Você pode obter detalhes sobre uma classe de armazenamento executando este comando:

```console
kubectl describe storageclass/<storage class name>
```

Exemplo:

```console
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

Você pode ver os volumes persistentes provisionados no momento e as declarações de volume persistentes executando os seguintes comandos:

```console
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

Exemplo de exibição de volumes persistentes:

```console

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

Exemplo de como mostrar as declarações de volume persistentes:

```console

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>Fatores a considerar ao escolher a configuração de armazenamento

A seleção da classe de armazenamento correta é importante para a resiliência e o desempenho dos dados. Escolher a classe de armazenamento incorreta pode colocar seus dados em risco de perda total de dados no caso de uma falha de hardware ou pode resultar em um desempenho menos ideal.

Em geral, há dois tipos de armazenamento:

- **Armazenamento local** -armazenamento provisionado em discos rígidos locais em um determinado nó. Esse tipo de armazenamento pode ser ideal em termos de desempenho, mas requer design específico para redundância de dados, replicando os dados entre vários nós.
- **Armazenamento remoto e compartilhado** -armazenamento provisionado em algum dispositivo de armazenamento remoto-por exemplo, um serviço de armazenamento em nuvem, San ou nas, como EBS ou arquivos do Azure. Esse tipo de armazenamento geralmente fornece a redundância de dados automaticamente, mas não é tão rápido quanto o armazenamento local pode ser.

> [!NOTE]
> Por enquanto, se você estiver usando o NFS, precisará definir allowRunAsRoot como true em seu arquivo de perfil de implantação antes de implantar o controlador de dados de arco do Azure.

### <a name="data-controller-storage-configuration"></a>Configuração de armazenamento do controlador de dados

Alguns serviços no Azure ARC para serviços de dados dependem de serem configurados para usar o armazenamento remoto compartilhado, pois os serviços não têm a capacidade de replicar os dados. Esses serviços são encontrados na coleção de pods do controlador de dados:

|**Serviço**|**Declarações de volume persistentes**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**Instância do SQL do controlador**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**Serviço de API do controlador**|`<namespace>/data-controller`|

No momento em que o controlador de dados é provisionado, a classe de armazenamento a ser usada para cada um desses volumes persistentes é especificada pela passagem de--Storage-Class | -SC parâmetro para o `azdata arc dc create` comando ou definindo as classes de armazenamento no control.jsno arquivo de modelo de implantação que é usado.

Os modelos de implantação fornecidos prontos para uso têm uma classe de armazenamento padrão especificada apropriada para o ambiente de destino, mas ela pode ser substituída durante a implantação. Consulte as etapas detalhadas para alterar [o perfil de implantação](create-data-controller.md) para alterar a configuração da classe de armazenamento para o pods do controlador de dados no momento da implantação.

Se você definir a classe de armazenamento usando a classe--Storage-Class | -SC parâmetro a classe de armazenamento será usada para as classes de armazenamento de dados e de log. Se você definir as classes de armazenamento no arquivo de modelo de implantação, poderá especificar classes de armazenamento diferentes para logs e dados.

Fatores importantes a serem considerados ao escolher uma classe de armazenamento para o pods do controlador de dados:

- Você **deve** usar uma classe de armazenamento compartilhada remota para garantir a durabilidade dos dados e, portanto, se um pod ou nó se tornar inativo quando o Pod for colocado em backup, ele poderá se conectar novamente ao volume persistente.
- Os dados que estão sendo gravados na instância do SQL do controlador, no banco de dados de métricas e no BD de logs normalmente são um volume bem baixo e não são sensíveis à latência, portanto, o armazenamento de desempenho extremamente rápido não é crítico. Se você tiver usuários com frequência usando as interfaces Grafana e Kibana e tiver um grande número de instâncias de banco de dados, os usuários poderão se beneficiar do armazenamento mais rápido.
- A capacidade de armazenamento necessária é variável com o número de instâncias de banco de dados que você implantou, pois os logs e as métricas são coletados para cada instância do banco de dados. Os dados são retidos no BD de logs e métricas por duas (2) semanas antes de serem limpos. 
- A alteração da classe de armazenamento após a implantação é difícil, não documentada e não tem suporte. Certifique-se de escolher a classe de armazenamento corretamente no momento da implantação.

> [!NOTE]
> Se nenhuma classe de armazenamento for especificada, a classe de armazenamento padrão será usada. Pode haver apenas uma classe de armazenamento padrão por cluster kubernetes. Você pode [alterar a classe de armazenamento padrão](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).

### <a name="database-instance-storage-configuration"></a>Configuração de armazenamento da instância do banco de dados

Cada instância de banco de dados tem os volumes persistentes de backup As classes de armazenamento desses volumes persistentes podem ser especificadas no momento da implantação. Se nenhuma classe de armazenamento for especificada, a classe de armazenamento padrão será usada.

Ao criar uma instância usando o `azdata arc sql mi create` ou o `azdata arc postgres server create` , há dois parâmetros que podem ser usados para definir as classes de armazenamento:

> [!NOTE]
> Alguns desses parâmetros estão em desenvolvimento e serão disponibilizados no `azdata arc sql mi create` e `azdata arc postgres server create` nas versões futuras.

|Nome do parâmetro, nome curto|Usada para|
|---|---|
|`--storage-class-data`, `-scd`|Usado para especificar a classe de armazenamento para todos os arquivos de dados, incluindo arquivos de log de transações|
|`--storage-class-logs`, `-scl`|Usado para especificar a classe de armazenamento para todos os arquivos de log|
|`--storage-class-data-logs`, `-scdl`|Usado para especificar a classe de armazenamento para os arquivos de log de transações do banco de dados. **Observação: ainda não está disponível.**|
|`--storage-class-backups`, `-scb`|Usado para especificar a classe de armazenamento para todos os arquivos de backup. **Observação: ainda não está disponível.**|

A tabela a seguir lista os caminhos dentro do contêiner do SQL Instância Gerenciada do Azure que é mapeado para o volume persistente para dados e logs:

|Nome do parâmetro, nome curto|Caminho dentro do contêiner MSSQL-Miaa|Descrição|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt|Contém diretórios para a instalação do MSSQL e outros processos do sistema. O diretório MSSQL contém dados padrão (incluindo logs de transações), log de erros & diretórios de backup|
|`--storage-class-logs`, `-scl`|/var/log|Contém diretórios que armazenam a saída do console (stderr, stdout), outras informações de log de processos dentro do contêiner|

A tabela a seguir lista os caminhos dentro do contêiner da instância PostgreSQL que é mapeado para o volume persistente para dados e logs:

|Nome do parâmetro, nome curto|Caminho dentro do contêiner postgres|Descrição|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt/postgresql|Contém diretórios de dados e de log para a instalação do postgres|
|`--storage-class-logs`, `-scl`|/var/log|Contém diretórios que armazenam a saída do console (stderr, stdout), outras informações de log de processos dentro do contêiner|

Cada instância do banco de dados terá um volume persistente separado para arquivos, logs e backups. Isso significa que haverá separação da e/s para cada um desses tipos de arquivos, sujeito a como o provisionamento de volume provisionará o armazenamento. Cada instância de banco de dados tem suas próprias declarações de volume persistentes e volumes persistentes.

Se houver vários bancos de dados em uma determinada instância de banco de dado, todos os bancos de dado usarão a mesma declaração de volume persistente, volume persistente e classe de armazenamento. Todos os backups-backups de log diferenciais e backups completos usarão a mesma declaração de volume persistente e volume persistente. As declarações de volume persistente para os pods da instância do banco de dados são mostradas abaixo:

|**Instância**|**Declarações de volume persistentes**|
|---|---|
|**Instância Gerenciada do SQL do Azure**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**Instância do banco de dados do Azure para PostgreSQL**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Hiperescala do PostgreSQL do Azure**|`<namespace>/logs-<instance namme>-<ordinal>`, `<namespace>/data-<instance namme>-<ordinal>` *(Intervalos ordinais de 0 a W, onde w é o número de trabalhadores)*|

Fatores importantes a serem considerados ao escolher uma classe de armazenamento para o pods da instância do banco de dados:

- As instâncias de banco de dados podem ser implantadas em um padrão Pod único ou em um padrão de Pod múltiplo. Um exemplo de um único padrão de Pod é uma instância de desenvolvedor da instância gerenciada do SQL do Azure ou um tipo de preço de finalidade geral instância gerenciada do Azure SQL. Um exemplo de um padrão de Pod múltiplo é uma instância gerenciada do SQL do Azure do tipo de preço crítico comercial altamente disponível. (Observação: os tipos de preço estão em desenvolvimento e ainda não estão disponíveis para os clientes.)  As instâncias de banco de dados implantadas com o padrão de Pod único **devem** usar uma classe de armazenamento remota compartilhada para garantir a durabilidade dos dados e, portanto, se um pod ou nó se tornar inativo quando o Pod for colocado no backup, ele poderá se conectar novamente ao volume persistente. Por outro lado, uma instância gerenciada do SQL do Azure altamente disponível usa Always On grupos de disponibilidade para replicar os dados de uma instância para outra de forma síncrona ou assíncrona. Especialmente no caso em que os dados são replicados de forma síncrona, sempre há várias cópias dos dados – geralmente três (3) cópias. Por isso, é possível usar o armazenamento local ou as classes de armazenamento compartilhadas remotas para arquivos de dados e de log. Se estiver utilizando armazenamento local, os dados ainda serão preservados mesmo no caso de um pod, nó ou hardware de armazenamento com falha. Devido a essa flexibilidade, você pode optar por usar o armazenamento local para melhorar o desempenho.
- O desempenho do banco de dados é basicamente uma função da taxa de transferência de e/s de um determinado dispositivo de armazenamento. Se o banco de dados for de leituras pesadas ou gravações pesadas, você deverá escolher uma classe de armazenamento com hardware projetado para esse tipo de carga de trabalho. Por exemplo, se o banco de dados for usado principalmente para gravações, você poderá escolher armazenamento local com RAID 0. Se o seu banco de dados for usado principalmente para leituras de uma pequena quantidade de pontos "ativos", mas houver um grande volume de armazenamento geral de dados frios, você poderá escolher um dispositivo SAN capaz de armazenar em camadas. Escolher a classe de armazenamento correta não é muito diferente de escolher o tipo de armazenamento que você usaria para qualquer banco de dados.
- Se você estiver usando um provisionamento de volume de armazenamento local, verifique se os volumes locais que são provisionados para dados, logs e backups são cada patamar em dispositivos de armazenamento subjacentes diferentes para evitar a contenção em e/s de disco. O sistema operacional também deve estar em um volume montado em discos (s) separados. Essa é essencialmente a mesma orientação que seria seguida para uma instância de banco de dados em hardware físico.
- Como todos os bancos de dados em uma determinada instância compartilham uma declaração de volume persistente e um volume persistente, certifique-se de não colocar as instâncias de banco de dados ocupadas na mesma instância de banco de dados. Se possível, separe bancos de dados ocupados em suas próprias instâncias de banco de dados para evitar contenção de e/s. Além disso, use o direcionamento de rótulo de nó para dividir instâncias de banco de dados em nós separados para distribuir o tráfego de e/s geral entre vários nós. Se você estiver usando a virtualização, considere distribuir o tráfego de e/s não apenas no nível do nó, mas também a atividade combinada de e/s acontecendo por todas as VMs de nó em um determinado host físico.

## <a name="estimating-storage-requirements"></a>Estimando os requisitos de armazenamento
Cada pod que contém dados com estado usa dois volumes persistentes nesta versão – um volume persistente para dados e outro volume persistente para logs. A tabela a seguir lista o número de volumes persistentes necessários para um único controlador de dados, instância gerenciada do SQL do Azure, instância do Azure Database para PostgreSQL e instância de hiperescala do Azure PostgreSQL:

|Tipo de recurso|Número de pods com estado|Número necessário de volumes persistentes|
|---|---|---|
|Controlador de exibição|4 ( `control` , `controldb` , `logsdb` , `metricsdb` )|4 * 2 = 8|
|Instância Gerenciada do Azure SQL|1|2|
|Instância do banco de dados do Azure para PostgreSQL|1| 2|
|Hiperescala do PostgreSQL do Azure|1 + W (W = número de trabalhadores)|2 * (1 + W)|

A tabela a seguir mostra o número total de volumes persistentes necessários para uma implantação de exemplo:

|Tipo de recurso|Número de instâncias|Número necessário de volumes persistentes|
|---|---|---|
|Controlador de exibição|1|4 * 2 = 8|
|Instância Gerenciada do Azure SQL|5|5 * 2 = 10|
|Instância do banco de dados do Azure para PostgreSQL|5| 5 * 2 = 10|
|Hiperescala do PostgreSQL do Azure|2 (número de trabalhadores = 4 por instância)|2 * 2 * (1 + 4) = 20|
|***Número total de volumes persistentes***||8 + 10 + 10 + 20 = 48|

Esse cálculo pode ser usado para planejar o armazenamento de seu cluster kubernetes com base no provisionamento ou no ambiente de armazenamento. Por exemplo, se o provisionamento de armazenamento local for usado para um cluster kubernetes com cinco nós (5), para a implantação de exemplo acima, cada nó exigirá pelo menos armazenamento para 10 volumes persistentes. Da mesma forma, ao provisionar um cluster AKS (serviço de kubernetes do Azure) com cinco nós (5), é essencial escolher um tamanho de VM apropriado para o pool de nós, de modo que 10 discos de dados possam ser anexados. Mais detalhes sobre como dimensionar os nós para as necessidades de armazenamento para nós AKS podem ser encontrados [aqui](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs).

## <a name="choosing-the-right-storage-class"></a>Escolhendo a classe de armazenamento correta

### <a name="on-premises-and-edge-sites"></a>Sites locais e de borda

A Microsoft e seus parceiros OEM, so e kubernetes estão trabalhando em um programa de certificação para os serviços de dados de arco do Azure. Este programa fornecerá aos clientes resultados de teste comparáveis de um kit de ferramentas de teste de certificação. Os testes avaliarão a compatibilidade de recursos, os resultados de testes de estresse e o desempenho e a escalabilidade. Cada um desses resultados de teste indicará o sistema operacional usado, a distribuição kubernetes usada, o HW usado, o complemento do CSI usado e as classes de armazenamento usadas. Isso ajudará os clientes a escolher a melhor classe de armazenamento, o sistema operacional, a distribuição kubernetes e o HW para seus requisitos. Mais informações sobre esse programa e os resultados iniciais do teste serão fornecidos mais perto da disponibilidade geral dos serviços de dados de arco do Azure.

#### <a name="public-cloud-managed-kubernetes-services"></a>Nuvem pública, serviços Kubernetess gerenciados

Para serviços de kubernetes gerenciados baseados em nuvem pública, podemos fazer as seguintes recomendações:

|Serviço de nuvem pública|Recomendação|
|---|---|
|**AKS (Serviço de Kubernetes do Azure)**|O AKS (serviço kubernetes do Azure) tem dois tipos de armazenamento-arquivos do Azure e Managed Disks do Azure. Cada tipo de armazenamento tem dois níveis de preço/desempenho-padrão (HDD) e Premium (SSD). Portanto, as quatro classes de armazenamento fornecidas em AKS são `azurefile` (camada padrão de arquivos do Azure), `azurefile-premium` (camada Premium de arquivos do Azure), `default` (camada Standard de discos do Azure) e `managed-premium` (camada Premium de discos do Azure). A classe de armazenamento padrão é `default` (camada padrão de discos do Azure). Há diferenças de **[preço](https://azure.microsoft.com/en-us/pricing/details/storage/)** substanciais entre os tipos e as camadas que devem ser fatorados em sua decisão. Para cargas de trabalho de produção com requisitos de alto desempenho, é recomendável usar `managed-premium` para todas as classes de armazenamento. Para cargas de trabalho de desenvolvimento/teste, provas de conceito, etc., em que o custo é uma consideração, `azurefile` a opção é menos cara. Todas as quatro opções podem ser usadas para situações que exigem armazenamento remoto e compartilhado, pois são todos os dispositivos de armazenamento conectados à rede no Azure. Leia mais sobre o [armazenamento AKs](../../aks/concepts-storage.md).|
|**AWS Elastic Kubernetes Service (EKS)**| O serviço elástico kubernetes da Amazon tem uma classe de armazenamento principal baseada no [Driver de armazenamento do EBS CSI](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html). Isso é recomendado para cargas de trabalho de produção. Há um novo driver de armazenamento- [Driver de armazenamento do EFS CSI](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) -que pode ser adicionado a um cluster EKS, mas ele está em um estágio Beta e está sujeito a alterações. Embora AWS informe que esse driver de armazenamento tem suporte para produção, não é recomendável usá-lo porque ele ainda está na versão beta e está sujeito a alterações. A classe de armazenamento EBS é o padrão e é chamada `gp2` . Leia mais sobre o [armazenamento EKS](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html).|
|**Google Kubernetes Engine (GKE)**|O mecanismo do Google kubernetes (GKE) tem apenas uma classe `standard` de armazenamento chamada, que é usada para [discos persistentes de GCE](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk). Sendo o único, ele também é o padrão. Embora haja um [provisionamento de volume estático e local](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner) para GKE que você pode usar com o SSDs com conexão direta, não é recomendável usá-lo, pois ele não é mantido nem suportado pelo Google. Leia mais sobre o [armazenamento GKE](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes).
