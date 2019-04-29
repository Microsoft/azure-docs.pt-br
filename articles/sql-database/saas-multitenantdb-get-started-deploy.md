---
title: Implantar e explorar um aplicativo SaaS multilocatário que usa o Banco de Dados SQL do Azure | Microsoft Docs
description: Implantar e explore o aplicativo SaaS do Wingtip multilicatário, que demonstra os padrões SaaS usando o Banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, stein
manager: craigg
ms.date: 10/16/2018
ms.openlocfilehash: 8f19303dba7fcf2a9edb325464fdd3748220903e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61486019"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Implantar e explorar um aplicativo multilocatário fragmentado

Neste tutorial, você pode implantar e explorar um aplicativo de SaaS multilocatário de exemplo chamado Wingtip Tickets. O aplicativo Wingtip Tickets foi projetado para demonstrar os recursos do Banco de dados SQL do Azure que habilitam de forma simples cenários SaaS.

Essa implementação do aplicativo Wingtip Tickets usa um padrão de banco de dados multilocatário fragmentado. A fragmentação é pelo identificador do locatário. Dados de locatário são distribuídos para um determinado banco de dados de acordo com os valores de identificador de locatário. 

Esse padrão de banco de dados permite que você armazene um ou mais locatários em cada fragmento ou banco de dados. Você pode otimizar o custo mais baixo tendo cada banco de dados a ser compartilhado por vários locatários. Ou você pode otimizar para isolamento, fazer com que cada banco de dados armazenar somente um locatário. Sua escolha de otimização pode ser feita independentemente para cada locatário específico. A opção pode ser feita quando o locatário é armazenado primeiro, ou você pode mudar de ideia mais tarde. O aplicativo é projetado para funcionar bem em qualquer modo.

## <a name="app-deploys-quickly"></a>Aplicativo implantado rapidamente

O aplicativo é executado na nuvem do Azure e usa o banco de dados SQL do Azure. A seção de implantação a seguir fornece o botão azul **Implantar no Azure**. Quando o botão é pressionado, o aplicativo é totalmente implantado na assinatura do Azure em cinco minutos. Você tem acesso completo para explorar e trabalhar com os componentes individuais do aplicativo.

O aplicativo é implantado com dados para três locatários de exemplo. Os locatários são armazenados juntos em um banco de dados de vários locatários.

Qualquer pessoa pode baixar o código fonte do C# e do PowerShell para Wingtip Tickets em [seu repositório do GitHub][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Neste tutorial, você aprende

> [!div class="checklist"]
> - Como implantar o aplicativo SaaS Wingtip Tickets.
> - Onde obter o código-fonte do aplicativo e scripts de gerenciamento.
> - Sobre os servidores e bancos de dados que constituem o aplicativo.
> - Quantos locatários são mapeados para seus dados com o *catálogo*.
> - Como provisionar um novo locatário.
> - Como monitorar a atividade de locatário no aplicativo.

Há uma série de tutoriais relacionados que utilizam essa implantação inicial. Os tutoriais exploram padrões de design e gerenciamento de um intervalo de SaaS. Quando você trabalhar com os tutoriais, você é incentivado a percorrer os scripts fornecidos para ver como os diferentes padrões de SaaS são implementados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

- O SDK mais recente do Azure PowerShell instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Implantar o aplicativo SaaS do Wingtip

### <a name="plan-the-names"></a>Planejar os nomes

Nas etapas desta seção, você fornece um valor *user* que é usado para garantir que os nomes de recursos fiquem globalmente exclusivos, e um nome para o *grupo de recursos* que contém todos os recursos criados por uma implantação do aplicativo. Para uma pessoa denominada *Aline Faria*, sugerimos:
- *Usuário:*  **af1** *(Suas iniciais, mais um dígito. Use um valor diferente (por exemplo, af2) se você implantar o aplicativo novamente.)*
- *Grupo de recursos:* **wingtip-mt-af1** *(wingtip-mt indica que esse é o aplicativo multilocatário fragmentado. A anexação do nome de usuário af1 correlaciona o nome do grupo de recursos com os nomes dos recursos que ele contém.)*

Escolha seus nomes agora e os escreva. 

### <a name="steps"></a>Etapas

1. Clique no botão azul **Implantar no Azure** a seguir.
   - Ele abre o portal do Azure com o modelo de implantação Wingtip tíquetes SaaS.

     [![Botão para Implantar no Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Insira os valores de parâmetros necessários para a implantação.

    > [!IMPORTANT]
    > Para essa demonstração, não use nenhum grupo de recursos, servidor ou grupo pré-existente. Em vez disso, escolha **Criar um novo grupo de recursos**. Exclua esse grupo de recursos quando tiver terminado com o aplicativo para interromper a cobrança relacionada.
    > Não use este aplicativo ou todos os recursos que cria, para a produção. Alguns aspectos de autenticação e as configurações de firewall de servidor são intencionalmente inseguras no aplicativo para facilitar a demonstração.

    - Para **Grupo de recursos** – selecione **Criar novo** e, em seguida, forneça um **Nome** para o grupo de recursos (diferencia maiúsculas de minúsculas).
        - Selecione uma **Localização** na lista suspensa.
    - Para **Usuário** – é recomendável que você escolha um valor curto de **Usuário**.

1. **Implantar o aplicativo**.

    - Clique se você concordar com os termos e condições.
    - Clique em **Comprar**.

1. Monitore o status da implantação clicando em **Notificações** (o ícone de sino à direita da caixa de pesquisa). A implantação do aplicativo Wingtip SaaS leva aproximadamente cinco minutos.

   ![implantação bem-sucedida](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Baixar e desbloquear os scripts SaaS do Wingtip

Enquanto o aplicativo for implantado, baixe os scripts de gerenciamento e de código-fonte.

> [!NOTE]
> Conteúdos executáveis (scripts, DLLs) podem ser bloqueados pelo Windows quando arquivos zip são baixados de uma fonte externa e extraídos. Ao extrair os scripts de um arquivo zip, siga as etapas abaixo para desbloquear o arquivo. zip antes da extração. Ao desbloquear o arquivo. zip, você garante que os scripts podem ser executados.

1. Navegue até o [repositório WingtipTicketsSaaS-MultiTenantDb do GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Clique em **Clonar ou baixar**.
3. Clique em **Baixar ZIP** e salve o arquivo.
4. Clique com botão direito no arquivo **WingtipSaaS master.zip** e selecione **Propriedades**.
5. Na guia **Geral**, selecione **Desbloquear** e clique em **Aplicar**.
6. Clique em **OK**.
7. Extraia os arquivos.

Os scripts estão localizados na pasta *... \\módulos de aprendizado\\WingtipSaaS mester\\*.

## <a name="update-the-configuration-file-for-this-deployment"></a>Atualize o arquivo de configuração para sua implantação

Antes de executar os scripts, defina o *grupo de recursos* e valores de *usuário* em **UserConfig.psm1**. Defina essas variáveis para os valores definidos durante a implantação.

1. Abra ...\\Módulos de aprendizado\\*UserConfig.psm1* no *ISE do PowerShell*.
2. Atualize *ResourceGroupName* e *Nomeie* com os valores específicos para sua implantação (somente nas linhas 10 e 11).
3. Salve as alterações.

Os valores definidos neste arquivo são usados por todos os scripts, portanto, é importante que eles sejam precisos. Caso reimplante o aplicativo, você deve escolher valores diferentes para o Usuário e Grupo de Recursos. Em seguida, atualize o arquivo UserConfig.psm1 novamente com os novos valores.

## <a name="run-the-application"></a>Executar o aplicativo

No aplicativo Wingtip, os locatários são locais. Um local pode ser uma sala de concertos, um clube de esportes ou qualquer outro local que receba eventos. Os locais registram-se no Wingtip como clientes, e um identificador de locatário é gerado para cada local. Cada local lista seus próximos eventos no Wingtip, assim, o público pode comprar ingressos para os eventos.

Cada local obtém um aplicativo Web personalizado para listar seus eventos e vender ingressos. Cada aplicativo Web é independente e isolado de outros locatários. Internamente no Banco de Dados SQL do Azure, por padrão, cada dado para cada locatário é armazenado em um banco de dados multilocatário fragmentado. Todos os dados são marcados com o identificador do locatário.

Uma página da Web central do **Hub de Eventos** fornece uma lista de links para os locatários em sua implantação particular. Use as seguintes etapas para vivenciar a página da Web do **Hub de Eventos** e um aplicativo Web individual:

1. Abra o **Hub de eventos** no navegador da web:
   - http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net &nbsp; *(Substitua &lt;user&gt; pelo valor de usuário da implantação.)*

     ![hub de eventos](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Clique em **Fabrikam Jazz Club** no **Hub de Eventos**.

   ![Eventos](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Gerenciador de Tráfego do Azure

Para controlar a distribuição das solicitações de entrada, o aplicativo Wingtip usa o [Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md). A página de eventos de cada locatário inclui o nome do locatário em sua URL. Cada URL também inclui seu valor específico de Usuário. Cada URL obedece o formato mostrado usando as seguintes etapas:

- http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net/*fabrikamjazzclub*

1. O aplicativo de eventos analisa o nome do locatário a partir da URL. O nome do locatário é *fabrikamjazzclub* na URL de exemplo anterior.
2. O aplicativo faz o hash do nome do locatário para criar uma chave para acessar um catálogo usando o [gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md).
3. O aplicativo encontra a chave no catálogo e obtém o local correspondente do banco de dados do locatário.
4. O aplicativo usa as informações de local para encontrar e acessar o banco de dados que contém todos os dados para o locatário.

### <a name="events-hub"></a>Hub de Eventos

1. O **Hub de Eventos** lista todos os locatários que estão registrados no catálogo e seus locais.
2. O **Hub de Eventos** usa metadados estendidos no catálogo para recuperar o nome do locatário associado a cada banco de dados.

Em um ambiente de produção, você normalmente cria um registro CNAME DNS para [apontar um domínio de Internet da empresa](../traffic-manager/traffic-manager-point-internet-domain.md) para o perfil do gerenciador de tráfego.

## <a name="start-generating-load-on-the-tenant-databases"></a>Iniciar a geração de carga nos bancos de dados de locatário

Agora que o aplicativo está implantado, vamos colocá-lo em funcionamento! O script do PowerShell *Demo-LoadGenerator* começa uma execução de carga de trabalho em todos os bancos de dados de locatário. A carga real em um aplicativo SaaS é normalmente esporádica e imprevisível. Para simular isso, o gerador de carga gera uma carga aleatória distribuída entre todos os locatários. A carga inclui intermitências aleatórias em cada locatário que ocorrem em intervalos aleatórias. Por isso, levará vários minutos para que o padrão de carga surja, portanto, é melhor permitir que o gerador seja executado por pelo menos três ou quatro minutos antes de monitorar a carga.

1. No *ISE do PowerShell*, abra o script ...\\Módulos de Aprendizado\\Utilitários\\*Demo-LoadGenerator.ps1*.
2. Pressione **F5** para executar o script e iniciar o gerador de carga (deixe os valores de parâmetro padrão por enquanto).

O *LoadGenerator.ps1 demonstração* script abre outra sessão do PowerShell, em que o gerador de carga é executado. O gerador de carga é executado nessa sessão como uma tarefa de primeiro plano que invoca os trabalhos de geração de carregamento em segundo plano, uma para cada locatário.

Depois que a tarefa de primeiro plano é iniciado, ele permanecerá em um estado de chamada de trabalho. A tarefa iniciar trabalhos de plano de fundo adicional para quaisquer novos locatários que são provisionados subsequentemente.

Fechando a sessão do PowerShell interrompe todos os trabalhos.

Deseja reiniciar a sessão do gerador de carga para usar valores de parâmetros diferentes. Nesse caso, feche o PowerShell sessão geração e, em seguida, execute novamente o *LoadGenerator.ps1 demonstração*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Provisionar um novo locatário para o banco de dados fragmentado

A implantação inicial inclui três locatários de exemplo no *Tenants1* banco de dados. Vamos criar outro locatário e observar seus efeitos no aplicativo implantado. Nesta etapa, você pressiona uma tecla para criar um novo locatário:

1. Abra ...\\Módulos de aprendizado\\Provisionar e Catalogar\\*Demo-ProvisionTenants.ps1* no *ISE do PowerShell*.
2. Pressione **F5** (e não **F8**) para executar o script (mantenha os valores padrão por enquanto).

   > [!NOTE]
   > Você deve executar os scripts do PowerShell apenas pressionando a tecla **F5**, e não **F8**, para executar uma parte selecionada do script. O problema com o **F8** é que a variável *$PSScriptRoot* não é avaliada. Essa variável é necessária para muitos scripts para navegar por pastas, invocar outros scripts ou importar módulos.

O novo locatário vermelho bordo corrida é adicionado ao *Tenants1* banco de dados e registrados no catálogo. O novo locatário do tíquete de venda **eventos** site abre no navegador:

![Novo locatário](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Atualize o **Hub de Eventos**: agora o novo locatário está na lista.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Provisionar um novo locatário em seu próprio banco de dados

O modelo multilocatário fragmentado permite que você escolha se deseja provisionar um novo locatário em um banco de dados multilocatário que contém outros locatários ou em um banco de dados separado. Um locatário isolado em seu próprio banco de dados tem os seguintes benefícios:

- O desempenho do banco de dados do locatário pode ser gerenciado sem a necessidade de se comprometer com as necessidades de outros locatários.
- Se necessário, o banco de dados pode ser restaurado para um ponto anterior no tempo, porque nenhum outro locatário seria afetado.

Você pode colocar clientes de avaliação gratuita ou de economia em bancos de dados de multilocatários. Também pode colocar cada locatário premium em seu próprio banco de dados dedicado. Se você criar muitos bancos de dados que contêm somente um locatário, você pode gerenciá-los todos coletivamente em um pool elástico para otimizar os custos do recurso.

Depois, vamos provisionar outro locatário, dessa vez, em seu próprio banco de dados:

1. Em ...\\Módulos de aprendizado\\Provisão e Catálogo\\*Demo-ProvisionTenants.ps1*, modifique *$TenantName* para **Salix Salsa**,  *$VenueType* para **dança** e *$Scenario* para **2**.

2. Pressione **F5** para executar o script.
    - Ao pressionar **F5**, o novo locatário é provisionado em um banco de dados separado. O banco de dados e de locatário são registrados no catálogo. Em seguida, o navegador é aberto para a página de eventos do locatário.

   ![Página de eventos Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Role até o final do painel à direita. Existe na faixa, verá o nome do banco de dados no qual os dados de locatário são armazenados.

3. Atualize o **Hub de Eventos**: agora os dois novos locatários estão na lista.

## <a name="explore-the-servers-and-tenant-databases"></a>Explorar os servidores e bancos de dados de locatários

Vamos examinar alguns dos recursos que foram implantados:

1. No [portal do Azure](https://portal.azure.com), procure a lista de grupos de recursos. Abra o grupo de recursos criados quando você implantou o aplicativo.

   ![grupo de recursos](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Clique no servidor **catalog-mt&lt;user&gt;** . O servidor de catálogo contém dois bancos de dados denominados *tenantcatalog* e *basetenantdb*. O *basetenantdb* banco de dados é um modelo vazio. Ele é copiado para criar um novo banco de dados de locatário, se usado para vários locatários ou apenas um locatário.

   ![servidor catalog](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Volte para o grupo de recursos e selecione o *tenants1 mt* servidor que hospeda os bancos de dados de locatário.
    - O banco de dados tenants1 é um banco de dados multilocatário em que os três locatários originais, além do primeiro locatário que você adicionou, é armazenado. Ele é configurado como um banco de dados Standard de DTU 50.
    - O **salixsalsa** banco de dados contém o local de dança Salix Salsa como seu único locatário. Ele é configurado como um banco de dados de edição Standard com 50 DTUs por padrão.

   ![servidor de locatários](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>O nível de desempenho do banco de dados

Se o gerador de carga estiver sendo executado por vários minutos, haverá telemetria suficiente disponível para examinar os recursos de monitoramento do banco de dados incorporados no portal do Azure.

1. Navegue até o servidor **tenants1-mt&lt;user&gt;**  e clique em **tenants1** para exibir a utilização de recursos para o banco de dados que tem quatro locatários nele. Cada locatário está sujeito a uma carga pesada esporádica do gerador de carga:

   ![Monitor tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   O gráfico de utilização de DTU bem ilustra como um banco de dados multilocatário pode dar suporte a uma carga de trabalho imprevisível entre vários locatários. Nesse caso, o gerador de carga está aplicando uma carga esporádica de aproximadamente 30 DTUs para cada locatário. Essa carga é igual a 60% da utilização de um banco de dados DTU 50. Picos que excedem 60% são o resultado de carga que está sendo aplicada a mais de um locatário ao mesmo tempo.

2. Navegue até o servidor **tenants1-mt&lt;user&gt;** e clique no banco de dados **salixsalsa**. Você pode ver a utilização de recursos nesse banco de dados que contém somente um locatário.

   ![banco de dados salixsalsa](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

O gerador de carga está aplicando uma carga semelhante para cada locatário, independentemente de qual banco de dados a cada locatário está em. Com apenas um locatário no **salixsalsa** banco de dados, você pode ver que o banco de dados pode sustentar uma carga muito maior que o banco de dados com vários locatários. 

### <a name="resource-allocations-vary-by-workload"></a>Alocações de recursos variam por carga de trabalho

Às vezes, um banco de dados multilocatário requer mais recursos para um bom desempenho do que um banco de dados de locatário único, mas não sempre. A alocação ideal de recursos depende das características específicas de carga de trabalho para os locatários no seu sistema.

As cargas de trabalho geradas pelo script do gerador de carga são apenas para fins ilustrativos.

## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre aplicativos SaaS multilocatários, consulte [Padrões de design para aplicativos SaaS multilocatários](saas-tenancy-app-design-patterns.md).

- Para saber mais sobre pools elásticos, consulte:

  - [Pools elásticos ajudam você a gerenciar e dimensionar vários bancos de dados SQL do Azure](sql-database-elastic-pool.md)
  - [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu:

> [!div class="checklist"]
> - Como implantar o aplicativo de Banco de Dados Multilocatário de SaaS chamado Wingtip Tickets.
> - Sobre os servidores e bancos de dados que constituem o aplicativo.
> - Que os locatários são mapeados para seus dados com o *catálogo*.
> - Como provisionar novos locatários, em um banco de dados de vários locatários e o banco de dados único locatário.
> - Como exibir a utilização do pool para monitorar a atividade do locatário.
> - Como excluir recursos de exemplo para interromper a cobrança relacionada.

Agora, tente fazer o [Tutorial para provisionar e catalogar](sql-database-saas-tutorial-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "O botão Implantar no Azure."

