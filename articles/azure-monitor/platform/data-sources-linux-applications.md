---
title: Coletar o desempenho de aplicativos do Linux no Azure Monitor | Microsoft Docs
description: Este artigo fornece detalhes para configurar o Agente do Log Analytics para Linux para coletar contadores de desempenho do MySQL e Apache HTTP Server.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: ea74440a5c8a9a2584e742ec72ccf888b6bb5ad9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628907"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Coletar contadores de desempenho para aplicativos do Linux no Azure Monitor 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Este artigo fornece detalhes sobre como configurar o [agente do Log Analytics para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) para coletar contadores de desempenho para aplicativos específicos no Azure Monitor.  Os aplicativos incluídos neste artigo são:  

- [MySQL](#mysql)
- [Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Se o MySQL Server ou MariaDB for detectado no computador quando o Agente do Log Analytics for instalado, um provedor de monitoramento de desempenho para o MySQL Server será automaticamente instalado. Esse provedor se conecta ao servidor MySQL/MariaDB local para expor as estatísticas de desempenho. Você precisa configurar as credenciais de usuário do MySQL para que o provedor possa acessar o MySQL Server.

### <a name="configure-mysql-credentials"></a>Configurar as credenciais do MySQL
O provedor de OMI do MySQL requer um usuário do MySQL pré-configurado e a instalação de bibliotecas do cliente do MySQL para consultar as informações de desempenho e integridade da instância do MySQL.  Essas credenciais são armazenadas em um arquivo de autenticação que é armazenado no agente Linux.  O arquivo de autenticação especifica que endereço de associação e porta em que a instância do MySQL está escutando e quais credenciais usar para obter métricas.  

Durante a instalação do Agente do Log Analytics para Linux, o provedor de OMI do MySQL examinará os arquivos de configuração my.cnf do MySQL (locais padrão) para obter o endereço de associação e a porta e para definir parcialmente o arquivo de autenticação de OMI do MySQL.

O arquivo de autenticação do MySQL é armazenado em `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Formato do arquivo de autenticação
A seguir, temos o formato para o arquivo de autenticação de OMI do MySQL

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

As entradas no arquivo de autenticação são descritas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--|:--|
| Porta | Representa a porta atual em que a instância do MySQL está escutando. A porta 0 significa que as propriedades a seguir são usadas para a instância padrão. |
| Endereço de Ligação| Endereço de associação atual de MySQL. |
| Nome de Usuário| Usuário do MySQL a utilizar para monitorar a instância do servidor MySQL. |
| Senha codificada em Base64| Senha do usuário de monitoramento do MySQL codificada em Base64. |
| Atualização Automática| Especifica se será feita uma nova varredura em busca de alterações no arquivo my.cnf e uma substituição do arquivo de Autenticação de OMI do MySQL quando o Provedor de OMI do MySQL é atualizado. |

### <a name="default-instance"></a>Instância padrão
O arquivo de autenticação de OMI do MySQL pode definir um número da porta e uma instância padrão para facilitar o gerenciamento de várias instâncias do MySQL em um host Linux.  A instância padrão é indicada por uma instância com a porta 0. Todas as instâncias adicionais herdarão as propriedades definidas da instância padrão, a menos que elas especifiquem valores diferentes. Por exemplo, se a instância do MySQL que estiver escutando na porta “3308” for adicionada, o endereço de associação, o nome de usuário e a senha codificada em Base64 da instância padrão serão usados para experimentar e monitorar a instância que escuta na porta 3308. Se a instância na porta 3308 estiver associada a outro endereço e usar o mesmo par de nome de usuário e senha do MySQL, somente o endereço de associação será necessário, enquanto as outras propriedades serão herdadas.

A tabela a seguir tem configurações de instância de exemplo 

| DESCRIÇÃO | Arquivo |
|:--|:--|
| Instância padrão e instância com porta 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Instância padrão e instância com porta 3308 e nome de usuário e senha diferentes. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Programa de Arquivo de Autenticação de OMI do MySQL
Incluído com a instalação do provedor de OMI do MySQL temos um programa de arquivo de autenticação de OMI do MySQL, que pode ser usado para editar o arquivo de autenticação de OMI do MySQL. O programa de arquivo de autenticação pode ser encontrado no local a seguir.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> O arquivo de credenciais deve ser legível para a conta do omsagent. É recomendável executar o comando mycimprovauth como omsgent.

A tabela a seguir fornece detalhes sobre a sintaxe para usar mycimprovauth.

| Operação | Exemplo | DESCRIÇÃO
|:--|:--|:--|
| autoupdate *false or true* | mycimprovauth autoupdate false | Define se o arquivo de autenticação será atualizado automaticamente ao reiniciar ou atualizar. |
| default *bind-address username password* | mycimprovauth default 127.0.0.1 root pwd | Define a instância padrão no arquivo de autenticação de OMI do MySQL.<br>O campo de senha deve ser inserido em texto sem formatação – a senha no arquivo de autenticação de OMI do MySQL será codificada em Base 64. |
| delete *default or port_num* | mycimprovauth 3308 | Exclui a instância especificada por um padrão ou por número da porta. |
| ajuda | mycimprov help | Imprime uma lista de comandos a serem usados. |
| print | mycimprov print | Imprime uma forma fácil de ler o arquivo de autenticação de OMI do MySQL. |
| update port_num *bind-address username password* | mycimprov update 3307 127.0.0.1 root pwd | Atualiza a instância especificada ou adiciona a instância se ela não existir. |

Os comandos de exemplo a seguir definem uma conta de usuário padrão para o servidor MySQL no localhost.  O campo de senha deve ser inserido em texto sem formatação – a senha no arquivo de autenticação de OMI do MySQL será codificada em Base 64

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Permissões de banco de dados necessárias para contadores de desempenho do MySQL
O usuário do MySQL requer acesso para as consultas a seguir para coletar dados de desempenho do MySQL Server. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


O usuário do MySQL também requer acesso SELECT às tabelas padrão a seguir.

- information_schema
- mysql. 

Esses privilégios podem ser concedidos por meio da execução dos seguintes comandos de concessão.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Para conceder permissões a um usuário de monitoramento do MySQL, o usuário que receber a concessão deverá ter o privilégio 'GRANT option', além do privilégio concedido.

### <a name="define-performance-counters"></a>Definir contadores de desempenho

Depois de configurar o agente do Log Analytics para Linux para enviar dados para o Azure Monitor, você precisará configurar os contadores de desempenho a serem coletados.  Use o procedimento descrito em [Fontes de dados de desempenho do Windows e do Linux no Azure Monitor](data-sources-performance-counters.md) com os contadores na tabela a seguir.

| Nome do Objeto | Nome do contador |
|:--|:--|
| Banco de Dados MySQL | Espaço em Disco em Bytes |
| Banco de Dados MySQL | Tabelas |
| MySQL Server | % de Conexão Anulada |
| MySQL Server | % de Uso da Conexão |
| MySQL Server | Uso de Espaço em Disco em Bytes |
| MySQL Server | % da verificação de tabela completa |
| MySQL Server | % de Ocorrências no Pool de Buffers InnoDB |
| MySQL Server | % de Uso do Pool de Buffers InnoDB |
| MySQL Server | % de Uso do Pool de Buffers InnoDB |
| MySQL Server | % de Ocorrências no Cache de Chaves |
| MySQL Server | % de Uso do Cache de Chaves |
| MySQL Server | % de Gravação no Cache de Chaves |
| MySQL Server | % de Ocorrências no Cache de Consulta |
| MySQL Server | % de Remoções do Cache de Consulta |
| MySQL Server | % de Uso do Cache de Consulta |
| MySQL Server | % de Ocorrências no Cache de Tabela |
| MySQL Server | % de Uso do Cache de Tabela |
| MySQL Server | % de Contenção de Bloqueio de Tabela |

## <a name="apache-http-server"></a>Apache HTTP Server 
Se o Apache HTTP Server for detectado no computador quando o pacote omsagent for instalado, um provedor de monitoramento de desempenho para o Apache HTTP Server será automaticamente instalado. Esse provedor se baseia em um módulo do Apache que deve ser carregado no Apache HTTP Server para acessar os dados de desempenho. Você pode carregar o módulo com o seguinte comando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Para descarregar o módulo de monitoramento do Apache, execute o seguinte comando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definir contadores de desempenho

Depois de configurar o agente do Log Analytics para Linux para enviar dados para o Azure Monitor, você precisará configurar os contadores de desempenho a serem coletados.  Use o procedimento descrito em [Fontes de dados de desempenho do Windows e do Linux no Azure Monitor](data-sources-performance-counters.md) com os contadores na tabela a seguir.

| Nome do Objeto | Nome do contador |
|:--|:--|
| Apache HTTP Server | Trabalhos Ocupados |
| Apache HTTP Server | Trabalhos ociosos |
| Apache HTTP Server | % de Trabalhos Ocupados |
| Apache HTTP Server | % Total da CPU |
| Host Virtual Apache | Erros por Minuto – Cliente |
| Host Virtual Apache | Erros por Minuto – Servidor |
| Host Virtual Apache | KB por Solicitação |
| Host Virtual Apache | KB de Solicitações por Segundo |
| Host Virtual Apache | Solicitações por Segundo |



## <a name="next-steps"></a>Próximas etapas
* [Coletar contadores de desempenho](data-sources-performance-counters.md) de agentes do Linux.
* Saiba mais sobre [registrar consultas](../log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções. 