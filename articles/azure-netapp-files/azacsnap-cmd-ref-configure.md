---
title: Configurar Aplicativo Azure ferramenta de instantâneo consistente para Azure NetApp Files | Microsoft Docs
description: Fornece um guia para executar o comando configurar do Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.
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
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 0875aae8bb9049fc96377c1c98efa7391211d08f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632571"
---
# <a name="configure-azure-application-consistent-snapshot-tool-preview"></a>Configurar Aplicativo Azure ferramenta de instantâneo consistente (versão prévia)

Este artigo fornece um guia para executar o comando configurar do Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.

## <a name="introduction"></a>Introdução

O arquivo de configuração pode ser criado ou editado usando o `azacsnap -c configure` comando.

## <a name="command-options"></a>Opções de comando

O `-c configure` comando tem as seguintes opções

- `--configuration new` para criar um novo arquivo de configuração.

- `--configuration edit` para editar um arquivo de configuração existente.

- `[--configfile <config filename>]` é um parâmetro opcional que permite nomes de arquivo de configuração personalizada.

## <a name="configuration-file-for-snapshot-tools"></a>Arquivo de configuração para ferramentas de instantâneo

Um arquivo de configuração pode ser criado executando `azacsnap -c configure --configuration new` .  Por padrão, o nome de arquivo de configuração é `azacsnap.json` .  Um nome de arquivo personalizado pode ser usado com o `--configfile=` parâmetro (por exemplo, `--configfile=<customname>.json` ) o exemplo a seguir é para a configuração de instância grande do Azure:

```bash
azacsnap -c configure --configuration new
```

```output
Building new config file
Add comment to config file (blank entry to exit adding comments):This is a new config file for `azacsnap`
Add comment to config file (blank entry to exit adding comments):
Add database to config? (y/n) [n]: y
HANA SID (for example, H80): H80
HANA Instance Number (for example, 00): 00
HANA HDB User Store Key (for example, `hdbuserstore List`): AZACSNAP
HANA Server's Address (hostname or IP address): testing01
Add ANF Storage to database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]: y
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]: y
Storage User Name (for example, clbackup25): clt1h80backup
Storage IP Address (for example, 192.168.1.30): 172.18.18.11
Storage Volume Name (for example, hana_data_h80_testing01_mnt00001_t250_vol): hana_data_h80_testing01_mnt00001_t020_vol
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]:
Add database to config? (y/n) [n]:
Editing configuration complete, writing output to 'azacsnap.json'
```

## <a name="details-of-required-values"></a>Detalhes dos valores necessários

As seções a seguir fornecem orientações detalhadas sobre os diversos valores necessários para o arquivo de configuração.

### <a name="sap-hana-values"></a>SAP HANA valores

Ao adicionar um *banco de dados* à configuração, os seguintes valores são necessários:

- **Endereço do servidor Hana** = o nome de host ou endereço IP do servidor do SAP Hana.
- **SID do Hana** = a ID do sistema SAP Hana.
- **Número de instância do Hana** = o SAP Hana número da instância.
- **Chave de armazenamento de usuário do Hana HDB** = o usuário SAP Hana configurado com permissões para executar backups de banco de dados.

- Nó único: IP e nome do host do nó
- HSR com STONITH: IP e nome do host do nó
- Expansão (N + N, N + M): IP do nó mestre atual e nome do host
- HSR sem STONITH: IP e nome do host do nó
- Vários SIDs em nó único: hostname e IP do nó que hospeda esses SIDs

### <a name="azure-large-instance-hli-storage-values"></a>Valores de armazenamento de instância grande do Azure (HLI)

Ao adicionar o *armazenamento de HLI* a uma seção de banco de dados, os seguintes valores são necessários:

- **Storage User Name** = esse valor é o nome de usuário usado para estabelecer a conexão SSH com o armazenamento.
- **Endereço IP de armazenamento** = o endereço do sistema de armazenamento.
- **Nome do volume de armazenamento** = o nome do volume para instantâneo.  Esse valor pode ser determinado de várias maneiras, talvez a mais simples seja tentar o seguinte comando de Shell:

    ```bash
    grep nfs /etc/fstab | cut -f2 -d"/" | sort | uniq
    ```

    ```output
    hana_data_p40_soldub41_mnt00001_t020_vol
    hana_log_backups_p40_soldub41_t020_vol
    hana_log_p40_soldub41_mnt00001_t020_vol
    hana_shared_p40_soldub41_t020_vol
    ```

### <a name="azure-netapp-files-anf-storage-values"></a>Valores de armazenamento Azure NetApp Files (seja)

Ao adicionar o *armazenamento seja* a uma seção de banco de dados, os seguintes valores são necessários:

- **Nome de arquivo de autenticação da entidade de serviço** = este é o `authfile.json` arquivo gerado no Cloud Shell ao configurar a comunicação com o armazenamento Azure NetApp files.
- **ID de recurso de volume de armazenamento seja completo** = a ID de recurso completa do volume que está sendo instantâneo.  Isso pode ser recuperado de: portal do Azure – > seja – > volume – > configurações/Propriedades – > ID do recurso

## <a name="configuration-file-overview-azacsnapjson"></a>Visão geral do arquivo de configuração ( `azacsnap.json` )

No exemplo a seguir, o `azacsnap.json` é configurado com um Sid.

Os valores de parâmetro devem ser definidos para o ambiente de SAP HANA específico do cliente.
Para o sistema de **instância grande do Azure** , essas informações são fornecidas pelo gerenciamento de serviços da Microsoft durante a chamada de integração/transferência e são disponibilizadas em um arquivo do Excel que é fornecido durante o transferência. Abra uma solicitação de serviço se precisar fornecer essas informações novamente.

Veja a seguir um exemplo apenas, atualize todos os valores de acordo.

```bash
cat azacsnap.json
```

```output
{
  "version": "5.0 Preview",
  "logPath": "./logs",
  "securityPath": "./security",
  "comments": [],
  "database": [
    {
      "hana": {
        "serverAddress": "sapprdhdb80",
        "sid": "H80",
        "instanceNumber": "00",
        "hdbUserStoreName": "SCADMIN",
        "savePointAbortWaitSeconds": 600,
        "hliStorage": [
          {
            "dataVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_data_h80_azsollabbl20a31_mnt00001_t210_vol"
              },
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_shared_h80_azsollabbl20a31_t210_vol"
              }
            ],
            "otherVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_log_backups_h80_azsollabbl20a31_t210_vol"
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

> [!NOTE]
> Para um cenário de recuperação de desastre em que os backups devem ser executados no local de recuperação de desastres, o nome do servidor HANA configurado no arquivo de configuração de DR (por exemplo, `DR.json` ) no local de recuperação de desastres deve ser igual ao nome do servidor de produção.

> [!NOTE]
> Para a instância grande do Azure, seu endereço IP de armazenamento deve estar na mesma sub-rede que o pool de servidores. Por exemplo, nesse caso, nossa sub-rede do pool de servidores é 172. 18. 18.0/24 e nosso IP de armazenamento atribuído é 172.18.18.11.

## <a name="next-steps"></a>Próximas etapas

- [Testar Aplicativo Azure ferramenta de instantâneo consistente](azacsnap-cmd-ref-test.md)
