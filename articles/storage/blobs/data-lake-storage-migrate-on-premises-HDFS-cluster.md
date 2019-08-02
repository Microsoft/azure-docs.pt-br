---
title: Use o Azure Data Box para migrar dados do local HDFS armazenar no armazenamento do Azure
description: Migrar dados de um repositório do HDFS local para o armazenamento do Azure
services: storage
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4445a8566c04d30cfb8743cbd33623f2e23f0dde
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595392"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Usar o Azure Data Box para migrar dados de um repositório do HDFS local para o armazenamento do Azure

Você pode migrar dados de um repositório do HDFS no local do seu cluster do Hadoop no armazenamento do Azure (armazenamento de BLOBs ou Data Lake armazenamento Gen2) usando um dispositivo Data Box. Você pode escolher entre uma caixa de 80 TB de dados ou uma pesada de caixa de dados de 770 TB.

Este artigo ajuda você a concluir essas tarefas:

> [!div class="checklist"]
> * Prepare para migrar seus dados.
> * Copie seus dados em uma caixa de dados ou um dispositivo de caixa de dados pesados.
> * Envie o dispositivo para a Microsoft.
> * Mova os dados no Data Lake armazenamento Gen2.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dessas coisas para concluir a migração.

* Duas contas de armazenamento; um que tenha um namespace hierárquico habilitado nele e outro que não.

* Um cluster de Hadoop local que contém os dados de origem.

* Uma [dispositivo Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

  * [Solicitar o Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) ou [Data Box pesado](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Durante a ordenação de seu dispositivo, lembre-se de escolher uma conta de armazenamento que **não** tem namespaces hierárquicos habilitados nele. Isso ocorre porque os dispositivos da caixa de dados ainda não suportam a ingestão direto no armazenamento do Azure Data Lake Gen2. Você precisará copiar para uma conta de armazenamento e, em seguida, fazer uma segunda cópia para a conta do ADLS Gen2. As instruções para isso são fornecidas nas etapas abaixo.

  * Cabo e conecte-se sua [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) ou [caixa de dados pesados](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) a uma rede local.

Se você estiver pronto, vamos começar.

## <a name="copy-your-data-to-a-data-box-device"></a>Copiar os dados para um dispositivo Data Box

Se seus dados se encaixa em um único dispositivo Data Box, você copiará os dados para o dispositivo Data Box. 

Se o tamanho dos dados excede a capacidade do dispositivo Data Box, em seguida, use o [procedimento opcional para dividir os dados em vários dispositivos do Data Box](#appendix-split-data-across-multiple-data-box-devices) e, em seguida, execute esta etapa. 

Para copiar os dados do repositório local de HDFS em um dispositivo Data Box, você vai configurar algumas coisas e, em seguida, usar o [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) ferramenta.

Siga estas etapas para copiar dados via o REST APIs do Blob/armazenamento de objeto para o dispositivo Data Box. A interface REST API fará o dispositivo aparecer como um repositório de HDFS para seu cluster.

1. Antes de copiar os dados por meio de REST, identifique os primitivos de segurança e conexão para conectar-se à interface REST na caixa de dados ou dados caixa pesada. Entrar para a interface do usuário da caixa de dados da web local e vá para **conectar e copiar** página. O armazenamento do Azure em relação a conta para seu dispositivo, em **configurações de acesso**, localize e selecione **REST**.

    ![Página de "Conectar e copiar"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Na conta de armazenamento de acesso e a caixa de diálogo dados de carregamento, copie o **ponto de extremidade de serviço Blob** e o **chave de conta de armazenamento**. Do ponto de extremidade de serviço blob, omita o `https://` e a barra à direita.

    Nesse caso, é o ponto de extremidade: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. A parte de host do URI que você usará é: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Por exemplo, consulte como [conectar ao REST sobre http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Caixa de diálogo "Acessar a conta de armazenamento e carregar dados"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Adicionar o ponto de extremidade e o endereço IP de nó de caixa de dados ou dados caixa pesada para `/etc/hosts` em cada nó.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Se você estiver usando algum outro mecanismo para DNS, você deve garantir que a caixa de dados do ponto de extremidade pode ser resolvido.

4. Defina a variável de shell `azjars` até o local do `hadoop-azure` e `azure-storage` arquivos jar. Você pode encontrar esses arquivos no diretório de instalação do Hadoop.

    Para determinar se esses arquivos existir, use o seguinte comando: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. Substitua o `<hadoop_install_dir>` espaço reservado com o caminho para o diretório onde você instalou o Hadoop. Certifique-se de usar caminhos totalmente qualificados.

    Exemplos:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Crie o contêiner de armazenamento que você deseja usar para cópia de dados. Você também deve especificar um diretório de destino como parte desse comando. Isso pode ser um diretório de destino fictício neste momento.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Substitua o `<blob_service_endpoint>` espaço reservado com o nome do seu ponto de extremidade de serviço de blob.

    * Substitua o `<account_key>` espaço reservado com a chave de acesso da sua conta.

    * Substitua o `<container-name>` espaço reservado com o nome do seu contêiner.

    * Substitua o `<destination_directory>` espaço reservado com o nome do diretório que você deseja copiar os dados.

6. Execute um comando de lista para garantir que seu contêiner e o diretório foram criados.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Substitua o `<blob_service_endpoint>` espaço reservado com o nome do seu ponto de extremidade de serviço de blob.

   * Substitua o `<account_key>` espaço reservado com a chave de acesso da sua conta.

   * Substitua o `<container-name>` espaço reservado com o nome do seu contêiner.

7. Copie dados do HDFS do Hadoop para o armazenamento de blobs de caixa de dados no contêiner que você criou anteriormente. Se o diretório que você estiver copiando para não for encontrado, o comando cria automaticamente.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Substitua o `<blob_service_endpoint>` espaço reservado com o nome do seu ponto de extremidade de serviço de blob.

    * Substitua o `<account_key>` espaço reservado com a chave de acesso da sua conta.

    * Substitua o `<container-name>` espaço reservado com o nome do seu contêiner.

    * Substitua o `<exlusion_filelist_file>` espaço reservado com o nome do arquivo que contém sua lista de exclusões de arquivo.

    * Substitua o `<source_directory>` espaço reservado com o nome do diretório que contém os dados que você deseja copiar.

    * Substitua o `<destination_directory>` espaço reservado com o nome do diretório que você deseja copiar os dados.

    O `-libjars` opção é usada para fazer a `hadoop-azure*.jar` e o dependente `azure-storage*.jar` arquivos disponíveis para `distcp`. Isso já pode ocorrer por alguns clusters.

    A exemplo a seguir mostra como o `distcp` comando é usado para copiar dados.

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

    * Tente alterar o número de mapeadores. (O exemplo acima usa `m` = 4 mapeadores.)

    * Tente executar vários `distcp` em paralelo.

    * Lembre-se de que arquivos grandes executam melhor do que arquivos pequenos.

## <a name="ship-the-data-box-to-microsoft"></a>Envie a caixa de dados à Microsoft

Siga estas etapas para preparar e enviar o dispositivo Data Box para a Microsoft.

1. Primeiro, [preparar para o envio em sua caixa de dados ou dados caixa pesada](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Após concluir a preparação do dispositivo, baixe os arquivos BOM. Você usa esses BOM ou arquivos posteriormente para verificar os dados carregados para o Azure de manifesto.

3. Desligar o dispositivo e remova os cabos.

4. Agende uma retirada com a UPS.

    * Para dispositivos da caixa de dados, consulte [envie o Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * Para dispositivos da caixa de dados pesados, consulte [enviar seus dados de caixa pesada](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Depois que a Microsoft recebe seu dispositivo, ele está conectado à rede do data center e os dados são carregados para a conta de armazenamento especificada (com namespaces hierárquicos desabilitados) quando você fez o pedido de dispositivo. Verifique se que todos os seus dados é carregado no Azure em relação aos arquivos BOM. Agora você pode mover esses dados para uma conta de armazenamento do Data Lake armazenamento Gen2.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Mova os dados para o armazenamento do Azure Data Lake Gen2

Você já tem os dados em sua conta de armazenamento do Azure. Agora você irá copiar os dados em sua conta de armazenamento do Azure Data Lake e aplicar permissões de acesso a arquivos e diretórios.

> [!NOTE]
> Essa etapa é necessária se você estiver usando o Azure Data Lake armazenamento Gen2 como repositório de dados. Se você estiver usando apenas uma conta de armazenamento de blob sem namespace hierárquico como seu armazenamento de dados, você pode ignorar esta seção.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Copiar dados para a conta de armazenamento do Azure Data Lake Gen 2

Você pode copiar dados usando o Azure Data Factory, ou por meio de seu cluster do Hadoop baseada no Azure.

* Para usar o Azure Data Factory, consulte [Azure Data Factory para mover dados para o ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Certifique-se de especificar **armazenamento de BLOBs do Azure** como a origem.

* Para usar o cluster Hadoop baseado no Azure, execute este comando DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * Substitua os `<source_account>` e `<dest_account>` espaços reservados com os nomes das contas de armazenamento de origem e de destino.

    * Substitua os `<source_container>` e `<dest_container>` espaços reservados com os nomes dos contêineres de origem e de destino.

    * Substitua os `<source_path>` e `<dest_path>` espaços reservados com os caminhos de diretório de origem e destino.

    * Substitua o `<source_account_key>` espaço reservado com a chave de acesso da conta de armazenamento que contém os dados.

    Este comando copia os dados e metadados de sua conta de armazenamento em sua conta de armazenamento do Data Lake armazenamento Gen2.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Criar uma entidade de serviço para sua conta de armazenamento do Azure Data Lake Gen2

Para criar uma entidade de serviço, consulte [como: Usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* Ao executar as etapas da seção [Atribuir o aplicativo a uma função](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) do artigo, atribua a função **Colaborador dos Dados do Storage Blob** à entidade de serviço.

* Ao executar as etapas a [obter valores para entrar no](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) seção do artigo, ID do aplicativo e os valores secretos do cliente em um arquivo de texto. Você precisará deles em breve.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Gerar uma lista de arquivos copiados com suas permissões

Do cluster Hadoop local, execute este comando:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Este comando gera uma lista de arquivos copiados com suas permissões.

> [!NOTE]
> Dependendo do número de arquivos no HDFS, esse comando pode demorar muito tempo para ser executado.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Gerar uma lista de identidades e mapeá-los a identidades do Azure Active Directory (Adicionar)

1. Baixe o `copy-acls.py` script. Consulte a [baixar scripts auxiliares e definir seu nó de borda para executá-los](#download-helper-scripts) seção deste artigo.

2. Execute este comando para gerar uma lista de identidades exclusivas.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Esse script gera um arquivo chamado `id_map.json` que contém as identidades que você precisa mapear para identidades baseadas em Adicionar.

3. Abra o arquivo `id_map.json` em um editor de texto.

4. Para cada objeto JSON que aparece no arquivo, atualize o `target` atributo de um AAD usuário nome Principal (UPN) ou a ID do objeto (OID), com os devidos mapeado identidade. Após terminar, salve o arquivo. Você precisará esse arquivo na próxima etapa.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Aplicar permissões a arquivos copiados e aplicar os mapeamentos de identidade

Execute este comando para aplicar permissões para os dados que você copiou para a conta do Data Lake armazenamento Gen2:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Substitua o espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

* Substitua o `<container-name>` espaço reservado com o nome do seu contêiner.

* Substitua os `<application-id>` e `<client-secret>` espaços reservados com o segredo de cliente e a ID do aplicativo que você coletou ao criar a entidade de serviço.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Apêndice: Dividir dados entre vários dispositivos de caixa de dados

Antes de mover seus dados em um dispositivo Data Box, você precisará baixar alguns scripts de auxiliar, certifique-se de que seus dados são organizados para caber em um dispositivo Data Box e excluir arquivos desnecessários.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Baixar scripts auxiliares e definir seu nó de borda para executá-los

1. Em sua borda ou nó principal do cluster Hadoop local, execute este comando:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Este comando clona o repositório do GitHub que contém os scripts auxiliares.

2. Certifique-se de que tem o [jq](https://stedolan.github.io/jq/) pacote instalado no computador local.

   ```bash
   
   sudo apt-get install jq
   ```

3. Instalar o [solicitações](http://docs.python-requests.org/en/master/) pacote do python.

   ```bash
   
   pip install requests
   ```

4. Conjunto de permissões de execução sobre os scripts necessários.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Certifique-se de que seus dados são organizados para caber em um dispositivo Data Box

Se o tamanho dos dados excede o tamanho de um único dispositivo Data Box, você pode dividir os arquivos de em grupos que você pode armazenar em vários dispositivos de caixa de dados.

Se seus dados não excederem o tamanho de um único dispositivo Data Box, você pode prosseguir para a próxima seção.

1. Com permissões elevadas, execute o `generate-file-list` script que você baixou seguindo as diretrizes na seção anterior.

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

2. Copie o arquivo gerado lista para o HDFS para que fiquem acessíveis para o [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) trabalho.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Excluir arquivos desnecessários

Você precisará excluir alguns diretórios de trabalho DisCp. Por exemplo, exclua diretórios que contêm informações de estado que manter o cluster em execução.

No cluster Hadoop no local em que você planeja iniciar o trabalho DistCp, crie um arquivo que especifica a lista de diretórios que você deseja excluir.

Veja um exemplo:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Próximas etapas

Saiba como o Data Lake armazenamento Gen2 funciona com clusters do HDInsight. Consulte [Usar o Azure Data Lake Storage Gen2 com clusters de HDInsight do Azure](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
