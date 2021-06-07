---
title: Usar SSH com Hadoop – Azure HDInsight
description: Você pode acessar o HDInsight usando o Secure Shell (SSH). Este documento fornece informações sobre como se conectar ao HDInsight usando os comandos SSH de clientes Windows, Linux, UNIX ou macOS.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 02/28/2020
ms.openlocfilehash: a8ad0c787235b94a600d14a3b447bbfee052321b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864849"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>Conectar o HDInsight (Apache Hadoop) usando SSH

Saiba como usar [SSH (Secure Shell)](https://en.wikipedia.org/wiki/Secure_Shell) para conectar com segurança o Apache Hadoop no Azure HDInsight. Para obter informações sobre como se conectar por meio de uma rede virtual, consulte [arquitetura de rede virtual do Azure HDInsight](./hdinsight-virtual-network-architecture.md). Consulte também [planejar uma implantação de rede virtual para clusters do Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).

A tabela a seguir contém as informações de endereço e porta necessárias ao se conectar ao HDInsight usando um cliente SSH:

| Endereço | Porta | Conecta-se a... |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Nó de cabeçalho primário |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Nó de cabeçalho secundário |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | nó de borda (serviços de ML no HDInsight) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | nó de borda (qualquer outro tipo de cluster, se existir um nó de borda) |

Substitua `<clustername>` pelo nome do cluster. Substitua `<edgenodename>` pelo nome do nó de borda.

Se o cluster contém um nó de borda, recomendamos que você __sempre se conecte ao nó de borda__ usando SSH. Os nós de cabeçalho hospedam serviços que são essenciais para a integridade do Hadoop. O nó de borda executa apenas o que você coloca nele. Para obter mais informações sobre o uso de nós de borda, confira [Usar nós de borda no HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node).

> [!TIP]  
> Quando você se conectar pela primeira vez ao HDInsight, seu cliente SSH poderá exibir um aviso de que a autenticidade do host não pode ser estabelecida. Quando for solicitado, selecione 'sim', para adicionar o host à lista de servidores confiáveis do cliente SSH.
>
> Se você tiver se conectado anteriormente a um servidor com o mesmo nome, receberá um aviso de que a chave do host armazenado não corresponde à chave do host do servidor. Veja a documentação do seu cliente SSH sobre como remover a entrada existente para o nome do servidor.

## <a name="ssh-clients"></a>Clientes SSH

Os sistemas Linux, Unix e macOS fornecem os comandos `ssh` e `scp`. O cliente `ssh` normalmente é usado para criar uma sessão de linha de comando remota com um sistema baseado em Unix ou Linux. O cliente `scp` é usado para copiar os arquivos entre o cliente e o sistema remoto com segurança.

O Microsoft Windows não instala clientes SSH por padrão. Os clientes `ssh` e `scp` estão disponíveis para Windows nos seguintes pacotes:

* [Cliente OpenSSH](/windows-server/administration/openssh/openssh_install_firstuse). Esse cliente é um recurso opcional introduzido na atualização dos criadores de outono do Windows 10.

* [Bash no Ubuntu no Windows 10](/windows/wsl/about).

* [Azure cloud Shell](../cloud-shell/quickstart.md). O Cloud Shell fornece um ambiente bash em seu navegador.

* [Git](https://git-scm.com/).

Também há vários clientes SSH gráficos [, como reproduzida e](https://www.chiark.greenend.org.uk/~sgtatham/putty/) [MobaXterm](https://mobaxterm.mobatek.net/). Embora esses clientes possam ser usados para se conectar ao HDInsight, o processo de conexão é diferente do que usa o utilitário `ssh`. Para obter mais informações, consulte a documentação do cliente gráfico que você está usando.

## <a name="authentication-ssh-keys"></a><a id="sshkey"></a>Autenticação: chaves SSH

As chaves SSH usam [criptografia de chave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) para autenticar sessões ssh. Chaves SSH são mais seguras do que senhas e fornecem uma maneira fácil de proteger o acesso ao seu cluster Hadoop.

Se a conta SSH for protegida usando uma chave, o cliente deverá fornecer a chave privada correspondente ao se conectar:

* A maioria dos clientes pode ser configurada para usar uma __chave padrão__. Por exemplo, o cliente `ssh` procura uma chave privada em `~/.ssh/id_rsa` em ambientes Unix e Linux.

* Você pode especificar o __caminho para uma chave particular__. Com o cliente `ssh`, o parâmetro `-i` é usado para especificar o caminho para a chave privada. Por exemplo, `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Se você tiver __várias chaves privadas__ para uso com servidores diferentes, considere usar um utilitário como [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent). O utilitário `ssh-agent` pode ser usado para selecionar a chave a ser usada ao estabelecer uma sessão SSH automaticamente.

> [!IMPORTANT]  
> Se proteger a chave privada com uma senha, você deverá inserir a senha ao usar a chave. Utilitários como o `ssh-agent` podem armazenar a senha em cache para conveniência.

### <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Use o comando `ssh-keygen` para criar arquivos de chaves públicas e privadas. O comando a seguir gera um par de chaves RSA de 2048 bits que podem ser usadas com o HDInsight:

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

Você será solicitado a fornecer informações durante o processo de criação de chave. Por exemplo, onde as chaves são armazenadas ou se deseja usar uma frase secreta. Após a conclusão do processo, dois arquivos são criados: uma chave pública e uma chave privada.

* A __chave pública__ é usada para criar um cluster HDInsight. A chave pública tem uma extensão de `.pub`.

* A __chave privada__ é usado para autenticar o cliente no cluster HDInsight.

> [!IMPORTANT]  
> Você pode proteger as chaves usando uma frase secreta. Uma frase secreta é efetivamente uma senha na chave privada. Mesmo se alguém obtiver a chave privada, deverá ter a senha para usar a chave.

### <a name="create-hdinsight-using-the-public-key"></a>Criar o HDInsight usando a chave pública

| Método de criação | Como usar a chave pública |
| ------- | ------- |
| Portal do Azure | Desmarque __usar senha de logon do cluster para SSH__ e, em seguida, selecione __chave pública__ como o tipo de autenticação SSH. Por fim, selecione o arquivo de chave pública ou cole o conteúdo do arquivo de texto do campo __Chave pública SSH__.</br>:::image type="content" source="./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png" alt-text="Caixa de diálogo de chave pública SSH na criação do cluster HDInsight"::: |
| Azure PowerShell | Use o `-SshPublicKey` parâmetro do cmdlet [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) e passe o conteúdo da chave pública como uma cadeia de caracteres.|
| CLI do Azure | Use o `--sshPublicKey` parâmetro do [`az hdinsight create`](/cli/azure/hdinsight#az-hdinsight-create) comando e passe o conteúdo da chave pública como uma cadeia de caracteres. |
| Modelo do Resource Manager | Para obter um exemplo de como usar chaves SSH com um modelo, confira [Implantar o HDInsight no Linux com uma chave SSH](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/). O elemento `publicKeys` o arquivo [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) são usados para passar as chaves do Azure ao criar o cluster. |

## <a name="authentication-password"></a>Autenticação: senha

Contas SSH podem ser protegidas usando uma senha. Ao se conectar ao HDInsight usando SSH, você será solicitado a inserir a senha.

> [!WARNING]  
> A Microsoft não recomenda o uso da autenticação de senha para o SSH. As senhas podem ser adivinhadas e são vulneráveis a ataques de força bruta. Em vez disso, é recomendável usar [chaves SSH para autenticação](#sshkey).

> [!IMPORTANT]  
> A senha da conta SSH expira 70 dias depois da criação do cluster HDInsight. Se a senha expirar, você pode alterá-la usando as informações do documento [Gerenciar HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords).

### <a name="create-hdinsight-using-a-password"></a>Criar o HDInsight usando uma senha

| Método de criação | Como especificar a senha |
| --------------- | ---------------- |
| Portal do Azure | Por padrão, a conta de usuário SSH tem a mesma senha que a conta de logon do cluster. Para usar uma senha diferente, desmarque __usar senha de logon do cluster para SSH__ e, em seguida, insira a senha no campo __senha ssh__ .</br>:::image type="content" source="./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png" alt-text="Caixa de diálogo de senha SSH na criação do cluster HDInsight":::|
| Azure PowerShell | Use o `--SshCredential` parâmetro do cmdlet [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) e passe um `PSCredential` objeto que contém o nome da conta de usuário SSH e a senha. |
| CLI do Azure | Use o `--ssh-password` parâmetro do [`az hdinsight create`](/cli/azure/hdinsight#az-hdinsight-create) comando e forneça o valor da senha. |
| Modelo do Resource Manager | Para obter um exemplo de como usar uma senha com um modelo, confira [Implantar o HDInsight no Linux com uma senha SSH](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). O elemento `linuxOperatingSystemProfile` no arquivo [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) é usado para passar o nome de conta do SSH e a senha para o Azure ao criar o cluster.|

### <a name="change-the-ssh-password"></a>Alterar a senha SSH

Para obter informações sobre como alterar a senha de conta de usuário do SSH, confira a seção __Alterar senhas__ do documento [Gerenciar o HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="authentication-domain-joined-hdinsight"></a>HDInsight ingressado no domínio de autenticação

Se você estiver usando um __cluster HDInsight ingressado no domínio__, deverá usar o `kinit` comando após a conexão com o usuário local SSH. Este comando solicita um usuário de domínio e uma senha e autentica a sessão com o domínio do Azure Active Directory associado ao cluster.

Você também pode habilitar a autenticação Kerberos em cada nó ingressado no domínio (por exemplo, nó de cabeçalho, nó de borda) para SSH usando a conta de domínio. Para fazer isso, edite o arquivo de configuração do sshd:

```bash
sudo vi /etc/ssh/sshd_config
```

remova os comentários e altere `KerberosAuthentication` para `yes`

```bash
sudo service sshd restart
```

Use `klist` o comando para verificar se a autenticação Kerberos foi bem-sucedida.

Para obter mais informações, confira [Configurar o HDInsight associado ao domínio](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-nodes"></a>Conectar os nós

Os nós de cabeçalho e o nó de borda (se houver) podem ser acessados pela Internet nas portas 22 e 23.

* Ao conetar os __nós principais__, use a porta __22__ para conectar o nó principal primário e a porta __23__ para conectar o nó principal secundário. O nome de domínio totalmente qualificado a usar é `clustername-ssh.azurehdinsight.net`, no qual `clustername` é o nome do cluster.

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```

* Ao se conectar ao __nó de borda__, use a porta 22. O nome de domínio totalmente qualificado é `edgenodename.clustername-ssh.azurehdinsight.net`, no qual `edgenodename` é o nome fornecido ao criar o nó de borda. `clustername` é o nome do cluster.

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]  
> Os exemplos anteriores supõem que você está usando a autenticação de senha ou que a autenticação do certificado está ocorrendo automaticamente. Se você usar um par de chaves SSH para a autenticação e o certificado não for usado automaticamente, use o parâmetro `-i` para especificar a chave privada. Por exemplo, `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`.

Uma vez conectado, o prompt é alterado para indicar o nome de usuário SSH e o nó ao qual você está conectado. Por exemplo, quando conectado ao nó principal primário como `sshuser`, o prompt é `sshuser@<active-headnode-name>:~$`.

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>Conectar o trabalho e nós do Apache ZooKeeper

Os nós de trabalho e os nós Zookeeper não são diretamente acessíveis pela Internet. Eles podem ser acessados dos nós principais do cluster ou do nós de borda. A seguir estão as etapas gerais para se conectar-se a outros nós:

1. Use o SSH para se conectar a um nó de cabeçalho ou de borda:

    ```bash
    ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net
    ```

2. Da conexão SSH para o nó de cabeçalho ou de borda, use o comando `ssh` para se conectar a um nó de trabalho no cluster:

    ```bash
    ssh sshuser@wn0-myhdi
    ```

    Para recuperar uma lista dos nomes dos nós, consulte o documento [Gerenciar HDInsight usando a API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

Se a conta SSH é protegida com __senha__, digite a senha ao se conectar.

Se a conta SSH é protegida usando __chaves SSH__, verifique se o encaminhamento de SSH está habilitado no cliente.

> [!NOTE]  
> Outra maneira de acessar diretamente todos os nós do cluster é instalar o HDInsight em uma Rede Virtual do Azure. Em seguida, você pode associar o computador remoto à mesma rede virtual e acessar diretamente todos os nós no cluster.
>
> Para obter mais informações, consulte [planejar uma rede virtual para o HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="configure-ssh-agent-forwarding"></a>Configurar o encaminhamento do agente SSH

> [!IMPORTANT]  
> As etapas a seguir pressupõem o uso de um sistema com base em Linux ou UNIX e funcionam com Bash no Windows 10. Se essas etapas não funcionarem para o seu sistema, você precisará conferir a documentação de seu cliente SSH.

1. Usando um editor de texto, abra `~/.ssh/config`. Se esse arquivo não existir, você poderá criá-lo digitando `touch ~/.ssh/config` na linha de comando.

2. Adicione o texto a seguir ao arquivo `config`.

    ```
    Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
        ForwardAgent yes
    ```

    Substitua as informações de __Host__ pelo endereço do nó ao qual você se conecta usando o SSH. O exemplo anterior usa o nó de borda. Essa entrada configura o encaminhamento de agente SSH para o nó especificado.

3. Teste o encaminhamento do agente SSH usando o seguinte comando no terminal:

    ```bash
    echo "$SSH_AUTH_SOCK"
    ```

    Esse comando retorna informações semelhantes ao seguinte texto:

    ```output
    /tmp/ssh-rfSUL1ldCldQ/agent.1792
    ```

    Se nada for retornado, `ssh-agent` não será executado. Para saber mais, confira as informações de scripts de inicialização de agente em [Usando ssh-agent com ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh)) ou confira a documentação do cliente SSH.

4. Depois de verificar se o **ssh-agent** está em execução, use o seguinte para adicionar sua chave privada SSH ao agente:

    ```bash
    ssh-add ~/.ssh/id_rsa
    ```

    Se a chave privada estiver armazenada em um arquivo diferente, substitua `~/.ssh/id_rsa` pelo caminho para o arquivo.

5. Conecte-se ao nó de borda de cluster ou aos nós de cabeçalho usando o SSH. Em seguida, use o comando SSH para se conectar a um nó de trabalho ou zookeeper. A conexão é estabelecida usando a chave encaminhada.

## <a name="next-steps"></a>Próximas etapas

* [Usar o túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)
* [Usar nós de borda no HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)
* [Usar o SCP com o HDInsight](./use-scp.md)