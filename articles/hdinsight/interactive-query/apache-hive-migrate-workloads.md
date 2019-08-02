---
title: Migrar cargas de trabalho do hive do Azure HDInsight 3,6 para o HDInsight 4,0
description: Saiba como migrar cargas de trabalho Apache Hive no HDInsight 3,6 para o HDInsight 4,0.
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: b9bf3b41bcd0a79027c5dd9a4f3df979fb0bd9f0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250162"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrar cargas de trabalho do hive do Azure HDInsight 3,6 para o HDInsight 4,0

Este documento mostra como migrar cargas de trabalho Apache Hive e LLAP no HDInsight 3,6 para o HDInsight 4,0. O HDInsight 4,0 fornece recursos mais recentes de Hive e LLAP, como exibições materializadas e cache de resultados de consultas. Ao migrar suas cargas de trabalho para o HDInsight 4,0, você pode usar muitos recursos mais recentes do hive 3 que não estão disponíveis no HDInsight 3,6.

Este artigo aborda os seguintes assuntos:

* Migração de metadados do hive para o HDInsight 4,0
* Migração segura de tabelas ACID e não ACID
* Preservação de políticas de segurança do hive em versões do HDInsight
* Execução de consulta e depuração do HDInsight 3,6 para o HDInsight 4,0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Migrar metadados de Apache Hive para o HDInsight 4,0

Uma vantagem do hive é a capacidade de exportar metadados para um banco de dados externo (chamado de metastore do hive). O **metastore do hive** é responsável por armazenar estatísticas de tabela, incluindo o local de armazenamento de tabela, nomes de coluna e informações de índice de tabela. O esquema de banco de dados do metastore difere entre as versões do hive. Faça o seguinte para atualizar um metastore do hive do HDInsight 3,6 para que ele seja compatível com o HDInsight 4,0.

1. Crie uma nova cópia do seu metastore externo. O HDInsight 3,6 e o HDInsight 4,0 exigem esquemas de metastore diferentes e não podem compartilhar um único metastore. Consulte [usar repositórios de metadados externos no Azure HDInsight](../hdinsight-use-external-metadata-stores.md) para saber mais sobre como anexar um metastore externo a um cluster HDInsight. 
2. Inicie uma ação de script em seu cluster do HDI 3,6, com "nós de cabeçalho" como o tipo de nó para execução. Cole o URI a seguir na caixa de texto marcada como "URI do https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh script bash":. Na caixa de texto marcada como "Arguments", insira o ServerName, o banco de  dados, o nome de usuário e a senha para o metastore do hive copiado, separados por espaços. Não inclua ". database.windows.net" ao especificar o servername.

> [!Warning]
> A atualização que converte o esquema de metadados do HDInsight 3,6 para o esquema do HDInsight 4,0 não pode ser revertida.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrar tabelas do hive para o HDInsight 4,0

Depois de concluir o conjunto anterior de etapas para migrar o metastore do hive para o HDInsight 4,0, as tabelas e os bancos de dados registrados no metastore estarão visíveis no cluster HDInsight 4,0 executando `show tables` ou `show databases` de dentro do cluster . Confira a [execução da consulta em versões do hdinsight](#query-execution-across-hdinsight-versions) para obter informações sobre a execução da consulta em clusters do hdinsight 4,0.

No entanto, os dados reais das tabelas não ficam acessíveis até que o cluster tenha acesso às contas de armazenamento necessárias. Para garantir que seu cluster HDInsight 4,0 possa acessar os mesmos dados que o cluster HDInsight 3,6 antigo, conclua as seguintes etapas:

1. Determine a conta de armazenamento do Azure da tabela ou do banco de dados usando descrever formatado.
2. Se o cluster HDInsight 4,0 já estiver em execução, anexe a conta de armazenamento do Azure ao cluster via Ambari. Se você ainda não criou o cluster HDInsight 4,0, verifique se a conta de armazenamento do Azure está especificada como a conta de armazenamento de cluster primária ou secundária. Para obter mais informações sobre como adicionar contas de armazenamento a clusters HDInsight, consulte [Adicionar contas de armazenamento adicionais ao HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> As tabelas são tratadas de forma diferente no HDInsight 3,6 e no HDInsight 4,0. Por esse motivo, você não pode compartilhar as mesmas tabelas para clusters de versões diferentes. Se você quiser usar o HDInsight 3,6 ao mesmo tempo que o HDInsight 4,0, deverá ter cópias separadas dos dados para cada versão.

A carga de trabalho do hive pode incluir uma mistura de tabelas ACID e não ACID. Uma das principais diferenças entre o hive no HDInsight 3,6 (Hive 2) e o hive no HDInsight 4,0 (Hive 3) é a conformidade com ACID para tabelas. No HDInsight 3,6, a habilitação do hive ACID-Compliance requer configuração adicional, mas nas tabelas do HDInsight 4,0 estão em conformidade com ACID por padrão. A única ação necessária antes da migração é executar uma compactação principal em relação à tabela ACID no cluster 3,6. Na exibição do hive ou em beeline, execute a seguinte consulta:

```bash
alter table myacidtable compact 'major';
```

Essa compactação é necessária porque as tabelas ACID do HDInsight 3,6 e do HDInsight 4,0 compreendem os deltas ACID de forma diferente. A compactação impõe um Slate limpo que garante a consistência. A seção 4 da [documentação de migração do hive](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) contém diretrizes para compactação em massa de tabelas Acid 3,6 do HDInsight.

Depois de concluir as etapas de migração e compactação do metastore, você pode migrar o depósito real. Depois de concluir a migração do depósito do hive, o depósito 4,0 do HDInsight terá as seguintes propriedades:

* As tabelas externas no HDInsight 3,6 serão tabelas externas no HDInsight 4,0
* Tabelas gerenciadas não transacionais no HDInsight 3,6 serão tabelas externas no HDInsight 4,0
* As tabelas gerenciadas transacionais no HDInsight 3,6 serão tabelas gerenciadas no HDInsight 4,0

Talvez seja necessário ajustar as propriedades do seu depósito antes de executar a migração. Por exemplo, se você espera que alguma tabela seja acessada por terceiros (como um cluster HDInsight 3,6), essa tabela deverá ser externa quando a migração for concluída. No HDInsight 4,0, todas as tabelas gerenciadas são transacionais. Portanto, as tabelas gerenciadas no HDInsight 4,0 só devem ser acessadas por clusters HDInsight 4,0.

Depois que as propriedades da tabela forem definidas corretamente, execute a ferramenta de migração de depósito do hive de um dos cabeçalho de cluster usando o Shell SSH:

1. Conecte-se ao seu cabeçalho de cluster usando SSH. Para obter instruções, consulte [conectar-se ao HDInsight usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Abra um shell de logon como o usuário do hive executando`sudo su - hive`
1. Determine a versão da pilha de plataformas de dados `ls /usr/hdp`do Hortonworks executando. Isso exibirá uma cadeia de caracteres de versão que você deve usar no próximo comando.
1. Execute o comando a seguir no Shell. Substituir `${{STACK_VERSION}}` pela cadeia de caracteres da versão da etapa anterior:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true  -m automatic  automatic  --modifyManagedTables --oldWarehouseRoot /apps/hive/warehouse
```

Após a conclusão da ferramenta de migração, o seu depósito do hive estará pronto para o HDInsight 4,0. 

> [!Important]
> Tabelas gerenciadas no HDInsight 4,0 (incluindo tabelas migradas de 3,6) não devem ser acessadas por outros serviços ou aplicativos, incluindo clusters HDInsight 3,6.

## <a name="secure-hive-across-hdinsight-versions"></a>Proteger o hive em versões do HDInsight

Desde o HDInsight 3,6, o HDInsight integra-se com Azure Active Directory usando o Enterprise Security Package do HDInsight (ESP). O ESP usa o Kerberos e o Apache Ranger para gerenciar as permissões de recursos específicos no cluster. As políticas de Ranger implantadas no hive no HDInsight 3,6 podem ser migradas para o HDInsight 4,0 com as seguintes etapas:

1. Navegue até o painel de Service Manager do Ranger no cluster do HDInsight 3,6.
2. Navegue até a política chamada **Hive** e exporte a política para um arquivo JSON.
3. Certifique-se de que todos os usuários referenciados no JSON da política exportada existam no novo cluster. Se um usuário for referenciado na política JSON, mas não existir no novo cluster, adicione o usuário ao novo cluster ou remova a referência da política.
4. Navegue até o painel de **Service Manager do Ranger** no cluster do HDInsight 4,0.
5. Navegue até a política chamada **Hive** e importe a política do Ranger JSON da etapa 2.

## <a name="query-execution-across-hdinsight-versions"></a>Execução de consulta em versões do HDInsight

Há duas maneiras de executar e depurar consultas Hive/LLAP em um cluster HDInsight 3,6. O HiveCLI fornece uma experiência de linha de comando e o modo de exibição tez/Hive fornece um fluxo de trabalho baseado em GUI.

No HDInsight 4,0, o HiveCLI foi substituído por Beeline. HiveCLI é um cliente Thrift para Hiveserver 1 e beeline é um cliente JDBC que fornece acesso ao Hiveserver 2. Beeline também pode ser usado para se conectar a qualquer outro ponto de extremidade de banco de dados compatível com JDBC. O beeline está disponível de fora da caixa no HDInsight 4,0 sem nenhuma instalação necessária.

No HDInsight 3,6, o cliente de GUI para interagir com o servidor do hive é a exibição do hive do Ambari. O HDInsight 4,0 substitui a exibição do hive pelo Hortonworks data Analytics Studio (DAS). O DAS não é fornecido com clusters HDInsight prontos para uso e não é um pacote oficialmente com suporte. No entanto, o DAS pode ser instalado no cluster da seguinte maneira:

Inicie uma ação de script no cluster, com "nós de cabeçalho" como o tipo de nó para execução. Cole o seguinte URI na caixa de texto marcada como "URI do script bash": https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

O data Analytics Studio pode ser iniciado com a URL\<: https://ClusterName >. azurehdinsight. net/das/



Quando o DAS estiver instalado, se você não vir as consultas que você executou no Visualizador de consultas, execute as seguintes etapas:

1. Defina as configurações de Hive, tez e DAS, conforme descrito neste [guia para solução de problemas de instalação do das](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Verifique se as seguintes configurações de diretório do armazenamento do Azure são blobs de página e se estão listadas `fs.azure.page.blob.dirs`em:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Reinicie HDFS, Hive, tez e DAS em ambos os cabeçalho.

## <a name="next-steps"></a>Próximas etapas

* [Comunicado do HDInsight 4,0](../hdinsight-version-release.md)
* [Aprofundamento no HDInsight 4,0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3-tabelas ACID](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
