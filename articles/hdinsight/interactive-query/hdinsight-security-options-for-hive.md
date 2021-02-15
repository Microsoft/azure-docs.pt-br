---
title: Opções de segurança para o hive no Azure HDInsight
description: Opções de segurança para o hive em clusters padrão e ESP.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: 1189a320d0dc700756c9f7664d0a6303be5dab51
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940901"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Opções de segurança para o hive no Azure HDInsight

Este documento descreve as opções de segurança recomendadas para o hive no HDInsight. Essas opções podem ser configuradas por meio de Ambari.

![' Opções de segurança para o hive '](./media/hdinsight-security-options-for-hive/security-options-hive.png "Opções de segurança para o hive")

## <a name="hiveserver2-authentication"></a>Autenticação HiveServer2

Para clusters padrão, a configuração recomendada para autenticação HiveServer2 é o padrão, que é nenhum. Para habilitar a autenticação, é recomendável atualizar para um cluster [ESP](../domain-joined/hdinsight-security-overview.md) (Enterprise Security Package). 

Para clusters ESP, a autenticação [Kerberos](https://web.mit.edu/Kerberos/) é habilitada por padrão. Não há suporte para módulos de autenticação conectável (PAM) e esquemas de autenticação personalizados.

## <a name="hiveserver2-authorization"></a>Autorização de HiveServer2

Para clusters padrão, a configuração padrão é None. [SqlStdAuth (autorização baseada em padrões do SQL)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) pode ser habilitada. Não há suporte para autorização por meio do [Apache Ranger](https://ranger.apache.org/) para clusters padrão. É recomendável atualizar para um cluster ESP para autorização do Ranger. 

Para clusters ESP, a autorização por meio do Ranger é habilitada por padrão. 


## <a name="ssl-encryption-for-hiveserver2"></a>Criptografia SSL para HiveServer2

Habilitar o SSL Hiveserver2 não é recomendado para clusters padrão ou ESP. Em vez disso, o SSL é habilitado no gateway. A [criptografia em trânsito](../domain-joined/encryption-in-transit.md) pode ser habilitada para criptografar comunicações entre os nós de cluster usando o [IPSec (Internet Protocol Security)](https://en.wikipedia.org/wiki/IPsec).


## <a name="next-steps"></a>Próximas etapas
* [Visão geral da autenticação do HiveServer2](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Visão geral da autorização do HiveServer2](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization)
* [Habilitando a autorização do hive baseada em padrões do SQL](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache Ranger com hive](../domain-joined/apache-domain-joined-run-hive.md)
