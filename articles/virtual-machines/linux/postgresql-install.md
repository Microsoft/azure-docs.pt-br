---
title: Configurar PostgreSQL em uma VM do Linux | Microsoft Docs
description: Saiba como instalar e configurar o PostgreSQL em uma máquina virtual Linux no Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 1a747363-0cc5-4ba3-9be7-084dfeb04651
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 086b36b347f214e1e9cdf44e4fb5a29fe501fa8b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667115"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instalar e configurar o PostgreSQL no Azure
PostgreSQL é um banco de dados avançado de código aberto semelhante ao Oracle e DB2. Ele inclui recursos corporativos como conformidade total com ACID, processamento transacional confiável e controle de simultaneidade de várias versões. Também oferece suporte a padrões como ANSI SQL e SQL/MED (inclusive wrappers de dados externos para Oracle, MySQL, MongoDB e muitos outros). Ele é altamente extensível com suporte para mais de 12 idiomas de procedimento, índices GIN e GiST, dados espaciais e vários recursos como NoSQL para aplicativos JSON ou de chave-valor.

Neste artigo, você aprenderá a instalar e configurar PostgreSQL em uma máquina virtual Azure que executa o Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>Instalar o PostgreSQL
> [!NOTE]
> Você já deve ter uma máquina virtual do Azure executando o Linux para concluir este tutorial. Para criar e configurar uma VM Linux antes de continuar, consulte o [tutorial da VM Linux do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Nesse caso, use a porta 1999 como a porta do PostgreSQL.  

Conecte-se à VM Linux criada via PuTTY. Se essa é a primeira vez que você está usando uma VM Linux do Azure, consulte [Como usar SSH com Linux no Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para saber como usar PuTTY para se conectar a uma VM Linux.

1. Execute o seguinte comando para alternar para a raiz (admin):
   
        # sudo su -
2. Algumas distribuições têm dependências que devem ser instaladas antes de instalar o PostgreSQL. Verifique sua distribuição nesta lista e execute o comando apropriado:
   
   * Linux baseado em Red Hat:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Linux baseado em Debian:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Baixe o PostgreSQL no diretório raiz e descompacte o pacote:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    Um exemplo foi mostrado acima. Você pode encontrar o endereço para download detalhado em [Índice de /pub/source/](https://ftp.postgresql.org/pub/source/).
4. Para iniciar a compilação, execute estes comandos:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Se você quiser compilar tudo o que for possível, incluindo a documentação (páginas HTML e man) e módulos adicionais (contrib), execute em vez disso o seguinte comando:
   
        # gmake install-world
   
    Você deverá receber a seguinte mensagem de confirmação:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Configurar PostgreSQL
1. (Opcional) Crie um link simbólico para encurtar a referência ao PostgreSQL a fim de não incluir o número de versão:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Crie um diretório para o banco de dados:
   
        # mkdir -p /opt/pgsql_data
3. Crie um usuário não raiz e modifique o perfil do usuário. Em seguida, alterne para esse novo usuário (chamado *postgres* em nosso exemplo):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Por motivos de segurança, o PostgreSQL usa um usuário não raiz para inicializar, iniciar ou desligar o banco de dados.
   > 
   > 
4. Edite o arquivo *bash_profile* inserindo os comandos abaixo. Essas linhas serão adicionadas ao final do arquivo *bash_profile*:
   
        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF
5. Execute o arquivo *bash_profile*:
   
        $ source .bash_profile
6. Valide a instalação usando o seguinte comando:
   
        $ which psql
   
    Se a instalação for bem-sucedida, você verá a seguinte resposta:
   
        /opt/pgsql/bin/psql
7. Você também pode verificar a versão do PostgreSQL:
   
        $ psql -V

8. Inicialize o banco de dados:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Você deverá receber o seguinte resultado:

![imagem](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Configurar o PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Execute os seguintes comandos:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Modifique duas variáveis no arquivo /etc/init.d/postgresql. O prefixo é definido como o caminho de instalação do PostgreSQL: **/opt/pgsql**. PGDATA é definido como o caminho de armazenamento de dados do PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![imagem](./media/postgresql-install/no2.png)

Altere o arquivo para torná-lo executável:

    # chmod +x /etc/init.d/postgresql

Inicie o PostgreSQL:

    # /etc/init.d/postgresql start

Verifique se o ponto de extremidade do PostgreSQL está em:

    # netstat -tunlp|grep 1999

Você deve ver o seguinte resultado:

![imagem](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Conectar-se ao banco de dados Postgres
Alterne para o usuário do postgres mais uma vez:

    # su - postgres

Crie um banco de dados Postgres:

    $ createdb events

Conecte-se ao banco de dados de eventos que você acabou de criar:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Criar e excluir uma tabela Postgres
Agora que estamos conectados ao banco de dados, podemos criar tabelas nele.

Por exemplo, crie um novo exemplo de tabela Postgres com o seguinte comando:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Agora você configurou uma tabela de quatro colunas com os seguintes nomes e restrições de coluna:

1. A coluna "nome" foi limitada pelo comando VARCHAR para ter menos de 20 caracteres.
2. A coluna "comida" indica a comida que cada pessoa trará. VARCHAR limita esse texto a menos de 30 caracteres.
3. A coluna "confirmada" registra se a pessoa confirmou a presença no jantar. Os valores aceitáveis são "S" e "N".
4. A coluna "date" mostra quando elas se inscreveram no evento. Postgres exige que as datas sejam gravadas como aaaa-mm-dd.

Você deverá ver o seguinte se a tabela tiver sido criada com êxito:

![imagem](./media/postgresql-install/no4.png)

Você também pode verificar a estrutura da tabela usando o seguinte comando:

![imagem](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Adicionar dados a uma tabela
Primeiro, insira as informações em uma linha:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Você deverá ver este resultado:

![imagem](./media/postgresql-install/no6.png)

É possível adicionar algumas pessoas à tabela. Aqui estão algumas opções, ou você pode criar as suas próprias:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Mostrar tabelas
Use o seguinte comando para mostrar uma tabela:

    select * from potluck;

A saída é:

![imagem](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Excluir dados de uma tabela
Use o seguinte comando para excluir dados de uma tabela:

    delete from potluck where name=’John’;

Isso exclui todas as informações na linha "John". A saída é:

![imagem](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Atualizar dados em uma tabela
Use o seguinte comando para atualizar dados em uma tabela. Por esse motivo, Sandy confirmou que eles são participar, portanto, vamos alterar o RSVP de "N" para "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Obter mais informações sobre o PostgreSQL
Agora que você concluiu a instalação do PostgreSQL em uma VM do Linux do Azure, aproveite-o usando-o no Azure. Para saber mais sobre o PostgreSQL, visite o [site do PostgreSQL](https://www.postgresql.org/).

