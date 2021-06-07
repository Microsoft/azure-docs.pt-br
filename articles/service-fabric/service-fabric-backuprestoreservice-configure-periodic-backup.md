---
title: Noções básicas sobre a configuração de backup periódico
description: Use o recurso backup e restauração periódicos do Service Fabric para configurar o backup periódico de seus serviços confiáveis com estado ou Reliable Actors.
ms.topic: article
ms.date: 2/01/2019
ms.openlocfilehash: 2607502af44b178131820d78f23bcdf4e32454a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018878"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Noções básicas sobre a configuração de backup periódico no Azure Service Fabric

A configuração do backup periódico dos seus serviços com estado confiáveis ou Reliable Actors é composto pelas seguintes etapas:

1. **Criação de políticas de backup**: nesta etapa, uma ou mais políticas de backup são criadas dependendo dos requisitos.

2. **Habilitação do backup**: nesta etapa, você associará políticas de backup criadas na **Etapa 1** às entidades necessárias, _Aplicativo_, _Serviço_ ou uma _Partição_.

## <a name="create-backup-policy"></a>Criar Política de Backup

Uma política de backup é composta pelas seguintes configurações:

* **Restauração automática na perda de dados**: especifica se você deve disparar a restauração automaticamente usando o último backup disponível caso a partição passe por um evento de perda de dados.
> [!NOTE]
> É recomendável não definir a restauração automática em clusters de produção
>

* **Máx. de backups incrementais**: define o número máximo de backups incrementais a serem executados entre dois backups completos. O máximo de backups incrementais especifica o limite superior. Um backup completo pode ser executado antes que um número especificado de backups incrementais seja concluído em uma das seguintes condições

    1. A réplica nunca executou um backup completo desde que se tornou primária.

    2. Alguns dos registros de log desde o último backup foram truncados.

    3. A réplica passou do limite MaxAccumulatedBackupLogSizeInMB.

* **Agendamento de backup**: o tempo ou a frequência em que se executa backups periódicos. É possível agendar backups para serem recorrentes e um intervalo especificado ou a uma hora fixa diária/semanalmente.

    1. **Agendamento de backup baseado em frequência**: esse tipo de agendamento deverá ser usado se for necessário executar backup de dados em intervalos fixos. O intervalo de tempo desejado entre dois backups consecutivos é definido usando o formato ISO8601. O agendamento de backup baseado em frequência é compatível com a resolução de intervalo ao minuto.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Agendamento de backup baseado em tempo**: esse tipo de agendamento deverá ser usado se for necessário executar backup de dados em horários específicos do dia ou semana. O tipo de frequência do agendamento pode ser diário ou semanal.
        1. **Agendamento de backup com base em tempo _diário_**: esse tipo de agendamento deve ser usado se a necessidade for fazer o backup de dados em horários específicos do dia. Para especificar isso, defina `ScheduleFrequencyType` como _Diário_; e defina `RunTimes` como a lista de tempo desejado durante o dia no formato ISO8601, a data especificada junto com a hora será ignorada. Por exemplo, `0001-01-01T18:00:00` representa _18h_ todos os dias, ignorando a parte da data _0001-01-01_. O exemplo abaixo ilustra a configuração para disparar o backup diário às _9h_ e _18h_ todos os dias.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **Agendamento de backup com base em tempo _semanal_**: esse tipo de agendamento deve ser usado se a necessidade for fazer o backup de dados em horários específicos do dia. Para especificar isso, defina `ScheduleFrequencyType` como _Semanal_; e defina `RunDays` como a lista de dias em uma semana quando o backup precisa ser disparado e `RunTimes` como a lista de horários desejados durante o dia no formato ISO8601, a data especificada junto com a hora será ignorada. Lista de dias de uma semana quando disparar o backup periódico. O exemplo abaixo ilustra a configuração para disparar um backup diário às _9h_ e às _18h_ de segunda a sexta-feira.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **Armazenamento de backup**: especifica o local para carregar backups. O armazenamento pode ser o armazenamento de blobs do Azure ou o compartilhamento de arquivos.
    1. **Armazenamento de blobs do Azure**: esse tipo de armazenamento deve ser selecionado quando a necessidade é armazenar backups gerados no Azure. Os clusters _autônomos_ e _baseados no Azure_ podem usar esse tipo de armazenamento. Uma descrição para esse tipo de armazenamento requer uma cadeia de conexão e um nome do contêiner, em que os backups precisam ser carregados. Se o contêiner com o nome especificado não estiver disponível, ele será criado durante o carregamento de um backup.

        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

        > [!NOTE]
        > O serviço de restauração de backup não funciona com o armazenamento v1 do Azure
        >

    2. **Compartilhamento de arquivos**: esse tipo de armazenamento deve ser selecionado para clusters _autônomos_ quando a necessidade é armazenar o backup de dados no local. Uma descrição desse tipo de armazenamento requer um caminho de compartilhamento de arquivos para o qual os backups precisam ser carregados. O acesso ao compartilhamento de arquivos pode ser configurado usando uma das seguintes opções
        1. _Autenticação Integrada do Windows_, em que o acesso ao compartilhamento de arquivos é fornecido a todos os computadores que pertencem ao cluster do Service Fabric. Nesse caso, defina os campos a seguir para configurar o armazenamento de backup baseado no _compartilhamento de arquivos_.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Proteção do compartilhamento de arquivos usando o nome do usuário e senha_, em que o acesso ao compartilhamento de arquivos é fornecido a usuários específicos. A especificação do armazenamento do compartilhamento de arquivos também oferece a capacidade de especificar um nome do usuário e senha secundários para fornecer credenciais de fallback caso a autenticação falhe com o nome do usuário e senha primários. Nesse caso, defina os campos a seguir para configurar o armazenamento de backup baseado no _compartilhamento de arquivos_.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> Certifique-se de que a confiabilidade do armazenamento atenda aos requisitos de confiabilidade dos dados de backup ou os exceda.
>

* **Política de retenção**: especifica a política para reter backups no armazenamento configurado. Há suporte para a Política de Retenção Básica.
    1. **Política de retenção básica**: essa política de retenção permite garantir a utilização de armazenamento ideal removendo arquivos de backup que não são mais necessários. `RetentionDuration` pode ser especificado para definir o período de tempo para os quais backups são necessários para ser mantidos no armazenamento. `MinimumNumberOfBackups` é um parâmetro opcional que pode ser especificado para certificar-se de que o número especificado de backups é sempre retido independentemente de `RetentionDuration`. O exemplo abaixo ilustra a configuração para manter os backups para _10_ dias e não permite que o número de backups caia para baixo de _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Habilitar backup periódico
Após definir a política de backup para atender aos requisitos de backup de dados, a política de backup deverá ser devidamente associada a um _aplicativo_ ou a um _serviço_ ou a uma _partição_.

> [!NOTE]
> Verifique se não há atualizações de aplicativo em andamento antes de habilitar o backup
>

### <a name="hierarchical-propagation-of-backup-policy"></a>Propagação hierárquica da política de backup
No Service Fabric, a relação entre aplicativo, serviço e partições é hierárquica, conforme explicado no [Modelo de aplicativo](./service-fabric-application-model.md). A política de backup pode ser associada a um _aplicativo_, _serviço_ ou uma _partição_ na hierarquia. A política de backup propaga-se hierarquicamente até o próximo nível. Supondo que haja apenas uma política de backup criada e associada a um _aplicativo_, o backup de todas as partições com estado que pertencem a todos os _serviços com estado confiáveis_ e _Reliable Actors_ do _aplicativo_ será realizado usando a política de backup. Ou, se a política de backup estiver associada a um _Serviço com estado confiável_, o backup de todas as suas partições será realizado usando a política de backup.

### <a name="overriding-backup-policy"></a>Substituindo a política de backup
Pode haver um cenário em que o backup de dados com o mesmo agendamento de backup é necessário para todos os serviços do aplicativo, exceto para os serviços específicos em que há a necessidade de ter um backup de dados usando o agendamento de frequência maior ou colocando o backup em uma conta de armazenamento ou compartilhamento de arquivos diferente. Para resolver esses cenários, o serviço de restauração de backup facilita a substituição da política propagada no escopo do serviço e da partição. Quando a política de backup está associada no _serviço_ ou na _partição_, ela substitui a política de backup propagada, se houver.

### <a name="example"></a>Exemplo

Este exemplo usa uma configuração com dois aplicativos, _MyApp_A_ e _MyApp_B_. O aplicativo _MyApp_A_ contém dois Serviços com estado confiáveis, _SvcA1_ & _SvcA3_ e um único serviço Reliable Actor, _ActorA2_. _SvcA1_ contém três partições, enquanto _ActorA2_ e _SvcA3_ contêm, cada um, duas partições.  O aplicativo _MyApp_B_ contém três serviços com estado confiáveis, _SvcB1_, _SvcB2_ e _SvcB3_. _SvcB1_ e _SvcB2_ contêm duas partições cada um, enquanto _SvcB3_ contém três partições.

Suponha que os requisitos de backup de dados desses aplicativos são os seguintes

1. MyApp_A
    1. Crie um backup diário dos dados para todas as partições de todos os _Serviços com estado confiáveis_ e _Reliable Actors_ pertencentes ao aplicativo. Carregue os dados de backup para o local _BackupStore1_.

    2. Um dos serviços, _SvcA3_, requer o backup dos dados a cada hora.

    3. O tamanho dos dados na partição _SvcA1_P2_ é maior do que o esperado, e seus dados de backup devem ser armazenados em um local de armazenamento diferente _BackupStore2_.

2. MyApp_B
    1. Crie um backup dos dados todo domingo às 8h para todas as partições do serviço _SvcB1_. Carregue os dados de backup para o local _BackupStore1_.

    2. Crie um backup dos dados todos os dias às 8h para a partição _SvcB2_P1_. Carregue os dados de backup para o local _BackupStore1_.

Para abordar esses requisitos de backup de dados, as políticas de backup BP_1 até BP_5 são criadas e o backup é habilitado da seguinte maneira.
1. MyApp_A
    1. Crie uma política de backup, _BP_1_, com o agendamento de backup baseado em frequência, em que a frequência é definida como 24 horas e o armazenamento de backup é configurado para usar o local de armazenamento _BackupStore1_. Habilite essa política para o aplicativo _MyApp_A_ usando a API [Habilitar o backup do aplicativo](/rest/api/servicefabric/sfclient-api-enableapplicationbackup). Essa ação permite o backup de dados usando a política de backup _BP_1_ para todas as partições de _serviços com estado confiáveis_ e _Reliable Actors_ pertencentes ao aplicativo _MyApp_A_.

    2. Crie uma política de backup, _BP_2_, com o agendamento de backup baseado em frequência, em que a frequência é definida como 1 hora e o armazenamento de backup é configurado para usar o local de armazenamento _BackupStore1_. Habilite essa política para o serviço _SvcA3_ usando a API [Habilitar o backup do serviço](/rest/api/servicefabric/sfclient-api-enableservicebackup). Essa ação substitui a política _BP_1_ propagada ao habilitar explicitamente a política de backup _BP_2_ para todas as partições do serviço _SvcA3_, levando a um backup de dados usando a política de backup _BP_2_ para essas partições.

    3. Crie uma política de backup, _BP_3_, com o agendamento de backup baseado em frequência, em que a frequência é definida como 24 horas e o armazenamento de backup é configurado para usar o local de armazenamento _BackupStore2_. Habilite essa política para a partição _SvcA1_P2_ usando a API [Habilitar backup de partição](/rest/api/servicefabric/sfclient-api-enablepartitionbackup). Essa ação substitui a política _BP_1_ propagada ao habilitar explicitamente a política de backup _BP_3_ para a partição _SvcA1_P2_.

2. MyApp_B
    1. Crie uma política de backup, _BP_4_, com um agendamento de backup baseado em horário, em que o tipo de frequência do agendamento é definido como semanal, os dias de execução são definidos como domingo e os horários de execução são definidos como 8h. O armazenamento de backup é configurado para usar o local de armazenamento _BackupStore1_. Habilite essa política para o serviço _SvcB1_ usando a API [Habilitar o backup do serviço](/rest/api/servicefabric/sfclient-api-enableservicebackup). Essa ação habilita o backup de dados usando a política de backup _BP_4_ para todas as partições do serviço _SvcB1_.

    2. Crie uma política de backup, _BP_5_, com um agendamento de backup baseado em horário, em que o tipo de frequência do agendamento é definido como diário e os horários de execução são definidos como 8h. O armazenamento de backup é configurado para usar o local de armazenamento _BackupStore1_. Habilite essa política para a partição _SvcB2_P1_ usando a API [Habilitar backup de partição](/rest/api/servicefabric/sfclient-api-enablepartitionbackup). Essa ação habilita o backup de dados usando a política de backup _BP_5_ para a partição _SvcB2_P1_.

Seguir o diagrama descreve as políticas de backup explicitamente habilitadas e as políticas de backup propagadas.

![Hierarquia de aplicativos do Service Fabric][0]

## <a name="disable-backup"></a>Desabilitar o backup
As políticas de backup poderão ser desabilitadas quando não houver necessidade de realizar backup de dados. A política de backup habilitada em um _aplicativo_ só poderá ser desabilitada no mesmo _aplicativo_ usando a API [Desabilitar backup de aplicativos](/rest/api/servicefabric/sfclient-api-disableapplicationbackup), a política de backup habilitada em um _serviço_ poderá ser desabilitada no mesmo _serviço_ usando a API [Desabilitar backup de serviços](/rest/api/servicefabric/sfclient-api-disableservicebackup) e a política de backup habilitada em uma _partição_ poderá ser desabilitada na mesma _partição_ usando a API [Desabilitar backup de partições](/rest/api/servicefabric/sfclient-api-disablepartitionbackup).

* Desabilitar a política de backup para um _aplicativo_ interrompe todos os backups periódicos de dados que ocorrem em decorrência da propagação da política de backup para as partições do serviço com estado confiável ou as partições do Reliable Actor.

* Desabilitar a política de backup para um _serviço_ interrompe todos os backups periódicos de dados que ocorrem em decorrência da propagação dessa política de backup para as partições do _serviço_.

* Desabilitar a política de backup para uma _partição_ interrompe todos os backups periódicos de dados que acontecem devido à política de backup na partição.

* Ao desabilitar o backup para uma entidade (aplicação/serviço/partição), `CleanBackup` pode ser definido como _true_ para excluir todos os backups no armazenamento configurado.
    ```json
    {
        "CleanBackup": true 
    }
    ```
> [!NOTE]
> Verifique se não há atualizações de aplicativo em andamento antes de desabilitar o backup
>

## <a name="suspend--resume-backup"></a>Suspender e retomar o backup
Uma determinada situação pode demandar a suspensão temporária do backup periódico de dados. Nessa situação, dependendo do requisito, a suspensão da API de backup pode ser usada em um _aplicativo_, em um _serviço_ ou em uma _partição_. A suspensão do backup periódico é transitiva na subárvore da hierarquia do aplicativo do ponto em que é aplicada. 

* Quando a suspensão é aplicada em um _aplicativo_ usando a API [Suspender o backup do aplicativo](/rest/api/servicefabric/sfclient-api-suspendapplicationbackup), então todos os serviços e partições nesse aplicativo são suspensos para o backup periódico de dados.

* Quando a suspensão é aplicada em um _serviço_ usando a API [Suspender o backup do serviço](/rest/api/servicefabric/sfclient-api-suspendservicebackup), então todas as partições nesse serviço são suspensas para o backup periódico dos dados.

* Quando a suspensão é aplicada em uma _partição_ usando a API [Suspender o backup da partição](/rest/api/servicefabric/sfclient-api-suspendpartitionbackup), então as partições nesse serviço são suspensas para o backup periódico dos dados.

Depois que a necessidade de suspensão tiver acabado, então o backup periódico dos dados poderá ser restaurado usando a respectiva API retomar backup. O backup periódico deve ser retomado no mesmo _aplicativo_, _serviços_ ou _partição_ em que ele foi suspenso.

* Se a suspensão foi aplicada em um _aplicativo_, ela deve ser retomada usando a API [Retomar backup de aplicativos](/rest/api/servicefabric/sfclient-api-resumeapplicationbackup). 

* Se a suspensão foi aplicada em um _serviço_, ela deve ser retomada usando a API [Retomar backup de serviços](/rest/api/servicefabric/sfclient-api-resumeservicebackup).

* Se a suspensão foi aplicada em uma _partição_, ela deve ser retomada usando a API [Retomar backup de partições](/rest/api/servicefabric/sfclient-api-resumepartitionbackup).

### <a name="difference-between-suspend-and-disable-backups"></a>Diferença entre Suspender e Desabilitar backups
Desabilitar backup deve ser usado quando os backups não forem mais necessários para um aplicativo, serviço ou partição específico. É possível invocar a solicitação de desabilitação de backup junto com o parâmetro de limpeza de backups como verdadeiro, o que significa que todos os backups existentes também são excluídos. No entanto, a solicitação suspender deve ser usada em cenários em que pretende-se desativar os backups temporariamente, por exemplo, quando o disco local fica cheio ou o upload de backup está falhando devido a um problema de rede conhecido, etc. 

Embora a desabilitação possa ser invocada apenas em um nível no qual tenha sido anteriormente habilitada de forma explícita para backup, a suspensão poderá ser aplicada em qualquer nível no qual esteja atualmente habilitada para backup diretamente ou por meio de herança/hierarquia. Por exemplo, se o backup estiver habilitado em um nível de aplicativo, será possível invocar apenas desabilitar no nível do aplicativo, no entanto, a solicitação suspender poderá ser invocada no aplicativo em qualquer serviço ou partição sob esse aplicativo. 

## <a name="auto-restore-on-data-loss"></a>Restauração automática em perda de dados
A partição do serviço pode perder dados devido a falhas inesperadas. Por exemplo, o disco de duas entre três réplicas de uma partição (incluindo a réplica primária) é corrompido ou apagado.

Quando o Service Fabric detecta que a partição está na perda de dados, ele invoca o método de interface `OnDataLossAsync` na partição e espera que ela execute a ação necessária para sair da perda de dados. Nessa situação, se a política de backup em vigor na partição tiver um sinalizador `AutoRestoreOnDataLoss` definido como `true`, então a restauração é será automaticamente usando o backup disponível mais recente para essa partição.

> [!NOTE]
> É recomendável não definir a restauração automática em clusters de produção
>

## <a name="get-backup-configuration"></a>Obter configuração de backup
As APIs separadas são disponibilizadas para obter informações de configuração de backup em um escopo do _aplicativo_, do _serviço_ e da _partição_. [Obter informações de configuração de backup de aplicativo](/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo), [Obter informações de configuração de backup de serviço](/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo) e [Obter informações de configuração de backup de partição](/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) são essas APIs, respectivamente. Principalmente, essas APIs retornam a política de backup aplicável, escopo no qual a política de backup é aplicada e a suspensão de backup é detalhada. A seguir, há uma breve descrição sobre os resultados retornados dessas APIs.

- Informações de configuração de backup de aplicativo: fornece os detalhes da política de backup aplicada em aplicativo e todas as políticas substituídas em serviços e partições pertencentes ao aplicativo. Elas também incluem as informações de suspensão do aplicativo e seus serviços e partições.

- Informações de configuração de backup de serviço: fornece os detalhes da política de backup em vigor em serviço e o escopo no qual essa política foi aplicada e todas as política substituídas em suas partições. Elas também incluem as informações de suspensão para o serviço e suas partições.

- Informações de configuração de backup de partição: fornece os detalhes da política de backup em vigor em partição e o escopo no qual essa política foi aplicada. Elas também incluem as informações de suspensão das partições.

## <a name="list-available-backups"></a>Listar backups disponíveis

Os backups disponíveis podem ser listados usando a API Obter lista de backup. O resultado da chamada à API inclui itens de informações de backup relacionados a todos os backups disponíveis no armazenamento de backup, configurado na política de backup aplicável. Diferentes variantes dessa API são fornecidas para listar os backups disponíveis pertencentes a um aplicativo, serviço ou partição. Essas APIs são compatíveis com a obtenção do backup disponível _mais recente_ de todas as partições aplicáveis ou com a filtragem de backups com base na _data de início_ e na _data de término_.

Essas APIs também são compatíveis com a paginação dos resultados, quando o parâmetro _MaxResults_ é definido como inteiro positivo diferente de zero; então, a API retorna o máximo de itens de informações de backup de _MaxResults_. No caso, há mais itens de informações de backup disponíveis do que o valor _MaxResults_; em seguida, um token de continuação é retornado. O parâmetro do token de continuação válido pode ser usado para obter o próximo conjunto de resultados. Quando o valor do token de continuação válido for passado para a próxima chamada da API, ela retornará o próximo conjunto de resultados. Nenhum token de continuação é incluído na resposta quando todos os resultados disponíveis são retornados.

A seguir, há informações breves sobre as variantes com suporte.

- [Obter lista de backup do aplicativo](/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): retorna uma lista de backups disponíveis para toda partição pertencente a um determinado aplicativo Service Fabric.

- [Obter lista de backup do serviço](/rest/api/servicefabric/sfclient-api-getservicebackuplist): retorna uma lista de backups disponíveis para toda partição pertencente a um determinado serviço do Service Fabric.
 
- [Obter lista de backup da partição](/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): retorna uma lista de backups disponíveis para a partição especificada.

## <a name="next-steps"></a>Próximas etapas
- [Referência da API REST de backup e restauração](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
