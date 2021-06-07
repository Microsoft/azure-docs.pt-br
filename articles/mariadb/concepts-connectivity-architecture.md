---
title: Arquitetura de conectividade-banco de dados do Azure para MariaDB
description: Descreve a arquitetura de conectividade do banco de dados do Azure para o servidor MariaDB.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/11/2021
ms.openlocfilehash: f2ea671a6d44d12b3b37d5d06fa9405b7c589cdf
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559410"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Arquitetura de conectividade no banco de dados do Azure para MariaDB
Este artigo explica a arquitetura de conectividade do banco de dados do Azure para MariaDB e também como o tráfego é direcionado para a instância do banco de dados do Azure para MariaDB de clientes dentro e fora do Azure.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

A conexão com o banco de dados do Azure para MariaDB é estabelecida por meio de um gateway que é responsável por rotear conexões de entrada para o local físico do servidor em nossos clusters. O diagrama a seguir ilustra o fluxo de tráfego.

![Visão geral da arquitetura de conectividade](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)


À medida que o cliente se conecta ao banco de dados, a cadeia de conexão para o servidor é resolvida para o endereço IP do gateway. O gateway escuta no endereço IP na porta 3306. Dentro do cluster de banco de dados, o tráfego é encaminhado para o banco de dados do Azure apropriado para MariaDB. Portanto, para se conectar ao servidor, como de redes corporativas, é necessário abrir o **Firewall do lado do cliente para permitir que o tráfego de saída seja capaz de acessar nossos gateways**. Abaixo, você pode encontrar uma lista completa dos endereços IP usados por nossos gateways por região.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Banco de dados do Azure para endereços IP de gateway MariaDB

O serviço de gateway é hospedado em um grupo de nós de computação sem monitoração de estado situadas atrás de um endereço IP, o qual o cliente primeiro alcançaria ao tentar se conectar a um banco de dados do Azure para o servidor MariaDB. 

Como parte da manutenção contínua do serviço, atualizaremos periodicamente o hardware de computação que hospeda os gateways para garantir que forneceremos a experiência mais segura e de alto desempenho. Quando o hardware de gateway é atualizado, um novo anel dos nós de computação é criado primeiro. Esse novo anel serve o tráfego para todos os servidores de banco de dados do Azure recém-criados para MariaDB e ele terá um endereço IP diferente dos anéis de gateway mais antigos na mesma região para diferenciar o tráfego. Quando o novo anel estiver totalmente funcional, o hardware de gateway mais antigo que atende aos servidores existentes será planejado para encerramento. Antes de encerrar um hardware de gateway, os clientes que executam seus servidores e se conectarem a anéis de gateway mais antigos serão notificados por email e na portal do Azure, três meses de antecedência antes do encerramento. O encerramento de gateways pode afetar a conectividade com seus servidores se 

* Você codifica os endereços IP do gateway na cadeia de conexão do seu aplicativo. Não é **recomendável**. Você deve usar o FQDN (nome de domínio totalmente qualificado) do servidor no formato <servername> . MariaDB.Database.Azure.com, na cadeia de conexão do seu aplicativo. 
* Você não atualiza os endereços IP de gateway mais recentes no firewall do lado do cliente para permitir que o tráfego de saída seja capaz de alcançar nossos novos anéis de gateway.

A tabela a seguir lista os endereços IP do gateway do banco de dados do Azure para o gateway MariaDB para todas as regiões de data. As informações mais atualizadas dos endereços IP do gateway para cada região são mantidas na tabela a seguir. Na tabela abaixo, as colunas representam as seguintes:

* **Endereços IP do gateway:** Esta coluna lista os endereços IP atuais dos gateways hospedados na última geração de hardware. Se você estiver provisionando um novo servidor, recomendamos que você abra o Firewall do lado do cliente para permitir o tráfego de saída para os endereços IP listados nesta coluna.
* **Endereços IP do gateway (descomissionamento):** Esta coluna lista os endereços IP dos gateways hospedados em uma geração mais antiga de hardware que está sendo encerrado no momento. Se você estiver provisionando um novo servidor, poderá ignorar esses endereços IP. Se você tiver um servidor existente, continue a reter a regra de saída do firewall para esses endereços IP, pois ainda não o descomissionamos. Se você descartar as regras de firewall para esses endereços IP, poderá obter erros de conectividade. Em vez disso, espera-se que você adicione proativamente os novos endereços IP listados na coluna endereços IP do gateway à regra de firewall de saída assim que receber a notificação de encerramento. Isso garantirá quando o servidor for migrado para o hardware de gateway mais recente, não haverá interrupções na conectividade com o servidor.
* **Endereços IP do gateway (encerrado):** Essas colunas listam os endereços IP dos anéis de gateway, que são encerrados e não estão mais em operações. Você pode remover com segurança esses endereços IP da regra de firewall de saída. 


| **Nome da região** | **Endereços IP do gateway** |**Endereços IP do gateway (encerramento)** | **Endereços IP do gateway (encerrado)** |
|:----------------|:-------------------------|:-------------------------------------------|:------------------------------------------|
| Austrália Central| 20.36.105.0  | | |
| Central2 da Austrália     | 20.36.113.0  | | |
| Leste da Austrália | 13.75.149.87, 40.79.161.1     |  | |
| Sudeste da Austrália |191.239.192.109, 13.73.109.251   |  | |
| Brazil South |191.233.201.8, 191.233.200.16    |  | 104.41.11.5|
| Canadá Central |40.85.224.249  | | |
| Leste do Canadá | 40.86.226.166    | | |
| Centro dos EUA | 23.99.160.139, 52.182.136.37, 52.182.136.38 | 13.67.215.62 | |
| Leste da China | 139.219.130.35    | | |
| Leste da China 2 | 40.73.82.1  | | |
| Norte da China | 139.219.15.17    | | |
| Norte da China 2 | 40.73.50.0     | | |
| Leste da Ásia | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     | | |
| Leste dos EUA |40.71.8.203, 40.71.83.113 |40.121.158.30|191.238.6.43 |
| Leste dos EUA 2 | 40.70.144.38, 52.167.105.38  | 52.177.185.181 | |
| França Central | 40.79.137.0, 40.79.129.1  | | |
| Sul da França | 40.79.177.0     | | |
| Alemanha Central | 51.4.144.100     | | |
| Norte da Alemanha | 51.116.56.0 | |
| Nordeste da Alemanha | 51.5.144.179  | | |
| Centro-Oeste da Alemanha | 51.116.152.0 | |
| Centro da Índia | 104.211.96.159     | | |
| Sul da Índia | 104.211.224.146  | | |
| Oeste da Índia | 104.211.160.80    | | |
| Japan East | 40.79.192.23, 40.79.184.8 | 13.78.61.196 | |
| Oeste do Japão | 191.238.68.11, 40.74.96.6, 40.74.96.7     | 104.214.148.156 | |
| Coreia Central | 52.231.17.13   | 52.231.32.42 | |
| Sul da Coreia | 52.231.145.3     | 52.231.200.86 | |
| Centro-Norte dos EUA | 52.162.104.35, 52.162.104.36    | 23.96.178.199 | |
| Norte da Europa | 52.138.224.6, 52.138.224.7  | 40.113.93.91 |191.235.193.75 |
| Norte da África do Sul  | 102.133.152.0    | | |
| Oeste da África do Sul | 102.133.24.0   | | |
| Centro-Sul dos Estados Unidos |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| Sudeste da Ásia | 40.78.233.2, 23.98.80.12     | 104.43.15.0 | |
| Norte da Suíça | 51.107.56.0 ||
| Oeste da Suíça | 51.107.152.0| ||
| EAU Central | 20.37.72.64  | | |
| Norte dos EAU | 65.52.248.0    | | |
| Sul do Reino Unido | 51.140.184.11   | | |
| Oeste do Reino Unido | 51.141.8.11  | | |
| Centro-Oeste dos EUA | 13.78.145.25     | | |
| Europa Ocidental |13.69.105.208, 104.40.169.187 | 40.68.37.158 | 191.237.232.75 |
| Oeste dos EUA |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| Oeste dos EUA 2 | 13.66.226.202  | | |
||||

## <a name="connection-redirection"></a>Redirecionamento de conexão

O banco de dados do Azure para MariaDB dá suporte a uma política de conexão adicional, **redirecionamento**, que ajuda a reduzir a latência de rede entre aplicativos cliente e servidores MariaDB. Com esse recurso, depois que a sessão TCP inicial é estabelecida com o banco de dados do Azure para o servidor MariaDB, o servidor retorna o endereço de back-end do nó que hospeda o servidor MariaDB para o cliente. Depois disso, todos os pacotes subsequentes fluem diretamente para o servidor, ignorando o gateway. À medida que os pacotes fluem diretamente para o servidor, a latência e a taxa de transferência têm desempenho aprimorado.

Esse recurso tem suporte no banco de dados do Azure para servidores MariaDB com versões de mecanismo 10,2 e 10,3.

O suporte para redirecionamento está disponível na extensão de [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) do PHP, desenvolvida pela Microsoft e está disponível em [PECL](https://pecl.php.net/package/mysqlnd_azure). Consulte o artigo [Configurando o redirecionamento](./howto-redirection.md) para obter mais informações sobre como usar o redirecionamento em seus aplicativos.

> [!IMPORTANT]
> No momento, o suporte para redirecionamento na extensão [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) do PHP está na versão prévia.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="what-you-need-to-know-about-this-planned-maintenance"></a>O que você precisa saber sobre essa manutenção planejada?
Essa é uma alteração de DNS somente que a torna transparente para os clientes. Embora o endereço IP do FQDN seja alterado no servidor DNS, o cache DNS local será atualizado em 5 minutos e será feito automaticamente pelos sistemas operacionais. Após a atualização do DNS local, todas as novas conexões se conectarão ao novo endereço IP, todas as conexões existentes permanecerão conectadas ao endereço IP antigo sem interrupção até que os endereços IP antigos sejam completamente descomissionados. O endereço IP antigo levará, aproximadamente, de três a quatro semanas antes de ser encerrado; Portanto, ele não deve ter nenhum efeito sobre os aplicativos cliente.

### <a name="what-are-we-decommissioning"></a>O que estamos descomissionando?
Somente nós de gateway serão encerrados. Quando os usuários se conectam a seus servidores, a primeira parada da conexão é para o nó do gateway, antes que a conexão seja encaminhada ao servidor. Estamos encerrando anéis de gateway antigos (não anéis de locatário onde o servidor está em execução) consulte a [arquitetura de conectividade](#connectivity-architecture) para obter mais esclarecimentos.

### <a name="how-can-you-validate-if-your-connections-are-going-to-old-gateway-nodes-or-new-gateway-nodes"></a>Como você pode validar se suas conexões vão para nós de gateway antigos ou novos nós de gateway?
Execute ping no FQDN do servidor, por exemplo  ``ping xxx.mariadb.database.azure.com`` . Se o endereço IP retornado for um dos IPs listados em endereços IP do gateway (encerramento) no documento acima, significa que sua conexão está passando pelo gateway antigo. Contrarily, se o endereço IP retornado for um dos IPs listados em endereços IP de gateway, significa que a conexão está passando pelo novo gateway.

Você também pode testar por [PSPing](/sysinternals/downloads/psping) ou TCPPing o servidor de banco de dados do seu aplicativo cliente com a porta 3306 e garantir que o endereço IP de retorno não seja um dos endereços IP de descomissionamento

### <a name="how-do-i-know-when-the-maintenance-is-over-and-will-i-get-another-notification-when-old-ip-addresses-are-decommissioned"></a>Como fazer saber quando a manutenção terminará e receberei outra notificação quando os endereços IP antigos forem encerrados?
Você receberá um email para informá-lo quando iniciar o trabalho de manutenção. A manutenção pode levar até um mês, dependendo do número de servidores que precisamos migrar nas regiões al. Prepare seu cliente para se conectar ao servidor de banco de dados usando o FQDN ou usando o novo endereço IP da tabela acima. 

### <a name="what-do-i-do-if-my-client-applications-are-still-connecting-to-old-gateway-server-"></a>O que devo fazer se meus aplicativos cliente ainda estiverem se conectando ao servidor gateway antigo?
Isso indica que seus aplicativos se conectam ao servidor usando o endereço IP estático em vez do FQDN. Examine as cadeias de conexão e a configuração do pool de conexões, configuração AKS ou até mesmo no código-fonte.

### <a name="is-there-any-impact-for-my-application-connections"></a>Há algum impacto para minhas conexões de aplicativo?
Essa manutenção é apenas uma alteração de DNS, portanto, é transparente para o cliente. Depois que o cache DNS é atualizado no cliente (feito automaticamente pelo sistema operacional), toda a nova conexão se conectará ao novo endereço IP e toda a conexão existente ainda funcionará bem até que o endereço IP antigo seja encerrado por completo, o que geralmente várias semanas depois. E a lógica de repetição não é necessária para esse caso, mas é bom ver que o aplicativo tem a lógica de repetição configurada. Use o FQDN para se conectar ao servidor de banco de dados ou habilite a lista os novos ' endereços IP de gateway ' na cadeia de conexão do aplicativo.
Essa operação de manutenção não removerá as conexões existentes. Ele apenas faz com que as novas solicitações de conexão vá para o novo anel do gateway.

### <a name="can-i-request-for-a-specific-time-window-for-the-maintenance"></a>Posso solicitar uma janela de tempo específica para a manutenção? 
Como a migração deve ser transparente e não afetar a conectividade do cliente, esperamos que não haja nenhum problema para a maioria dos usuários. Examine seu aplicativo proativamente e certifique-se de usar o FQDN para se conectar ao servidor de banco de dados ou habilitar a lista de novos ' endereços IP de gateway ' na cadeia de conexão do aplicativo.

### <a name="i-am-using-private-link-will-my-connections-get-affected"></a>Estou usando o link privado, minhas conexões serão afetadas?
Não, essa é uma descomissionação de hardware de gateway e não tem relação com o link privado ou endereços IP privados, ele afetará apenas endereços IP públicos mencionados nos endereços IP de encerramento.


## <a name="next-steps"></a>Próximas etapas

* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para MariaDB usando o portal do Azure](./howto-manage-firewall-portal.md)
* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para MariaDB usando a CLI do Azure](./howto-manage-firewall-cli.md)