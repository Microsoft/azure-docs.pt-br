---
title: Dicas e truques para usar Aplicativo Azure ferramenta de instantâneo consistente para Azure NetApp Files | Microsoft Docs
description: Fornece dicas e truques para usar o Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 6465acc0d4ce760e0bf89c73dace7c8c66d37c49
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869932"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool-preview"></a>Dicas e truques para usar Aplicativo Azure ferramenta de instantâneo consistente (versão prévia)

Este artigo fornece dicas e truques que podem ser úteis quando você usa o AzAcSnap.

## <a name="limit-service-principal-permissions"></a>Limitar permissões de entidade de serviço

Pode ser necessário limitar o escopo da entidade de serviço AzAcSnap.  Examine a [documentação do RBAC do Azure](../role-based-access-control/index.yml) para obter mais detalhes sobre o gerenciamento de acesso refinado de recursos do Azure.  

Veja a seguir um exemplo de definição de função com as ações mínimas necessárias para que o AzAcSnap funcione.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

Para que as opções de restauração funcionem com êxito, a entidade de serviço AzAcSnap também precisa ser capaz de criar volumes.  Nesse caso, a definição de função precisa de uma ação adicional, portanto, a entidade de serviço completa deve ser semelhante ao exemplo a seguir.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots and restores on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```


## <a name="take-snapshots-manually"></a>Tirar instantâneos manualmente

Antes de executar qualquer comando de backup ( `azacsnap -c backup` ), verifique a configuração executando os comandos de teste e verifique se eles são executados com êxito.  A execução correta desses testes provados `azacsnap` pode se comunicar com o banco de dados do SAP Hana instalado e o sistema de armazenamento subjacente do SAP Hana no sistema de **instância grande** ou **Azure NetApp files** do Azure.

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

Em seguida, para fazer um backup de instantâneo de banco de dados manual, execute o seguinte comando:

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>Configurar backup automático de instantâneo

É uma prática comum em sistemas UNIX/Linux usar `cron` para automatizar a execução de comandos em um sistema. A prática padrão para as ferramentas de instantâneo é configurar o usuário `crontab` .

Abaixo está um exemplo de um `crontab` para o usuário `azacsnap` automatizar os instantâneos.

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

Explicação do crontab acima.

- `MAILTO=""`: tendo um valor vazio, isso impede que o cron tente automaticamente enviar um email ao usuário ao executar a entrada crontab, pois ele provavelmente terminaria no arquivo de email do usuário local.
- Versões abreviadas de tempo para entradas crontab são autoexplicativas:
  - `@monthly` = Executar uma vez por mês, ou seja, "0 0 1 * *".
  - `@weekly`  = Executar uma vez por semana, ou seja, "0 0 * * 0".
  - `@daily`   = Executar uma vez por dia, ou seja, "0 0 * * *".
  - `@hourly`  = Executar uma vez por hora, ou seja, "0 * * * *".
- As primeiras cinco colunas são usadas para designar tempos, consulte os exemplos de coluna abaixo:
  - `0,15,30,45`: A cada 15 minutos
  - `0-23`: A cada hora
  - `*` : Todos os dias
  - `*` : Todos os meses
  - `*` : Todos os dias da semana
- Linha de comando a ser executada entre colchetes "()"
  - `. /home/azacsnap/.profile` = efetuar pull no perfil do usuário para configurar seu ambiente, incluindo $PATH, etc.
  - `cd /home/azacsnap/bin` = alterar o diretório de execução para o local "/Home/azacsnap/bin" onde os arquivos de configuração são.
  - `azacsnap -c .....` = o comando azacsnap completo a ser executado, incluindo todas as opções.

Mais explicações de cron e o formato do arquivo crontab aqui: <https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> Os usuários são responsáveis por monitorar os trabalhos cron para garantir que os instantâneos sejam gerados com êxito.

## <a name="monitor-the-snapshots"></a>Monitorar os instantâneos

As seguintes condições devem ser monitoradas para garantir um sistema íntegro:

1. Espaço em disco disponível. Os instantâneos consumirão lentamente o espaço em disco, pois manter blocos de disco mais antigos são mantidos no instantâneo.
    1. Para ajudar a automatizar o gerenciamento de espaço em disco, use as `--retention` `--trim` Opções e para limpar automaticamente os instantâneos antigos e os arquivos de log do banco de dados.
1. Execução bem-sucedida das ferramentas de instantâneo
    1. Verifique o `*.result` arquivo para obter o êxito ou a falha da execução mais recente do `azacsnap` .
    1. Verifique `/var/log/messages` a saída do `azacsnap` comando.
1. Consistência dos instantâneos restaurando-os para outro sistema periodicamente.

> [!NOTE]
> Para listar detalhes de instantâneo, execute o comando `azacsnap -c details` .

## <a name="delete-a-snapshot"></a>Excluir um instantâneo

Para excluir um instantâneo, execute o comando `azacsnap -c delete` . Não é possível excluir instantâneos do nível do sistema operacional. Você deve usar o comando correto ( `azacsnap -c delete` ) para excluir os instantâneos de armazenamento.

> [!IMPORTANT]
> Esteja atento ao excluir um instantâneo. Depois de excluído, é **impossível** recuperar os instantâneos excluídos.

## <a name="restore-a-snapshot"></a>Restaurar um instantâneo

Um instantâneo do volume de armazenamento pode ser restaurado para um novo volume ( `-c restore --restore snaptovol` ).  Para o Azure instância grande, o volume pode ser revertido para um instantâneo ( `-c restore --restore revertvolume` ).

> [!NOTE]
> Não há **nenhum** comando de recuperação de banco de dados fornecido.

Um instantâneo pode ser copiado de volta para a área de dados SAP HANA, mas SAP HANA não deve estar em execução quando uma cópia é feita ( `cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*` ).

Para a instância grande do Azure, você pode entrar em contato com a equipe de operações da Microsoft abrindo uma solicitação de serviço para restaurar um instantâneo desejado dos instantâneos disponíveis existentes. Você pode abrir uma solicitação de serviço do portal do Azure: <https://portal.azure.com>

Se você decidir executar o failover de recuperação de desastre, o `azacsnap -c restore --restore revertvolume` comando no site de Dr disponibilizará automaticamente os instantâneos de volume mais recentes ( `/hana/data` e `/hana/logbackups` ) para permitir uma recuperação SAP Hana. Use esse comando com cuidado, pois ele interrompe a replicação entre os sites de produção e de DR.

## <a name="set-up-snapshots-for-boot-volumes-only"></a>Configurar instantâneos somente para volumes de ' inicialização '

> [!IMPORTANT]
> Esta operação se aplica somente à instância grande do Azure.

Em alguns casos, os clientes já têm ferramentas para proteger SAP HANA e só querem configurar instantâneos de volume de ' inicialização '.  Nesse caso, a tarefa é simplificada e as etapas a seguir devem ser executadas.

1. Conclua as etapas 1-4 dos pré-requisitos para a instalação.
1. Habilite a comunicação com o armazenamento.
1. Baixe o executar o instalador para instalar as ferramentas de instantâneo.
1. Concluir a configuração das ferramentas de instantâneo.
1. Crie um novo arquivo de configuração da seguinte maneira. Os detalhes do volume de inicialização devem estar no OtherVolume Stanza (entradas do usuário em <span style="color:red">vermelho</span>):
    ```output
    > <span style="color:red">azacsnap -c configure --configuration new --configfile BootVolume.json</span>
    Building new config file
    Add comment to config file (blank entry to exit adding comments):<span style="color:red">Boot only config file.</span>
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: <span style="color:red">y</span>
    HANA SID (for example, H80): <span style="color:red">X</span>
    HANA Instance Number (for example, 00): <span style="color:red">X</span>
    HANA HDB User Store Key (for example, `hdbuserstore List`): <span style="color:red">X</span>
    HANA Server's Address (hostname or IP address): <span style="color:red">X</span>
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: <span style="color:red">y</span>
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: <span style="color:red">y</span>
    Storage User Name (for example, clbackup25): <span style="color:red">shoasnap</span>
    Storage IP Address (for example, 192.168.1.30): <span style="color:red">10.1.1.10</span>
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): <span style="color:red">t210_sles_boot_azsollabbl20a31_vol</span>
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. Verifique o arquivo de configuração, consulte o seguinte exemplo:

    Use `cat` o comando para exibir o conteúdo do arquivo de configuração:

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0 Preview",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "shoasnap",
                    "ipAddress": "10.1.1.10",
                    "volume": "t210_sles_boot_azsollabbl20a31_vol"
                  }
                ]
              }
            ],
            "anfStorage": []
          }
        }
      ]
    }
    ```

1. Testar um backup de volume de inicialização

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. Verifique se está listado, observe a adição da `--snapshotfilter` opção para limitar a lista de instantâneos retornada.

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    Saída do comando:
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t210_sles_boot_azsollabbl20a31_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 Preview (20200617.75879)", 200KB
    , t210_sles_boot_azsollabbl20a31_vol, , , Size used by Snapshots, 1.31GB
    ```

1. Configure o backup automático de instantâneo.

> [!NOTE]
> Não é necessário configurar a comunicação com o SAP HANA.

## <a name="restore-a-boot-snapshot"></a>Restaurar um instantâneo de ' inicialização '

> [!IMPORTANT]
> Esta operação é para o somente de instância grande do Azure.
> O servidor será restaurado até o ponto em que o instantâneo foi tirado.

Um instantâneo de ' inicialização ' pode ser recuperado da seguinte maneira:

1. O cliente precisará desligar o servidor.
1. Depois que o servidor for desligado, o cliente precisará abrir uma solicitação de serviço que contenha a ID da máquina e o instantâneo a ser restaurado.
    > Os clientes podem abrir uma solicitação de serviço do portal do Azure: <https://portal.azure.com>
1. A Microsoft irá restaurar o LUN do sistema operacional usando a ID de máquina e o instantâneo especificados e, em seguida, inicializar o servidor.
1. O cliente precisará confirmar se o servidor está inicializado e íntegro.

Não há etapas adicionais a serem executadas após a restauração.

## <a name="key-facts-to-know-about-snapshots"></a>Fatos importantes a saber sobre instantâneos

Principais atributos de instantâneos de volume de armazenamento:

- **Local dos instantâneos**: os instantâneos podem ser encontrados em um diretório virtual ( `.snapshot` ) dentro do volume.  Consulte os exemplos a seguir para a **instância grande do Azure**:
  - Banco `/hana/data/<SID>/mnt00001/.snapshot`
  - Compartilhado `/hana/shared/<SID>/.snapshot`
  - Logs `/hana/logbackups/<SID>/.snapshot`
  - Inicialização: OS instantâneos de inicialização para o HLI **não são visíveis** do nível do sistema operacional, mas podem ser listados usando `azacsnap -c details` .

  > [!NOTE]
  >  `.snapshot` é uma pasta *virtual* oculta somente leitura que fornece acesso somente leitura aos instantâneos.

- **Instantâneo máximo:** O hardware pode sustentar até 250 instantâneos por volume. O comando de instantâneo manterá um número máximo de instantâneos para o prefixo com base no conjunto de retenção na linha de comando e excluirá o instantâneo mais antigo se ele ultrapassar o número máximo para reter.
- **Nome do instantâneo:** O nome do instantâneo inclui o rótulo de prefixo fornecido pelo cliente.
- **Tamanho do instantâneo:** Depende do tamanho/das alterações no nível do banco de dados.
- **Local do arquivo de log:** Os arquivos de log gerados pelos comandos são impressos em pastas, conforme definido no arquivo de configuração JSON, que, por padrão, é uma subpasta sob a qual o comando é executado (por exemplo, `./logs` ).

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas](azacsnap-troubleshoot.md)
