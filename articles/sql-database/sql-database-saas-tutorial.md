---
title: "Tutorial de SaaS multilocatário- Banco de Dados SQL do Azure | Microsoft Docs"
description: "Implantar e explore o aplicativo SaaS do Wingtip multilicatário, que demonstra os padrões SaaS usando o Banco de dados SQL do Azure."
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: sstein
ms.openlocfilehash: 2e6816ce2740b0325c6f59338f0365c6b40539f3
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="deploy-and-explore-a-multi-tenant-application-that-uses-azure-sql-database---wingtip-saas"></a>Implantar e explorar um aplicativo SaaS multilocatário que usa o Banco de dados SQL do Azure

Neste tutorial, você implanta e explora o aplicativo SaaS do Wingtip. O aplicativo usa um padrão de aplicativo SaaS de um banco de dados por locatário para atender a vários locatários. O aplicativo foi projetado para demonstrar os recursos do Banco de dados SQL do Azure que habilitam de forma simples cenários SaaS.

Cinco minutos depois de clicar no botão *Implantar no Azure* abaixo, você terá um aplicativo SaaS multilocatário, usando o Banco de Dados SQL, em funcionamento na nuvem. O aplicativo é implantado com três locatários de exemplo, cada um com seu próprio banco de dados, todos implantados em um pool elástico do SQL. O aplicativo é implantado na sua assinatura do Azure, dando a você acesso completo para explorar e trabalhar com os componentes individuais do aplicativo. Os scripts de gerenciamento e o código-fonte do aplicativo estão disponíveis no repositório GitHub do WingtipSaaS.


Neste tutorial, você aprende:

> [!div class="checklist"]

> * Como implantar o aplicativo SaaS do Wingtip
> * Onde obter o código-fonte do aplicativo e scripts de gerenciamento
> * Sobre os servidores, pools e bancos de dados que constituem o aplicativo
> * Quantos locatários são mapeados para seus dados com o *catálogo*
> * Como provisionar um novo locatário
> * Como monitorar a atividade de locatário no aplicativo

Para explorar os vários padrões de design e gerenciamento de SaaS, uma [série de tutoriais relacionados](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials) que aproveita essa implantação inicial está disponível. Ao percorrer os tutoriais, aprofunde-se nos scripts fornecidos e examine como os diferentes padrões de SaaS são implementados. Percorra os scripts em cada tutorial para obter uma maior compreensão de como implementar os diversos recursos de Banco de Dados SQL que simplificam o desenvolvimento de aplicativos SaaS.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-saas-application"></a>Implantar o aplicativo SaaS do Wingtip

Implantar o aplicativo SaaS do Wingtip:

1. Clicar no botão **Implantar no Azure** abre o portal do Azure para o modelo de implantação SaaS do Wingtip. O modelo requer dois valores de parâmetros; um nome para um novo grupo de recursos e um nome de usuário que distingue esta implantação de outras implantações do aplicativo SaaS do Wingtip. A próxima etapa fornece detalhes para definir esses valores.

   Certifique-se de observar os valores exatos que usa, pois você precisará inseri-los em um arquivo de configuração.

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Insira os valores de parâmetros necessários para a implantação:

    > [!IMPORTANT]
    > Algumas autenticações e firewalls de servidor estão intencionalmente desprotegidos para fins de demonstração. **Criar um novo grupo de recursos** e não usar grupos de recursos, servidores ou pools existentes. Não use este aplicativo ou todos os recursos que cria, para a produção. Exclua esse grupo de recursos quando tiver terminado com o aplicativo para interromper a cobrança relacionada.

    * **Grupo de recursos** – selecione **Criar novo** e forneça um **Nome** para o grupo de recursos. Selecione uma **Localização** na lista suspensa.
    * **Usuário** - Alguns recursos exigem nomes que são globalmente exclusivos. Para garantir a exclusividade, toda vez que você implanta, o aplicativo fornece um valor para diferenciar os recursos que você cria dos recursos criados por outros usuários que estão implantando o aplicativo Wingtip. Recomenda-se usar um nome de **Usuário** curto, como suas iniciais com algum número (por exemplo, *bg1*) e, em seguida, usá-lo no nome do grupo de recursos (por exemplo, *wingtip-bg1*). O parâmetro **Usuário** só pode conter letras, números e hifens (sem espaços). O primeiro e último caracteres devem ser uma letra ou um número (recomenda-se todas em minúsculas).


1. **Implantar o aplicativo**.

    * Clique se você concordar com os termos e condições.
    * Clique em **Comprar**.

1. Monitore o status da implantação clicando em **Notificações** (o ícone de sino à direita da caixa de pesquisa). A implantação do aplicativo Wingtip SaaS leva aproximadamente cinco minutos.

   ![implantação bem-sucedida](media/sql-database-saas-tutorial/succeeded.png)

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Baixar e desbloquear os scripts SaaS do Wingtip

Enquanto o aplicativo for implantado, baixe os scripts de gerenciamento e de código-fonte.

> [!IMPORTANT]
> Conteúdos executáveis (scripts, dlls) podem ser bloqueados pelo Windows quando arquivos zip são baixados de uma fonte externa e extraídos. Ao extrair os scripts de um arquivo zip, siga as etapas abaixo para desbloquear o arquivo. zip antes da extração. Isso garante que os scripts podem ser executados.

1. Navegue até [o repositório github SaaS do Wingtip](https://github.com/Microsoft/WingtipSaaS).
1. Clique em **Clonar ou baixar**.
1. Clique em **Baixar ZIP** e salve o arquivo.
1. Clique com botão direito no arquivo **WingtipSaaS master.zip** e selecione **Propriedades**.
1. Na guia **Geral**, selecione **Desbloquear** e clique em **Aplicar**.
1. Clique em **OK**.
1. Extraia os arquivos.

Os scripts estão localizados na pasta *... \\módulos de aprendizado\\WingtipSaaS mester*.

## <a name="update-the-configuration-file-for-this-deployment"></a>Atualize o arquivo de configuração para sua implantação

Antes de executar os scripts, defina o *grupo de recursos* e valores de *usuário* em **UserConfig.psm1**. Defina essas variáveis para os valores definidos durante a implantação.

1. Abra ...\\Módulos de aprendizado\\*UserConfig.psm1* no *ISE do PowerShell*
1. Atualize *ResourceGroupName* e *Nomeie* com os valores específicos para sua implantação (somente nas linhas 10 e 11).
1. Salve as alterações!

Definir esses valores aqui simplesmente impede que você precise atualizar esses valores específicos de implantação em cada script.

## <a name="run-the-application"></a>Executar o aplicativo

O aplicativo demonstra locais, como teatros, clubes de jazz, clubes esportivos, que hospedam eventos. Os locais se registram como os clientes (ou locatários) da plataforma Wingtip para uma maneira fácil de listar eventos e vender ingressos. Cada local obtém um aplicativo Web personalizado para gerenciar e listar seus eventos e vender ingressos, independente e isolado de outros locatários. Nos bastidores, cada locatário obtém um banco de dados SQL implantado em um pool elástico do SQL.

Um **Hub de Eventos** central fornece uma lista de URLs de locatários específica da sua implantação.

1. Abra o _Hub de Eventos_ em seu navegador da Web: http://events.wtp.&lt;USER&gt;.trafficmanager.net (substitua pelo seu nome de usuário de implantação):

    ![hub de eventos](media/sql-database-saas-tutorial/events-hub.png)

1. Clique em **Fabrikam Jazz Club** no *Hub de Eventos*.

   ![Eventos](./media/sql-database-saas-tutorial/fabrikam.png)


Para controlar a distribuição das solicitações de entrada, o aplicativo usa o [*Gerenciador de Tráfego do Azure*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview). As páginas de eventos, que são específicas do locatário, exigem que os nomes de locatário sejam incluídos nas URLs. Todas as URLs de locatário incluem o seu valor específico de *User* e seguem este formato: http://events.wtp.&lt;USER&gt;.trafficmanager.net/*fabrikamjazzclub*. O aplicativo de eventos analisa o nome do locatário com base na URL e o utiliza para criar uma chave para acessar um catálogo usando o [*gerenciamento de mapa de fragmentos*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management). O catálogo mapeia a chave para o local do banco de dados do locatário. O **Hub de Eventos** usa metadados estendidos no catálogo para recuperar o nome do locatário associado a cada banco de dados para fornecer a lista de URLs.

Em um ambiente de produção, você normalmente criaria um registro DNS CNAME para [*apontar um domínio de Internet da empresa*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) para o perfil do gerenciador de tráfego.

## <a name="start-generating-load-on-the-tenant-databases"></a>Iniciar a geração de carga nos bancos de dados de locatário

Agora que o aplicativo está implantado, vamos colocá-lo em funcionamento! O script do PowerShell *Demo-LoadGenerator* começa uma execução de carga de trabalho em todos os bancos de dados de locatário. A carga real em um aplicativo SaaS é normalmente esporádica e imprevisível. Para simular esse tipo de carga, o gerador produz uma carga distribuída em todos os locatários com intermitências aleatórias em cada locatário, que ocorrem em intervalos aleatórios. Por isso, levará vários minutos para que o padrão de carga surja, portanto, é melhor permitir que o gerador seja executado por pelo menos três ou quatro minutos antes de monitorar a carga.

1. No *ISE do PowerShell*, abra o script ...\\Módulos de Aprendizado\\Utilitários\\*Demo-LoadGenerator.ps1*.
1. Pressione **F5** para executar o script e iniciar o gerador de carga (deixe os valores de parâmetro padrão por enquanto).

> [!IMPORTANT]
> Para executar outros scripts, abra uma nova janela do ISE do PowerShell. O gerador de carga está sendo executado como uma série de trabalhos em sua sessão local do PowerShell. O script *Demo-LoadGenerator.ps1* inicia o script do gerador de carga real, que é executado como uma tarefa em primeiro plano além de uma série de trabalhos de geração de cargas em segundo plano. Um trabalho do gerador de carga é chamado para cada banco de dados registrado no catálogo. Os trabalhos estão em execução na sua sessão local do PowerShell, então fechar a sessão do PowerShell interrompe todos os trabalhos. Se você suspender seu computador, a geração de carga será pausada e continuará quando você ativá-lo novamente.

Depois que o gerador de carga invoca os trabalhos de geração de carga para cada locatário, a tarefa em primeiro plano permanece em um estado de invocação de trabalho, onde ela inicia trabalhos de plano de fundo adicionais para quaisquer novos locatários que forem provisionados subsequentemente. Você pode usar *Ctrl-C* ou pressionar o botão *Parar* para interromper a tarefa em primeiro plano, mas trabalhos em segundo plano existentes continuarão gerando carga em cada banco de dados. Se você precisar monitorar e controlar os trabalhos em segundo plano, use *Get-Job*, *Receive-Job* e *Stop-Job*. Enquanto a tarefa em primeiro plano está em execução, não é possível usar a mesma sessão do PowerShell para executar outros scripts. Para executar outros scripts, abra uma nova janela do ISE do PowerShell.

Se deseja reiniciar a sessão do gerador de carga, por exemplo, com parâmetros diferentes, você pode interromper a tarefa em primeiro plano e, em seguida, executar novamente o script *Demo-LoadGenerator.ps1*. Executando novamente o *Demo-LoadGenerator.ps1*, primeiro ele para os trabalhos atualmente em execução e, em seguida, reinicia o gerador, que inicia um novo conjunto de trabalhos usando os parâmetros atuais.

Por enquanto, deixe o gerador de carga em execução no estado de invocação de trabalho.


## <a name="provision-a-new-tenant"></a>Provisionar um novo locatário

A implantação inicial cria três locatários de exemplo, mas vamos criar outro locatário para ver como isso afeta o aplicativo implantado. O fluxo de trabalho de locatários de provisionamento SaaS do Wingtip é descrito em detalhes no [tutorial Provisionamento e catálogo](sql-database-saas-tutorial-provision-and-catalog.md). Nesta etapa, você cria rapidamente um novo locatário.

1. Abra ...\\Módulos de aprendizado\Provisionar e catalogar\\*Demo-ProvisionAndCatalog.ps1* no *ISE do PowerShell*.
1. Pressione **F5** para executar o script (mantenha os valores padrão por enquanto).

   > [!NOTE]
   > Usam vários scripts SaaS Wingtip *$PSScriptRoot* para permitir a navegação pelas pastas para chamar funções em outros scripts. Essa variável é avaliada apenas quando o script é executado, pressionando **F5**.  Realçar e executar uma seleção (**F8**) pode resultar em erros, então pressione **F5** ao executar scripts.

O novo banco de dados de locatário é criado em um pool elástico do SQL, inicializado e registrado no catálogo. Após o provisionamento bem-sucedido, o novo site de vendas de ingresso do locatário *Evento s*aparece no seu navegador:

![Novo locatário](./media/sql-database-saas-tutorial/red-maple-racing.png)

Atualize o *Hub de Eventos* e o novo locatário agora está na lista.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorar os servidores, os pools e os bancos de dados de locatário

Agora que você começou a executar uma carga na coleção de locatários, vamos analisar alguns dos recursos que foram implantados:

1. No [Portal do Azure](http://portal.azure.com), navegue até sua lista de servidores SQL e abra o servidor **catálogo-&lt;USER&gt;**. O servidor de catálogo contém dois bancos de dados. O **tenantcatalog** e o **basetenantdb** (um banco de dados de modelo ou *ouro* vazio que foi copiado para criar novos locatários).

   ![databases](./media/sql-database-saas-tutorial/databases.png)

1. Volte para sua lista de servidores SQL e abra o servidor **tenants1-&lt;USER&gt;** que contém os bancos de dados de locatário. Cada banco de dados de locatário é um banco de dados _elástico padrão_ em um pool padrão de 50 eDTU. Observe também que há um banco de dados _Red Maple Racing_, o banco de dados de locatário que você provisionou anteriormente.

   ![Servidor](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>Monitorar o pool

Se o gerador de carga estiver sendo executado por vários minutos, dados suficientes deverão estar disponíveis para começar a examinar alguns dos recursos de monitoramento incorporados em pools e bancos de dados.

1. Navegue até **tenants1 -&lt;USER&gt;** do servidor e clique em **Pool1** para exibir a utilização de recursos para o pool (o gerador de carga foi executado em uma hora nos gráficos a seguir):

   ![monitorar pool](./media/sql-database-saas-tutorial/monitor-pool.png)

O que esses dois gráficos bem ilustram, é como os pools elásticos e o Banco de Dados SQL são bem adequados para cargas de trabalho de aplicativos SaaS. Quatro bancos de dados que estão, cada um, atingindo picos de até 40 eDTUs tem suporte facilmente em um pool de 50 eDTU. Se eles foram provisionados como bancos de dados independentes, cada um deles precisa ser um S2 (50 DTU) para dar suporte a intermitências. O custo de 4 bancos de dados independentes S2 seria de quase 3 vezes o preço do pool e o pool ainda teria bastante espaço para muitos bancos de dados mais. Em situações reais, os clientes do Banco de dados SQL estão executando atualmente até 500 bancos de dados em 200 pools de eDTU. Para obter mais informações, consulte o [tutorial de monitoramento de desempenho](sql-database-saas-tutorial-performance-monitoring.md).


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu:

> [!div class="checklist"]

> * Como implantar o aplicativo SaaS do Wingtip
> * Sobre os servidores, pools e bancos de dados que constituem o aplicativo
> * Que os locatários são mapeados para seus dados com o *catálogo*
> * Como provisionar novos locatários
> * Como exibir a utilização do pool para monitorar a atividade do locatário
> * Como excluir recursos de exemplo para interromper a cobrança relacionada

Agora, tente fazer o [Tutorial para provisionar e catalogar](sql-database-saas-tutorial-provision-and-catalog.md)



## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam o aplicativo SaaS Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* Para saber mais sobre pools elásticos, consulte [*O que é um pool elástico do SQL do Azure*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* Para saber mais sobre os trabalhos elásticos, consulte [*Gerenciando bancos de dados de nuvem com escalonamento horizontal*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
* Para saber mais sobre aplicativos SaaS multilocatários, consulte [*Padrões de design para aplicativos SaaS multilocatários*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)
