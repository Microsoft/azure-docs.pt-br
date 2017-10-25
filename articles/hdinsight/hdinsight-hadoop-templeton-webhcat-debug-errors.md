---
title: "Entenda e resolva erros do WebHCat no HDInsight – Azure | Microsoft Docs"
description: "Saiba sobre erros comuns retornados pelo WebHCat no HDInsight e como resolvê-los."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1b3d94b1-207d-4550-aece-21dc45485549
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2017
ms.author: larryfr
ms.openlocfilehash: 1e4d72540a44f3b1838b6ed4dfad47dbe84489dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Entenda e resolva erros recebidos do WebHCat no HDInsight

Saiba mais sobre erros recebidos ao usar o WebHCat com HDInsight e como resolvê-los. WebHCat é usado internamente por ferramentas de cliente como o Azure PowerShell e as Ferramentas do Data Lake para Visual Studio.

## <a name="what-is-webhcat"></a>O que é o WebHCat

O [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) é uma API REST para o [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), uma camada de gerenciamento de armazenamento e tabela para Hadoop. O WebHCat é habilitado por padrão em clusters do HDInsight e é usado por várias ferramentas para enviar trabalhos, obter o status do trabalho etc. sem logon no cluster.

## <a name="modifying-configuration"></a>Modificando a configuração

> [!IMPORTANT]
> Vários dos erros listados neste documento ocorrerem porque um máximo configurado foi excedido. Quando a etapa de solução menciona que você pode alterar um valor, use um dos seguintes para realizar a alteração:

* Para clusters do **Windows** : use uma ação de script para configurar o valor durante a criação do cluster. Para obter mais informações, consulte [Desenvolver ações de script](hdinsight-hadoop-script-actions.md).

* Para clusters do **Linux** : use o Ambari (API REST ou Web) para modificar o valor. Para obter mais informações, consulte [Gerenciar o HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md)

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

## <a name="bad-request-content-could-not-find-job"></a>Conteúdo de solicitação incorreto: não foi possível encontrar o trabalho

**Código de status HTTP**: 400

| Causa | Resolução |
| --- | --- |
| Os detalhes do trabalho foram apagados pelo limpador de histórico de trabalhos |O período de retenção padrão do histórico de trabalhos é de 7 dias. O período de retenção padrão pode ser alterado modificando `mapreduce.jobhistory.max-age-ms`. Para obter mais informações, consulte [Modificar a configuração](#modifying-configuration) |
| O trabalho foi encerrado devido a um failover |Repita o envio do trabalho em até dois minutos |
| Foi usada uma ID de trabalho inválida |Verifique se a ID de trabalho está correta |

## <a name="bad-gateway"></a>Gateway inválido

**Código de status HTTP**: 502

| Causa | Resolução |
| --- | --- |
| A coleta de lixo interna está ocorrendo no processo do WebHCat |Aguarde até que a coleta de lixo seja concluída ou reinicie o serviço do WebHCat |
| Tempo limite atingido ao aguardar uma resposta do serviço ResourceManager. Esse erro pode ocorrer quando o número de aplicativos ativos atinge o máximo configurado (padrão de 10.000) |Aguarde até que os trabalhos em execução no momento sejam concluídos ou aumente o limite de trabalhos simultâneos modificando `yarn.scheduler.capacity.maximum-applications`. Para obter mais informações, consulte a seção [Modificar a configuração](#modifying-configuration). |
| Tentar recuperar todos os trabalhos por meio da chamada [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) quando `Fields` está definido como `*` |Não recupere *todos* os detalhes do trabalho. Em vez disso, use `jobid` para recuperar detalhes somente de trabalhos maiores que determinada ID de trabalho. Ou não use `Fields` |
| O serviço do WebHCat está inativo durante o failover do HeadNode |Aguarde dois minutos e repita a operação |
| Há mais de 500 trabalhos pendentes enviados por meio do WebHCat |Aguarde até que os trabalhos pendentes no momento sejam concluídos antes de enviar mais trabalhos |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
