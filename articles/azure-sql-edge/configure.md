---
title: Configurar o SQL do Azure no Edge
description: Saiba mais sobre como configurar o Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 0c49f5ab9f10456c32f7f8516cba0e851fa80e74
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392326"
---
# <a name="configure-azure-sql-edge"></a>Configurar o SQL do Azure no Edge

O SQL do Azure no Edge dá suporte para a configuração por meio de uma destas duas opções:

- Variáveis de ambiente
- Um arquivo MSSQL. conf colocado na pasta/var/opt/MSSQL

> [!NOTE]
> A configuração de variáveis de ambiente substitui as configurações especificadas no arquivo MSSQL. conf.

## <a name="configure-by-using-environment-variables"></a>Configurar usando variáveis de ambiente

O SQL do Azure no Edge expõe várias variáveis de ambiente diferentes que podem ser usadas para configurar o contêiner do SQL no Edge. Essas variáveis de ambiente são um subconjunto dos itens disponíveis para SQL Server em Linux. Para obter mais informações sobre variáveis de ambiente SQL Server em Linux, consulte [variáveis de ambiente](/sql/linux/sql-server-linux-configure-environment-variables/).

As novas variáveis de ambiente a seguir foram adicionadas ao Azure SQL Edge. 

| Variável de ambiente | Descrição | Valores |     
|-----|-----| ---------- | 
| **PlanId** | Especifica a SKU do Azure SQL Edge a ser usada durante a inicialização. Essa variável de ambiente só é necessária ao implantar o Azure SQL Edge usando Azure IoT Edge. | **ASDE-Developer-on-IOT-Edge** ou **ASDE-Premium-on-IOT-Edge** | 
| **MSSQL_TELEMETRY_ENABLED** | Habilitar ou desabilitar a coleta de dados de diagnóstico e uso. | TRUE ou FALSE |  
| **MSSQL_TELEMETRY_DIR** | Define o diretório de destino para os arquivos de auditoria de coleta de dados de uso e diagnóstico. | Local da pasta no contêiner do SQL Edge. Essa pasta pode ser mapeada para um volume de host usando pontos de montagem ou volumes de dados. | 
| **MSSQL_PACKAGE** | Especifica o local do dacpac ou pacote bacpac a ser implantado. | Pasta, arquivo ou URL SAS que contém os pacotes dacpac ou bacpac. Para obter mais informações, consulte [implantar o banco de dados SQL DACPAC e pacotes BACPAC no SQL Edge](deploy-dacpac.md). |


A variável de ambiente SQL Server em Linux a seguir não tem suporte para o Azure SQL Edge. Se definido, essa variável de ambiente será ignorada durante a inicialização do contêiner.

| Variável de ambiente | Descrição |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Habilitar grupo de disponibilidade. Por exemplo, **1** está habilitado e **0** está desabilitado. |

> [!IMPORTANT]
> A variável de ambiente **MSSQL_PID** do SQL no Edge só aceita **Premium** e **Developer** como valores válidos. O Azure SQL Edge não dá suporte à inicialização usando uma chave do produto.

### <a name="specify-the-environment-variables"></a>Especificar as variáveis de ambiente

Especifique as variáveis de ambiente para o SQL Edge ao implantar o serviço por meio do [portal do Azure](deploy-portal.md). Você pode adicioná-los na seção de **variáveis de ambiente** da implantação do módulo ou como parte das **Opções de criação do contêiner**.

Adicione valores em **variáveis de ambiente**.

![Definir usando a lista de variáveis de ambiente](media/configure/set-environment-variables.png)

Adicione valores nas **Opções de criação de contêiner**.

![Definir usando opções de criação de contêiner](media/configure/set-environment-variables-using-create-options.png)

> [!NOTE]
> No modo de implantação desconectado, as variáveis de ambiente podem ser especificadas usando o `-e` ou `--env` a `--env-file` opção do `docker run` comando.

## <a name="configure-by-using-an-mssqlconf-file"></a>Configurar usando um arquivo MSSQL. conf

O Azure SQL Edge não inclui o [Utilitário de configuração MSSQL-conf,](/sql/linux/sql-server-linux-configure-mssql-conf/) como SQL Server em Linux. Você precisa configurar manualmente o arquivo MSSQL. conf e colocá-lo na unidade de armazenamento persistente que é mapeada para a pasta/var/opt/MSSQL/no módulo SQL Edge. Quando você estiver implantando o SQL Edge do Azure Marketplace, esse mapeamento será especificado como a opção **montagens** nas **Opções de criação do contêiner**.

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

As novas opções MSSQL. conf a seguir foram adicionadas para o Azure SQL Edge. 

|Opção|Descrição|
|:---|:---|
|**customerfeedback** | Escolha se SQL Server envia comentários à Microsoft. Para obter mais informações, consulte [desabilitar o uso e coleta de dados de diagnóstico](usage-and-diagnostics-data-configuration.md#disable-usage-and-diagnostic-data-collection)|      
|**userrequestedlocalauditdirectory** | Define o diretório de destino para os arquivos de auditoria de coleta de dados de uso e diagnóstico. Para obter mais informações, consulte [auditoria local de uso e coleta de dados de diagnóstico](usage-and-diagnostics-data-configuration.md#local-audit-of-usage-and-diagnostic-data-collection) |        

As opções MSSQL. conf a seguir não são aplicáveis ao SQL Edge:

|Opção|Descrição|
|:---|:---|
|**Comentários do cliente** | Escolha se SQL Server envia comentários à Microsoft. |
|**Perfil do Database Mail** | Definir o perfil padrão do Database Mail para o SQL Server em Linux. |
|**Alta disponibilidade** | Habilitar grupos de disponibilidade. |
|**Coordenador de Transações Distribuídas da Microsoft** | Configurar e solucionar problemas do MSDTC no Linux. Não há suporte para as opções de configuração relacionadas à transação distribuída adicional para o SQL Edge. Para obter mais informações sobre essas opções de configuração adicionais, consulte [Configurar o MSDTC](/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**EULAs de serviços ML** | Aceite os EULAs do R e do Python para pacotes Azure Machine Learning. Aplica-se somente ao SQL Server 2019.|
|**outboundnetworkaccess** |Habilitar o acesso à rede para extensões Java, R e Python dos [Serviços de Machine Learning](/sql/linux/sql-server-linux-setup-machine-learning/).|

O arquivo MSSQL. conf de exemplo a seguir funciona para o SQL Edge. Para obter mais informações sobre o formato de um arquivo MSSQL. conf, consulte o [formato MSSQL. conf](/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="run-azure-sql-edge-as-non-root-user"></a>Executar o Azure SQL Edge como um usuário não raiz

Por padrão, os contêineres do Azure SQL Edge são executados com um usuário/grupo não raiz. Quando implantado por meio do Azure Marketplace (ou usando a execução do Docker), a menos que um usuário/grupo diferente seja especificado, contêineres do SQL Edge são iniciados como o usuário MSSQL (não raiz). Para especificar um usuário não raiz diferente durante a implantação, adicione o `*"User": "<name|uid>[:<group|gid>]"*` par chave-valor em opções de criação de contêiner. No exemplo abaixo, o SQL Edge está configurado para iniciar como o usuário `*IoTAdmin*` .

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

Para permitir que o usuário não raiz Acesse arquivos de BD que estão em volumes montados, verifique se o usuário/grupo no qual você executa o contêiner tem permissões de leitura & gravação no armazenamento de arquivos persistente. No exemplo abaixo, definimos o usuário não raiz com user_id 10001 como o proprietário dos arquivos. 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>Atualizando de versões anteriores do CTP

Os CTPs anteriores do Azure SQL Edge foram configurados para execução como os usuários raiz. As opções a seguir estão disponíveis ao atualizar de CTPs anteriores.

- Continuar a usar o usuário raiz – para continuar usando o usuário raiz, adicione o `*"User": "0:0"*` par chave-valor em opções de criação de contêiner.
- Usar o usuário MSSQL padrão-para usar o usuário MSSQL padrão, siga as etapas abaixo
  - Adicione um usuário chamado MSSQL no host do Docker. No exemplo a seguir, adicionamos um usuário MSSQL com a ID 10001. Esse usuário também é adicionado ao grupo raiz.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - Alterar a permissão no volume de montagem/diretório no qual reside o arquivo de banco de dados 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- Usar uma conta diferente de usuário não raiz-para usar uma conta de usuário diferente da raiz
  - Atualize as opções de criação do contêiner para especificar o `*"User": "user_name | user_id*` par adicionar chave-valor em opções de criação de contêiner. Substitua user_name ou user_id por um user_name ou user_id real do host do Docker. 
  - Altere as permissões no volume de montagem/diretório.

## <a name="persist-your-data"></a> Manter seus dados

As alterações de configuração do Azure SQL Edge e os arquivos de banco de dados são mantidos no contêiner, mesmo se você reiniciar o contêiner com `docker stop` e `docker start` . No entanto, se você remover o contêiner com `docker rm` , tudo no contêiner será excluído, incluindo o Azure SQL Edge e seus bancos de dados. A seção a seguir explica como usar **volumes de dados** para persistir seus arquivos de banco de dados mesmo que os contêineres associados sejam excluídos.

> [!IMPORTANT]
> Para o Azure SQL Edge, é essencial que você compreenda a persistência de dados no Docker. Além da discussão nesta seção, confira a documentação do Docker sobre [como gerenciar dados em contêineres do Docker](https://docs.docker.com/engine/tutorials/dockervolumes/).

### <a name="mount-a-host-directory-as-data-volume"></a>Montar um diretório de host como volume de dados

A primeira opção é montar um diretório em seu host como um volume de dados em seu contêiner. Para fazer isso, use o comando `docker run` com o sinalizador `-v <host directory>:/var/opt/mssql`. Isso permite que os dados sejam restaurados entre as execuções do contêiner.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

Essa técnica também permite que você compartilhe e exiba os arquivos no host fora do Docker.

> [!IMPORTANT]
> Atualmente, o mapeamento de volume do host para o **Docker no Windows** não dá suporte ao mapeamento do diretório `/var/opt/mssql` completo. No entanto, você pode mapear um subdiretório, como `/var/opt/mssql/data` para o computador host.

> [!IMPORTANT]
> Não há suporte para o mapeamento de volume do host para o **Docker no Mac** com a imagem do Azure SQL Edge no momento. Use contêineres de volume de dados em vez disso. Essa restrição é específica do diretório `/var/opt/mssql`. A leitura de um diretório montado funciona bem. Por exemplo, você pode montar um diretório de host usando -v no Mac e restaurar um backup de um arquivo .bak que reside no host.

### <a name="use-data-volume-containers"></a>Usar contêineres de volume de dados

A segunda opção é usar um contêiner de volume de dados. Você pode criar um contêiner de volume de dados especificando um nome de volume, em vez de um diretório de host com o parâmetro `-v`. O exemplo a seguir cria um volume de dados compartilhado chamado **sqlvolume**.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

> [!NOTE]
> Essa técnica para criar implicitamente um volume de dados no comando executar não funciona com versões anteriores do Docker. Nesse caso, use as etapas explícitas descritas na documentação do Docker, [Como criar e montar um contêiner de volume de dados](https://docs.docker.com/engine/tutorials/dockervolumes/#creating-and-mounting-a-data-volume-container).

Mesmo que você pare e remova esse contêiner, o volume de dados persiste. Você pode exibi-lo com o comando `docker volume ls`.

```bash
docker volume ls
```

Se você criar outro contêiner com o mesmo nome de volume, o novo contêiner usará os mesmos dados do Azure SQL Edge contidos no volume.

Para remover um contêiner de volume de dados, use o comando `docker volume rm`.

> [!WARNING]
> Se você excluir o contêiner de volume de dados, todos os dados do Azure SQL Edge no contêiner serão excluídos *permanentemente* .


## <a name="next-steps"></a>Próximas etapas

- [Conectar-se ao SQL do Azure no Edge](connect.md)
- [Criar uma solução IoT de ponta a ponta com o SQL no Edge](tutorial-deploy-azure-resources.md)