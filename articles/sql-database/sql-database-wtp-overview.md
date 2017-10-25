---
title: "Exemplo de aplicativo de multilocatário do Banco de Dados SQL do Azure – Wingtip SaaS | Microsoft Docs"
description: "Aprenda usando um aplicativo de multilocatário de exemplo que usa o Banco de Dados SQL do Azure, o exemplo do Wingtip SaaS"
keywords: tutorial do banco de dados SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: sstein
ms.openlocfilehash: 68a9d97a881f3a7628a08b66091c3feb4c4dbbfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>Introdução a um exemplo de aplicativo SaaS multilocatário do Banco de Dados SQL

O aplicativo *SaaS Wingtip* é um aplicativo multilocatário de exemplo que demonstra as vantagens exclusivas do Banco de Dados SQL. O aplicativo usa um padrão de aplicativo SaaS de um banco de dados por locatário para atender a vários locatários. O aplicativo foi projetado para demonstrar os recursos do Banco de Dados SQL do Azure que habilitam cenários de SaaS, incluindo vários padrões de design e gerenciamento de SaaS. Para obter rapidamente e executar, o aplicativo Wingtip SaaS é implantado em menos de cinco minutos!

Os scripts de gerenciamento e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Para executar os scripts, [baixe a pasta Módulos de Aprendizado](#download-and-unblock-the-wingtip-saas-scripts) em seu computador local.

## <a name="sql-database-wingtip-saas-tutorials"></a>Tutoriais do SaaS Wingtip do Banco de Dados SQL

Depois de implantar o aplicativo, explore a seguinte coleção de tutoriais que aproveitam a implantação inicial. Esses tutoriais exploram padrões comuns de SaaS que aproveitam os recursos internos do banco de dados SQL, SQL Data Warehouse e outros serviços do Azure. Os tutoriais incluem scripts PowerShell, com explicações detalhadas que simplificam bastante a compreensão e a implementação dos mesmos padrões de gerenciamento de SaaS em seus aplicativos.


| Tutorial | Descrição |
|:--|:--|
|[Implantar e explorar o aplicativo SaaS Wingtip](sql-database-saas-tutorial.md)| **COMECE AQUI!** Implantar e explorar o aplicativo SaaS Wingtip à sua assinatura do Azure. |
|[Provisionar e catalogar locatários](sql-database-saas-tutorial-provision-and-catalog.md)| Saiba como o aplicativo se conecta aos locatários usando um banco de dados do catálogo, e como o catálogo mapeia locatários para seus dados. |
|[Monitorar e gerenciar o desempenho](sql-database-saas-tutorial-performance-monitoring.md)| Saiba como usar os recursos de monitoramento do Banco de Dados SQL e como definir alertas quando os limites de desempenho são excedidos. |
|[Monitorar com o Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md) | Saiba mais sobre como usar [Log Analytics](../log-analytics/log-analytics-overview.md) para monitorar grandes quantidades de recursos, em vários pools. |
|[Restaurar um único locatário](sql-database-saas-tutorial-restore-single-tenant.md)| Saiba como restaurar um banco de dados de locatário em um ponto anterior no tempo. As etapas para restaurar um banco de dados paralelo, deixando o banco de dados existente do locatário online, também estão incluídas. |
|[Gerenciar esquemas de locatário](sql-database-saas-tutorial-schema-management.md)| Saiba como atualizar o esquema e atualizar dados de referência, em todos os locatários Wingtip SaaS. |
|[Executar análise local](sql-database-saas-tutorial-adhoc-analytics.md) | Criar um banco de dados de análise ad hoc e executar consultas distribuídas em tempo real em todos os locatários.  |
|[Executar análise de locatário](sql-database-saas-tutorial-tenant-analytics.md) | Extrai dados de locatário em um análise banco de dados ou data warehouse para executar consultas analíticas offline. |



## <a name="application-architecture"></a>Arquitetura do aplicativo

O aplicativo SaaS Wingtip usa o modelo de banco de dados por locatário e usa pools elásticos de SQL para maximizar a eficiência. Para provisionar e mapear locatários para os seus dados, é usado um banco de dados do catálogo. O aplicativo SaaS Wingtip principal, usa um pool com três locatários de exemplo, além do banco de dados do catálogo. Completando muitos dos tutoriais do SaaS Wingtip estão os complementos para a implantação inicial, introduzindo bancos de dados analíticos, bancos de dados gerenciamento de esquema, etc.


![Arquitetura de SaaS Wingtip](media/sql-database-wtp-overview/app-architecture.png)


Ao percorrer os tutoriais e trabalhar com o aplicativo, é importante ter em mente os padrões de SaaS como eles se relacionam com a camada de dados. Em outras palavras, concentre-se na camada de dados e não na análise excessiva do aplicativo em si. A compreensão da implementação desses padrões de SaaS é a chave para implementar esses padrões nos seus aplicativos, considerando todas as modificações necessárias para as suas necessidades corporativas específicas.

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Baixar e desbloquear os scripts do SaaS Wingtip

Conteúdos executáveis (scripts, dlls) podem ser bloqueados pelo Windows quando arquivos zip são baixados de uma fonte externa e extraídos. Ao extrair os scripts de um arquivo zip, ***siga as etapas abaixo para desbloquear o arquivo. zip antes da extração***. Isso garante que os scripts podem ser executados.

1. Navegue até [o repositório github SaaS do Wingtip](https://github.com/Microsoft/WingtipSaaS).
1. Clique em **Clonar ou baixar**.
1. Clique em **Baixar ZIP** e salve o arquivo.
1. Clique com botão direito no arquivo **WingtipSaaS master.zip** e selecione **Propriedades**.
1. Na guia **geral**, selecione **Desbloquear**.
1. Clique em **OK**.
1. Extraia os arquivos.

Os scripts estão localizados na pasta *... \\módulos de aprendizado\\WingtipSaaS mester*.


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>Trabalhando com os scripts do PowerShell do SaaS Wingtip

Para aproveitar ao máximo o exemplo, você precisa saber mais sobre os scripts fornecidos. Usar pontos de interrupção e percorrer os scripts, examinando os detalhes de como os diferentes padrões de SaaS são implementados. Para percorrer facilmente os módulos e scripts fornecidos para a melhor compreensão, é recomendável usar o [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Atualizar o arquivo de configuração para sua implantação

Editar o **UserConfig.psm1** arquivo com o valor de usuário e grupo de recursos que podem ser definidos durante a implantação:

1. Abra o *ISE do PowerShell* e carregue... \\UserConfig.psm1\\ do *Módulo de Aprendizado* 
1. Atualize *ResourceGroupName* e *Nomeie* com os valores específicos para sua implantação (somente nas linhas 10 e 11).
1. Salve as alterações!

Definir esses valores aqui simplesmente impede que você precise atualizar esses valores específicos de implantação em cada script.

### <a name="execute-scripts-by-pressing-f5"></a>Executar Scripts pressionando F5

Vários scripts usam *$PSScriptRoot* para permitir a navegação em pastas e *$PSScriptRoot* é avaliada somente quando o script é executado, ao pressionar **F5**.  Realçar e executar uma seleção (**F8**) pode resultar em erros, então pressione **F5** ao executar scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Percorrer os scripts para examinar a implementação

É a melhor maneira de entender os scripts, percorrendo-los para ver o que fazem. Confira os scripts para **demonstração** incluídos que apresentam uma forma fácil de seguir o fluxo de trabalho de alto nível. Os script para **demonstração** mostram as etapas necessárias para executar cada tarefa, portanto, defina pontos de interrupção e analise mais detalhadamente em chamadas individuais para ver os detalhes de implementação para os diferentes padrões de SaaS.

Dicas para explorar e depurar scripts do PowerShell:

* Abra os scripts para **demonstração** no ISE do PowerShell.
* Execute ou continue com **F5** (o uso de **F8** não é recomendável porque o *$PSScriptRoot* não é avaliado durante a execução de seleções de um script).
* Coloque pontos de interrupção, clicando ou selecionando uma linha e pressionando **F9**.
* Passe por uma função ou chamada de script usando **F10**.
* Intervenha em uma função ou chamada de script usando **F11**.
* Saia da função atual ou da chamada de script usando **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Explorar o esquema de banco de dados e executar consultas SQL usando o SSMS

Use o [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para se conectar e procurar os servidores e bancos de dados do aplicativo.

A implantação inicialmente tem dois servidores de Banco de Dados SQL aos quais se conectar – o servidor *tenants1-&lt;User&gt;* e o servidor *catalog-&lt;User&gt;*. Para garantir uma conexão bem-sucedida de demonstração, ambos os servidores têm um [regra de firewall](sql-database-firewall-configure.md) permitindo todos os IPs.


1. Abra o *SSMS* e se conecte ao servidor *tenants1-&lt;User&gt;.database.windows.net*.
1. Clique em **Conectar** > **Mecanismo de Banco de Dados...**:

   ![servidor catalog](media/sql-database-wtp-overview/connect.png)

1. As credenciais de demonstração são: logon = *developer*, senha = *P@ssword1*

   ![connection](media\sql-database-wtp-overview\tenants1-connect.png)

1. Repita as etapas 2 a 3 e conecte-se ao servidor *catalog-&lt;User&gt;.database.windows.net*.

Após se conectar com êxito, você deverá ver ambos os servidores. Sua lista de bancos de dados pode ser diferente, dependendo dos locatários que você tenha configurado:

![pesquisador de objetos](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>Próximas etapas

[Implantar o aplicativo SaaS Wingtip](sql-database-saas-tutorial.md)