---
title: Entenda e resolva erros do WebHCat no Azure HDInsight
description: Saiba sobre erros comuns retornados pelo WebHCat no HDInsight e como resolvê-los.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 683580ba65ad775ccec105c78cc1af66fbb63c37
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761116"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Entenda e resolva erros recebidos do WebHCat no HDInsight

Saiba mais sobre erros recebidos ao usar o WebHCat com HDInsight e como resolvê-los. WebHCat é usado internamente por ferramentas de cliente como o Azure PowerShell e as Ferramentas do Data Lake para Visual Studio.

## <a name="what-is-webhcat"></a>O que é o WebHCat

O [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) é uma API REST para o [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), uma camada de gerenciamento de armazenamento e tabela para Apache Hadoop. O WebHCat é habilitado por padrão em clusters do HDInsight e é usado por várias ferramentas para enviar trabalhos, obter o status do trabalho etc. sem logon no cluster.

## <a name="modifying-configuration"></a>Modificando a configuração

> [!IMPORTANT]  
> Vários dos erros listados neste documento ocorrerem porque um máximo configurado foi excedido. Quando a etapa de solução menciona que você pode alterar um valor, use um dos seguintes para realizar a alteração:

* Para clusters do **Windows**: use uma ação de script para configurar o valor durante a criação do cluster. Para obter mais informações, consulte [Desenvolver ações de script](hdinsight-hadoop-script-actions-linux.md).

* Para clusters do **Linux**: use o Apache Ambari (API REST ou Web) para modificar o valor. Para obter mais informações, consulte [Gerenciar o HDInsight usando o Apache Ambari](hdinsight-hadoop-manage-ambari.md)

> [!IMPORTANT]  
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

### <a name="default-configuration"></a>Configuração padrão

Se os seguintes valores padrão forem excedidos, isso poderá prejudicar o desempenho do WebHCat ou causar erros:

| Configuração | O que faz | Valor padrão |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |O número máximo de trabalhos que podem estar ativos ao mesmo tempo (em execução ou pendentes) |10.000 |
| [templeton.exec.max-procs][max-procs] |O número máximo de solicitações que podem ser atendidas simultaneamente |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |O número de dias pelos quais o histórico de trabalhos é mantido |7 dias |

## <a name="too-many-requests"></a>Número excessivo de solicitações

**Código de status HTTP**: 429

| Causa | Resolução |
| --- | --- |
| Você excedeu o máximo de solicitações simultâneas atendidas pelo WebHCat por minuto (padrão de 20) |Reduza a carga de trabalho para garantir que não sejam enviadas mais do que o número máximo de solicitações simultâneas ou aumente o limite de solicitações simultâneas modificando `templeton.exec.max-procs`. Para obter mais informações, consulte [Modificar a configuração](#modifying-configuration) |

## <a name="server-unavailable"></a>Servidor indisponível

**Código de status HTTP**: 503

| Causa | Resolução |
| --- | --- |
| Esse código de status geralmente ocorre durante o failover entre o nó de cabeçalho primário e secundário para o cluster |Aguarde dois minutos e repita a operação |

## <a name="bad-request-content-could-not-find-job"></a>Conteúdo de solicitação inválido: Não foi possível localizar o trabalho

**Código de status HTTP**: 400

| Causa | Resolução |
| --- | --- |
| Os detalhes do trabalho foram apagados pelo limpador de histórico de trabalhos |O período de retenção padrão do histórico de trabalhos é de 7 dias. O período de retenção padrão pode ser alterado modificando `mapreduce.jobhistory.max-age-ms`. Para obter mais informações, consulte [Modificar a configuração](#modifying-configuration) |
| O trabalho foi encerrado devido a um failover |Repita o envio do trabalho em até dois minutos |
| Uma ID de trabalho inválido foi usada |Verifique se a ID do trabalho está correta |

## <a name="bad-gateway"></a>Gateway inválido

**Código de status HTTP**: 502

| Causa | Resolução |
| --- | --- |
| A coleta de lixo interna está ocorrendo no processo do WebHCat |Aguarde até que a coleta de lixo seja concluída ou reinicie o serviço do WebHCat |
| Tempo limite atingido ao aguardar uma resposta do serviço ResourceManager. Esse erro pode ocorrer quando o número de aplicativos ativos atinge o máximo configurado (padrão de 10.000) |Aguarde até que os trabalhos em execução no momento sejam concluídos ou aumente o limite de trabalhos simultâneos modificando `yarn.scheduler.capacity.maximum-applications`. Para obter mais informações, consulte a seção [Modificar a configuração](#modifying-configuration). |
| Tentar recuperar todos os trabalhos por meio da chamada [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) quando `Fields` está definido como `*` |Não recupere *todos* os detalhes do trabalho. Em vez disso use `jobid` para recuperar detalhes de trabalhos somente maiores que determinadas ID do trabalho. Ou não use `Fields` |
| O serviço do WebHCat está inativo durante o failover do HeadNode |Aguarde dois minutos e repita a operação |
| Há mais de 500 trabalhos pendentes enviados por meio do WebHCat |Aguarde até que os trabalhos pendentes no momento sejam concluídos antes de enviar mais trabalhos |

[maximum-applications]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
