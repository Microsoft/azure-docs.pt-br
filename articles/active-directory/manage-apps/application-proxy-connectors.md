---
title: Noções básicas sobre conectores do Proxy de Aplicativo Azure AD | Microsoft Docs
description: Cobre as noções básicas sobre os conectores do Proxy de Aplicativo Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4666a9d084f6fc12cd68b69d5c71cafc3de0439
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807790"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Noções básicas sobre conectores de Proxy de Aplicativo Azure AD

Os conectores são o que torna o Proxy de Aplicativo Azure AD possível. Eles são simples, fáceis de implantar e manter, além de superpotentes. Este artigo aborda o que são conectores, como eles funcionam e algumas sugestões sobre como otimizar sua implantação.

## <a name="what-is-an-application-proxy-connector"></a>O que é um conector do Proxy de Aplicativo?

Conectores são agentes leves que ficam no local e facilitam a conexão de saída para o serviço Proxy de Aplicativo. Os conectores devem ser instalados em um servidor Windows que tenha acesso ao aplicativo de back-end. Você pode organizar os conectores em grupos de conector, com cada grupo tratando o tráfego de aplicativos específicos.

## <a name="requirements-and-deployment"></a>Requisitos e implantação

Para implantar o Proxy de Aplicativo com êxito, você precisa de pelo menos um conector, mas recomendamos dois ou mais para uma maior capacidade de recuperação. Instalar o conector em um computador executando o Windows Server 2012 R2 ou posterior. O conector precisa se comunicar com o serviço Proxy de Aplicativo e com os aplicativos locais que você publicar.

### <a name="windows-server"></a>Windows Server
Você precisa de um servidor executando o Windows Server 2012 R2 ou posterior no qual possa instalar o conector do Proxy de Aplicativo. O servidor precisa se conectar aos serviços de Proxy de Aplicativo no Azure e aos aplicativos locais que você está publicando.

O servidor do Windows precisa ter o TLS 1.2 ativado antes de instalar o conector do Proxy de Aplicativo. Para habilitar o TLS 1.2 no servidor:

1. Defina as seguintes chaves do registro:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Reinicie o servidor

Para saber mais sobre os requisitos de rede para o servidor de conector, confira [Introdução ao Proxy de Aplicativo e instalar um conector](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Manutenção

Os conectores e o serviço cuidam de todas as tarefas de alta disponibilidade. Eles podem ser adicionados ou removidos dinamicamente. Sempre que uma nova solicitação chega, ela é roteada para um dos conectores que está disponível no momento. Se um conector estiver temporariamente indisponível, ele não responderá a esse tráfego.

Os conectores são sem estado e não têm nenhum dado de configuração no computador. Os únicos dados que eles armazenam são as configurações para conectar o serviço e seu certificado de autenticação. Quando se conectam ao serviço, eles extraem todos os dados de configuração necessários e os atualizam a cada dois minutos.

Os conectores também sondam o servidor para descobrir se há uma versão mais recente do conector. Se for encontrada, os próprios conectores se atualizam.

É possível monitorar os conectores no computador em que eles estão em execução, usando o log de eventos e os contadores de desempenho. Ou você pode exibir o status da página do Proxy de Aplicativo do portal do Azure:

![Exemplo: Conectores de Proxy de aplicativo do AD do Azure](./media/application-proxy-connectors/app-proxy-connectors.png)

Não é necessário excluir manualmente os conectores que não foram utilizados. Quando um conector está em execução, ele permanece ativo, pois se conecta ao serviço. Os conectores não utilizados são marcados como _inativos_ e são removidos depois de 10 dias de inatividade. No entanto, se você quiser desinstalar um conector, desinstale o serviço Conector e o serviço Atualizador do servidor. Reinicie o computador para remover completamente o serviço.

## <a name="automatic-updates"></a>Atualizações automáticas

O Azure AD fornece atualizações automáticas para todos conectores que você implanta. Desde que o serviço Atualizador do Conector de Proxy de Aplicativo esteja em execução, os conectores são atualizados automaticamente. Caso você não veja o serviço Atualizador do Conector no servidor, precisará [reinstalar o conector](application-proxy-add-on-premises-application.md) para obter todas as atualizações.

Se você não quiser aguardar uma atualização automática chegar ao seu conector, você poderá fazer uma atualização manual. Vá para o [página de download do conector](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) no servidor em que o conector está localizado e selecione **Baixar**. Esse processo inicia uma atualização do conector local.

Para locatários com vários conectores, as atualizações automáticas destinam-se a um conector de cada vez em cada grupo para evitar tempo de inatividade em seu ambiente.

Poderá ocorrer tempo de inatividade quando o conector for atualizado se:
  
- você possui somente um conector, recomendamos que instale um segundo conector e [crie um grupo de conectores](application-proxy-connector-groups.md). Isso evitará tempo de inatividade e oferece maior disponibilidade.  
- Um conector estava no meio de uma transação quando a atualização foi iniciada. Embora a transação inicial seja perdida, o navegador deverá repetir a operação automaticamente ou você poderá atualizar a página. Quando a solicitação é enviada novamente, o tráfego é direcionado para um conector de backup.

Para obter informações sobre as versões lançadas anteriormente e quais alterações elas incluem, confira [Proxy de Aplicativo – histórico de lançamento de versão](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Criando grupos de conector

Os grupos de conectores permitem atribuir conectores específicos para atender a aplicativos específicos. Você pode agrupar um número de conectores e, em seguida, atribuir cada aplicativo a um grupo.

Os grupos de conectores facilitam o gerenciamento de grandes implantações. Eles também melhoram a latência para locatários que têm aplicativos hospedados em regiões diferentes, pois você pode criar grupos com base no local de conector para servir apenas aos aplicativos locais.

Para saber mais sobre os grupos de conectores, confira [Publicar aplicativos em redes e locais separados usando grupos de conector](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>planejamento de capacidade

É importante você confirmar se planejou capacidade suficiente entre os conectores para lidar com o volume de tráfego esperado. É recomendável que cada grupo de conectores tem pelo menos dois conectores para fornecer alta disponibilidade e escala. É ideal ter três conectores no caso de você precisará de uma máquina em qualquer ponto de serviço.

Em geral, quanto mais usuários você tiver, maior será o computador necessário. Abaixo está uma tabela fornecendo uma estrutura de tópicos do volume e latência prevista máquinas diferentes podem manipular. Observe que isso é baseado em Transações por Segundo (TPS) esperadas e não em usuário, já que os padrões de uso variam e não podem ser usados para prever a carga. Haverá também algumas diferenças com base no tamanho das respostas e no tempo de resposta do aplicativo de back-end. Tamanhos de resposta maiores e tempos de resposta mais lentos resultarão em uma menor TPS máxima. Também recomendamos ter máquinas adicionais para que a carga distribuída entre as máquinas sempre fornece buffer suficiente. A capacidade extra garantirá que você tenha alta disponibilidade e resiliência.

|Núcleos|RAM|Latência esperada (MS)-P99|TPS máximo|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* Esta máquina usado uma configuração personalizada para gerar alguns dos limites de conexão padrão além do .NET, as configurações recomendadas. Recomendamos a execução de um teste com as configurações padrão antes de contatar o suporte para alterar esse limite para o seu locatário.

> [!NOTE]
> Não há muita diferença no TPS máximo entre computadores de 4, 8 e 16 núcleos. A principal diferença entre eles é a latência prevista.  

## <a name="security-and-networking"></a>Rede e segurança

Os conectores podem ser instalados em qualquer lugar na rede que permite que eles enviem solicitações para o serviço de Proxy de aplicativo. O importante é que o computador que executa o conector também tenha acesso aos aplicativos. Você pode instalar conectores dentro de sua rede corporativa ou em uma máquina virtual que é executada na nuvem. Os conectores podem ser executados em uma rede de perímetro, também conhecida como uma DMZ (zona desmilitarizada), mas não é necessário porque todo o tráfego é de saída para que sua rede fica segura.

Os conectores só enviam solicitações de saída. O tráfego de saída é enviado ao serviço de Proxy de Aplicativo e aos aplicativos publicados. Você não precisa abrir portas de entrada porque o tráfego flui nos dois sentidos quando uma sessão é estabelecida. Você também não precisa configurar o acesso de entrada por meio de firewalls.

Para saber mais sobre como configurar regras de firewall de saída, confira [Trabalhar com servidores proxy locais existentes](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Desempenho e escala

A escala para o serviço Proxy de Aplicativo é transparente, mas a escala é uma questão para conectores. Você precisa ter conectores suficientes para tratar do pico de tráfego. Como os conectores não têm estado, eles não são afetados pelo número de usuários ou sessões. Em vez disso, eles respondem ao número de solicitações e a seu tamanho do conteúdo. Com o tráfego padrão da Web, um computador médio pode manipular milhares de solicitações por segundo. A capacidade específica depende das características exatas do computador.

O desempenho do conector está vinculado à CPU e rede. O desempenho da CPU é necessário para criptografia e descriptografia SSL, enquanto a rede é importante para obter conectividade rápida com os aplicativos e com o serviço online no Azure.

Por outro lado, a memória é uma questão menos significativa para os conectores. O serviço online cuida de grande parte do processamento e de todo o tráfego não autenticado. Tudo o que pode ser feito na nuvem é feito na nuvem.

Se por algum motivo aquele conector ou computador ficar indisponível, o tráfego começará a ir para outro conector no grupo. Essa resiliência também é o motivo para recomendarmos ter vários conectores.

Outro fator que afeta o desempenho é a qualidade da rede entre os conectores, incluindo:

- **O serviço online**: Conexões lentas ou de alta latência para o serviço Proxy de Aplicativo no Azure influenciam o desempenho do conector. Para obter o melhor desempenho, conecte sua organização ao Azure com o ExpressRoute. Caso contrário, faça com que a equipe de rede garanta que as conexões com o Azure são tratadas da maneira mais eficiente possível.
- **Os aplicativos de back-end**: Em alguns casos, há outros proxies entre o conector e os aplicativos de back-end que podem retardar ou impedir as conexões. Para solucionar problemas nesse cenário, abra um navegador no servidor de conector e tente acessar o aplicativo. Se você executar os conectores no Azure, mas os aplicativos forem locais, a experiência poderá não ocorrer como esperado pelos usuários.
- **Os controladores de domínio**: Se os conectores executarem logon único (SSO) usando a Delegação Restrita de Kerberos, eles contatarão os controladores de domínio antes de enviarem a solicitação ao back-end. Os conectores têm um cache de tíquetes Kerberos, mas em um ambiente ocupado, a capacidade de resposta dos controladores de domínio pode afetar o desempenho. Esse problema é mais comum em conectores executados no Azure, mas que se comunicam com os controladores de domínio locais.

Para saber mais sobre como otimizar sua rede, confira [Considerações sobre a topologia de rede ao usar o Proxy de Aplicativo do Azure Active Directory](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Ingresso no domínio

Os conectores podem ser executados em um computador que não foi ingressado no domínio. Entretanto, se você desejar efetuar SSO (logon único) em aplicativos que usam a IWA (Autenticação Integrada do Windows), precisará de um computador ingressado no domínio. Nesse caso, os computadores do conector devem ser adicionados a um domínio que possa executar a Delegação Restrita de [Kerberos](https://web.mit.edu/kerberos) em nome dos usuários para os aplicativos publicados.

Os conectores também podem ser ingressados em domínios ou florestas que têm uma relação de confiança parcial ou em controladores de domínio somente leitura.

## <a name="connector-deployments-on-hardened-environments"></a>Implantações de conector em ambientes protegidos

Normalmente, a implantação do conector é simples e não exige nenhuma configuração especial. No entanto, há algumas condições exclusivas que devem ser consideradas:

- As organizações que limitam o tráfego de saída devem [abrir as portas necessárias](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- Os computadores em conformidade com FIPS podem precisar alterar sua configuração para permitir que os processos do conector gerem e armazenem um certificado.
- As organizações que bloqueiam o ambiente de acordo com os processos que emitem as solicitações de rede precisam ter certeza de que os serviços do conector estão habilitados para acessar todas as portas e IPs necessários.
- Em alguns casos, os proxies de encaminhamento de saída podem interromper a autenticação de certificado bidirecional e causar falha na comunicação.

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

![Gerenciar logs de eventos com o Visualizador de Eventos](./media/application-proxy-connectors/event-view-window.png)

e contadores de desempenho do Windows.

![Adicionar contadores ao conector com o Monitor de Desempenho](./media/application-proxy-connectors/performance-monitor.png)

Os conectores têm logs de administrador e sessão. Os logs de administrador incluem eventos de chave e seus erros. Os logs de sessão incluem todas as transações e seus detalhes de processamento.

Para ver os logs, acesse o Visualizador de Eventos, abra o menu **Exibir** e habilite **Mostrar logs analíticos e de depuração**. Em seguida, habilite-os para iniciar a coleta de eventos. Esses logs não aparecem no Proxy de Aplicativo Web no Windows Server 2012 R2, pois os conectores são baseados em uma versão mais recente.

Você pode examinar o estado do serviço na janela Serviços. O conector é composto por dois Serviços do Windows: o conector real e o atualizador. Ambos devem ser executados o tempo todo.

 ![Exemplo: Janela Serviços mostrando os serviços do Azure AD local](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Próximas etapas

- [Publicar aplicativos em redes e locais separados usando grupos de conectores](application-proxy-connector-groups.md)
- [Trabalhar com servidores proxy locais existentes](application-proxy-configure-connectors-with-proxy-servers.md)
- [Solucionar erros do conector e do Proxy de Aplicativo](application-proxy-troubleshoot.md)
- [Como fazer uma instalação silenciosa do Conector de Proxy de Aplicativo Azure AD](application-proxy-register-connector-powershell.md)
