---
title: Tutorial de SaaS de locatário único
description: Implante e explore um aplicativo SaaS autônomo de locatário único que usa o Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/07/2018
ms.openlocfilehash: 896f4edb1e94c1eca06b046382727a5042375e3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793272"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Implantar e explorar um aplicativo autônomo de locatário único que usa o Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você implanta e explora o aplicativo de exemplo Wingtip Tickets SaaS desenvolvido usando o aplicativo autônomo ou o padrão de aplicativo por locatário.  O aplicativo foi projetado para demonstrar os recursos do Banco de dados SQL do Azure que habilitam de forma simples cenários SaaS multilocatário.

O aplicativo autônomo ou um padrão de aplicativo por locatário implanta uma instância de aplicativo para cada locatário.  Cada aplicativo é configurado para um locatário específico e implantado em um grupo de recursos do Azure à parte. Várias instâncias do aplicativo são provisionadas para fornecer uma solução de multilocatário. Esse padrão é mais indicado para números menores, de locatários em que o isolamento é uma prioridade máxima. O Azure tem programas de parceria que permitem que esses recursos sejam implantados na assinatura de um locatário e gerenciados por um provedor de serviços em nome do locatário. 

Neste tutorial, você implantará três aplicativos autônomos para três locatários na assinatura do Azure.  Você tem acesso completo para explorar e trabalhar com os componentes individuais do aplicativo.

O código-fonte do aplicativo e os scripts de gerenciamento estão disponíveis no repositório GitHub [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp). O aplicativo foi criado usando o Visual Studio 2015, e não é possível abri-lo e compilá-lo no Visual Studio 2019 sem atualizar.


Neste tutorial, você aprende:

> [!div class="checklist"]
> * Como implantar o Aplicativo Autônomo SaaS Wingtip Tickets.
> * Onde obter o código-fonte do aplicativo e scripts de gerenciamento.
> * Sobre os servidores e bancos de dados que constituem o aplicativo.

Outros tutoriais serão liberados. Esses tutoriais permitirão explorar uma variedade de cenários de gerenciamento com base nesse padrão de aplicativo.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Implantar o Aplicativo Autônomo SaaS Wingtip Tickets

Implante o aplicativo para os três locatários fornecidos:

1. Clique em cada botão azul **Implantar no Azure** para abrir o modelo de implantação no [Portal do Azure](https://portal.azure.com). Cada modelo exige dois valores de parâmetro: um nome para um novo grupo de recursos e um nome de usuário que distingue essa implantação de outras implantações do aplicativo. A próxima etapa fornece detalhes para definir esses valores.

   **Contoso Concert Hall**   
   [![Imagem mostrando um botão rotulado como "Implantar no Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-contoso)

   **Dogwood Dojo**   
   [![Imagem mostrando um botão rotulado como "Implantar no Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-dogwood)

   **Fabrikam Jazz Club**   
   [![Imagem mostrando um botão rotulado como "Implantar no Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-fabrikam)
 
2. Insira os valores de parâmetro necessários para cada implantação.

    > [!IMPORTANT]
    > Algumas autenticações e firewalls de servidor estão intencionalmente desprotegidos para fins de demonstração. **Crie um novo grupo de recursos** para cada implantação de aplicativo.  Não use um grupo de recursos existente. Não use este aplicativo ou todos os recursos que cria, para a produção. Exclua todos os grupos de recursos quando terminar com os aplicativos para interromper a cobrança relacionada.

    É melhor usar apenas letras minúsculas, números e hifens nos nomes de recursos.
    * Para Grupo de recursos, selecione Criar novo e, em seguida, forneça um Nom para o grupo de recursos (em minúsculas). **wingtip-sa-\<venueName\>-\<user\>** é o padrão recomendado.  Para \<venueName\>, substitua o nome do local por nenhum espaço. Para \<user\>, substitua o valor do usuário abaixo.  Com esse padrão, os nomes do grupo de recursos podem ser *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Selecione uma **Localização** na lista suspensa.

    * Para **Usuário** – Recomendamos um valor de usuário curto, como as iniciais mais um dígito: por exemplo, *af1*.


3. **Implantar o aplicativo**.

    * Clique se você concordar com os termos e condições.
    * Clique em **Comprar**.

4. Monitore o status de todas as três implantações clicando em **Notificações** (o ícone de sino à direita da caixa de pesquisa). A implantação dos aplicativos leva cerca de cinco minutos.


## <a name="run-the-applications"></a>Executar os aplicativos

O aplicativo apresenta locais que organizam eventos.  Os locais são os locatários do aplicativo. Cada local obtém um site personalizado para listar seus eventos e vender ingressos. Os tipos de local são casas de shows, clubes de jazz e de esportes. No exemplo, o tipo de local determina a fotografia do plano de fundo mostrada no site do local.   No modelo de aplicativo autônomo, cada local tem uma instância de aplicativo separada com o próprio Banco de Dados SQL do Azure autônomo.

1. Abra a página de eventos de cada um dos três locatários em guias separadas do navegador:

   - http://events.contosoconcerthall.&lt;user&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;user&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;user&gt;.trafficmanager.net

     (Em cada URL, substitua &lt;user&gt; pelo valor do usuário da implantação.)

   ![Eventos](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Para controlar a distribuição das solicitações de entrada, o aplicativo usa o [*Gerenciador de Tráfego do Azure*](../../traffic-manager/traffic-manager-overview.md). Cada instância de aplicativo específica do locatário inclui o nome do locatário como parte do nome de domínio na URL. Todas as URLs de locatário incluem o valor do **Usuário** específico. As URLs seguem este formato:
- http://events.&lt;venuename&gt;.&lt;user&gt;.trafficmanager.net

O **Local** do banco de dados de cada locatário está incluído nas configurações do aplicativo implantado correspondente.

Em um ambiente de produção, normalmente, um registro DNS CNAME é criado para [*apontar um domínio da Internet da empresa*](../../traffic-manager/traffic-manager-point-internet-domain.md) para a URL do perfil do gerenciador de tráfego.


## <a name="explore-the-servers-and-tenant-databases"></a>Explorar os servidores e bancos de dados de locatários

Vamos examinar alguns dos recursos que foram implantados:

1. No [portal do Azure](https://portal.azure.com), procure a lista de grupos de recursos.
2. Você deverá ver os três grupos de recursos de locatários.
3. Abra o grupo de recursos **wingtip-sa-fabrikam-&lt;user&gt;**, que contém os recursos para a implantação do Fabrikam Jazz Club.  O servidor **fabrikamjazzclub-&lt;user&gt;** contém o banco de dados **fabrikamjazzclub**.

Cada banco de dados de locatários é um banco de dados *autônomo* de 50 DTUs.

## <a name="additional-resources"></a>Recursos adicionais

<!--
* Additional [tutorials that build on the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](elastic-pool-overview.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](./elastic-jobs-overview.md)
-->

- Para saber mais sobre aplicativos SaaS multilocatários, consulte [Padrões de design para aplicativos SaaS multilocatários](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Exclua grupos de recursos para parar a cobrança ##

Ao terminar de usar o exemplo, exclua todos os grupos de recursos que você criou para parar a cobrança associada.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu:

> [!div class="checklist"]
> * Como implantar o Aplicativo Autônomo SaaS Wingtip Tickets.
> * Sobre os servidores e bancos de dados que constituem o aplicativo.
> * Como excluir recursos de exemplo para interromper a cobrança relacionada.

Em seguida, experimente o tutorial [Provisionar e Catalogar](saas-standaloneapp-provision-and-catalog.md), no qual você vai explorar o uso de um catálogo de locatários que permite uma variedade de cenários entre locatários, como gerenciamento de esquema e análise de locatário.
