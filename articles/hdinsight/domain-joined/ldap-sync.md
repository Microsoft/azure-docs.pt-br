---
title: Sincronização LDAP no Ranger e Apache Ambari no Azure HDInsight
description: Resolva a sincronização LDAP no Ranger e no Ambari e forneça diretrizes gerais.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fb9035b4d816c1af84b15e6865335aa1bdf86f5b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933416"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Sincronização LDAP no Ranger e Apache Ambari no Azure HDInsight

Os clusters do Enterprise Security Package do HDInsight (ESP) usam o Ranger para autorização. O Apache Ambari e o Ranger sincronizam usuários e grupos de forma independente e funcionam um pouco diferentemente. Este artigo destina-se a tratar da sincronização LDAP no Ranger e no Ambari.

## <a name="general-guidelines"></a>Diretrizes gerais

* Sempre implante clusters com um ou mais grupos.
* Se você quiser usar mais grupos no cluster, verifique se faz sentido atualizar as associações de grupo no Azure Active Directory (AD do Azure).
* Se você quiser alterar os grupos de cluster, poderá alterar os filtros de sincronização usando Ambari.
* Todas as alterações de associação de grupo no Azure AD são refletidas no cluster em Sincronizações subsequentes. As alterações precisam ser sincronizadas para Azure AD Domain Services (Azure AD DS) primeiro e, em seguida, para os clusters.
* Os clusters HDInsight usam samba/Winbind para projetar as associações de grupo nos nós de cluster.
* Os membros do grupo são sincronizados transitivamente (todos os subgrupos e seus membros) para Ambari e ranger. 

## <a name="users-are-synced-separately"></a>Os usuários são sincronizados separadamente

 * O Ambari e o Ranger não compartilham o banco de dados do usuário porque atendem a duas finalidades diferentes. 
   * Se um usuário precisar usar a interface do usuário do amAmbari, o usuário precisará ser sincronizado com o Ambari. 
   * Se o usuário não estiver sincronizado com o Ambari, a interface do usuário/API do Ambari o rejeitará, mas outras partes do sistema funcionarão (elas são protegidas pelo Ranger ou pelo Resource Manager, e não pelo Ambari).
   * Para incluir usuários ou grupos em políticas de Ranger, as entidades de segurança precisam ser sincronizadas explicitamente no Ranger.

## <a name="ambari-user-sync-and-configuration"></a>Sincronização e configuração de usuário do Ambari

Dos nós de cabeçalho, um trabalho cron, `/opt/startup_scripts/start_ambari_ldap_sync.py` , é executado a cada hora para agendar a sincronização do usuário. O trabalho cron chama as APIs REST do Ambari para executar a sincronização. O script envia uma lista de usuários e grupos para sincronização (já que os usuários podem não pertencer aos grupos especificados, ambos são especificados individualmente). O Ambari sincroniza o sAMAccountName como o nome de usuário e todos os membros do grupo, transitivamente.

Os logs devem estar no `/var/log/ambari-server/ambari-server.log` . Para obter mais informações, consulte [Configurar o nível de log Ambari](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

Em clusters Data Lake, o gancho de criação de usuário post é usado para criar as pastas base para os usuários sincronizados e eles são definidos como os proprietários das pastas base. Se o usuário não estiver sincronizado com o Ambari corretamente, o usuário poderá enfrentar falhas em trabalhos em execução, pois a pasta base pode não estar configurada corretamente.

## <a name="ranger-user-sync-and-configuration"></a>Sincronização e configuração de usuário do Ranger

O Ranger tem um mecanismo de sincronização interno que é executado a cada hora para sincronizar os usuários. Ele não compartilha o banco de dados do usuário com Ambari. O HDInsight configura o filtro de pesquisa para sincronizar o usuário administrador, o usuário Watchdog e os membros do grupo especificados durante a criação do cluster. Os membros do grupo serão sincronizados de forma transitiva:

1. Desabilite a sincronização incremental.
1. Habilite o mapa de sincronização do grupo de usuários.
1. Especifique o filtro de pesquisa para incluir os membros do grupo transitivo.
1. Sincronize o atributo sAMAccountName para usuários e o atributo Name para grupos.

### <a name="group-or-incremental-sync"></a>Grupo ou sincronização incremental

O Ranger dá suporte a uma opção de sincronização de grupo, mas funciona como uma interseção com filtro de usuário, não como uma União entre associações de grupo e filtro de usuário. Um caso de uso típico para o filtro de sincronização de grupo no Ranger é-grupo filtro: (DN = clusteradmingroup), filtro de usuário: (cidade = Seattle).

A sincronização incremental funciona apenas para os usuários que já estão sincronizados (na primeira vez). A incremental não sincronizará nenhum novo usuário adicionado aos grupos após a sincronização inicial.

### <a name="update-ranger-sync-filter"></a>Atualizar filtro de sincronização do Ranger

O filtro LDAP pode ser encontrado na interface do usuário do amAmbari, na seção de configuração de sincronização de usuários do Ranger. O filtro existente estará no formato `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` . Certifique-se de adicionar predicado no final e testar o filtro usando o `net ads` comando de pesquisa ou ldp.exe ou algo semelhante.

## <a name="ranger-user-sync-logs"></a>Logs de sincronização do usuário do Ranger

A sincronização de usuários do Ranger pode ocorrer fora de um dos cabeçalho. Os logs estão em `/var/log/ranger/usersync/usersync.log` . Para aumentar o nível de detalhes dos logs, execute as seguintes etapas:

1. Faça logon no Ambari.
1. Vá para a seção de configuração do Ranger.
1. Vá para a seção **Log4J avançada de usersync** .
1. Altere o `log4j.rootLogger` `DEBUG` nível para. (Depois de alterá-lo, ele deve se parecer `log4j.rootLogger = DEBUG,logFile,FilterLog` ).
1. Salve a configuração e reinicie o Ranger.

## <a name="known-issues-with-ranger-user-sync"></a>Problemas conhecidos com a sincronização de usuário do Ranger
* Se o nome do grupo tiver caracteres Unicode, a sincronização do Ranger falhará ao sincronizar esse objeto. Se um usuário pertencer a um grupo que tem caracteres internacionais, o Ranger sincronizará a associação de grupo parcial
* O nome de usuário (sAMAccountName) e o nome do grupo (nome) devem ter 20 caracteres de comprimento ou menor. Se o nome do grupo for maior, o usuário será tratado como se não pertencer ao grupo, ao calcular as permissões.

## <a name="next-steps"></a>Próximas etapas

* [Problemas de autenticação no Azure HDInsight](./domain-joined-authentication-issues.md)
* [Sincronizar usuários do Azure AD com um cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
