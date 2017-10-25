---
title: "Noções básicas sobre conectores do Proxy de Aplicativo Azure AD | Microsoft Docs"
description: "Cobre as noções básicas sobre os conectores do Proxy de Aplicativo Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: billmath
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: fdee5703adc76e750aebd83d4122e7b79244c0e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Noções básicas sobre conectores de Proxy de Aplicativo Azure AD

Os conectores são o que torna o Proxy de Aplicativo Azure AD possível. Eles são simples, fáceis de implantar e manter, além de superpotentes. Este artigo aborda o que são conectores, como eles funcionam e algumas sugestões sobre como otimizar sua implantação. 

## <a name="what-is-an-application-proxy-connector"></a>O que é um conector do Proxy de Aplicativo

Conectores são agentes leves que ficam no local e facilitam a conexão de saída para o serviço Proxy de Aplicativo. Os conectores devem ser instalados em um servidor Windows que tenha acesso ao aplicativo de back-end. Você pode organizar os conectores em grupos de conector, com cada grupo tratando o tráfego de aplicativos específicos. Os conectores fazem o balanceamento de carga automaticamente e podem ajudar a otimizar sua estrutura de rede. 

## <a name="requirements-and-deployment"></a>Requisitos e implantação

Para implantar o Proxy de Aplicativo com êxito, você precisa de pelo menos um conector, mas recomendamos dois ou mais para uma maior capacidade de recuperação. Instale o conector em um Windows Server 2012 R2 ou em uma computador de 2016. O conector deve ser capaz de se comunicar com o serviço Proxy de Aplicativo e com os aplicativos locais que você publicar. 

Para saber mais sobre os requisitos de rede para o servidor de conector, confira [Introdução ao Proxy de Aplicativo e instalar um conector](active-directory-application-proxy-enable.md).

## <a name="maintenance"></a>Manutenção 
Os conectores e o serviço cuidam de todas as tarefas de alta disponibilidade. Eles podem ser adicionados ou removidos dinamicamente. Sempre que uma nova solicitação chega, ela é roteada para um dos conectores que está disponível no momento. Se um conector estiver temporariamente indisponível, ele não responderá a esse tráfego.

Os conectores são sem estado e não têm nenhum dado de configuração no computador. Os únicos dados que eles armazenam são as configurações para conectar o serviço e seu certificado de autenticação. Quando se conectam ao serviço, eles extraem todos os dados de configuração necessários e os atualizam a cada dois minutos.

Os conectores também sondam o servidor para descobrir se há uma versão mais recente do conector. Se for encontrada, os próprios conectores se atualizam.

É possível monitorar os conectores no computador em que eles estão em execução, usando o log de eventos e os contadores de desempenho. Ou você pode exibir o status da página do Proxy de Aplicativo do portal do Azure:

 ![Conectores do Proxy de Aplicativo Azure AD](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

Não é necessário excluir manualmente os conectores que não foram utilizados. Quando um conector está em execução, ele permanece ativo, pois se conecta ao serviço. Os conectores não utilizados são marcados como _inativos_ e são removidos depois de 10 dias de inatividade. No entanto, se você quiser desinstalar um conector, desinstale o serviço Conector e o serviço Atualizador do servidor. Reinicie o computador para remover completamente o serviço.

## <a name="automatic-updates"></a>Atualizações automáticas

O Azure AD fornece atualizações automáticas para todos conectores que você implanta. Desde que o serviço Atualizador do Conector de Proxy de Aplicativo esteja em execução, os conectores são atualizados automaticamente. Caso você não veja o serviço Atualizador do Conector no servidor, precisará [reinstalar o conector](active-directory-application-proxy-enable.md) para obter todas as atualizações. 

Se você não quiser aguardar uma atualização automática chegar a seu conector, você poderá executar uma atualização manual. Vá para o [página de download do conector](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) no servidor em que o conector está localizado e selecione **Baixar**. Esse processo inicia uma atualização do conector local. 

Para locatários com vários conectores, as atualizações automáticas destinam-se a um conector de cada vez em cada grupo para evitar tempo de inatividade em seu ambiente. 

Poderá ocorrer tempo de inatividade quando o conector for atualizado se:  
- Você só tiver um conector. Para evitar esse tempo de inatividade e melhorar a alta disponibilidade, recomendamos instalar um segundo conector e [criar um grupo de conectores](active-directory-application-proxy-connectors-azure-portal.md).  
- Um conector estava no meio de uma transação quando a atualização foi iniciada. Embora a transação inicial seja perdida, o navegador deverá repetir a operação automaticamente ou você poderá atualizar a página. Quando a solicitação é enviada novamente, o tráfego é direcionado para um conector de backup.

## <a name="creating-connector-groups"></a>Criando grupos de conector

Os grupos de conectores permitem atribuir conectores específicos para atender a aplicativos específicos. Você pode agrupar um número de conectores e, em seguida, atribuir cada aplicativo a um grupo. 

Os grupos de conectores facilitam o gerenciamento de grandes implantações. Eles também melhoram a latência para locatários que têm aplicativos hospedados em regiões diferentes, pois você pode criar grupos com base no local de conector para servir apenas aos aplicativos locais. 

Para saber mais sobre os grupos de conectores, confira [Publicar aplicativos em redes e locais separados usando grupos de conector](active-directory-application-proxy-connectors-azure-portal.md).

## <a name="capacity-planning"></a>Planejamento da capacidade 

Embora os conectores façam automaticamente o balanceamento de carga dentro de um grupo de conectores, também é importante planejar capacidade suficiente entre os conectores para lidar com o volume de tráfego esperado. Em geral, quanto mais usuários você tiver, maior será o computador necessário. Abaixo está uma tabela que apresenta o volume que diferentes computadores podem processar. Observe que isso é baseado TPS (Transações por Segundo) esperadas e não em usuário, já que os padrões de uso variam e não podem ser usados para prever a carga.  Observe também que haverá algumas diferenças com base no tamanho das respostas e no tempo de resposta do aplicativo de back-end. Tamanhos de resposta maiores e tempos de resposta mais lentos resultarão em uma menor TPS máxima.

|Núcleos|RAM|Latência esperada (MS)-P99|TPS máximo|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|
\* Esse computador tem um limite de conexão de 200. Para todos os outros computadores, usamos o limite de conexão padrão de 200.
 
>[!NOTE]
>O limite de configuração padrão é de 200 (para dois, quatro e oito núcleos).  Durante o teste com 16 núcleos, o limite de conexão foi alterado para 800. Não há muita diferença no TPS máximo entre computadores de 4, 8 e 16 núcleos. A principal diferença entre eles é a latência prevista.  

## <a name="security-and-networking"></a>Rede e segurança

Os conectores podem ser instalados em qualquer lugar na rede que permite que eles enviem solicitações para o serviço de Proxy de aplicativo. O importante é que o computador que executa o conector também tenha acesso aos aplicativos. Você pode instalar conectores dentro de sua rede corporativa ou em uma máquina virtual que é executada na nuvem. Os conectores podem ser executados em uma DMZ (zona desmilitarizada), mas não é necessário porque todo o tráfego é de saída e, portanto, sua rede fica segura.

Os conectores só enviam solicitações de saída. O tráfego de saída é enviado ao serviço de Proxy de Aplicativo e aos aplicativos publicados. Você não precisa abrir portas de entrada porque o tráfego flui nos dois sentidos quando uma sessão é estabelecida. Você não precisa configurar o balanceamento de carga entre os conectores ou configurar o acesso de entrada por meio dos firewalls. 

Para saber mais sobre como configurar regras de firewall de saída, confira [Trabalhar com servidores proxy locais existentes](application-proxy-working-with-proxy-servers.md).

Use a [Ferramenta de Teste de Portas do Conector de Proxy de Aplicativo Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) para verificar se o conector pode alcançar o serviço Proxy de Aplicativo. No mínimo, verifique se a região EUA Central e a região mais próxima de você tem todas as marcas de seleção verdes. Além disso, um número maior de marcas de seleção verdes significa maior resiliência. 

## <a name="performance-and-scalability"></a>Desempenho e escala

A escala para o serviço Proxy de Aplicativo é transparente, mas a escala é uma questão para conectores. Você precisa ter conectores suficientes para tratar do pico de tráfego. No entanto, você não precisa configurar o balanceamento de carga, pois todos os conectores dentro de um grupo de conector fazem o balanceamento de carga automaticamente.

Como os conectores não têm estado, eles não são afetados pelo número de usuários ou sessões. Em vez disso, eles respondem ao número de solicitações e a seu tamanho do conteúdo. Com o tráfego padrão da Web, um computador médio pode manipular milhares de solicitações por segundo. A capacidade específica depende das características exatas do computador. 

O desempenho do conector está vinculado à CPU e rede. O desempenho da CPU é necessário para criptografia e descriptografia SSL, enquanto a rede é importante para obter conectividade rápida com os aplicativos e com o serviço online no Azure.

Por outro lado, a memória é uma questão menos significativa para os conectores. O serviço online cuida de grande parte do processamento e de todo o tráfego não autenticado. Tudo o que pode ser feito na nuvem é feito na nuvem. 

O balanceamento de carga ocorre entre conectores de um determinado grupo de conectores. Fazemos uma variação de um round robin para determinar qual conector no grupo atende a uma determinada solicitação. Depois de escolher um conector, mantemos uma afinidade de sessão entre esse usuário e o aplicativo durante a sessão. Se por algum motivo aquele conector ou computador ficar indisponível, o tráfego começará a ir para outro conector no grupo. Essa resiliência também é o motivo para recomendarmos ter vários conectores.

Outro fator que afeta o desempenho é a qualidade da rede entre os conectores, incluindo: 

* **O serviço online**: conexões lentas ou de alta latência para o serviço Proxy de Aplicativo no Azure influenciam o desempenho do conector. Para obter o melhor desempenho, conecte sua organização ao Azure com o ExpressRoute. Caso contrário, faça com que a equipe de rede garanta que as conexões com o Azure são tratadas da maneira mais eficiente possível. 
* **Os aplicativos de back-end:** em alguns casos, há outros proxies entre o conector e os aplicativos de back-end que possam retardar ou impedir as conexões. Para solucionar problemas nesse cenário, abra um navegador no servidor de conector e tente acessar o aplicativo. Se você executar os conectores no Azure, mas os aplicativos forem locais, a experiência poderá não ocorrer como esperado pelos usuários.
* **Os controladores de domínio**: se os conectores executarem SSO usando a KCD (Delegação Restrita de Kerberos), eles contatarão os controladores de domínio antes de enviarem a solicitação ao back-end. Os conectores têm um cache de tíquetes Kerberos, mas em um ambiente ocupado, a capacidade de resposta dos controladores de domínio pode afetar o desempenho. Esse problema é mais comum em conectores executados no Azure, mas que se comunicam com os controladores de domínio locais. 

Para saber mais sobre como otimizar sua rede, confira [Considerações sobre a topologia de rede ao usar o Proxy de Aplicativo do Azure Active Directory](application-proxy-network-topology-considerations.md).

## <a name="domain-joining"></a>Ingresso no domínio

Os conectores podem ser executados em um computador que não foi ingressado no domínio. Entretanto, se você desejar efetuar SSO (logon único) em aplicativos que usam a IWA (Autenticação Integrada do Windows), precisará de um computador ingressado no domínio. Nesse caso, os computadores do conector devem ser adicionados a um domínio que possa executar a Delegação Restrita de [Kerberos](https://web.mit.edu/kerberos) em nome dos usuários para os aplicativos publicados.

Os conectores também podem ser ingressados em domínios ou florestas que têm uma relação de confiança parcial ou em controladores de domínio somente leitura.

## <a name="connector-deployments-on-hardened-environments"></a>Implantações de conector em ambientes protegidos

Normalmente, a implantação do conector é simples e não exige nenhuma configuração especial. No entanto, há algumas condições exclusivas que devem ser consideradas:

* As organizações que limitam o tráfego de saída devem [abrir as portas necessárias](active-directory-application-proxy-enable.md#open-your-ports).
* Os computadores em conformidade com FIPS podem precisar alterar sua configuração para permitir que os processos do conector gerem e armazenem um certificado.
* As organizações que bloqueiam o ambiente de acordo com os processos que emitem as solicitações de rede precisam ter certeza de que os serviços do conector estão habilitados para acessar todas as portas e IPs necessários.
* Em alguns casos, os proxies de encaminhamento de saída podem interromper a autenticação de certificado bidirecional e causar falha na comunicação.

## <a name="connector-authentication"></a>Autenticação do conector

Para fornecer um serviço seguro, os conectores devem fazer a autenticação no serviço e o serviço deve fazer a autenticação no conector. Essa autenticação é feita usando certificados de cliente e servidor quando os conectores iniciam a conexão. Dessa forma, o nome de usuário e a senha do administrador não são armazenados no computador do conector.

Os certificados usados são específicos ao serviço de Proxy de Aplicativo. Eles são criados durante o registro inicial e renovados automaticamente pelos conectores no intervalo de alguns meses. 

Se um conector não estiver conectado ao serviço por vários meses, seus certificados poderão estar desatualizados. Nesse caso, desinstale e reinstale o conector para disparar o registro. É possível executar os seguintes comandos do PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Nos bastidores

Os conectores baseiam-se no Proxy de Aplicativo Web do Windows Server e, portanto, têm a maioria das mesmas ferramentas de gerenciamento, incluindo os Logs de Eventos do Windows

 ![Gerenciar logs de eventos com o Visualizador de Eventos](./media/application-proxy-understand-connectors/event-view-window.png)

e contadores de desempenho do Windows. 

 ![Adicionar contadores ao conector com o Monitor de Desempenho](./media/application-proxy-understand-connectors/performance-monitor.png)

Os conectores têm logs de administrador e sessão. Os logs de administrador incluem eventos de chave e seus erros. Os logs de sessão incluem todas as transações e seus detalhes de processamento. 

Para ver os logs, acesse o Visualizador de Eventos, abra o menu **Exibir** e habilite **Mostrar logs analíticos e de depuração**. Em seguida, habilite-os para iniciar a coleta de eventos. Esses logs não aparecem no Proxy de Aplicativo Web no Windows Server 2012 R2, pois os conectores são baseados em uma versão mais recente.

Você pode examinar o estado do serviço na janela Serviços. O conector é composto por dois Serviços do Windows: o conector real e o atualizador. Ambos devem ser executados o tempo todo.

 ![Local dos Serviços do Azure AD](./media/application-proxy-understand-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Próximas etapas


* [Publicar aplicativos em redes e locais separados usando grupos de conectores](active-directory-application-proxy-connectors-azure-portal.md)
* [Trabalhar com servidores proxy locais existentes](application-proxy-working-with-proxy-servers.md)
* [Solucionar erros do conector e do Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)
* [Como fazer uma instalação silenciosa do Conector de Proxy de Aplicativo Azure AD](active-directory-application-proxy-silent-installation.md)

