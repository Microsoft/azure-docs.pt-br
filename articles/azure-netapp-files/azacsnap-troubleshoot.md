---
title: Solucionar problemas Aplicativo Azure ferramenta de instantâneo consistente para Azure NetApp Files | Microsoft Docs
description: Fornece a solução de problemas de conteúdo para usar o Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.
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
ms.topic: troubleshooting
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 7ba5a05515284612e17d5aba4cc673c7e78f7ba1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869915"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool-preview"></a>Solucionar problemas Aplicativo Azure ferramenta de instantâneo consistente (versão prévia)

Este artigo fornece a solução de problemas de conteúdo para usar o Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.

Veja a seguir os problemas comuns que você pode encontrar ao executar os comandos. Siga as instruções de resolução mencionadas para corrigir o problema. Se você ainda encontrar um problema, abra uma solicitação de serviço de portal do Azure e atribua a solicitação à fila SAP HANA instância grande para Suporte da Microsoft responder.

## <a name="log-files"></a>Arquivos de log

Uma das melhores fontes de informações para depuração de erros com AzAcSnap são os arquivos de log.  

### <a name="log-file-location"></a>Local do arquivo de log

Os arquivos de log são armazenados no diretório configurado de acordo com o `logPath` parâmetro no arquivo de configuração AzAcSnap.  O nome de arquivo de configuração padrão é `azacsnap.json` e o valor padrão para `logPath` é o `"./logs"` que significa que os arquivos de log são gravados no `./logs` diretório em relação ao local em que o `azacsnap` comando é executado.  A criação de `logPath` um local absoluto (por exemplo, `/home/azacsnap/logs` ) garantirá `azacsnap` a saída dos logs, `/home/azacsnap/logs` independentemente de onde o `azacsnap` comando foi executado.

### <a name="log-file-naming"></a>Nomenclatura de arquivo de log

O nome do arquivo de log é baseado no nome do aplicativo (por exemplo, `azacsnap` ), a opção de comando ( `-c` ) usada (por exemplo,,, `backup` `test` `details` etc.) e o nome de arquivo de configuração (por exemplo, padrão = `azacsnap.json` ).  Portanto, se estiver usando o `-c backup` comando, o nome de arquivo de log por padrão será `azacsnap-backup-azacsnap.log` e será gravado no diretório configurado pelo `logPath` .  

Essa convenção de nomenclatura foi estabelecida para permitir vários arquivos de configuração, um por banco de dados e garantir a facilidade de localizar arquivos de log associados.  Portanto, se o nome de arquivo de configuração for `SID.json` , o nome de arquivo de resultado ao usar as `azacsnap -c backup --configfile SID.json` opções será `azacsnap-backup-SID.log` .

### <a name="result-file-and-syslog"></a>Arquivo de resultado e syslog

Para a `-c backup` opção de comando, AzAcSnap grava em um `*.result` arquivo e no log do sistema ( `/var/log/messages` ) usando o `logger` comando.  O nome de arquivo `*.result` tem o mesmo nome base do [arquivo de log](#log-file-naming) e entra no mesmo [local que o arquivo de log](#log-file-location).  É um arquivo simples de saída de uma linha, de acordo com os exemplos a seguir.

Exemplo de saída do `*.result` arquivo.
```output
Database # 1 (PR1) : completed ok
```

Exemplo de saída do `/var/log/messages` arquivo.
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-azure-netapp-files"></a>Falha na comunicação com o Azure NetApp Files

Ao validar a comunicação com o Azure NetApp Files, a comunicação pode falhar ou expirar.  Verifique se as regras de firewall não estão bloqueando o tráfego de saída do sistema que executa o AzAcSnap para os seguintes endereços e portas TCP/IP:-

- (https://) Management. Azure. com: 443
- (https://) login. microsoftonline. com: 443 

## <a name="failed-communication-with-sap-hana"></a>Falha na comunicação com o SAP HANA

Ao validar a comunicação com SAP HANA executando um teste com `azacsnap -c test --test hana` o e ele fornece o seguinte erro:

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**Solução:**

1. Verifique o arquivo de configuração (por exemplo, `azacsnap.json` ) para cada instância do Hana para garantir que os valores do banco de dados SAP Hana estejam corretos.
1. Tente executar o comando a seguir para verificar se o `hdbsql` comando está no caminho e se ele pode se conectar ao servidor de SAP Hana. O exemplo a seguir mostra a execução correta do comando e sua saída.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    Neste exemplo, o `hdbsql` comando não está nos usuários `$PATH` .

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    Neste exemplo, o `hdbsql` comando é adicionado temporariamente ao usuário `$PATH` , mas quando a execução mostra que a chave de conexão não foi configurada corretamente com o `hdbuserstore Set` comando (consulte o guia de introdução para obter detalhes):

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (SCADMIN)
    ```

    > [!NOTE]
    > Para adicionar permanentemente ao usuário `$PATH` , atualize o arquivo do usuário `$HOME/.profile`

## <a name="the-hdbuserstore-location"></a>O `hdbuserstore` local

Ao configurar a comunicação com o SAP HANA, o `hdbuserstore` programa é usado para criar as configurações de comunicação segura.  O `hdbuserstore` programa geralmente é encontrado em `/usr/sap/<SID>/SYS/exe/hdb/` ou `/usr/sap/hdbclient` .  Normalmente, o instalador adiciona o local correto ao `azacsnap` usuário `$PATH` .

## <a name="failed-test-with-storage"></a>Falha no teste com o armazenamento

O comando `azacsnap -c test --test storage` não é concluído com êxito.

### <a name="azure-large-instance"></a>Instância grande do Azure

O exemplo a seguir é executado `azacsnap` em SAP Hana na instância grande do Azure:

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**Solução:** O erro acima normalmente aparece quando o usuário de armazenamento de instância grande do Azure não tem acesso ao armazenamento subjacente. Para validar o acesso ao armazenamento com o usuário de armazenamento fornecido, execute o `ssh` comando para validar a comunicação com a plataforma de armazenamento.

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

Um exemplo com saída esperada:

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>A autenticidade do host ' 172.18.18.11 (172.18.18.11) ' não pode ser estabelecida

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**Solução:** Não selecione Sim. Verifique se o endereço IP do armazenamento está correto. Se ainda houver um problema, confirme o endereço IP de armazenamento com a equipe de operações da Microsoft.

### <a name="azure-netapp-files"></a>Azure NetApp Files

O exemplo a seguir é executado `azacsnap` em uma VM usando Azure NetApp Files:

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**Solução:**

1. Verifique a existência do arquivo de entidade de serviço, `azureauth.json` , conforme definido no `azacsnap.json` arquivo de configuração.
1. Verifique o arquivo de log (por exemplo, `logs/azacsnap-test-azacsnap.log` ) para ver se a entidade de serviço ( `azureauth.json` ) tem o conteúdo correto.  Exemplo do log da seguinte maneira:

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. Verifique o arquivo de log (por exemplo, `logs/azacsnap-test-azacsnap.log` ) para ver se a entidade de serviço ( `azureauth.json` ) expirou. Exemplo do log da seguinte maneira:

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>Próximas etapas

- [Dicas](azacsnap-tips.md)
