---
title: Migrar cargas de trabalho de Hive do Azure HDInsight 3.6 HDInsight 4.0
description: Saiba como migrar cargas de trabalho Apache Hive no HDInsight 3.6 HDInsight 4.0.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/15/2019
ms.openlocfilehash: 144e0ada0ce3a15a5dcd13b31f46f50162750a74
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126555"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrar cargas de trabalho de Hive do Azure HDInsight 3.6 HDInsight 4.0

Este documento mostra como migrar cargas de trabalho do Apache Hive e LLAP no HDInsight 3.6 HDInsight 4.0. HDInsight 4.0 fornece recursos mais recentes de Hive e LLAP como Exibições materializadas e cache de resultado da consulta. Ao migrar suas cargas de trabalho para HDInsight 4.0, você pode usar muitos recursos mais recentes do Hive 3 que não estão disponíveis no HDInsight 3.6.

Este artigo aborda os seguintes assuntos:

* Migração de metadados de Hive para HDInsight 4.0
* Migração segura de transações ACID e tabelas não ACID
* Preservação da seção de políticas de segurança entre as versões do HDInsight
* Execução de consulta e de depuração de HDInsight 3.6 para HDInsight 4.0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Migrar metadados Apache Hive para HDInsight 4.0

Uma vantagem do Hive é a capacidade de exportar metadados para um banco de dados externo (conhecido como o Metastore do Hive). O **Metastore Hive** é responsável por armazenar as estatísticas de tabela, incluindo o local de armazenamento de tabela, nomes de coluna e informações de índice de tabela. O esquema de banco de dados do metastore é diferente entre as versões do Hive. Faça o seguinte para atualizar um Metastore do Hive do HDInsight 3.6 para que ele seja compatível com o HDInsight 4.0.

1. Crie uma nova cópia do seu metastore externo. A versão 3.6 do HDInsight e HDInsight 4.0 exigem metastore diferentes esquemas e não é possível compartilhar um único metastore.
1. Anexe a nova cópia do metastore a) um cluster HDInsight 4.0 existente ou b) um cluster que você está criando pela primeira vez. Ver [usar armazenamentos de metadados externos no Azure HDInsight](../hdinsight-use-external-metadata-stores.md) para saber mais sobre como anexar um metastore externo a um cluster HDInsight. Depois que o Metastore é anexado, ele será convertido automaticamente em um metastore 4.0 compatível.

> [!Warning]
> A atualização que converte o esquema de metadados 3.6 do HDInsight para o esquema de HDInsight 4.0, não poderá ser revertida.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrar de tabelas do Hive para HDInsight 4.0

Depois de concluir o conjunto anterior de etapas para migrar o Metastore do Hive para HDInsight 4.0, as tabelas e bancos de dados registrados no metastore será visíveis de dentro do cluster HDInsight 4.0 executando `show tables` ou `show databases` de dentro do cluster . Ver [execução da consulta entre as versões do HDInsight](#query-execution-across-hdinsight-versions) para obter informações sobre a execução da consulta em clusters de HDInsight 4.0.

Os dados reais das tabelas, no entanto, não estão acessíveis até que o cluster tem acesso às contas de armazenamento necessário. Para garantir que seu cluster HDInsight 4.0 pode acessar os mesmos dados que seu cluster HDInsight 3.6 antigo, conclua as seguintes etapas:

1. Determinar a conta de armazenamento do Azure da sua tabela ou banco de dados usando descrevem formatado.
2. Se seu cluster HDInsight 4.0 já está em execução, anexe a conta de armazenamento do Azure para o cluster por meio do Ambari. Se você ainda não criou o cluster HDInsight 4.0, verifique se que a conta de armazenamento do Azure é especificada como a primária ou uma conta de armazenamento de cluster secundário. Para obter mais informações sobre como adicionar contas de armazenamento para clusters de HDInsight, consulte [adicionar outras contas de armazenamento ao HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Tabelas são tratadas de maneira diferente no HDInsight 3.6 e 4.0 do HDInsight. Por esse motivo, é possível compartilhar as mesmas tabelas para clusters de versões diferentes. Se você quiser usar o HDInsight 3.6 ao mesmo tempo como HDInsight 4.0, você deve ter cópias separadas dos dados para cada versão.

Sua carga de trabalho do Hive pode incluir uma combinação de ACID e tabelas não ACID. Uma diferença importante entre o Hive no HDInsight 3.6 (seção 2) e o Hive no HDInsight 4.0 (seção 3) é a conformidade de ACID para tabelas. No HDInsight 3.6, permitindo a Hive ACID-conformidade requer configuração adicional, mas no HDInsight 4.0 tabelas são compatíveis com ACID por padrão. A única ação necessária antes da migração é executar uma compactação de principal com base na tabela ACID no cluster 3.6. Da exibição do Hive ou do Beeline, execute a seguinte consulta:

```bash
alter table myacidtable compact 'major';
```

Essa compactação é necessária porque a versão 3.6 do HDInsight e HDInsight 4.0 ACID tabelas entender deltas ACID diferente. A compactação impõe uma ficha limpa que garante a consistência. Seção 4 do [documentação de migração do Hive](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) contém orientações para a compactação em massa de tabelas de ACID do HDInsight 3.6.

Depois de concluir as etapas de migração e a compactação de metastore, você pode migrar o depósito real. Depois de concluir a migração de warehouse do Hive, o depósito 4.0 HDInsight terá as seguintes propriedades:

* Tabelas externas no HDInsight 3.6 será tabelas externas no HDInsight 4.0
* As tabelas não transacional de gerenciado no HDInsight 3.6 estarão tabelas externas no HDInsight 4.0
* Transacionais tabelas gerenciadas no HDInsight 3.6 será tabelas gerenciadas no HDInsight 4.0

Talvez você precise ajustar as propriedades do warehouse antes de executar a migração. Por exemplo, se você espera que alguns tabela será acessada por terceiros (por exemplo, um cluster HDInsight 3.6), essa tabela deve ser externa quando a migração for concluída. No HDInsight 4.0, todas as tabelas gerenciadas são transacionais. Portanto, tabelas gerenciadas no HDInsight 4.0 só devem ser acessadas pelos clusters HDInsight 4.0.

Depois que as propriedades da tabela estão definidas corretamente, execute a ferramenta de migração de warehouse do Hive de um de nós de cabeçalho de cluster usando o shell SSH:

1. Conecte-se ao seu nó principal do cluster usando SSH. Para obter instruções, consulte [conectar-se ao HDInsight usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Abra um shell de logon do usuário do Hive, executando `sudo su - hive`
1. Determinar a versão do Hortonworks Data Platform pilha executando `ls /usr/hdp`. Isso exibirá uma cadeia de caracteres de versão que você deve usar o próximo comando.
1. Execute o seguinte comando do shell. Substitua `${{STACK_VERSION}}` com a cadeia de caracteres de versão da etapa anterior:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true  -m automatic  automatic  --modifyManagedTables --oldWarehouseRoot /apps/hive/warehouse
```

Depois que a ferramenta de migração for concluída, seu Hive warehouse estará pronto para o HDInsight 4.0. 

> [!Important]
> Tabelas gerenciadas no 4.0 HDInsight (incluindo tabelas migradas do 3.6) não devem ser acessadas por outros serviços ou aplicativos, incluindo clusters de HDInsight 3.6.

## <a name="secure-hive-across-hdinsight-versions"></a>Proteja o Hive no HDInsight versões

Desde o HDInsight 3.6, HDInsight integra-se com o Azure Active Directory usando o pacote de segurança de Enterprise HDInsight (ESP). ESP usa Kerberos e Apache Ranger para gerenciar as permissões de recursos específicos dentro do cluster. Políticas do Ranger implantadas no Hive no HDInsight 3.6 podem ser migradas para o HDInsight 4.0 com as seguintes etapas:

1. Navegue até o painel do Ranger Service Manager no seu cluster HDInsight 3.6.
2. Navegue até a diretiva denominada **HIVE** e exportar a política para um arquivo json.
3. Certifique-se de que todos os usuários chamados em json política exportada existem no novo cluster. Se um usuário é chamado no json de política, mas não existe no novo cluster, adicione o usuário para o novo cluster, ou remova a referência da política.
4. Navegue até a **Ranger Service Manager** painel no seu cluster HDInsight 4.0.
5. Navegue até a diretiva denominada **HIVE** e importe o json de política do ranger da etapa 2.

## <a name="query-execution-across-hdinsight-versions"></a>Execução de consulta entre versões do HDInsight

Há duas maneiras de executar e depurar consultas do Hive/LLAP dentro de um cluster HDInsight 3.6. HiveCLI fornece uma experiência de linha de comando e o modo de exibição do Tez exibição/Hive fornece um fluxo de trabalho baseado em GUI.

No HDInsight 4.0, HiveCLI foi substituído com o Beeline. HiveCLI é um cliente de thrift do Hiveserver 1 e o Beeline é um cliente JDBC que fornece acesso ao Hiveserver 2. Beeline também pode ser usado para se conectar a qualquer outro ponto de extremidade de banco de dados de compatível com JDBC. Beeline é disponível out-of-box no HDInsight 4.0 sem qualquer instalação necessária.

No HDInsight 3.6, o cliente de GUI para interagir com o servidor do Hive é o modo de exibição de Hive do Ambari. HDInsight 4.0 substitui a exibição do Hive com Hortonworks Data Analytics Studio (DAS). DAS não é fornecida com clusters de HDInsight out-of-box e não é um pacote com suporte oficial. No entanto, DAS pode ser instaladas da seguinte maneira no cluster:

Inicie uma ação de script em relação a seu cluster, com "Nós de cabeça" como o tipo de nó para execução. Cole o URI a seguir na caixa de texto marcado como "URI do Script Bash": https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh



Depois que o DAS é instalada, se você não vir as consultas que você executou no Visualizador de consultas, execute as seguintes etapas:

1. Defina as configurações para o Hive, o Tez e DAS conforme descrito em [deste guia para solução de problemas de instalação DAS](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Certifique-se de que as seguintes configurações de diretório armazenamento do Azure são blobs de página e estão listados sob `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Reinicie o HDFS, Hive, o Tez e em ambos os de cabeçalho.

## <a name="next-steps"></a>Próximos passos

* [Comunicado de HDInsight 4.0](../hdinsight-version-release.md)
* [Aprofundamento de HDInsight 4.0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [3 tabelas ACID do hive](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
