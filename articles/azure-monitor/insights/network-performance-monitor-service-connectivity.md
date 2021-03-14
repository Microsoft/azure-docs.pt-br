---
title: Conectividade do serviço de solução Monitor de Desempenho de Rede-Log Analytics do Azure
description: Use o recurso Monitor de Conectividade de Serviço em Monitor de Desempenho de Rede para monitorar a conectividade de rede para qualquer ponto de extremidade que tem uma porta TCP aberta.
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: a5b42e61af398e111b2014f3734a0011a4bee778
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734155"
---
# <a name="service-connectivity-monitor"></a>Monitor de Conectividade de Serviço

> [!IMPORTANT]
> A partir de 1 de julho de 2021, você não poderá adicionar novos testes em um espaço de trabalho existente ou habilitar um novo espaço de trabalho no Monitor de Desempenho de Rede. Você pode continuar a usar os testes criados antes de 1 de julho de 2021. Para minimizar a interrupção do serviço para suas cargas de trabalho atuais, [migre seus testes de monitor de desempenho de rede para o novo monitor de conexão](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) no observador de rede do Azure antes de 29 de fevereiro de 2024.

Você pode usar o recurso Monitor de Conectividade de Serviço em [Monitor de Desempenho de Rede](network-performance-monitor.md) para monitorar a conectividade de rede para qualquer ponto de extremidade que tem uma porta TCP aberta. Esses pontos de extremidade incluem sites, aplicativos SaaS, os aplicativos PaaS e bancos de dados SQL. 

Você pode executar as seguintes funções com o Monitor de Conectividade de Serviço: 

- Monitore a conectividade de rede para seus aplicativos e serviços de rede de vários locais ou de ramificação. Aplicativos e serviços de rede incluem Microsoft 365, Dynamics CRM, aplicativos internos de linha de negócios e bancos de dados SQL.
- Use testes internos para monitorar a conectividade de rede com os pontos de extremidade Microsoft 365 e Dynamics 365. 
- Determinar o tempo de resposta, latência de rede e perda de pacotes apresentados ao se conectar ao ponto de extremidade.
- Determinar se o desempenho insatisfatório do aplicativo é devido à rede ou devido a algum problema no final do provedor de aplicativo.
- Identifica pontos de acesso na rede que podem estar causando o desempenho insatisfatório do aplicativo exibindo a latência contribuída por cada salto em um mapa de topologia.


![Monitor de Conectividade de Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Configuração 
Para abrir a configuração para Monitor de Desempenho de Rede, abra a [solução monitor de desempenho de rede](network-performance-monitor.md) e selecione **Configurar**.

![Configure o Monitor de Desempenho de Rede](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Configurar agentes do Log Analytics para monitoramento
Habilite as seguintes regras de firewall em nós usado para o monitoramento para que a solução possa descobrir a topologia de seus nós para o ponto de extremidade de serviço: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Criar testes do Monitor de Conectividade de Serviço 

Comece criando os testes para monitorar a conectividade de rede aos pontos de extremidade de serviço.

1. Selecione a guia **Monitor de Conectividade de Serviço**.
2. Selecione **Adicionar teste** e insira o nome do teste e a descrição. Você pode criar um máximo de 450 testes por espaço de trabalho. 
3. Selecione o tipo de teste:<br>

    * Selecione o **Web** para monitorar a conectividade a um serviço que responde às solicitações HTTP/S, como outlook.office365.com ou bing.com.<br>
    * Selecione a **Rede** para monitorar a conectividade a um serviço que responde à solicitação TCP, mas não responde à solicitação HTTP/S, como um SQL server, servidor FTP ou porta SSH etc. 
    * Por exemplo: para criar um teste na Web para uma conta de armazenamento de BLOBs, selecione **Web** e insira destino como *yourstorageaccount*. blob.Core.Windows.net. Da mesma forma, você pode criar testes para outros armazenamentos de tabelas, armazenamento de fila e arquivos do Azure usando [este link.](../../storage/common/storage-account-overview.md#storage-account-endpoints)
4. Se você não deseja executar medidas de rede, como latência de rede, perda de pacotes e descoberta de topologia, desmarque a caixa de seleção **Executar medidas de rede**. Mantenha selecionado para obter o máximo benefício da funcionalidade. 
5. Em **Destino**, insira o endereço IP/FQDN/URL para o qual você deseja monitorar a conectividade de rede.
6. Em **Número da porta**, insira o número da porta do serviço de destino. 
7. Em **Frequência de Teste**, insira um valor para a frequência com a qual você deseja que o teste execute. 
8. Selecione os nós do qual você deseja monitorar a conectividade de rede para o serviço. Verifique se o número de agentes adicionados por teste é menor que 150. Qualquer agente pode testar o máximo de 150 pontos de extremidade/agentes.

    >[!NOTE]
    > Para nós de servidor Windows, o recurso usa solicitações com base em TCP para executar as medidas de rede. Para nós de clientes Windows, o recurso usa solicitações com base em ICMP para executar as medidas de rede. Em alguns casos, o aplicativo de destino bloqueia solicitações ICMP quando os nós forem baseados em cliente Windows. A solução não pode realizar medidas de rede. É recomendável usar nós de servidores Windows nesses casos. 

9. Se não quiser criar eventos de integridade para os itens que você selecionou, desmarque **Ativar o Monitoramento de Integridade nos destinos cobertos por esse teste**. 
10. Escolha as condições de monitoramento. Você pode definir limites personalizados para geração de eventos de integridade digitando os valores de limite. Sempre que o valor da condição ultrapassar o limite selecionado para o par de rede ou sub-rede selecionado, será gerado um evento de integridade. 
11. Selecione **salvar** para salvar a configuração. 

    ![Configurações de teste do Monitor de Conectividade de Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Passo a passo 

Vá até a visualização do painel do Monitor de Desempenho de Rede. Para obter um resumo da integridade de testes diferentes que você criou, consulte a página **Monitor de Conectividade de Serviço**. 

![Página de Monitor de Conectividade de Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Selecione o bloco para visualizar detalhes dos testes na página de **Testes**. Na tabela à esquerda, você pode exibir a integridade de point-in-time e o valor do tempo de resposta do serviço, latência de rede e perda de pacotes para todos os testes. Use o controle de Gravador de Estado da Rede para exibir o instantâneo de rede em outro momento no passado. Selecione o teste na tabela que você deseja investigar. Nos gráficos no painel à direita, você pode exibir as tendências históricas de valores de perda, latência e tempo de resposta. Selecione o link Clique **Detalhes de Teste** para exibir o desempenho de cada nó.

![Testes do Monitor de Conectividade de Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

No modo de exibição **nós de teste**, você pode observar a conectividade de rede de cada nó. Selecione o nó que tem a degradação do desempenho. Este é o nó em que o aplicativo é observado em execução lenta.

Determine se o desempenho insatisfatório do aplicativo é devido à rede ou devido a algum problema no final do provedor do aplicativo, observando a correlação entre o tempo de resposta do aplicativo e a latência de rede. 

* **Problema de aplicativo:** um aumento no tempo de resposta, mas a latência de rede é sugere que a rede está funcionando corretamente e o problema é devido a um problema no final do aplicativo. 

    ![Emita o aplicativo de Monitor de Conectividade de Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Problema de Rede:** um aumento no tempo de resposta é acompanhado de um aumento correspondente na latência de rede que o aumento no tempo de resposta é devido a um aumento na latência de rede. 

    ![Emita a rede de Monitor de Conectividade de Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Depois de determinar que o problema é devido à rede, selecione a exibição **Topologia** para identificar o salto problemático no mapa de topologia. Um exemplo é mostrado na imagem a seguir. Fora dos 105ms de latência total entre o nó e o ponto de extremidade do aplicativo, 96ms é devido ao salto marcado em vermelho. Depois de identificar o salto problemático, você pode adotar uma ação corretiva. 

![Topologia de ponto de extremidade do monitor de conectividade de serviço](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnósticos 

Se você observar uma anormalidade, siga estas etapas:

* Se o tempo de resposta do serviço, perda de rede e latência são mostrados como NA, pode ser devido a um ou mais dos seguintes motivos:

    - O aplicativo está inoperante.
    - O nó usado para verificar a conectividade de rede para o serviço está inoperante.
    - O destino inserido na configuração de teste está incorreto.
    - O nó não tem nenhuma conectividade de rede.

* Se um tempo de resposta do serviço válido é exibido mas a perda de rede e latência são mostrados como NA, pode ser devido a um ou mais dos seguintes motivos:

    - Se o nó usado para verificar a conectividade de rede para o serviço for máquina cliente Windows, o serviço de destino está bloqueando as solicitações ICMP ou um firewall de rede está bloqueando as solicitações ICMP provenientes do nó.
    - A caixa de seleção **Executar medidas de rede** está em branco na configuração de teste. 

* Se o tempo de resposta do serviço for NA, mas a perda da rede e latência são válidas, isso sugere que o serviço de destino não é um aplicativo da web. Edite a configuração de teste e escolha o tipo de teste como **Rede** em vez de **Web**. 

* Se o aplicativo está sendo executado lentamente, determine se o desempenho insatisfatório do aplicativo é devido à rede ou devido a algum problema no final do provedor de aplicativo.

## <a name="gcc-office-urls-for-us-government-customers"></a>URLs de GCC Office para clientes do governo dos EUA
Para a região da Virgínia do governo dos EUA, somente as URLs do DOD são NPM internas. Os clientes que usam URLs GCC precisam criar testes personalizados e adicionar cada URL individualmente.

| Campo | GCC |
|:---   |:--- |
| Portal do Office 365 e compartilhado | portal.apps.mil |
| Identidade e autenticação do Office 365 | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www. office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| MS Teams | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Próximas etapas
[Pesquisar logs](../logs/log-query-overview.md) para exibir registros de dados de desempenho de rede detalhados.