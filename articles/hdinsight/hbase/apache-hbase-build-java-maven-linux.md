---
title: Usar o Apache Maven para criar um cliente HBase Java para o Azure HDInsight
description: Saiba como usar o Apache Maven para compilar um aplicativo do Apache HBase baseado em Java e depois implantá-lo no HBase no HDInsight do Azure.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seodec18, devx-track-java
ms.date: 12/24/2019
ms.openlocfilehash: 13616cf99db39ec4dac1d13e3dcd2cefc5a44614
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942963"
---
# <a name="build-java-applications-for-apache-hbase"></a>Compilar aplicativos Java para Apache HBase

Saiba como criar um aplicativo [Apache HBase](https://hbase.apache.org/) em Java. Depois, use o aplicativo com o HBase no Azure HDInsight.

As etapas deste documento usam o [Apache Maven](https://maven.apache.org/) para criar e construir o projeto. Maven é uma ferramenta de software para compreensão e gerenciamento de projetos que permite a você compilar software, documentação e relatórios para projetos Java.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache HBase no HDInsight. Consulte [introdução ao Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

* [Java Developer Kit (JDK) versão 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [Apache Maven](https://maven.apache.org/download.cgi) corretamente [instalado](https://maven.apache.org/install.html) de acordo com o Apache.  O Maven é um sistema de construção de projetos para projetos Java.

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Se estiver usando o PowerShell, você precisará do [módulo AZ](/powershell/azure/).

* Um editor de texto. Este artigo usa o bloco de notas da Microsoft.

## <a name="test-environment"></a>Ambiente de teste

O ambiente usado para este artigo foi um computador que executa o Windows 10.  Os comandos foram executados em um prompt de comando e os vários arquivos foram editados com o bloco de notas. Modifique de acordo com o seu ambiente.

Em um prompt de comando, insira os comandos abaixo para criar um ambiente de trabalho:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Criar um projeto Maven

1. Digite o seguinte comando para criar um projeto Maven chamado **hbaseapp**:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Esse comando cria um diretório chamado `hbaseapp` no local atual, que contém um projeto básico do Maven. O segundo comando altera o diretório de trabalho para `hbaseapp` . O terceiro comando cria um novo diretório, `conf` , que será usado posteriormente. O diretório `hbaseapp` contém os seguintes itens:

    * `pom.xml`: O[POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)(modelo de objeto de projeto) contém informações e detalhes de configuração usados para compilar o projeto.
    * `src\main\java\com\microsoft\examples`: contém o código do aplicativo.
    * `src\test\java\com\microsoft\examples`: contém testes para o seu aplicativo.

2. Remova o código de exemplo gerado. Exclua os arquivos de teste e de aplicativo gerados `AppTest.java` e `App.java` Insira os comandos abaixo:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Atualize o modelo do objeto do projeto

Para obter uma referência completa do arquivo pom.xml, consulte https://maven.apache.org/pom.html.  Abra `pom.xml` inserindo o comando a seguir:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Adicionar dependências

No `pom.xml` , adicione o seguinte texto na `<dependencies>` seção:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-shaded-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

Esta seção indica que o projeto precisa dos componentes **hbase-client** e **phoenix-core**. Em tempo de compilação, essa dependência será baixada do repositório padrão do Maven. Você pode usar a [Pesquisa de repositório central do Maven](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) para saber mais sobre essa dependência.

> [!IMPORTANT]  
> O número da versão do hbase-client deve corresponder à versão do Apache HBase que é fornecida com o cluster do HDInsight. Use a tabela a seguir para localizar o número da versão correta.

| Versão do cluster HDInsight | Apache HBase versão a ser usada |
| --- | --- |
| 3,6 | 1.1.2 |
| 4,0 | 2.0.0 |

Para saber mais sobre as versões e os componentes do HDInsight, consulte [Quais são os diferentes componentes do Apache Hadoop disponíveis com o HDInsight?](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Configuração de compilação

Plug-ins do Maven permitem que você personalize os estágios de compilação do projeto. Esta seção será usada para adicionar plug-ins, recursos e outras opções de configuração de compilação.

Adicione o seguinte código ao `pom.xml` arquivo e, em seguida, salve e feche o arquivo. Esse texto deve estar dentro das marcas `<project>...</project>` no arquivo, por exemplo, entre `</dependencies>` e `</project>`.

```xml
<build>
    <sourceDirectory>src</sourceDirectory>
    <resources>
    <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
        <include>hbase-site.xml</include>
        </includes>
    </resource>
    </resources>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
            <goals>
            <goal>shade</goal>
            </goals>
        </execution>
        </executions>
    </plugin>
    </plugins>
</build>
```

Esta seção configura um recurso (`conf/hbase-site.xml`), que contém informações de configuração para o HBase.

> [!NOTE]  
> Você também pode definir valores de configuração via código. Veja os comentários no exemplo `CreateTable`.

Essa seção também [ configura o Plug-in Compiler do Apache Maven](https://maven.apache.org/plugins/maven-compiler-plugin/) e o [Plug-in Shade do Apache Maven](https://maven.apache.org/plugins/maven-shade-plugin/). O plug-in compilador é usado para compilar a topologia. O plug-in de tonalidade é usado para evitar a duplicação de licenças no pacote JAR que é criado pelo Maven. O plug-in serve para evitar o erro de arquivos de licença duplicados no momento da execução no cluster do HDInsight. Utilize o plug-in de tonalidade do Maven com a implementação `ApacheLicenseResourceTransformer` para isso.

O plug-in de tonalidade do Maven também produzirá um uber jar, que contém todas as dependências exigidas pelo aplicativo.

### <a name="download-the-hbase-sitexml"></a>Baixar o arquivo hbase-site.xml

Use o seguinte comando para copiar a configuração HBase do cluster HBase para o diretório `conf`. Substitua `CLUSTERNAME` pelo nome do cluster HDInsight e, em seguida, insira o comando:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Criar o aplicativo

### <a name="implement-a-createtable-class"></a>Implementar uma classe CreateTable

Digite o comando a seguir para criar e abrir um novo arquivo `CreateTable.java` . Selecione **Sim** no prompt para criar um novo arquivo.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Em seguida, copie e cole o código Java abaixo no novo arquivo. Em seguida, feche o arquivo.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class CreateTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Example of setting zookeeper values for HDInsight
    // in code instead of an hbase-site.xml file
    //
    // config.set("hbase.zookeeper.quorum",
    //            "zookeepernode0,zookeepernode1,zookeepernode2");
    //config.set("hbase.zookeeper.property.clientPort", "2181");
    //config.set("hbase.cluster.distributed", "true");
    //
    //NOTE: Actual zookeeper host names can be found using Ambari:
    //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

    //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
    config.set("zookeeper.znode.parent","/hbase-unsecure");

    // create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // create the table...
    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
    // ... with two column families
    tableDescriptor.addFamily(new HColumnDescriptor("name"));
    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
    admin.createTable(tableDescriptor);

    // define some people
    String[][] people = {
        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
        { "4", "Rae", "Schroeder", "rae@contoso.com" },
        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

    HTable table = new HTable(config, "people");

    // Add each person to the table
    //   Use the `name` column family for the name
    //   Use the `contactinfo` column family for the email
    for (int i = 0; i< people.length; i++) {
        Put person = new Put(Bytes.toBytes(people[i][0]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
        person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
        table.put(person);
    }
    // flush commits and close the table
    table.flushCommits();
    table.close();
    }
}
```

Esse código é a `CreateTable` classe, que cria uma tabela chamada `people` e a preenche com alguns usuários predefinidos.

### <a name="implement-a-searchbyemail-class"></a>Implementar uma classe SearchByEmail

Digite o comando a seguir para criar e abrir um novo arquivo `SearchByEmail.java` . Selecione **Sim** no prompt para criar um novo arquivo.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Em seguida, copie e cole o código Java abaixo no novo arquivo. Em seguida, feche o arquivo.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.filter.RegexStringComparator;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.util.GenericOptionsParser;

public class SearchByEmail {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Use GenericOptionsParser to get only the parameters to the class
    // and not all the parameters passed (when using WebHCat for example)
    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
    if (otherArgs.length != 1) {
        System.out.println("usage: [regular expression]");
        System.exit(-1);
    }

    // Open the table
    HTable table = new HTable(config, "people");

    // Define the family and qualifiers to be used
    byte[] contactFamily = Bytes.toBytes("contactinfo");
    byte[] emailQualifier = Bytes.toBytes("email");
    byte[] nameFamily = Bytes.toBytes("name");
    byte[] firstNameQualifier = Bytes.toBytes("first");
    byte[] lastNameQualifier = Bytes.toBytes("last");

    // Create a regex filter
    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
    // Attach the regex filter to a filter
    //   for the email column
    SingleColumnValueFilter filter = new SingleColumnValueFilter(
        contactFamily,
        emailQualifier,
        CompareOp.EQUAL,
        emailFilter
    );

    // Create a scan and set the filter
    Scan scan = new Scan();
    scan.setFilter(filter);

    // Get the results
    ResultScanner results = table.getScanner(scan);
    // Iterate over results and print  values
    for (Result result : results ) {
        String id = new String(result.getRow());
        byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
        String firstName = new String(firstNameObj);
        byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
        String lastName = new String(lastNameObj);
        System.out.println(firstName + " " + lastName + " - ID: " + id);
        byte[] emailObj = result.getValue(contactFamily, emailQualifier);
        String email = new String(emailObj);
        System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
    }
    results.close();
    table.close();
    }
}
```

A `SearchByEmail` classe pode ser usada para consultar linhas por endereço de email. Já que ele utiliza um filtro de expressão comum, você pode fornecer uma cadeia de caracteres ou então uma expressão comum ao utilizar a classe.

### <a name="implement-a-deletetable-class"></a>Implementar uma classe deleteTable

Digite o comando a seguir para criar e abrir um novo arquivo `DeleteTable.java` . Selecione **Sim** no prompt para criar um novo arquivo.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Em seguida, copie e cole o código Java abaixo no novo arquivo. Em seguida, feche o arquivo.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;

public class DeleteTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // Disable, and then delete the table
    admin.disableTable("people");
    admin.deleteTable("people");
    }
}
```

A `DeleteTable` classe limpa as tabelas do HBase criadas neste exemplo desabilitando e descartando a tabela criada pela `CreateTable` classe.

## <a name="build-and-package-the-application"></a>Compilar e criar o pacote do aplicativo

1. Use o diretório `hbaseapp`, use o comando a seguir para compilar um arquivo JAR que contém o aplicativo:

    ```cmd
    mvn clean package
    ```

    Este comando compila e empacota o aplicativo em um arquivo .jar.

2. Após a conclusão do comando, o diretório `hbaseapp/target` conterá um arquivo chamado `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]  
   > O arquivo `hbaseapp-1.0-SNAPSHOT.jar` é um uber jar. Ele contém todas as dependências exigidas para executar o aplicativo.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Carregue o arquivo JAR e execute trabalhos (SSH)

As etapas a seguir usam `scp` para copiar o JAR para o nó principal primário de seu Apache HBase no cluster HDInsight. Então, o comando `ssh` será utilizado para se conectar ao cluster e executar o exemplo diretamente no nó principal.

1. Carregue o JAR no cluster. Substitua `CLUSTERNAME` pelo nome do cluster HDInsight e, em seguida, digite o seguinte comando:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Conecte-se ao cluster HBase. Substitua `CLUSTERNAME` pelo nome do cluster HDInsight e, em seguida, digite o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Para criar uma tabela do HBase usando o aplicativo Java, use o seguinte comando em sua conexão SSH aberta:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Esse comando cria uma nova tabela do HBase chamada **people** e a preenche com dados.

4. Use o seguinte comando para procurar endereços de email armazenados na tabela:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Você receberá as resultados a seguir:

    ```console
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

5. Para excluir a tabela, use o seguinte comando:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Carregue o arquivo JAR e execute trabalhos (PowerShell)

As etapas a seguir usam o [módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az) para carregar o jar para o armazenamento padrão para o cluster do Apache HBase. Os cmdlets do HDInsight serão usados para executar os exemplos remotamente.

1. Depois de instalar e configurar o módulo AZ, crie um arquivo chamado `hbase-runner.psm1` . Use o seguinte texto como o conteúdo deste arquivo:

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Connect-AzAccount
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    Esse arquivo contém dois módulos:

   * **Add-HDInsightFile** – utilizado para carregar arquivos no cluster
   * **Start-HBaseExample** - utilizado para executar as classes criadas anteriormente

2. Salve o `hbase-runner.psm1` arquivo no `hbaseapp` diretório.

3. Registre os módulos com Azure PowerShell. Abra uma nova janela de Azure PowerShell e edite o comando abaixo, substituindo pelo `CLUSTERNAME` nome do cluster. Em seguida, insira os seguintes comandos:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. Use o comando a seguir para carregar o `hbaseapp-1.0-SNAPSHOT.jar` em seu cluster.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Quando solicitado, insira o nome e a senha do logon do cluster (admin). O comando carrega o `hbaseapp-1.0-SNAPSHOT.jar` no local `example/jars` no armazenamento primário do cluster.

5. Para criar uma tabela usando `hbaseapp`, use o seguinte comando:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Quando solicitado, insira o nome e a senha do logon do cluster (admin).

    Esse comando cria uma tabela chamada **people** no HBase em seu cluster HDInsight. Este comando não mostra nenhuma saída na janela do console.

6. Para procurar por entradas na tabela, use o comando a seguir:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Quando solicitado, insira o nome e a senha do logon do cluster (admin).

    Esse comando usa a classe `SearchByEmail` para procurar por quaisquer linhas nas quais a família de colunas `contactinformation` e a coluna `email` contenham a cadeia de caracteres `contoso.com`. Você deve receber os seguintes resultados:

    ```output
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

    Usar **fabrikam.com** como o valor de `-emailRegex` retornará os usuários que tenham **fabrikam.com** no campo de email. Você também pode usar expressões regulares como o termo de pesquisa. Por exemplo, **^ r** retorna endereços que começam com a letra 'r'.

7. Para excluir a tabela, use o seguinte comando:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nenhum resultado ou resultados inesperados ao usar o Start-HBaseExample

Utilize o parâmetro `-showErr` para exibir o erro padrão (STDERR) produzido durante a execução do trabalho.

## <a name="next-steps"></a>Próximas etapas

[Saiba como usar o sqlline com o Apache HBase](apache-hbase-query-with-phoenix.md)