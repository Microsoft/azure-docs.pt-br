---
title: Gerenciador de recuperação para corrigir problemas de mapa de fragmentos
description: Use a classe RecoveryManager para resolver problemas com mapas de fragmentos
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 91bcd998849c619a328a198c97bb8c977b9d8232
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792218"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Usando a classe RecoveryManager para corrigir problemas do mapa de fragmentos
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A classe [RecoveryManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) fornece aos aplicativos ADO.net a capacidade de detectar e corrigir facilmente qualquer inconsistência entre o GSM (mapa de fragmentos global) e o LSM (mapa de fragmentos local) em um ambiente de banco de dados fragmentado.

O GSM e o LSM rastreiam o mapeamento de cada banco de dados em um ambiente fragmentado. Ocasionalmente, ocorre uma interrupção entre o GSM e o LSM. Nesse caso, use a classe RecoveryManager para detectar e reparar a interrupção.

A classe RecoveryManager faz parte da [biblioteca de cliente do Banco de Dados Elástico](elastic-database-client-library.md).

![Mapa de fragmentos][1]

Para obter definições de termos, consulte o [Glossário de ferramentas do Banco de Dados Elástico](elastic-scale-glossary.md). Para entender como o **ShardMapManager** é usado para gerenciar dados em uma solução fragmentada, consulte [Gerenciamento do mapa de fragmentos](elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Por que usar o gerenciador de recuperação

Em um ambiente de banco de dados fragmentado, há um locatário por banco de dados e muitos bancos de dados por servidor. Também pode haver vários servidores no ambiente. Cada banco de dados é mapeado no mapa de fragmento para que as chamadas possam ser encaminhadas para o banco de dados e servidor corretos. Os bancos de dados são controlados de acordo com uma **chave de fragmentação** e um **intervalo de valores de chave** é atribuído a cada fragmento. Por exemplo, uma chave de fragmentação pode representar os nomes de clientes de "D" a "F". O mapeamento de todos os fragmentos (também conhecidos como bancos de dados) e seus intervalos de mapeamento estão contidos no **GSM (mapa de fragmentos global)** . Cada banco de dados também contém um mapa dos intervalos contidos no fragmento – isso é conhecido como o **LSM (mapa de fragmentos local)** . Quando um aplicativo se conecta a um fragmento, o mapeamento é armazenado em cache com o aplicativo para recuperação rápida. O LSM é usado para validar dados em cache.

O GSM e o LSM podem ficar fora de sincronia pelos seguintes motivos:

1. Exclusão de um fragmento cujo intervalo acredita-se não estar mais sendo usado, ou renomeação de um fragmento. A exclusão de um fragmento resulta em um **mapeamento de fragmento órfão** . De modo semelhante, um banco de dados renomeado pode causar um mapeamento de fragmentos órfãos. Dependendo da intenção da alteração, o fragmento pode precisar ser removido ou a localização do fragmento precisa ser atualizada. Para recuperar um banco de dados excluído, consulte [Restaurar um banco de dados excluído](recovery-using-backups.md).
2. Ocorre um evento de failover geográfico. Para continuar, é necessário atualizar o nome do servidor e o nome do banco de dados do Gerenciador do mapa de fragmentos no aplicativo e, em seguida, atualizar os detalhes do mapeamento de fragmentos para todos os fragmentos em um mapa de fragmentos. Em caso de um failover geográfico, tal lógica de recuperação deverá ser automatizada no fluxo de trabalho do failover. A automação das ações de recuperação proporciona capacidade de gerenciamento ininterrupta para bancos de dados habilitados geograficamente e evita ações humanas manuais. Para saber mais sobre as opções para recuperar um banco de dados se houver uma interrupção data center, consulte [continuidade de negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md) e [recuperação de desastres](disaster-recovery-guidance.md).
3. Um fragmento ou o banco de dados ShardMapManager é restaurado para um ponto anterior. Para saber mais sobre recuperação pontual usando backups, consulte [Recuperação usando backups](recovery-using-backups.md).

Para saber mais sobre as ferramentas do Banco de dados elástico do Banco de dados SQL do Azure, a Restauração e a Replicação geográfica, consulte os artigos a seguir:

* [Visão geral: continuidade dos negócios de nuvem e recuperação de desastre do banco de dados](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Introdução às ferramentas de banco de dados elástico](elastic-scale-get-started.md)  
* [Gerenciamento de ShardMap](elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Recuperando o RecoveryManager de um ShardMapManager

A primeira etapa é criar uma instância do RecoveryManager. O [método GetRecoveryManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) retorna o gerenciador de recuperação da instância [ShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) atual. Para solucionar as inconsistências no mapa de fragmentos, você deverá primeiramente recuperar o RecoveryManager para o mapa de fragmentos específico.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

Neste exemplo, o RecoveryManager é inicializado no ShardMapManager. O ShardMapManager que contém um ShardMap também já foi inicializado.

Uma vez que o código desse aplicativo manipula o mapa de fragmentos em si, as credenciais usadas no método de fábrica (no exemplo anterior, smmConnectionString) devem ser credenciais que tenham permissões de leitura/gravação no banco de dados GSM referenciado pela cadeia de conexão. Essas credenciais normalmente são diferentes das credenciais usadas para abrir conexões de roteamento dependente de dados. Para saber mais, consulte [Usando credenciais no cliente do banco de dados elástico](elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Removendo um fragmento do ShardMap depois que um fragmento é excluído

O [método DetachShard](/previous-versions/azure/dn842083(v=azure.100)) desanexa o fragmento determinado do mapa de fragmentos e exclui os mapeamentos associados ao fragmento.  

* O parâmetro location é o local do fragmento, especificamente o nome do servidor e o nome do banco de dados, do fragmento que está sendo desanexado.
* O parâmetro shardMapName é o nome do mapa de fragmentos. Isso só será necessário quando vários mapas de fragmentos forem gerenciados pelo mesmo gerenciador de mapas de fragmentos. Opcional.

> [!IMPORTANT]
> Use essa técnica somente se você tiver certeza de que o intervalo para o mapeamento atualizado está vazio. Os métodos acima não verificam os dados para o intervalo que está sendo movido, portanto, é melhor incluir verificações em seu código.

Este exemplo remove fragmentos do mapa do fragmento.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

O mapa de fragmento reflete a localização do fragmento no GSM antes de sua exclusão. Como o fragmento foi excluído, é pressuposto que isso foi intencional, e o intervalo de chaves de fragmentação não está mais em uso. Se esse não for o caso, você poderá executar a restauração pontual. para recuperar o fragmento de um ponto no tempo anterior. (Nesse caso, examine a seção a seguir para detectar inconsistências de fragmentos.) Para recuperar, consulte [recuperação pontual](recovery-using-backups.md).

Uma vez que é pressuposto que a exclusão do banco de dados foi intencional, a ação de limpeza administrativa final é excluir a entrada para o fragmento no gerenciador de mapas de fragmentos. Isso impede que o aplicativo grave informações inadvertidamente em um intervalo não esperado.

## <a name="to-detect-mapping-differences"></a>Para detectar diferenças de mapeamento

O [método DetectMappingDifferences](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) seleciona e retorna um dos mapas de fragmentos (local ou global) como a fonte da relação de confiança e reconcilia os mapeamentos em ambos os mapas de fragmento (GSM e LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* O *local* especifica o nome do servidor e o nome do banco de dados.
* O parâmetro *shardMapName* é o nome do mapa de fragmentos. Isso só será necessário se vários mapas de fragmentos forem gerenciados pelo mesmo gerenciador de mapas de fragmentos. Opcional.

## <a name="to-resolve-mapping-differences"></a>Para resolver as diferenças de mapeamento

O [método ResolveMappingDifferences](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) seleciona um dos mapas de fragmentos (local ou global) como a fonte da relação de confiança e reconcilia os mapeamentos em ambos os mapas de fragmento (GSM e LSM).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* O parâmetro *RecoveryToken* enumera as diferenças nos mapeamentos entre o GSM e o LSM para o fragmento específico.
* A [enumeração MappingDifferenceResolution](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) é usada para indicar o método de resolução da diferença entre os mapeamentos de fragmento.
* **MappingDifferenceResolution.KeepShardMapping** é recomendado no caso em que o LSM contém o mapeamento preciso e, portanto, o mapeamento no fragmento deve ser usado. Normalmente, isso acontece no caso de um failover: o fragmento agora reside em um novo servidor. Uma vez que o fragmento deve primeiramente ser removido do GSM (usando o método RecoveryManager.DetachShard), um mapeamento não existe mais no GSM. Assim, o LSM deve ser usado para restabelecer o mapeamento de fragmento.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Anexar um fragmento ao ShardMap depois que um fragmento é restaurado

O [método AttachShard](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) anexa o fragmento determinado ao mapa de fragmentos. Ele detecta todas as inconsistências do mapa de fragmentos e atualiza os mapeamentos para corresponder ao fragmento no ponto de restauração do fragmento. É pressuposto que o banco de dados também seja renomeado para refletir o nome do banco de dados original (antes de o fragmento ter sido restaurado), já que a restauração pontual é padronizada para um novo banco de dados com o carimbo de data/hora.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* O parâmetro *location* é o nome do servidor e o nome do banco de dados do fragmento que está sendo anexado.
* O parâmetro *shardMapName* é o nome do mapa de fragmentos. Isso só será necessário quando vários mapas de fragmentos forem gerenciados pelo mesmo gerenciador de mapas de fragmentos. Opcional.

Este exemplo adiciona um fragmento ao Mapa de Fragmentos que foi restaurado recentemente de um ponto anterior. Uma vez que o fragmento (ou seja, o mapeamento para o fragmento no LSM) tenha sido restaurado, ele é potencialmente inconsistente com a entrada de fragmento no GSM. Fora desse código de exemplo, o fragmento foi restaurado e renomeado para o nome original do banco de dados. Como ele foi restaurado, é pressuposto que o mapeamento no LSM seja o mapeamento confiável.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Atualizando os locais do fragmento depois de um failover geográfico (restauração) dos fragmentos

Em caso de um failover geográfico, o banco de dados secundário será disponibilizado para gravação e se tornará o novo banco de dados primário. O nome do servidor e, possivelmente, do banco de dados (dependendo da sua configuração) podem ser diferentes do primário original. Portanto, as entradas do mapeamento para o fragmento no GSM e LSM devem ser corrigidas. Da mesma forma, se o banco de dados for restaurado para um nome ou local diferente, ou para um ponto anterior no tempo, isso poderá causar inconsistências nos mapas de fragmentos. O Gerenciador de Mapas de Fragmentos manipula a distribuição de conexões abertas para o banco de dados correto. A distribuição baseia-se nos dados no mapa de fragmentos e no valor da chave de fragmentação que é o destino da solicitação dos aplicativos. Após um failover geográfico, essas informações devem ser atualizadas com o nome de servidor, nome do banco de dados e mapeamento de fragmentos precisos do banco de dados recuperado.

## <a name="best-practices"></a>Práticas recomendadas

O failover geográfico e a recuperação são operações normalmente gerenciadas por um administrador de nuvem do aplicativo, intencionalmente utilizando recursos de continuidade de negócios do banco de dados SQL do Azure. O planejamento da continuidade de negócios exige processos, procedimentos e medidas para garantir que as operações de negócios possam continuar sem interrupção. Os métodos disponíveis como parte da classe RecoveryManager devem ser usados dentro desse fluxo de trabalho para garantir que o GSM e LSM sejam mantidos atualizados com base na ação de recuperação tomada. Há cinco etapas básicas para garantir que o GSM e o LSM reflitam adequadamente as informações precisas depois de um evento de failover. O código do aplicativo para executar essas etapas pode ser integrado ao fluxo de trabalho e às ferramentas existentes.

1. Recupere o RecoveryManager do ShardMapManager.
2. Desanexe o fragmento antigo do mapa de fragmentos.
3. Anexe o novo fragmento ao mapa de fragmentos, incluindo o novo local do fragmento.
4. Detecte inconsistências no mapeamento entre o GSM e o LSM.
5. Resolva as diferenças entre o GSM e o LSM, confiando no LSM.

Este exemplo executa as seguintes etapas:

1. Remove os fragmentos do Mapa de Fragmentos que refletem locais do fragmento antes do evento de failover.
2. Anexa fragmentos ao Mapa de Fragmentos que reflete os novos locais do fragmento (o parâmetro "Configuration.SecondaryServer" é o novo nome do servidor, mas o nome do banco de dados é o mesmo).
3. Recupera os tokens de recuperação detectando diferenças de mapeamento entre o GSM e o LSM para cada fragmento.
4. Resolve as inconsistências confiando no mapeamento do LSM de cada fragmento.

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-database-recovery-manager/recovery-manager.png