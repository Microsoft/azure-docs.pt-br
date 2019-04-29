---
title: Recuperação de desastre regional para o Azure Databricks
description: Este artigo descreve uma abordagem para realizar a recuperação de desastre no Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: bd91d9201e81c884b48b41de27146c186eeb9598
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784650"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Recuperação de desastre regional para clusters do Azure Databricks

Este artigo descreve uma arquitetura de recuperação de desastre útil para clusters do Azure Databricks e as etapas para obter esse design.

## <a name="azure-databricks-architecture"></a>Arquitetura do Azure Databricks

Em um alto nível, ao criar um workspace do Azure Databricks do Portal do Azure, um [dispositivo gerenciado](../managed-applications/overview.md) é implantado como um recurso do Azure em sua assinatura, na região do Azure escolhida (por exemplo, Oeste dos EUA). Esse dispositivo é implantado em uma [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) com um [Grupo de Segurança de Rede](../virtual-network/manage-network-security-group.md) e uma conta de Armazenamento do Azure, disponível em sua assinatura. A rede virtual oferece segurança em nível de perímetro ao workspace do Databricks e é protegida por meio do grupo de segurança de rede. Dentro do workspace, é possível criar clusters do Databricks fornecendo ao trabalhador e ao driver o tipo de VM e a versão de tempo de execução do Databricks. Os dados persistentes estão disponíveis em sua conta de armazenamento, que pode ser Armazenamento de Blobs do Azure ou Azure Data Lake Storage. Após a criação do cluster, será possível executar trabalhos por meio de pontos de extremidade ODBC/JDBC, APIs REST e blocos de anotações anexando-os a um cluster específico.

O painel de controle do Databricks gerencia e monitora o ambiente de workspace do Databricks. Qualquer operação de gerenciamento como criação de cluster será iniciada no painel de controle. Todos os metadados, como trabalhos agendados, são armazenados em um Banco de dados do Azure com replicação geográfica para obter tolerância a falhas.

![Arquitetura do Databricks](media/howto-regional-disaster-recovery/databricks-architecture.png)

Uma das vantagens dessa arquitetura é que os usuários podem conectar o Azure Databricks a qualquer recurso de armazenamento em sua conta. Um benefício importante é que a computação (Azure Databricks) e o armazenamento podem ser dimensionados independentemente uns dos outros.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Como criar uma topologia de recuperação de desastre regional

Como você observar na descrição da arquitetura acima, há um número de componentes usados para um pipeline de Big Data com o Azure Databricks:  O armazenamento do Azure, banco de dados do Azure e outras fontes de dados. O Azure Databricks é a *computação* para pipeline de Big Data. Ele é *efêmero* por natureza, o que significa que, embora seus dados ainda estejam disponíveis no armazenamento do Azure, a *computação* (cluster do Azure Databricks) poderá ser encerrada para que não seja necessário pagar pela computação quando você ela não for necessária. A *computação* (Azure Databricks) e fontes de armazenamento devem estar na mesma região para que os trabalhos não apresentem alta latência.  

Para criar sua própria topologia de recuperação de desastre regional, siga estes requisitos:

   1. Provisione vários workspaces do Azure Databricks em regiões do Azure separadas. Por exemplo, crie o workspace primário do Azure Databricks no Leste dos EUA 2. Crie o workspace secundário do Azure Databricks para recuperação de desastre em uma região separada, como Oeste dos EUA.

   2. Use o [armazenamento com redundância geográfica](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage). Os dados associados ao Azure Databricks são armazenados no Armazenamento do Azure por padrão. Os resultados dos trabalhos do Databricks também são armazenados no Armazenamento de Blobs do Azure para que os dados processados sejam duráveis e permaneçam altamente disponíveis após a terminação do cluster. Como o cluster de Armazenamento e do Databricks estão colocalizados, é necessário usar o armazenamento com redundância geográfica para que os dados possam ser acessados na região secundária se a região primária não estiver mais acessível.

   3. Depois de criar a região secundária, será necessário migrar os usuários, pastas do usuário, blocos de notas, configuração do cluster, configuração de trabalhos, bibliotecas, armazenamento, scripts init e reconfigurar o controle de acesso. Mais detalhes são descritos na seguinte seção.

## <a name="detailed-migration-steps"></a>Etapas de migração detalhadas

1. **Configurar a interface de linha de comando do Databricks em seu computador**

   Este artigo mostra um número de exemplos de código que usam a interface de linha de comando para a maioria das etapas automatizadas, porque ela é um wrapper fácil de usar por meio da API REST do Azure Databricks.

   Antes de executar quaisquer etapas de migração, instale o databricks-cli em seu computador desktop ou em uma máquina virtual em que você planeja realizar o trabalho. Para obter mais informações, consulte [Install Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html) (Instalar CLI do Databricks)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Quaisquer scripts python fornecidos neste artigo devem funcionar com o Python 2.7+ < 3.x.

2. **Configurar dois perfis.**

   Configure um para o workspace primário e outro para o workspace secundário:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   Os blocos de código neste artigo alternam entre perfis em cada etapa subsequente usando o comando do workspace correspondente. Certifique-se de que os nomes dos perfis criados sejam substituídos em cada bloco de código.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   É possível alternar manualmente na linha de comando, se necessário:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Migrar usuários do Azure Active Directory**

   Adicione manualmente os mesmos usuários do Azure Active Directory ao workspace secundário que existe no workspace primário.

4. **Migrar as pastas de usuário e os blocos de anotações**

   Use o seguinte código do python para migrar os ambientes de usuário em sandbox, que incluem os blocos de anotações e a estrutura de pasta aninhada por usuário.

   > [!NOTE]
   > As bibliotecas não são copiadas nesta etapa, porque a API subjacente não dá suporte a elas.

   Copiar e salvar o seguinte script python em um arquivo e executá-lo em sua linha de comando do Databricks. Por exemplo, `python scriptname.py`.

   ```python
   from subprocess import call, check_output

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = user_list_out.splitlines()

   # Export sandboxed environment (folders, notebooks) for each user and import into new workspace.
   # Libraries are not included with these APIs / commands.

   for user in user_list:
     print "Trying to migrate workspace for user " + user

     call("mkdir -p " + user, shell=True)
     export_exit_status = call("databricks workspace export_dir /Users/" + user + " ./" + user + " --profile " + EXPORT_PROFILE, shell=True)

     if export_exit_status==0:
       print "Export Success"
       import_exit_status = call("databricks workspace import_dir ./" + user + " /Users/" + user + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print "Import Success"
       else:
         print "Import Failure"
     else:
       print "Export Failure"

   print "All done"
   ```

5. **Migrar as configurações de cluster**

   Após a migração dos blocos de notas, será possível, opcionalmente, migrar as configurações de cluster para o novo workspace. Usar o databricks-cli é quase uma etapa totalmente automatizada, a menos que você deseje realizar a migração de configuração de cluster seletiva em vez de para todos.

   > [!NOTE]
   > Infelizmente, não há nenhum ponto de extremidade de configuração de cluster de criação, e esse script tenta criar cada cluster imediatamente. Se não houver núcleos suficientes disponíveis em sua assinatura, a criação do cluster poderá falhar. A falha pode ser ignorada, contanto que a configuração seja transferida com êxito.

   O script fornecido a seguir imprime um mapeamento de IDs de cluster antigas para novas, o que poderia ser usado para migração de trabalhos posteriormente (para trabalhos configurados para usar clusters existentes).

   Copiar e salvar o seguinte script python em um arquivo e executá-lo em sua linha de comando do Databricks. Por exemplo, `python scriptname.py`.

   ```python
   from subprocess import call, check_output import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace 
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE]) clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids 
   clusters_list = [] for cluster_info in clusters_info_list:   clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements 
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf"," node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","sp ark_env_vars","autotermination_minutes","enable_elastic_disk"]

   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {} for cluster in clusters_list:   print "Trying to migrate cluster " + cluster

   cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
   print "Got cluster config from old workspace"

   # Remove extra content from the config, as we need to build create request with allowed elements only
   cluster_req_json = json.loads(cluster_get_out)    
   cluster_json_keys = cluster_req_json.keys()   

   for key in cluster_json_keys:     
      if key not in cluster_req_elems:       
         cluster_req_json.pop(key, None)
  
   # Create the cluster, and store the mapping from old to new cluster ids
   cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE]) 
   cluster_create_out_json = json.loads(cluster_create_out)   
   cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

   print "Sent cluster create request to new workspace successfully"

   print "Cluster mappings: " + json.dumps(cluster_old_new_mappings)
   print "All done"
   ```

6. **Migrar a configuração de trabalhos**

   Se você migrou configurações de cluster na etapa anterior, é possível optar por migrar configurações de trabalho para o novo workspace. Usar o databricks-cli é uma etapa totalmente automatizada, a menos que você deseje realizar uma migração de configuração de trabalho seletiva, em vez de fazer isso para todos os trabalhos.

   > [!NOTE]
   > A configuração de um trabalho agendado contém as informações do "agendamento" também; portanto, por padrão, ela começará a funcionar de acordo com o tempo configurado assim que for migrada. Portanto, o bloco de código a seguir remove quaisquer informações de agendamento durante a migração (para evitar execuções duplicadas entre workspaces antigos e novos). Configure os agendamentos para esses trabalhos depois que estiver pronto para a substituição.

   A configuração do trabalho requer configurações para um cluster novo ou um existente. Se estiver usando um cluster, o script/código abaixo tentará substituir a ID do cluster antigo pela nova.

   Copiar e salvar o seguinte script python para um arquivo. Substitua o valor de `old_cluster_id` e `new_cluster_id` pela saída da migração do cluster feita na etapa anterior. Execute-o em sua linha de comando databricks-cli, por exemplo, `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"old_cluster_id": "new_cluster_id"}

   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print "No jobs to migrate"
     sys.exit(0)

   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])

   # Optionally filter job ids out manually, so as to create only required ones in new workspace

   # Create each job in the new workspace based on corresponding settings in the old workspace

   for job in jobs_list:
     print "Trying to migrate " + job

     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print "Got job config from old workspace"

     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']

     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)

     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print "Mapping not available for old cluster id " + job_req_settings_json['existing_cluster_id']
         continue

     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print "Sent job create request to new workspace successfully"

   print "All done"
   ```

7. **Migrar bibliotecas**

   No momento, não há uma maneira simples de migrar bibliotecas de um workspace para outro. Em vez disso, reinstale essas bibliotecas no novo workspace manualmente. É possível automatizar usando a combinação da [CLI DBFS](https://github.com/databricks/databricks-cli#dbfs-cli-examples) para carregar bibliotecas personalizadas para o workspace e para a [CLI das bibliotecas](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Migrar o armazenamento de blobs do Azure e as montagens do Azure Data Lake Storage**

   Manualmente, monte novamente todos os [armazenamento de BLOBs do Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html) e [Azure Data Lake Store (Ger 2)](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) usando uma solução baseada no bloco de anotações de pontos de montagem. Os recursos de armazenamento teriam sido montados no workspace primário, e isso precisaria ser repetido no workspace secundário. Não há nenhuma API externa para montagens.

9. **Migrar scripts de inicialização de cluster**

   Quaisquer scripts de inicialização de cluster podem ser migrados do workspace antigo para o novo usando a [CLI DBFS](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Primeiro, copie os scripts necessários do `dbfs:/dat abricks/init/..` para a sua área de trabalho local ou máquina virtual. Em seguida, copie esses scripts para o novo workspace no mesmo caminho.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Reconfigurar manualmente e reaplicar o controle de acesso.**

    Se seu workspace primário existente for configurado para usar a camada Premium (SKU), você provavelmente também estará usando o [recurso de Controle de Acesso](https://docs.azuredatabricks.net/administration-guide/admin-settings/index.html#manage-access-control).

    Se você usar o recurso de Controle de acesso manualmente, reaplique o controle de acesso aos recursos (Blocos de anotações, clusters, trabalhos, tabelas).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Recuperação de desastres para a ecossistema do Azure

Se você estiver usando outros serviços do Azure, certifique-se de implementar as práticas recomendadas de recuperação de desastres para esses serviços, muito. Por exemplo, se você optar por usar uma instância de metastore do Hive externa, você deve considerar a recuperação de desastres para [SQL Server do Azure](../sql-database/sql-database-disaster-recovery.md), [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md), e/ou [banco de dados do Azure para MySQL ](../mysql/concepts-business-continuity.md). Para obter informações gerais sobre a recuperação de desastres, consulte [recuperação de desastre para aplicativos do Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte a [documentação do Azure Databricks](https://docs.azuredatabricks.net/user-guide/index.html).
