---
title: Migrar do repositório HDFS local para o armazenamento do Azure com Azure Data Box
description: Migre dados de um repositório HDFS local para o armazenamento do Azure (armazenamento de BLOBs ou Data Lake Storage Gen2) usando um dispositivo Data Box.
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: e58137dd680ff9a2be2bd657f0969304b526873f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95913106"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Migrar do repositório HDFS local para o armazenamento do Azure com Azure Data Box

Você pode migrar dados de um repositório HDFS local de seu cluster Hadoop para o armazenamento do Azure (armazenamento de BLOBs ou Data Lake Storage Gen2) usando um dispositivo Data Box. Você pode escolher entre Disco do Data Box, um Data Box de 80 TB ou um Data Box Heavy de 770 TB.

Este artigo ajuda você a concluir estas tarefas:

> [!div class="checklist"]
> * Prepare-se para migrar seus dados.
> * Copie seus dados para um Disco do Data Box, Data Box ou um dispositivo Data Box Heavy.
> * Envie o dispositivo de volta para a Microsoft.
> * Aplicar permissões de acesso a arquivos e diretórios (somente Data Lake Storage Gen2)

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dessas coisas para concluir a migração.

* Uma conta de armazenamento do Azure.

* Um cluster Hadoop local que contém os dados de origem.

* Um [dispositivo Azure data Box](https://azure.microsoft.com/services/storage/databox/).

  * [Ordene seu data Box](../../databox/data-box-deploy-ordered.md) ou [Data Box Heavy](../../databox/data-box-heavy-deploy-ordered.md). 

  * Conectar o cabo e conecte seu [Data Box](../../databox/data-box-deploy-set-up.md) ou [Data Box Heavy](../../databox/data-box-heavy-deploy-set-up.md) a uma rede local.

Se você estiver pronto, vamos começar.

## <a name="copy-your-data-to-a-data-box-device"></a>Copiar seus dados para um dispositivo Data Box

Se os dados couberem em um único dispositivo Data Box, você copiará os dados para o dispositivo Data Box. 

Se o tamanho dos dados exceder a capacidade do dispositivo Data Box, use o [procedimento opcional para dividir os dados em vários dispositivos data Box](#appendix-split-data-across-multiple-data-box-devices) e, em seguida, execute esta etapa. 

Para copiar os dados do seu repositório HDFS local para um dispositivo Data Box, você definirá algumas coisas e, em seguida, usará a ferramenta [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) .

Siga estas etapas para copiar dados por meio das APIs REST do armazenamento de blob/objeto para o dispositivo Data Box. A interface da API REST fará com que o dispositivo apareça como um repositório HDFS para o cluster.

1. Antes de copiar os dados via REST, identifique os primitivos de segurança e conexão para se conectar à interface REST no Data Box ou Data Box Heavy. Entre na interface do usuário da Web local do Data Box e vá para a página **conectar e copiar** . Na conta de armazenamento do Azure para seu dispositivo, em **configurações de acesso**, localize e selecione **REST**.

    ![Página "conectar e copiar"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Na caixa de diálogo acessar conta de armazenamento e carregar dados, copie o **ponto de extremidade do serviço blob** e a **chave da conta de armazenamento**. No ponto de extremidade do serviço BLOB, omita o `https://` e a barra à direita.

    Nesse caso, o ponto de extremidade é: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/` . A parte do host do URI que você usará é: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com` . Para obter um exemplo, consulte como [se conectar ao REST sobre http](../../databox/data-box-deploy-copy-data-via-rest.md). 

     ![Caixa de diálogo "acessar conta de armazenamento e carregar dados"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Adicione o ponto de extremidade e o Data Box ou o endereço IP do nó de Data Box Heavy para `/etc/hosts` em cada nó.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Se você estiver usando algum outro mecanismo para DNS, certifique-se de que o ponto de extremidade de Data Box possa ser resolvido.

4. Defina a variável `azjars` do Shell para o local dos `hadoop-azure` `azure-storage` arquivos jar e. Você pode encontrar esses arquivos no diretório de instalação do Hadoop.

    Para determinar se esses arquivos existem, use o seguinte comando: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` . Substitua o `<hadoop_install_dir>` espaço reservado pelo caminho para o diretório em que você instalou o Hadoop. Certifique-se de usar caminhos totalmente qualificados.

    Exemplos:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Crie o contêiner de armazenamento que você deseja usar para a cópia de dados. Você também deve especificar um diretório de destino como parte desse comando. Esse pode ser um diretório de destino fictício neste ponto.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Substitua o `<blob_service_endpoint>` espaço reservado pelo nome do ponto de extremidade do serviço BLOB.

    * Substitua o `<account_key>` espaço reservado pela chave de acesso da sua conta.

    * Substitua o `<container-name>` espaço reservado pelo nome do seu contêiner.

    * Substitua o `<destination_directory>` espaço reservado pelo nome do diretório no qual você deseja copiar os dados.

6. Execute um comando de lista para garantir que o contêiner e o diretório foram criados.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Substitua o `<blob_service_endpoint>` espaço reservado pelo nome do ponto de extremidade do serviço BLOB.

   * Substitua o `<account_key>` espaço reservado pela chave de acesso da sua conta.

   * Substitua o `<container-name>` espaço reservado pelo nome do seu contêiner.

7. Copie dados do HDFS do Hadoop para Data Box armazenamento de BLOBs no contêiner que você criou anteriormente. Se o diretório no qual você está copiando não for encontrado, o comando o criará automaticamente.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Substitua o `<blob_service_endpoint>` espaço reservado pelo nome do ponto de extremidade do serviço BLOB.

    * Substitua o `<account_key>` espaço reservado pela chave de acesso da sua conta.

    * Substitua o `<container-name>` espaço reservado pelo nome do seu contêiner.

    * Substitua o `<exlusion_filelist_file>` espaço reservado pelo nome do arquivo que contém a lista de exclusões de arquivo.

    * Substitua o `<source_directory>` espaço reservado pelo nome do diretório que contém os dados que você deseja copiar.

    * Substitua o `<destination_directory>` espaço reservado pelo nome do diretório no qual você deseja copiar os dados.

    A `-libjars` opção é usada para tornar o `hadoop-azure*.jar` e os arquivos dependentes `azure-storage*.jar` disponíveis para o `distcp` . Isso pode já ocorrer em alguns clusters.

    O exemplo a seguir mostra como o `distcp` comando é usado para copiar dados.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Para melhorar a velocidade de cópia:

    * Tente alterar o número de Mapeadores. (O exemplo acima usa `m` = 4 Mapeadores.)

    * Tente executar vários `distcp` em paralelo.

    * Lembre-se de que arquivos grandes têm desempenho melhor do que arquivos pequenos.

## <a name="ship-the-data-box-to-microsoft"></a>Envie o Data Box para a Microsoft

Siga estas etapas para preparar e enviar o dispositivo de Data Box para a Microsoft.

1. Primeiro,  [preparação para o envio em seu data Box ou data Box Heavy](../../databox/data-box-deploy-copy-data-via-rest.md).

2. Após a conclusão da preparação do dispositivo, baixe os arquivos da BOM. Você usará esses arquivos de manifesto ou BOM posteriormente para verificar os dados carregados no Azure.

3. Desligue o dispositivo e remova os cabos.

4. Agende uma retirada com a UPS.

    * Para dispositivos Data Box, consulte [enviar sua data Box](../../databox/data-box-deploy-picked-up.md).

    * Para dispositivos Data Box Heavy, consulte [enviar sua data Box Heavy](../../databox/data-box-heavy-deploy-picked-up.md).

5. Depois que a Microsoft receber seu dispositivo, ele será conectado à rede data center e os dados serão carregados na conta de armazenamento especificada quando você colocou a ordem do dispositivo. Verifique nos arquivos da BOM que todos os dados são carregados no Azure. 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>Aplicar permissões de acesso a arquivos e diretórios (somente Data Lake Storage Gen2)

Você já tem os dados em sua conta de armazenamento do Azure. Agora, você aplicará permissões de acesso a arquivos e diretórios.

> [!NOTE]
> Essa etapa será necessária somente se você estiver usando Azure Data Lake Storage Gen2 como seu armazenamento de dados. Se você estiver usando apenas uma conta de armazenamento de BLOBs sem namespace hierárquico como seu armazenamento de dados, poderá ignorar esta seção.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Criar uma entidade de serviço para sua conta de Azure Data Lake Storage Gen2

Para criar uma entidade de serviço, consulte [como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md).

* Ao executar as etapas da seção [Atribuir o aplicativo a uma função](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) do artigo, atribua a função **Colaborador dos Dados do Storage Blob** à entidade de serviço.

* Ao executar as etapas na seção [obter valores para entrar no](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) artigo, salve a ID do aplicativo e os valores de segredo do cliente em um arquivo de texto. Você precisará deles em breve.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Gerar uma lista de arquivos copiados com suas permissões

No cluster Hadoop local, execute este comando:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Esse comando gera uma lista de arquivos copiados com suas permissões.

> [!NOTE]
> Dependendo do número de arquivos no HDFS, esse comando pode levar muito tempo para ser executado.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Gerar uma lista de identidades e mapeá-las para Azure Active Directory (Adicionar) identidades

1. Baixe o `copy-acls.py` script. Consulte a seção [baixar scripts auxiliares e configurar o nó de borda para executá-los](#download-helper-scripts) deste artigo.

2. Execute este comando para gerar uma lista de identidades exclusivas.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Esse script gera um arquivo chamado `id_map.json` que contém as identidades que você precisa mapear para as identidades baseadas em Adicionar.

3. Abra o arquivo `id_map.json` em um editor de texto.

4. Para cada objeto JSON que aparece no arquivo, atualize o `target` atributo de um UPN (nome principal de usuário) do AAD ou ObjectId (OID), com a identidade mapeada apropriada. Depois de terminar, salve o arquivo. Você precisará desse arquivo na próxima etapa.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Aplicar permissões a arquivos copiados e aplicar mapeamentos de identidade

Execute este comando para aplicar permissões aos dados que você copiou para a conta de Data Lake Storage Gen2:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Substitua o espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

* Substitua o `<container-name>` espaço reservado pelo nome do seu contêiner.

* Substitua os `<application-id>` `<client-secret>` espaços reservados e pela ID do aplicativo e o segredo do cliente que você coletou ao criar a entidade de serviço.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Apêndice: dividir dados em vários dispositivos Data Box

Antes de mover seus dados para um dispositivo Data Box, você precisará baixar alguns scripts auxiliares, garantir que seus dados sejam organizados para caber em um dispositivo de Data Box e excluir quaisquer arquivos desnecessários.

<a id="download-helper-scripts"></a>

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Baixar scripts auxiliares e configurar o nó de borda para executá-los

1. No seu nó de borda ou de cabeçalho do seu cluster Hadoop local, execute este comando:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Esse comando clona o repositório do GitHub que contém os scripts auxiliares.

2. Certifique-se de que o pacote [JQ](https://stedolan.github.io/jq/) esteja instalado no computador local.

   ```bash
   
   sudo apt-get install jq
   ```

3. Instale o pacote Python de [solicitações](https://2.python-requests.org/en/master/) .

   ```bash
   
   pip install requests
   ```

4. Defina as permissões de execução nos scripts necessários.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Verifique se os dados estão organizados para caber em um dispositivo Data Box

Se o tamanho de seus dados exceder o tamanho de um único dispositivo Data Box, você poderá dividir arquivos em grupos que podem ser armazenados em vários dispositivos Data Box.

Se os dados não excederem o tamanho de um único dispositivo Data Box, você poderá prosseguir para a próxima seção.

1. Com permissões elevadas, execute o `generate-file-list` script que você baixou seguindo as orientações na seção anterior.

   Aqui está uma descrição dos parâmetros de comando:

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Copie as listas de arquivos geradas para HDFS para que elas sejam acessíveis para o trabalho [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) .

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Excluir arquivos desnecessários

Você precisará excluir alguns diretórios do trabalho DisCp. Por exemplo, exclua diretórios que contêm informações de estado que mantêm o cluster em execução.

No cluster Hadoop local em que você planeja iniciar o trabalho DistCp, crie um arquivo que especifique a lista de diretórios que você deseja excluir.

Aqui está um exemplo:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Próximas etapas

Saiba como Data Lake Storage Gen2 funciona com clusters HDInsight. Consulte [Usar o Azure Data Lake Storage Gen2 com clusters de HDInsight do Azure](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).