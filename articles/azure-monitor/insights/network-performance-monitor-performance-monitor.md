---
title: Monitor de desempenho no Monitor de Desempenho de Rede
description: O recurso Monitor de Desempenho no Monitor de Desempenho de Rede ajuda a monitorar a conectividade de rede em vários pontos em sua rede. Você pode monitorar as implantações de nuvem e locais internos, vários data centers e filiais, e aplicativos críticos ou microsserviços de várias camadas.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 6502d4259aa383c1466967d01282cecfbb8d1ded
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832956"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Solução do Monitor de Desempenho de Rede – Monitoramento de Desempenho

> [!IMPORTANT]
> A partir de 1 de julho de 2021, você não poderá adicionar novos testes em um espaço de trabalho existente ou habilitar um novo espaço de trabalho no Monitor de Desempenho de Rede. Você pode continuar a usar os testes criados antes de 1 de julho de 2021. Para minimizar a interrupção do serviço para suas cargas de trabalho atuais, [migre seus testes de monitor de desempenho de rede para o novo monitor de conexão](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) no observador de rede do Azure antes de 29 de fevereiro de 2024.

O recurso Monitor de Desempenho no [Monitor de Desempenho de Rede](network-performance-monitor.md) ajuda a monitorar a conectividade de rede em vários pontos em sua rede. Você pode monitorar as implantações de nuvem e locais internos, vários data centers e filiais, e aplicativos críticos ou microsserviços de várias camadas. Com o Monitor de Desempenho, você pode detectar problemas de rede antes de seus usuários reclamarem. As principais vantagens são que você pode: 

- Monitorar perda e latência em várias sub-redes e definir alertas.
- Monitorar todos os caminhos (incluindo caminhos redundantes) na rede.
- Solucionar problemas de rede transitórios e relevantes a um certo período de tempo, que são difíceis de replicar.
- Determinar um segmento específico na rede, que é responsável pela redução do desempenho.
- Monitorar a integridade da rede, sem a necessidade de SNMP.


![Monitor de Desempenho de Rede](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Configuração
Para abrir a configuração do Monitor de Desempenho de Rede, abra a [solução Monitor de Desempenho de Rede](network-performance-monitor.md) e selecione **Configurar**.

![Configure o Monitor de Desempenho de Rede](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Criar novas redes

Uma rede no Monitor de Desempenho de Rede é um contêiner lógico para sub-redes. Ele ajuda você a organizar o monitoramento da infraestrutura de rede de acordo com suas necessidades. Você pode criar uma rede com um nome amigável e adicionar sub-redes a ele de acordo com a sua lógica de negócios. Por exemplo, você pode criar uma rede chamada Londres e adicionar todas as sub-redes em seu data center Londres. Ou você pode criar uma rede chamada *ContosoFrontEnd* e adicionar a essa rede todas as sub-redes chamadas Contoso que servem ao front-end do seu aplicativo. A solução cria automaticamente uma rede padrão que contém todas as sub-redes descobertas no seu ambiente. 

Sempre que você criar uma rede, você adiciona uma sub-rede a ela. Essa sub-rede será removida da rede padrão. Se você excluir uma rede, todas as suas sub-redes serão retornadas automaticamente para a rede padrão. A rede Padrão age como o contêiner para todas as sub-redes que não estão contidas em nenhuma rede definida pelo usuário. Você não pode editar nem excluir a rede padrão. Ela sempre permanece no sistema. Você pode criar tantas redes personalizadas quantas forem necessárias. Na maioria dos casos, as sub-redes em sua organização são distribuídas em mais de uma rede. Crie uma ou mais redes para agrupar suas sub-redes para a sua lógica de negócios.

Para criar uma nova rede:


1. Selecione a guia **Redes**.
1. Selecione  **Adicionar rede** e insira o nome e a descrição da rede. 
2. Selecione uma ou mais sub-redes e selecione **Adicionar**. 
3. Selecione **salvar** para salvar a configuração. 


### <a name="create-monitoring-rules"></a>Crie regras de monitoramento 

O Monitor de Desempenho gera eventos de integridade quando o limite do desempenho de conexões de rede entre duas sub-redes ou entre duas redes é ultrapassado. O sistema pode aprender automaticamente esses limites. Você também pode fornecer limites personalizados. O sistema cria automaticamente uma regra Padrão, que gera um evento de integridade sempre que a perda ou a latência entre qualquer par de links de rede ou sub-rede viola o limite aprendido pelo sistema. Esse processo ajudará a solução a monitorar a infraestrutura de rede enquanto você não tiver criado nenhuma regra de monitoramento explicitamente. Se a regra padrão estiver habilitada, todos os nós enviarão transações sintéticas para todos os outros nós que você tiver habilitado para monitoramento. A regra padrão é útil para redes pequenas. Um exemplo é um cenário no qual você tem um pequeno número de servidores que executam um microsserviço e você deseja garantir que todos os servidores tenham conectividade entre si.

>[!NOTE]
> Recomendamos que você desabilite a regra padrão e cria regras de monitoramento personalizadas, especialmente com grandes redes em que você use um grande número de nós para monitoramento. As regras de monitoramento podem reduzir o tráfego gerado pela solução e ajudará você a organizar o monitoramento da rede.

Crie regras de monitoramento de acordo com a sua lógica de negócios. Um exemplo é se você deseja monitorar o desempenho da conectividade de rede de dois escritórios filiais com o escritório central. Agrupe todas as sub-redes no site1 do escritório na rede O1. Em seguida, agrupe todas as sub-redes no site2 do escritório na rede O2. Por fim, agrupe todas as sub-redes na sede na rede H. Crie duas regras de monitoramento: uma entre O1 e H e a outra entre O2 e H. 

Para criar regras de monitoramento personalizadas:

1. Selecione **Adicionar Regra** na guia **Monitor** e digite o nome da regra e uma descrição.
2. Selecione nas listas o par de links de rede ou sub-rede para monitorar. 
3. Selecione a rede que contém as sub-redes que você deseja na lista suspensa de rede. Em seguida, selecione as sub-redes na lista suspensa de sub-rede correspondente. Se desejar monitorar todas as sub-redes em um link de rede, selecione **Todas as sub-redes**. Da mesma forma, selecione outras sub-redes desejadas. Para excluir o monitoramento de links de sub-rede específicos das seleções feitas, selecione **Adicionar Exceção**. 
4. Escolha o protocolo TCP ou ICMP para executar transações sintéticas. 
5. Se não quiser criar eventos de integridade para os itens que você selecionou, desmarque **Ativar o monitoramento de integridade nos links cobertos por essa regra**. 
6. Escolha as condições de monitoramento. Para definir limites personalizados para geração de eventos de integridade, digite os valores de limite. Sempre que o valor da condição exceder o limite selecionado para o par de rede ou sub-rede selecionado, será gerado um evento de integridade. 
7. Selecione **salvar** para salvar a configuração. 

Depois de salvar uma regra de monitoramento, você pode integrar essa regra ao Gerenciamento de Alertas selecionando **Criar Alerta**. Uma regra de alerta é criada automaticamente com a consulta de pesquisa. Outros parâmetros necessários são preenchidos automaticamente. Usando uma regra de alerta, você pode receber alertas baseados em email, além de alertas existentes em Monitor de Desempenho de Rede. Os alertas também podem disparar ações corretivas com runbooks, ou podem se integrar a soluções de gerenciamento de serviços existentes usando WebHooks. Selecione **Gerenciar Alerta** para editar as configurações de alerta. 

Agora você pode criar mais regras para o Monitor de Desempenho ou passar para o painel de solução a fim de usar o recurso.

### <a name="choose-the-protocol"></a>Escolha o protocolo

O Monitor de Desempenho de Rede usa transações sintéticas para calcular métricas de desempenho de rede como perda de pacote e latência de link. Para entender esse conceito melhor, considere um agente de Monitor de Desempenho de Rede conectado a uma extremidade de um link de rede. Este agente de Monitor de Desempenho de Rede envia pacotes de teste para um segundo agente do Monitor de Desempenho de Rede conectado à outra extremidade da rede. O segundo agente responde com pacotes de resposta. Esse processo é repetido algumas vezes. Medindo o número de respostas e o tempo necessário para receber cada resposta, o primeiro agente de Monitor de Desempenho de Rede avalia a latência de link e os descartes de pacotes. 

O formato, o tamanho e a sequência desses pacotes são determinados pelo protocolo que você escolheu ao criar regras de monitoramento. Com base no protocolo dos pacotes, os dispositivos de rede intermediários, como roteadores e comutadores, podem processar esses pacotes de maneira diferente. Consequentemente, a opção de protocolo afeta a precisão dos resultados. A escolha de protocolo também determina se será necessário executar alguma etapa manual após implantar a solução Monitor de Desempenho de Rede. 

O Monitor de Desempenho de Rede permite que você escolha entre os protocolos TCP e ICMP para executar transações sintéticas. Se você escolher o ICMP quando criar uma regra de transação sintética, os agentes de Monitor de Desempenho de Rede usarão mensagens de eco ICMP para calcular a latência de rede e a perda de pacote. O Eco ICMP usa a mesma mensagem enviada pelo utilitário ping convencional. Quando você usa o TCP como protocolo, os agentes de Monitor de Desempenho de Rede enviam pacotes TCP SYN pela rede. Essa etapa é seguida pela conclusão de um handshake TCP e a conexão é removida usando pacotes RST. 

Antes de escolher um protocolo, considere as seguintes informações: 

* **Descoberta de várias rotas de rede.** O TCP é mais preciso ao descobrir várias rotas e exige menos agentes em cada sub-rede. Por exemplo, um ou dois agentes que usem TCP podem descobrir todos os caminhos redundantes entre sub-redes. Você precisa de vários agentes que usam o ICMP para alcançar resultados semelhantes. Usando o ICMP e, se você tiver várias rotas entre duas sub-redes, serão necessários mais do que 5N agentes na sub-rede de origem ou de destino.

* **Precisão dos resultados.** Roteadores e comutadores tendem a atribuir a prioridade mais baixa para pacotes de eco ICMP em comparação com pacotes TCP. Em determinadas situações, quando os dispositivos de rede estão sobrecarregados, os dados obtidos pelo TCP refletem mais a perda e a latência apresentadas pelos aplicativos. Isso ocorre porque a maioria do tráfego do aplicativo flui pelo TCP. Nesses casos, o ICMP fornece resultados menos precisos em comparação ao TCP. 

* **Configuração do firewall.** O protocolo TCP exige que os pacotes TCP sejam enviados a uma porta de destino. A porta padrão usada por agentes do Monitor de Desempenho de Rede é 8084. Você pode alterar a porta quando configurar agentes. Verifique se os firewalls de rede ou as regras do Grupo de Segurança de Rede (NSG) no Azure permitem o tráfego na porta. Você também precisa certificar-se de que o firewall local nos computadores em que os agentes são instalados esteja configurado para permitir o tráfego nesta porta. Você pode usar scripts do PowerShell para configurar regras de firewall em computadores que executam o Windows, no entanto, você precisa configurar manualmente o firewall da rede. Por outro lado, o ICMP não funciona por meio de uma porta. Na maioria dos cenários de negócios, o tráfego do ICMP é permitido por meio de firewalls para que você use ferramentas de diagnóstico de rede como o utilitário Ping. Se você puder executar Ping de um computador para o outro, poderá usar o protocolo ICMP sem ter que configurar firewalls manualmente.

>[!NOTE] 
> Alguns firewalls podem bloquear o ICMP, o que pode levar à retransmissão resultando em um grande número de eventos em seu sistema de gerenciamento de evento e informações de segurança. Verifique se o protocolo que você escolher não está bloqueado por um firewall de rede ou NSG. Caso contrário, o Monitor de Desempenho de Rede não pode monitorar o segmento de rede. Recomendamos que você use TCP para monitoramento. Use ICMP nos cenários em que você não pode usar o TCP, como quando: 
>
> - Você usa nós de cliente com base em Windows, porque soquetes brutos TCP não são permitidos no cliente Windows.
> - Seu firewall de rede ou NSG bloqueia o TCP.
> - Você não sabe como mudar o protocolo.

Se você optar por usar o ICMP durante a implantação, poderá mudar para TCP a qualquer momento, editando a regra de monitoramento padrão.

1. Vá para monitor de **desempenho de rede**   >  ****   >  **Configurar**   >  **Monitor**. Em seguida, selecione **Regra padrão**. 
2. Role até a seção **Protocolo** e selecione o protocolo que você deseja usar. 
3. Selecione **Salvar** para aplicar a configuração. 

Mesmo se a regra padrão usar um protocolo específico, você poderá criar novas regras com um protocolo diferente. Você pode até mesmo criar uma combinação de regras em que algumas regras usem ICMP e as outras usem TCP. 

## <a name="walkthrough"></a>Passo a passo 

Agora examine uma investigação simples para saber a causa raiz de um evento de integridade.

No painel de solução, um evento de integridade mostra que um link de rede não está íntegro. Para investigar o problema, selecione o bloco **Links de rede que estão sendo monitorados**.

A página de busca detalhada mostra que o link de rede **DMZ2 DMZ1** não está íntegro. Selecione **Exibir links de sub-rede** para este link de rede. 


A página de busca detalhada mostra todos os links de sub-rede no link de rede **DMZ2-DMZ1**. Para ambos os links de sub-rede, a latência ultrapassou o limite, tornando o link de rede não íntegro. Você também pode ver as tendências de latência de ambos os links de sub-rede. Use o controle de seleção de tempo do grafo para se concentrar no intervalo de tempo necessário. Você pode ver a hora do dia em que a latência atingiu seu pico. Pesquise mais tarde os logs desse período de tempo para investigar o problema. Selecione **Exibir links do nó** para continuar com a busca detalhada. 
 
 ![Página Links de sub-rede](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

De forma semelhante à página anterior, a página de busca detalhada do link de sub-rede específico lista seus links de nós constituintes. Você pode executar ações semelhantes aqui, como fez na etapa anterior. Selecione **Exibir topologia** para exibir a topologia entre os dois nós. 
 
 ![Página Links de nó](media/network-performance-monitor-performance-monitor/node-links.png) 

Todos os caminhos entre os dois nós selecionados são criados em gráfico no mapa de topologia. Você pode visualizar a topologia de salto a salto das rotas entre dois nós no mapa de topologia. Isso lhe dá uma visão clara de quantas rotas existem entre os dois nós e quais caminhos os pacotes de dados adotam. Os gargalos de desempenho de rede são mostrados em vermelho. Para localizar uma conexão de rede ou um dispositivo de rede com defeito, observe os elementos em vermelho no mapa de topologia. 

 ![Painel de topologia com mapa de topologia](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Analise a perda, a latência e o número de saltos em cada caminho no painel **Ação**. Use a barra de rolagem para exibir os detalhes dos caminhos não íntegros. Use os filtros para selecionar os caminhos com o nó íntegro, de forma que somente a topologia para os caminhos selecionados seja criada em gráfico. Para ampliar ou reduzir o mapa de topologia, use a roda do mouse. 

Na imagem a seguir, a causa raiz das áreas problemáticas para a seção específica da rede aparece nos caminhos e nos saltos vermelhos. Selecione um nó no mapa de topologia para revelar as propriedades do nó, incluindo o FQDN e o endereço IP. Ao selecionar um salto, um endereço IP do nó será exibido. 
 
![Mapa de topologia com as propriedades do nó selecionadas](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Próximas etapas
[Pesquisar logs](../log-query/log-query-overview.md) para exibir registros de dados de desempenho de rede detalhados.
