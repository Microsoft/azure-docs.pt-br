---
title: 'Solucione problemas de desempenho de rede virtual: Azure | Microsoft Docs'
description: Esta página fornece um método padronizado de teste de desempenho de link de rede do Azure.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: 9ec310ffaa9d2bb297abde9341bf7b6c2dc763b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883213"
---
# <a name="troubleshooting-network-performance"></a>Solução de problemas de desempenho de rede
## <a name="overview"></a>Visão geral
O Azure oferece maneiras estáveis e rápidas de se conectar da sua rede local ao Azure. Métodos como VPN site a site e ExpressRoute são usados com êxito por grandes e pequenos clientes para executar seus negócios no Azure. Mas o que acontece quando o desempenho não satisfaz suas expectativas ou a experiência anterior? Este documento pode ajudar a padronizar a maneira de testar e fazer uma linha de base específica de seu ambiente.

Este documento mostra como você pode testar a latência de rede e a largura de banda entre dois hosts de maneira fácil e consistente. Ele também fornece algumas dicas de como examinar a rede do Azure e ajudar a isolar pontos problemáticos. O script do PowerShell e as ferramentas abordadas exigem dois hosts na rede (em ambas as extremidades do link que está sendo testado). Um host deve ser um Windows Server ou Desktop; o outro pode ser Windows ou Linux. 

>[!NOTE]
>A abordagem para solução de problemas, as ferramentas e os métodos usados são preferências pessoais. Este documento descreve a abordagem e as ferramentas que eu costumo usar. Sua abordagem provavelmente será diferente e não há nada errado com diferentes abordagens para a solução de problemas. No entanto, se você não tem uma abordagem estabelecida, este documento pode ajudá-lo a começar a criar seus próprios métodos, ferramentas e preferências para solução de problemas de rede.
>
>

## <a name="network-components"></a>Componentes de rede
Antes de nos aprofundarmos na solução de problemas, vamos falar sobre alguns termos e componentes comuns. Esta discussão garante que nos lembremos de cada componente na cadeia de ponta a ponta que permite a conectividade no Azure.
[![1]][1]

No nível mais alto, descrevo três principais domínios de roteamento de rede;

- a rede do Azure (nuvem azul à direita)
- com a Internet ou WAN (nuvem verde no centro)
- a Rede corporativa (nuvem cor de pêssego à esquerda)

Ao examinar o diagrama da direita para esquerda, vamos falar resumidamente sobre cada componente:
 - **Máquina virtual** – o servidor pode ter várias NICs; certifique-se de as rotas estáticas, as rotas padrão e as configurações do sistema operacional estejam enviando e recebendo tráfego da forma você pensa que estão. Além disso, cada SKU de VM tem uma restrição de largura de banda. Se você estiver usando um SKU de VM menor, o tráfego será limitado pela largura de banda disponível para a NIC. Eu geralmente uso uma DS5v2 para testes (e excluo assim que terminar os testes para economizar dinheiro) para garantir a largura de banda adequada na VM.
 - **NIC** – certifique-se de saber qual IP privado está atribuído à NIC em questão.
 - **NSG de NIC** – pode ser que haja NSGs específicos aplicados no nível da NIC; certifique-se de que o conjunto de regras do NSG seja apropriado ao tráfego que você está tentando passar. Por exemplo, certifique-se de que as portas 5201 para iPerf, 3389 para RDP ou 22 para SSH estejam abertas para permitir a passagem do tráfego de teste.
 - **Sub-rede de VNet** – a NIC é atribuída a uma sub-rede específica. Certifique-se de que você saiba qual e quais as regras associadas a essa sub-rede.
 - **NSG de Sub-rede** – assim como a NIC, os NSGs também podem ser aplicados na sub-rede. Certifique-se de que o conjunto de regras de NSG é adequado para o tráfego que você está tentando passar. (para o tráfego de entrada à NIC, o NSG da sub-rede se aplica primeiro e, depois, o NSG da NIC; por outros lado, para o tráfego de saída da VM, o NSG da NIC aplica-se primeiro e, depois, entra em ação o NSG da sub-rede).
 - **UDR de Sub-rede** – as rotas definidas pelo usuário podem direcionar o tráfego para um salto intermediário (como um firewall ou balanceador de carga). Verifique se há um UDR em vigor para o tráfego e, se for o caso, para onde o tráfego vai e o que esse próximo salto fará com o tráfego. (por exemplo, um firewall pode passar algum tráfego e negar outro entre os mesmos dois hosts).
 - **NSG/UDR de sub-rede de gateway** – assim como a sub-rede de VM, a sub-rede de gateway pode ter NSGs e UDRs. Verifique se eles existem e que efeitos eles têm sobre seu tráfego.
 - **Gateway de Rede Virtual (ExpressRoute)** – uma vez que o emparelhamento (ExpressRoute) ou VPN estiver habilitado, não haverá muitas configurações que podem afetar como ou se o tráfego é roteado. Se você tem vários circuitos de ExpressRoute ou túneis de VPN conectados ao mesmo Gateway de Rede Virtual, é preciso saber das configurações de peso da conexão, pois essa configuração afeta a preferência de conexão e o caminho que o tráfego adota.
 - **Filtro de rota** (não mostrado) – um filtro de rota se aplica apenas ao Emparelhamento da Microsoft no ExpressRoute, mas é essencial verificar se você não está vendo as rotas esperadas no Emparelhamento da Microsoft. 

Neste ponto, você está na parte WAN do link. Esse domínio de roteamento pode ser o seu provedor de serviços, sua WAN corporativa ou a Internet. Os diversos saltos, tecnologias e empresas envolvidas com estes links podem fazer com que a solução de problemas se torne um pouco difícil. Geralmente, você trabalha para excluir o Azure e as suas Redes corporativas antes de avançar para esta coleção de empresas e saltos.

No diagrama anterior, na extrema esquerda, está sua rede corporativa. Dependendo do tamanho da sua empresa, esse domínio de roteamento pode ser de alguns dispositivos de rede entre você e a WAN ou várias camadas de dispositivos em uma rede de um campus ou uma empresa.

Considerando as complexidades desses três diferentes ambientes de rede de alto nível, geralmente o ideal é iniciar pelas bordas e tentar mostrar onde o desempenho é bom e onde ele se degrada. Essa abordagem pode ajudar a identificar qual é o domínio de roteamento problemático dentre os três e, então, concentrar a solução de problemas no ambiente específico.

## <a name="tools"></a>Ferramentas
A maioria dos problemas de rede podem ser analisados e isolados usando ferramentas básicas, como o ping e o rastreamento de rotas. É raro você ter que avançar até uma análise de pacotes como Wireshark. Para ajudar na solução de problemas, o AzureCT (Kit de ferramentas de conectividade do Azure) foi desenvolvido para colocar algumas dessas ferramentas em um pacote simples. Para teste de desempenho, eu gosto de usar iPerf e PSPing. O iPerf é uma ferramenta de uso geral e é executado na maioria dos sistemas operacionais. O iPerf é bom para testes básicos de desempenho e é razoavelmente fácil de usar. O PSPing é uma ferramenta de ping desenvolvida pelo SysInternals. O PSPing é uma maneira fácil de executar pings de ICMP e TCP em um comando que também é fácil de usar. Ambas as ferramentas são leves e são "instaladas" de maneira simples, através da cópia dos arquivos em um diretório no host.

Eu encapsulei todas essas ferramentas e métodos em um módulo do PowerShell (AzureCT) para que você possa instalar e usar.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT – o Kit de ferramentas de conectividade do Azure
O módulo do PowerShell AzureCT tem dois componentes: [Teste de disponibilidade][Availability Doc] e [Teste de desempenho][Performance Doc]. Este documento aborda somente o teste de desempenho, assim, vamos nos concentrar nos dois comandos de Desempenho de links deste módulo do PowerShell.

Há três etapas básicas para usar este kit de ferramentas para teste de desempenho. 1) Instalar o módulo do PowerShell; 2) Instalar os aplicativos de suporte, iPerf e PSPing; 3) Executar o teste de desempenho.

1. Instalação do módulo do PowerShell

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Esse comando baixa o módulo do PowerShell e o instala localmente.

2. Instalar os aplicativos de suporte
    ```powershell
    Install-LinkPerformance
    ```
    Este comando do AzureCT instala o iPerf e o PSPing em um novo diretório "C:\ACTTools". Ele também abre as portas do Firewall do Windows para permitir tráfego de ICMP e da porta 5201 (iPerf).

3. Executar o teste de desempenho

    Primeiro, no host remoto, você deve instalar e executar o iPerf no modo de servidor. Verifique também se o host remoto está escutando na 3389 (RDP para Windows) ou na 22 (SSH para Linux), e permitindo tráfego na porta 5201 para o iPerf. Se o host remoto for Windows, instale o AzureCT e execute o comando de Install-LinkPerformance para configurar o iPerf e as regras de firewall necessárias para iniciar o iPerf no modo de servidor com êxito. 
    
    Quando o computador remoto estiver pronto, abra o PowerShell no computador local e inicie o teste:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Este comando executa uma série de testes simultâneos de carga e latência para ajudar a estimar a capacidade da largura de banda e a latência do seu link de rede.

4. Examinar o resultado dos testes

    O formato da saída do PowerShell é semelhante a:

    [![4]][4]

    Os resultados detalhados de todos os testes do PSPing e do iPerf estão em arquivos de texto individuais no diretório de ferramentas do AzureCT em "C:\ACTTools".

## <a name="troubleshooting"></a>solução de problemas
Se o teste de desempenho não fornecer os resultados esperados, conhecer as razões deverá seja um processo passo a passo progressivo. Considerando o número de componentes no caminho, uma abordagem sistemática geralmente oferece um caminho mais rápido para a solução, em vez de ficar pulando e, potencialmente, fazendo o mesmo teste várias vezes de forma desnecessária.

>[!NOTE]
>Essa é uma situação de um problema de desempenho e não de um problema de conectividade. As etapas seriam diferentes se o tráfego não estivesse passando de nenhuma forma.
>
>

Primeiro, desafie suas suposições. A sua expectativa é razoável? Por exemplo, se você tem um circuito de 1 Gbps do ExpressRoute e latência de 100 ms, não é razoável esperar o tráfego total de 1 Gbps, considerando as características de desempenho do TCP em links de alta latência. Consulte a [seção Referências](#references) para saber mais sobre premissas de desempenho.

Em seguida, recomendo começar pelas bordas entre domínios de roteamento e tentar isolar o problema a um único domínio de roteamento principal; a Rede corporativa, a WAN ou a Rede do Azure. As pessoas costumam culpar a "caixa preta" no caminho. Culpar a caixa preta é fácil, mas isso pode atrasar significativamente a resolução, especialmente se, na verdade, o problema está em uma área em que você pode fazer alterações. Faça tudo o que for possível antes de passar para seu provedor de serviços ou ISP.

Depois de identificar o domínio de roteamento principal que parece conter o problema, você deverá criar um diagrama da área em questão. Não importa se é num quadro de comunicações, no bloco de notas ou no Visio, pois um diagrama fornece um "mapa de batalha" concreto para permitir uma abordagem metódica para isolar ainda mais o problema. Você pode planejar pontos de teste e atualizar o mapa ao descartar as áreas ou obter detalhes conforme o progresso do teste.

Agora que você tem um diagrama, comece a dividir a rede em segmentos e restringir o problema. Descubra em que locais ela funciona e onde não funciona. Continue movendo seus pontos de teste para isolar até o componente com problema.

Além disso, não se esqueça de examinar outras camadas do modelo OSI. É fácil se concentrar na rede e nas camadas de 1 a 3 (camadas física, de dados e de rede), mas os problemas também podem estar até na camada 7, na camada de aplicativo. Mantenha uma mente aberta e verifique as suposições.

## <a name="advanced-expressroute-troubleshooting"></a>Solução de problemas avançada do ExpressRoute
Se você não tem certeza de onde realmente está borda da nuvem, isolar os componentes do Azure pode se tornar um desafio. Quando o ExpressRoute é usado, a borda é um componente de rede chamado MSEE (Microsoft Enterprise Edge). **Ao usar o ExpressRoute**, o MSEE é o primeiro ponto de contato com a rede da Microsoft e o último salto ao sair dela. Ao criar um objeto de conexão entre o gateway de Rede Virtual e o circuito do ExpressRoute, você está, na verdade, fazendo uma conexão com o MSEE. Reconhecer o MSEE como o primeiro ou último salto (dependendo de qual direção você está indo) é fundamental para isolar problemas da Rede do Azure, seja para provar que o problema está no Azure ou mais adiante, na WAN ou na Rede corporativa. 

[![2]][2]

>[!NOTE]
> Observe que o MSEE não está na nuvem do Azure. Na verdade, o ExpressRoute está na borda da rede da Microsoft e não no Azure. Ao conectar o ExpressRoute a um MSEE, você está conectado à rede da Microsoft; daí você pode acessar qualquer um dos serviços de nuvem, como o Office 365 (com o Emparelhamento da Microsoft) ou o Azure (com Emparelhamento privado e/ou da Microsoft).
>
>

Se duas Redes Virtuais (Redes Virtuais A e B, no diagrama) estão conectadas ao **mesmo** circuito do ExpressRoute, você pode realizar uma série de testes para isolar o problema no Azure (ou provar que ele não está no Azure)
 
### <a name="test-plan"></a>Plano de teste
1. Execute o teste Get-LinkPerformance entre a VM1 e a VM2. Esse teste fornece informações sobre se o problema é local ou não. Se esse teste produzir resultados de latência e de largura de banda aceitáveis, você poderá marcar a Rede Virtual local como boa.
2. Supondo que o tráfego da Rede Virtual local esteja bom, execute o teste Get-LinkPerformance entre a VM1 e a VM3. Esse teste exerce a conexão pela rede da Microsoft até a MSEE, retornando para o Azure. Se esse teste produzir resultados de latência e de largura de banda aceitáveis, você poderá marcar a rede do Azure como boa.
3. Se o Azure for excluído, você poderá executar uma sequência de testes semelhante em sua Rede corporativa. Se esses testes também produzirem bons resultados, será hora de trabalhar com seu provedor de serviços ou ISP para diagnosticar a conexão da WAN. Exemplo: Execute este teste entre duas filiais ou entre sua mesa e um servidor de data center. Dependendo do que você está testando, localize pontos de extremidade (servidores, computadores, etc.) que podem exercer esse caminho.

>[!IMPORTANT]
> É fundamental que, para cada teste, você marque a hora do dia em que executou o teste e registre os resultados em um local comum (eu gosto do OneNote ou do Excel). Cada execução de teste deve ter saída idêntica para que você possa comparar os dados resultantes entre execuções de teste e não ter "lacunas" nos dados. A consistência entre vários testes é a principal razão pela qual eu uso o AzureCT para solução de problemas. A mágica não está nos cenários de carga exatos que eu executo, mas a *mágica* está no fato de que obtenho uma *saída consistente de teste e dados* de cada teste. Registrar a hora e sempre ter dados consistentes será especialmente útil se você descobrir mais tarde que o problema é esporádico. Antecipe-se no cuidado com a coleta de dados e você evitará perda de tempo refazendo testes com os mesmos cenários (eu aprendi isso da maneira difícil há muitos anos).
>
>

## <a name="the-problem-is-isolated-now-what"></a>O problema está isolado, e agora?
Quanto mais você puder isolar o problema, mais fácil será corrigi-lo, no entanto, geralmente você chega ao ponto em que não é possível detalhar mais ou avançar na solução de problemas. Exemplo: você vê o link entre o provedor de serviços realizando saltos na Europa, mas o caminho esperado seria todo na Ásia. Esse é momento em que você deve obter ajuda. A pessoa a quem você solicitará ajuda dependerá do domínio de roteamento ao qual você isolou o problema. Se você puder encontrar o erro de um componente específico, será ainda melhor.

Para problemas de rede corporativa, o departamento de TI interno ou o provedor de serviços que dá suporte à sua rede (que pode ser o fabricante do hardware) poderá ajudar com a configuração do dispositivo ou o reparo do hardware.

Em relação à WAN, o compartilhamento dos resultados de teste com o provedor de serviços ou o ISP poderá ajudá-los a começar e evitar realizar os mesmos testes que você já fez. No entanto, não se sinta ofendido se eles desejarem verificar os resultados por si mesmos. "Confiar, mas verificar" é um bom lema quando a solução de problemas é baseada nos resultados relatados por outras pessoas.

Com o Azure, quando você isola o problema com todos os detalhes possíveis, é hora de examinar a [Documentação da Rede do Azure][Network Docs] e, em seguida, caso ainda seja necessário, [abrir um tíquete de suporte][Ticket Link].

## <a name="references"></a>Referências
### <a name="latencybandwidth-expectations"></a>Expectativas de largura de banda/latência
>[!TIP]
> A latência geográfica (em milhas ou quilômetros) entre os pontos de extremidade que você está testando é, sem dúvida, o maior componente de latência. Embora haja latência de equipamento (componentes físicos e virtuais, número de saltos, etc.) envolvida, a geografia é, comprovadamente, o maior componente de latência geral ao lidar com conexões WAN. Também é importante observar que a distância é a distância que a fibra percorre e não a distância em linha reta ou a distância do mapa. É incrivelmente difícil obter essa distância com algum nível de precisão. Assim, eu geralmente uso uma calculadora de distância de cidade na Internet e sei que esse método resulta em uma medida bastante imprecisa, mas é o suficiente para definir uma expectativa geral.
>
>

Eu tenho uma instalação do ExpressRoute em Seattle, Washington, nos EUA. A tabela a seguir mostra a latência e a largura de banda que eu obtive ao testar com vários locais do Azure. Eu estimei a distância geográfica entre cada extremidade do teste.

Configuração do teste:
 - Um servidor físico executando o Windows Server 2016 com NIC de 10 Gbps, conectado a um circuito do ExpressRoute.
 - Um circuito Premium do ExpressRoute de 10 Gbps no local identificado, com o Emparelhamento privado habilitado.
 - Uma Rede Virtual do Azure com um gateway UltraPerformance na região especificada.
 - Uma VM DS5v2 executando o Windows Server 2016 na Rede Virtual. A VM era não ingressada no domínio, criada com base na imagem padrão do Azure (sem otimização ou personalização), com o AzureCT instalado.
 - Todos os testes usaram o comando Get-LinkPerformance do AzureCT com um teste de carga de 5 minutos para cada uma das seis execuções de teste. Por exemplo: 

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - O fluxo de dados de cada teste tinha a carga fluindo do servidor físico local (cliente iPerf em Seattle) até a VM do Azure (servidor iPerf na região do Azure listada).
 - Os dados da coluna "Latência" são do teste Sem carga (um teste de latência de TCP sem execução do iPerf).
 - Os dados da coluna "Largura de banda máx." são dos 16 testes de carga de fluxo de TCP com um tamanho de janela de 1 MB.

[![3]][3]

### <a name="latencybandwidth-results"></a>Resultados de latência/largura de banda
>[!IMPORTANT]
> Esses números servem apenas como referência geral. Muitos fatores afetam a latência e, embora esses valores sejam geralmente consistentes ao longo do tempo, as condições no Azure ou na rede dos provedores de serviços podem enviar o tráfego por diferentes caminhos a qualquer momento, afetando, assim, a latência e a largura de banda. Geralmente, os efeitos dessas alterações não resultam em diferenças significativas.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Local padrão|Azure<br/>Região|Estimada (km)<br/>Distância|Latency|Sessão 1<br/>Largura de banda|Máximo<br/>Largura de banda|
| Seattle | Oeste dos EUA 2        |    191 km |   5 ms | 262,0 Mbits/s |  3,74 Gbits/s |
| Seattle | Oeste dos EUA          |  1.094 km |  18 ms |  82,3 Mbits/s |  3,70 Gbits/s |
| Seattle | Centro dos EUA       |  2.357 km |  40 ms |  38,8 Mbits/s |  2,55 Gbits/s |
| Seattle | Centro-Sul dos Estados Unidos |  2.877 km |  51 ms |  30,6 Mbits/s |  2,49 Gbits/s |
| Seattle | Centro-Norte dos EUA |  2.792 km |  55 ms |  27,7 Mbits/s |  2,19 Gbits/s |
| Seattle | Leste dos EUA 2        |  3.769 km |  73 ms |  21,3 Mbits/s |  1,79 Gbits/s |
| Seattle | Leste dos EUA          |  3.699 km |  74 ms |  21,1 Mbits/s |  1,78 Gbits/s |
| Seattle | Leste do Japão       |  7.705 km | 106 ms |  14,6 Mbits/s |  1,22 Gbits/s |
| Seattle | Sul do Reino Unido         |  7.708 km | 146 ms |  10,6 Mbits/s |   896 Mbits/s |
| Seattle | Europa Ocidental      |  7.834 km | 153 ms |  10,2 Mbits/s |   761 Mbits/s |
| Seattle | Leste da Austrália   | 12.484 km | 165 ms |   9,4 Mbits/s |   794 Mbits/s |
| Seattle | Sudeste Asiático   | 12.989 km | 170 ms |   9,2 Mbits/s |   756 Mbits/s |
| Seattle | Sul do Brasil *   | 10.930 km | 189 ms |   8,2 Mbits/s |   699 Mbits/s |
| Seattle | Sul da Índia      | 12.918 km | 202 ms |   7,7 Mbits/s |   634 Mbits/s |

\* A latência até o Brasil é um bom exemplo em que a distância em linha reta difere significativamente da distância que a fibra percorre. Eu esperava que a latência seria de cerca de 160 ms, mas é de 189 ms na realidade. Essa diferença em relação a minha expectativa poderia indicar um problema de rede em algum lugar, mas é mais provável que o caminho da fibra não vá até o Brasil em uma linha reta e tenha cerca de 1.000 km a mais para chegar até o Brasil, partindo de Seattle.

## <a name="next-steps"></a>Próximas etapas
1. Baixe o Kit de ferramentas de conectividade do Azure do GitHub em [https://aka.ms/AzCT][ACT]
2. Siga as instruções para [teste de desempenho de link][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Componentes de rede do Azure"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "Solução de problemas do ExpressRoute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Ambiente de teste de desempenho"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "Saída do PowerShell"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT
