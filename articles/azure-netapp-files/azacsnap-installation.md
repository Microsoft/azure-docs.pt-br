---
title: Instale o Aplicativo Azure ferramenta de instantâneo consistente para Azure NetApp Files | Microsoft Docs
description: Fornece um guia para a instalação do Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.
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
ms.openlocfilehash: 458f4d3f29cb08a94095167ed45133f5cd70f5f4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869184"
---
# <a name="install-azure-application-consistent-snapshot-tool-preview"></a>Instalar Aplicativo Azure ferramenta de instantâneo consistente (versão prévia)

Este artigo fornece um guia para a instalação do Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.

## <a name="introduction"></a>Introdução

O autoinstalador baixável foi projetado para facilitar a configuração e a execução das ferramentas de instantâneo com privilégios de usuário não raiz (por exemplo, azacsnap). O instalador irá configurar o usuário e colocar as ferramentas de instantâneo no `$HOME/bin` subdiretório usuários (padrão = `/home/azacsnap/bin` ).
O instalador automático tenta determinar as configurações e os caminhos corretos para todos os arquivos com base na configuração do usuário que executa a instalação (por exemplo, raiz). Se as etapas de pré-requisito (habilitar a comunicação com o armazenamento e o SAP HANA) forem executadas como raiz, a instalação copiará a chave privada e `hdbuserstore` para o local do usuário de backup. No entanto, é possível que as etapas que permitem a comunicação com o back-end de armazenamento e SAP HANA sejam feitas manualmente por um administrador experiente após a instalação.

## <a name="prerequisites-for-installation"></a>Pré-requisitos para instalação

Siga as diretrizes para configurar e executar os comandos instantâneos e recuperação de desastre. É recomendável que as etapas a seguir sejam concluídas como raiz antes de instalar e usar as ferramentas de instantâneo.

1. O **sistema operacional é corrigido**: consulte aplicação de patch e configuração de SMT em [como instalar e configurar SAP Hana (instâncias grandes) no Azure](../virtual-machines/workloads/sap/hana-installation.md#operating-system).
1. A **sincronização de horário está configurada**. O cliente precisará fornecer um servidor de horário compatível com NTP e configurar o sistema operacional de acordo.
1. O **Hana está instalado** : consulte as instruções de instalação do Hana na [instalação do SAP NetWeaver no banco de dados Hana](/archive/blogs/saponsqlserver/sap-netweaver-installation-on-hana-database).
1. **[Habilitar comunicação com o armazenamento](#enable-communication-with-storage)** (consulte a seção separada para obter mais detalhes): o cliente deve configurar o SSH com um par de chaves privada/pública e fornecer a chave pública para cada nó em que as ferramentas de instantâneo estão planejadas para execução nas operações da Microsoft para instalação no back-end de armazenamento.
   1. **Para Azure NetApp Files (consulte a seção separada para obter detalhes)**: o cliente deve gerar o arquivo de autenticação da entidade de serviço.
   1. **Para a instância grande do Azure (consulte a seção separada para obter detalhes)**: o cliente deve configurar o SSH com um par de chaves privada/pública e fornecer a chave pública para cada nó em que as ferramentas de instantâneo estão planejadas para serem executadas nas operações da Microsoft para instalação no back-end de armazenamento.

      Teste isso usando SSH para se conectar a um dos nós (por exemplo, `ssh -l <Storage UserName> <Storage IP Address>` ).
      Digite `exit` para fazer logoff do prompt de armazenamento.

      As operações da Microsoft fornecerão o usuário de armazenamento e o IP de armazenamento no momento do provisionamento.
  
1. **[Habilitar comunicação com SAP Hana](#enable-communication-with-sap-hana)** (consulte a seção separada para obter mais detalhes): o cliente deve configurar um usuário de SAP Hana apropriado com os privilégios necessários para executar o instantâneo.
   1. Essa configuração pode ser testada na linha de comando da seguinte maneira usando o texto em `grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - Os exemplos acima são para comunicação não SSL para SAP HANA.

## <a name="enable-communication-with-storage"></a>Habilitar a comunicação com o armazenamento

Esta seção explica como habilitar a comunicação com o armazenamento do.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Criar entidade de serviço RBAC

1. Em uma sessão de Azure Cloud Shell, verifique se você está conectado à assinatura em que você deseja estar associado à entidade de serviço por padrão:

    ```azurecli-interactive
    az account show
    ```

1. Se a assinatura não estiver correta, use

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. Criar uma entidade de serviço usando CLI do Azure de acordo com o exemplo a seguir

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. Isso deve gerar uma saída como o exemplo a seguir:

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > Esse comando atribuirá automaticamente a função colaborador do RBAC à entidade de serviço no nível da assinatura, você poderá restringir o escopo ao grupo de recursos específico em que os testes criarão os recursos.

1. Recorte e cole o conteúdo de saída em um arquivo chamado `azureauth.json` armazenado no mesmo sistema que o `azacsnap` comando e proteja o arquivo com as permissões apropriadas do sistema.

### <a name="azure-large-instance"></a>Instância grande do Azure

A comunicação com o back-end de armazenamento é executada em um canal SSH criptografado. As etapas de exemplo a seguir são para fornecer orientação sobre a instalação do SSH para essa comunicação.

1. Modificar o `/etc/ssh/ssh_config` arquivo

    Consulte a seguinte saída onde a `MACs hmac-sha1` linha foi adicionada:

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. Criar um par de chaves privada/pública

    Usando o comando de exemplo a seguir para gerar o par de chaves, não insira uma senha ao gerar uma chave.

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. Enviar a chave pública para o Microsoft Operations

    Envie a saída do `cat /root/.ssh/id_rsa.pub` comando (exemplo abaixo) para o Microsoft Operations para permitir que as ferramentas de instantâneo se comuniquem com o subsistema de armazenamento.

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>Habilitar a comunicação com o SAP HANA

As ferramentas de instantâneo se comunicam com SAP HANA e precisam de um usuário com as permissões apropriadas para iniciar e liberar o ponto de salvamento do banco de dados. O exemplo a seguir mostra a configuração do usuário do SAP HANA V2 e o `hdbuserstore` para comunicação com o banco de dados do SAP Hana.

Os comandos de exemplo a seguir configuram um usuário (AZACSNAP) no SYSTEMDB no SAP HANA 2.
banco de dados, altere o endereço IP, os nomes de email e as senhas conforme apropriado:

1. Conectar-se ao SYSTEMDB para criar o usuário

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. Criar o usuário

    Este exemplo cria o usuário AZACSNAP no SYSTEMDB.

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. Conceder as permissões de usuário

    Este exemplo define a permissão para o usuário AZACSNAP permitir a execução de um instantâneo de armazenamento consistente com o banco de dados.

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *Opcional* – impedir que a senha do usuário expire

    > [!NOTE]
    > Verifique com a política corporativa antes de fazer essa alteração.

   Este exemplo desabilita a expiração da senha para o usuário AZACSNAP, sem essa alteração a senha do usuário expirará, impedindo que os instantâneos sejam retirados corretamente.  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. Configurar o repositório de usuário de SAP HANA seguro (alterar a senha) Este exemplo usa o `hdbuserstore` comando do shell do Linux para configurar o SAP Hana armazenamento de usuário seguro.

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. Marque a SAP HANA armazenamento de usuário seguro para verificar se o repositório de usuários seguro está configurado corretamente, use o `hdbuserstore` comando para listar a saída semelhante ao exemplo a seguir. Mais detalhes sobre como usar `hdbuserstore` estão disponíveis no site do SAP.

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="using-ssl-for-communication-with-sap-hana"></a>Usando SSL para comunicação com o SAP HANA

A `azacsnap` ferramenta utiliza o comando da SAP Hana `hdbsql` para se comunicar com SAP Hana. Isso inclui o uso de opções de SSL ao criptografar a comunicação com o SAP HANA. `azacsnap` usa `hdbsql` as opções de SSL do comando da seguinte maneira.

Os itens a seguir são sempre usados ao usar a `azacsnap --ssl` opção:

- `-e` – Habilita a criptografia TLS encryptionTLS/SSL. O servidor escolhe o mais alto disponível.
- `-ssltrustcert` -Especifica se o certificado do servidor deve ser validado.
- `-sslhostnameincert "*"` -Especifica o nome do host usado para verificar a identidade do servidor. Ao especificar `"*"` como o nome do host, o nome do host do servidor não é validado.

A comunicação SSL também requer armazenamento de chaves e arquivos de repositório de confiança.  Embora seja possível que esses arquivos sejam armazenados em locais padrão em uma instalação do Linux, para garantir que o material de chave correto esteja sendo usado para vários sistemas de SAP HANA (ou seja, nos casos em que diferentes arquivos de repositório de chaves e de repositório de confiança são usados para cada sistema de SAP HANA) `azacsnap` , o espera que os arquivos de armazenamento de chaves e de armazenamento de confiança sejam armazenados no `securityPath` local, conforme especificado no `azacsnap` arquivo de configuração.

#### <a name="key-store-files"></a>Arquivos do repositório de chaves

- Se estiver usando vários SIDs com o mesmo material de chave, será mais fácil criar links para o local securityPath, conforme definido no `azacsnap` arquivo de configuração.  Verifique se esses valores existem para cada SID usando SSL.
  - Para OpenSSL:
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - Para commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- Se estiver usando vários SIDs com o material de chave diferente por SID, copie (ou mova e renomeie) os arquivos para o local securityPath, conforme definido no arquivo de configuração de SIDs `azacsnap` .
  - Para OpenSSL:
    - `mv key.pem <securityPath>/<SID>_keystore`
  - Para commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

Quando `azacsnap` chamadas `hdbsql` , ele será adicionado `-sslkeystore=<securityPath>/<SID>_keystore` à linha de comando.

#### <a name="trust-store-files"></a>Arquivos de repositório de confiança

- Se você estiver usando vários SIDs com o mesmo material de chave, crie links físicos no local securityPath, conforme definido no `azacsnap` arquivo de configuração.  Verifique se esses valores existem para cada SID usando SSL.
  - Para OpenSSL:
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - Para commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- Se estiver usando vários SIDs com o material de chave diferente por SID, copie (ou mova e renomeie) os arquivos para o local securityPath, conforme definido no arquivo de configuração de SIDs `azacsnap` .
  - Para OpenSSL:
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - Para commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> O `<SID>` componente dos nomes de arquivo deve ser o SAP Hana identificador de sistema em letras maiúsculas (por exemplo,,, `H80` `PR1` etc.)

Quando `azacsnap` chamadas `hdbsql` , ele será adicionado `-ssltruststore=<securityPath>/<SID>_truststore` à linha de comando.

Portanto, em execução `azacsnap -c test --test hana --ssl openssl` onde o `SID` está `H80` no arquivo de configuração, ele executaria as conexões da `hdbsql` seguinte maneira:

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> O `\` caractere é um encapsulamento de linha de comando para melhorar a clareza dos vários parâmetros passados na linha de comando.

## <a name="installing-the-snapshot-tools"></a>Instalando as ferramentas de instantâneo

O autoinstalador baixável foi projetado para facilitar a configuração e a execução das ferramentas de instantâneo com privilégios de usuário não raiz (por exemplo, azacsnap). O instalador irá configurar o usuário e colocar as ferramentas de instantâneo no `$HOME/bin` subdiretório usuários (padrão = `/home/azacsnap/bin` ).

O instalador automático tenta determinar as configurações e os caminhos corretos para todos os arquivos com base na configuração do usuário que executa a instalação (por exemplo, raiz). Se as etapas de configuração anteriores (habilitar a comunicação com o armazenamento e SAP HANA) forem executadas como raiz, a instalação copiará a chave privada e a `hdbuserstore` para o local do usuário de backup. No entanto, é possível para as etapas que permitem a comunicação com o back-end de armazenamento e a SAP HANA ser feita manualmente por um administrador experiente após a instalação.

> [!NOTE]
> Para SAP HANA anteriores em instalações de instância grande do Azure, o diretório de ferramentas de instantâneo pré-instaladas era `/hana/shared/<SID>/exe/linuxx86_64/hdb` .

Com as [etapas de pré-requisito](#prerequisites-for-installation) concluídas, agora é possível instalar as ferramentas de instantâneo usando o instalador automático da seguinte maneira:

1. Copie o autoinstalador baixado para o sistema de destino.
1. Execute o instalador automático como o `root` usuário, consulte o exemplo a seguir. Se necessário, faça o arquivo executável usando o `chmod +x *.run` comando.

A execução do comando Autoinstaller sem argumentos exibirá a ajuda sobre como usar o instalador para instalar as ferramentas de instantâneo da seguinte maneira:

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> O autoinstalador tem uma opção para extrair (-X) as ferramentas de instantâneo do pacote sem executar nenhuma criação e configuração de usuário. Isso permite que um administrador experiente conclua as etapas de instalação manualmente ou copie os comandos para atualizar uma instalação existente.

### <a name="easy-installation-of-snapshot-tools-default"></a>Instalação fácil de ferramentas de instantâneo (padrão)

O instalador foi projetado para instalar rapidamente as ferramentas de instantâneo para SAP HANA no Azure. Por padrão, se o instalador for executado apenas com a opção-I, ele executará as seguintes etapas:

1. Crie o usuário de instantâneo ' azacsnap ', o diretório base e defina a associação de grupo.
1. Configure o logon do usuário do azacsnap `~/.profile` .
1. Pesquisar o sistema de arquivos para diretórios a serem adicionados ao azacsnap `$PATH` , esses são normalmente os caminhos para as ferramentas de SAP Hana, como `hdbsql` e `hdbuserstore` .
1. Pesquise o sistema de arquivos em busca de diretórios para adicionar ao azacsnap `$LD_LIBRARY_PATH` . Muitos comandos exigem que um caminho de biblioteca seja definido para ser executado corretamente, isso o configura para o usuário instalado.
1. Copie as chaves SSH para armazenamento de back-end para azacsnap do usuário "raiz" (o usuário que está executando a instalação). Isso pressupõe que o usuário "raiz" já configurou a conectividade com o armazenamento
    - consulte a seção "[habilitar a comunicação com o armazenamento](#enable-communication-with-storage)".
1. Copie o repositório de usuário seguro de conexão de SAP HANA para o usuário de destino, azacsnap. Isso pressupõe que o usuário "raiz" já tenha configurado o repositório de usuários seguros – consulte a seção "habilitar a comunicação com o SAP HANA".
1. As ferramentas de instantâneo são extraídas no `/home/azacsnap/bin/` .
1. Os comandos no `/home/azacsnap/bin/` têm suas permissões definidas (Propriedade e bit executável, etc.).

O exemplo a seguir mostra a saída correta do instalador quando executado com a opção de instalação padrão.

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>Desinstalar as ferramentas de instantâneo

Se as ferramentas de instantâneo tiverem sido instaladas usando as configurações padrão, a desinstalação exigirá apenas a remoção do usuário para o qual os comandos foram instalados (padrão = azacsnap).

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>Instalação manual das ferramentas de instantâneo

Em alguns casos, é necessário instalar as ferramentas manualmente, mas a recomendação é usar a opção padrão do instalador para facilitar esse processo.

Cada linha que começa com um `#` caractere demonstra os comandos de exemplo após o caractere que são executados pelo usuário raiz. O `\` no final de uma linha é o caractere de continuação de linha padrão para um comando do Shell.

Como o superusuário raiz, uma instalação manual pode ser obtida da seguinte maneira:

1. Obtenha a ID do grupo "SAPs", nesse caso, a ID do grupo = 1010

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. Crie o usuário de instantâneo ' azacsnap ', o diretório base e defina a associação de grupo usando a ID de grupo da etapa 1.

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. Verifique se o logon do usuário azacsnap `.profile` existe.

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. Pesquisar o sistema de arquivos para diretórios a serem adicionados ao $PATH do azacsnap, esses são normalmente os caminhos para as ferramentas de SAP HANA, como `hdbsql` e `hdbuserstore` .

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Adicionar o caminho atualizado ao perfil do usuário

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. Pesquise o sistema de arquivos para os diretórios a serem adicionados ao _LIBRARY_PATH $LD do azacsnap.

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Adicionar o caminho de biblioteca atualizado ao perfil do usuário

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. Em instâncias grandes do Azure
    1. Copie as chaves SSH para armazenamento de back-end para azacsnap do usuário "raiz" (o usuário que está executando a instalação). Isso pressupõe que o usuário "raiz" já configurou a conectividade com o armazenamento
       > consulte a seção "[habilitar a comunicação com o armazenamento](#enable-communication-with-storage)".

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. Definir as permissões de usuário corretamente para os arquivos SSH

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. Em Azure NetApp Files
    1. Configure o caminho do usuário `DOTNET_BUNDLE_EXTRACT_BASE_DIR` de acordo com as diretrizes de extração de arquivo único do .NET Core.
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. Copie o repositório de usuário seguro de conexão de SAP HANA para o usuário de destino, azacsnap. Isso pressupõe que o usuário "raiz" já configurou o repositório de usuários seguro.
    > consulte a seção "[habilitar a comunicação com o SAP Hana](#enable-communication-with-sap-hana)".

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. Definir as permissões de usuário corretamente para os `hdbuserstore` arquivos

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. Extraia as ferramentas de instantâneo no/Home/azacsnap/bin/.

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. Tornar os comandos executáveis

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. Verifique se as permissões de propriedade corretas estão definidas no diretório base do usuário

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>Concluir a configuração das ferramentas de instantâneo

O instalador fornece as etapas a serem concluídas após a conclusão da instalação das ferramentas de instantâneo.
Siga estas etapas para configurar e testar as ferramentas de instantâneo.  Após o teste bem-sucedido, execute o primeiro instantâneo de armazenamento consistente com o banco de dados.

A saída a seguir mostra as etapas a serem concluídas após a execução do instalador com as opções de instalação padrão:

1. Alterar para a conta de usuário do instantâneo
    1. `su - azacsnap`
1. Configurar o repositório de usuários seguros do HANA
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. Alterar para o local dos comandos
   1. `cd /home/azacsnap/bin/`
1. Configurar o arquivo de detalhes do cliente
   1. `azacsnap -c configure –-configuration new`
1. Testar a conexão com o armazenamento....
   1. `azacsnap -c test –-test storage`
1. Testar a conexão com o HANA....
    1. sem SSL
       1. `azacsnap -c test –-test hana`
    1. com o SSL, você precisará escolher a opção de SSL correta
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. Executar seu primeiro backup de instantâneo
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

A etapa 2 será necessária se "[habilitar a comunicação com o SAP Hana](#enable-communication-with-sap-hana)" não tiver sido feito antes da instalação.

> [!NOTE]
> Os comandos de teste devem ser executados corretamente. Caso contrário, os comandos podem falhar.

## <a name="configuring-the-database"></a>Configurando o banco de dados

Esta seção explica como configurar o banco de dados do.

### <a name="sap-hana-configuration"></a>Configuração de SAP HANA

Há algumas alterações recomendadas a serem aplicadas a SAP HANA para garantir a proteção dos backups e catálogos de log. Por padrão, o `basepath_logbackup` e produzirá `basepath_catalogbackup` seus arquivos para o `$(DIR_INSTANCE)/backup/log` diretório, e é improvável que esse caminho esteja em um volume `azacsnap` configurado para fazer o instantâneo desses arquivos não serão protegidos com instantâneos de armazenamento.

Os exemplos de comando a seguir `hdbsql` destinam-se a demonstrar como definir os caminhos de log e catálogo para locais que estão em volumes de armazenamento que podem ser instantâneos `azacsnap` . Verifique se os valores na linha de comando correspondem à configuração de SAP HANA local.

### <a name="configure-log-backup-location"></a>Configurar local de backup de log

Neste exemplo, a alteração está sendo feita para o `basepath_logbackup` parâmetro.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>Configurar local de backup do catálogo

Neste exemplo, a alteração está sendo feita para o `basepath_catalogbackup` parâmetro. Primeiro, verifique se o `basepath_catalogbackup` caminho existe no sistema de arquivos, se não criar o caminho com a mesma Propriedade do diretório.

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Se o caminho precisar ser criado, o exemplo a seguir criará o caminho e definirá a propriedade e as permissões corretas. Esses comandos precisarão ser executados como raiz.

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

O exemplo a seguir irá alterar a configuração de SAP HANA.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>Verificar localizações de log e de backup do catálogo

Depois de fazer as alterações acima, confirme se as configurações estão corretas com o comando a seguir.
Neste exemplo, as configurações que foram definidas seguindo as diretrizes acima serão exibidas como configurações do sistema.

> Essa consulta também retorna as configurações padrão para comparação.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>Configurar tempo limite de backup de log

A configuração padrão para SAP HANA executar um backup de log é de 900 segundos (15 minutos). É recomendável reduzir esse valor para 300 segundos (ou seja, 5 minutos).  Em seguida, é possível executar backups regulares (por exemplo, a cada 10 minutos) adicionando o log_backups volume à seção outro volume do arquivo de configuração.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>Verificar tempo limite de backup de log

Depois de fazer a alteração no tempo limite do backup de log, verifique se isso foi definido da seguinte maneira.
Neste exemplo, as configurações que foram definidas serão exibidas como configurações do sistema, mas essa consulta também retornará as configurações padrão para comparação.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>Próximas etapas

- [Configurar Aplicativo Azure ferramenta de instantâneo consistente](azacsnap-cmd-ref-configure.md)
