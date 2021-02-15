---
title: Fazer backup usando Aplicativo Azure ferramenta de instantâneo consistente para Azure NetApp Files | Microsoft Docs
description: Fornece um guia para executar o comando de backup do Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.
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
ms.openlocfilehash: 17c29fdf88495f6ecc40963eda08858887173fd1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730931"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool-preview"></a>Fazer backup usando Aplicativo Azure ferramenta de instantâneo consistente (versão prévia)

Este artigo fornece um guia para executar o comando backup do Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.

## <a name="introduction"></a>Introdução

Um backup baseado em instantâneo de armazenamento é executado usando o `azacsnap -c backup` comando.  Esse comando executa a orquestração de um instantâneo de armazenamento consistente com o banco de dados nos volumes e um instantâneo de armazenamento (sem nenhuma configuração de consistência de banco de dado) nos outros volumes.  

Para os volumes de dados prepararão `azacsnap` o Database para um instantâneo de armazenamento, ele usará o instantâneo de armazenamento para todos os volumes configurados. por fim, ele aconselhará o banco de dados que o instantâneo está concluído.  Ele também gerenciará todas as entradas de banco de dados que registram a atividade de backup de instantâneo (por exemplo SAP HANA catálogo de backup).

## <a name="command-options"></a>Opções de comando

O `-c backup` comando usa os seguintes argumentos:

- `--volume=` tipo de volume para instantâneo, este parâmetro pode conter `data` ou `other`
  - `data` captura os volumes no datavolume Stanza do arquivo de configuração.
  - `other` captura os volumes dentro do otherVolume Stanza do arquivo de configuração.
  
  > [!NOTE]
  > Ao criar um arquivo de configuração separado com o volume de inicialização como o otherVolume, é possível que os `boot` instantâneos sejam feitos em um agendamento totalmente diferente (por exemplo, diariamente).

- `--prefix=` o prefixo de instantâneo do cliente para o nome do instantâneo. Esse parâmetro tem duas finalidades. O primeiro propósito é fornecer um nome exclusivo para o agrupamento de instantâneos. Em segundo lugar, para determinar o `--retention` número de instantâneos de armazenamento que são mantidos para o especificado `--prefix` .

    > [!IMPORTANT]
    > Somente os caracteres alfanuméricos ("A-Z, a-z, 0-9"), sublinhado ("_") e hífen ("-") são permitidos.

- `--retention` o número de instantâneos do definido `--prefix` a ser mantido. Todos os instantâneos adicionais são removidos após a tomada de um novo instantâneo para isso `--prefix` .

- `--trim` disponível para o SAP HANA V2 e posterior, essa opção mantém o catálogo de backup e o catálogo de disco e backups de log. O número de entradas a serem mantidas no catálogo de backup é determinado pela `--retention` opção acima e exclui as entradas mais antigas para o prefixo definido ( `--prefix` ) do catálogo de backup e o backup de logs físicos relacionados. Ele também exclui as entradas de backup de log mais antigas que a entrada de backup não log mais antiga. Essas operações ajudam a impedir que os backups de log usem todo o espaço em disco disponível.

  > [!NOTE]
  > O comando de exemplo a seguir manterá 9 instantâneos de armazenamento e garantirá que o catálogo de backup seja ajustado continuamente para corresponder aos 9 instantâneos de armazenamento que estão sendo retidos.

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` um parâmetro opcional que define o método de criptografia usado para se comunicar com o SAP HANA, `openssl` ou `commoncrypto` . Se definido, o `azacsnap -c backup` comando espera encontrar dois arquivos no mesmo diretório, esses arquivos devem ser nomeados após o Sid correspondente. Consulte [usando SSL para comunicação com o SAP Hana](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana). O exemplo a seguir usa um `hana` instantâneo de tipo com um prefixo de `hana_TEST` e irá mantê `9` -los se comunicando com SAP Hana usando SSL ( `openssl` ).

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` é um parâmetro opcional que permite nomes de arquivo de configuração personalizada.

## <a name="snapshot-backups-are-fast"></a>Backups de instantâneo são rápidos

A duração de um backup de instantâneo é independente do tamanho do volume, com um volume de 10 TB que está sendo encaixado no mesmo tempo aproximado de um volume de 10 GB.  

Os fatores primários que impactam o tempo de execução geral são o número de volumes a serem instantâneos e as alterações no `--retention` parâmetro (em que uma redução pode aumentar o tempo de execução à medida que os instantâneos em excesso são removidos).

Na configuração de exemplo acima (para **instância grande do Azure**), os instantâneos para os dois volumes levaram menos de 5 segundos para serem concluídos. Por **Azure NetApp files**, os instantâneos para os dois volumes levarão cerca de 60 segundos.

> [!NOTE]
> Se o `--retention` for reduzido significativamente da hora anterior `azacsnap` for executado (por exemplo, de `--retention 50` para `--retention 5` ), o tempo gasto aumentará conforme a necessidade de `azacsnap` remover os instantâneos extras.

## <a name="example-with-data-parameter"></a>Exemplo com `data` parâmetro

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

O comando não faz a saída para o console, mas grava em um arquivo de log, um arquivo de resultado e `/var/log/messages` .

O *arquivo de log* é composto pelo nome do comando + a opção-c + o nome do arquivo de configuração. Por padrão, um nome de arquivo de log para uma `-c backup` execução com um nome de arquivo de configuração padrão `azacsnap-backup-azacsnap.log` .

O arquivo de *resultado* tem o mesmo nome base que o arquivo de log, com `.result` como seu sufixo, por exemplo, `azacsnap-backup-azacsnap.result` que contém a seguinte saída:

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

O `/var/log/messages` arquivo contém a mesma saída que o `.result` arquivo. Consulte o seguinte exemplo (executar como raiz):

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>Exemplo com `other` parâmetro

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

O comando não faz a saída para o console, mas grava somente um arquivo de log.  Ele não _grava em_ um arquivo de resultado ou `/var/log/messages` .

O *arquivo de log* é composto pelo nome do comando + a opção-c + o nome do arquivo de configuração. Por padrão, um nome de arquivo de log para uma `-c backup` execução com um nome de arquivo de configuração padrão `azacsnap-backup-azacsnap.log` .

## <a name="example-with-other-parameter-to-backup-host-os"></a>Exemplo com `other` parâmetro (para o sistema operacional do host de backup)

> [!NOTE]
> O uso de outro arquivo de configuração ( `--configfile bootVol.json` ) que contém apenas os volumes de inicialização.

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

O comando não faz a saída para o console, mas grava somente um arquivo de log.  Ele não _grava em_ um arquivo de resultado ou `/var/log/messages` .

O nome do *arquivo de log* neste exemplo é `azacsnap-backup-bootVol.log` .

> [!NOTE]
> O nome do arquivo de log é composto de "(nome do comando-(a `-c` opção)-(o nome de arquivo config)".  Por exemplo, se estiver usando a `-c backup` opção com um nome de arquivo de log de `h80.json` , o arquivo de log será chamado `azacsnap-backup-h80.log` .  Ou, se estiver usando a `-c test` opção com o mesmo arquivo de configuração, o arquivo de log será chamado `azacsnap-test-h80.log` .

- Tipo de instância grande do HANA: há dois valores válidos com `TYPEI` ou `TYPEII` dependentes da unidade de instância grande do Hana.
- Consulte [SKUs disponíveis para instâncias grandes do Hana](../virtual-machines/workloads/sap/hana-available-skus.md) para confirmar os SKUs disponíveis.

## <a name="next-steps"></a>Próximas etapas

- [Obter detalhes do instantâneo](azacsnap-cmd-ref-details.md)
- [Excluir instantâneos](azacsnap-cmd-ref-delete.md)