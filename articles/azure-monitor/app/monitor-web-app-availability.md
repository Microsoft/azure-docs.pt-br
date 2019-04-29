---
title: Monitorar a disponibilidade e a capacidade de resposta de qualquer site da Web | Microsoft Docs
description: Configure testes da web no Application Insights. Obtenha alertas se um site fica indisponível ou responde lentamente.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/22/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 9f48303396d1ecd03fdffd2c6ab1e0c122615a21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60701398"
---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Monitorar a disponibilidade e a capacidade de resposta de qualquer site
Após implantar o aplicativo Web ou site em qualquer servidor, você pode configurar testes para monitorar sua disponibilidade e capacidade de resposta. [Application Insights do Azure](../../azure-monitor/app/app-insights-overview.md) envia solicitações da Web ao aplicativo em intervalos regulares de pontos no mundo todo. Ele o alertará se o aplicativo não responder ou responder lentamente.

Você pode configurar testes de disponibilidade para qualquer ponto de extremidade HTTP ou HTTPS que for acessível da Internet pública. Não é necessário adicionar qualquer coisa ao site que você está testando. Ele nem precisa ser o seu site: você pode testar um serviço de API REST do qual você depende.

Há dois tipos de testes de disponibilidade:

* [Teste de ping de URL](#create): um teste simples que você pode criar no Portal do Azure.
* [Teste na Web de várias etapas](#multi-step-web-tests): criado no Visual Studio Enterprise e carregado no portal.

Você pode criar até 100 testes de disponibilidade por recurso de aplicativo.


## <a name="create"></a>Abrir um recurso para os seus relatórios de teste de disponibilidade

**Se você já configurou o Application Insights** para seu aplicativo web, abra o recurso Application Insights no [portal do Azure](https://portal.azure.com).

**Ou, se preferir ver os relatórios em um novo recurso,** vá ao [Portal do Azure](https://portal.azure.com)e crie um recurso do Application Insights.

![Criar um recurso > Ferramentas para Desenvolvedores > Application Insights](./media/monitor-web-app-availability/1create-resource-appinsights.png)

Clique em **Todos os recursos** para abrir a folha de Visão geral do novo recurso.

## <a name="setup"></a>Criar um teste de ping de URL
Abra a folha Disponibilidade e adicione um teste.

![Preencha pelo menos o URL do seu site](./media/monitor-web-app-availability/2addtest-url.png)

* **A URL** pode ser qualquer página da web que você deseja testar, mas ela deve estar visível na Internet pública. A URL pode incluir uma cadeia de consulta. Por exemplo, você pode utilizar um pouco seu banco de dados. Se a URL for resolvida para um redirecionamento, nós a seguiremos, até um máximo de 10 redirecionamentos.
* **Analisar solicitações dependentes**: se esta opção estiver marcada, o teste solicitará imagens, scripts, arquivos de estilo e outros arquivos que fazem parte da página da Web em teste. O tempo de resposta gravado inclui o tempo necessário para obter esses arquivos. O teste falhará se todos esses recursos não puderem ser baixados com êxito dentro do tempo limite para o teste inteiro. Se a opção não estiver marcada, o teste solicitará apenas o arquivo na URL especificada.

* **Habilitar novas tentativas**:  se esta opção estiver marcada, quando o teste falhar, ele será repetido após um breve intervalo. Uma falha só será relatada se três tentativas sucessivas falharem. Testes subsequentes são então executados com a frequência de teste normal. A repetição é suspensa temporariamente até o próximo sucesso. Essa regra é aplicada independentemente em cada local de teste. Recomendamos essa opção. Em média, aproximadamente 80% das falhas desaparecem na repetição.

* **Frequência de teste**: define a frequência com que o teste é executado em cada localização de teste. Com uma frequência padrão de cinco minutos e cinco locais de teste, seu site é testado em média a cada minuto.

* **locais de teste** são os locais por meio dos quais nossos servidores enviam solicitações da Web para sua URL. Nosso número mínimo de locais teste recomendado é cinco e para assim garantir que você possa diferenciar problemas no seu site de problemas da rede. Você pode selecionar até 16 locais.

> [!NOTE]
> * É altamente recomendável testar vários locais com um mínimo de cinco locais. Isso é para evitar alarmes falsos que podem resultar de problemas temporários com um local específico. Além disso, descobrimos que a configuração ideal é ter o número de locais de teste a ser igual ao limite de alerta local + 2. 
> * Habilitar a opção "Analisar solicitações dependentes" resulta em uma verificação mais rigorosa. O teste de possibilidade de falha de casos que não seja perceptíveis ao navegar manualmente o site.

* **Critérios de sucesso**:

    **Tempo limite de teste**: diminua esse valor para ser alertado sobre respostas lentas. O teste é considerado uma falha se as respostas de seu site não são recebidas dentro desse período. Se você tiver selecionado **Analisar solicitações dependentes**, todas as imagens, arquivos de estilo, scripts e outros recursos dependentes devem ter sido recebidos dentro desse período.

    **Resposta HTTP**: o código de status retornado que é contado como êxito. 200 é o código que indica que uma página da Web normal foi retornada.

    **Correspondência de conteúdo**: uma cadeia de caracteres como "Bem-vindo!" Faremos o teste que uma correspondência exata de maiúsculas e minúsculas ocorre em todas as respostas. É necessário que seja uma cadeia de caracteres simples, sem curingas. Lembre-se de que se o conteúdo de sua página for alterado, talvez seja necessário atualizá-lo.

* **Limite de alerta de localização**: é recomendável um mínimo de 3/5 locais. É a relação ideal entre o limite de alerta local e o número de locais de teste **limite de alerta local** = **número de locais teste** - 2, com um mínimo de cinco de teste locais.

## <a name="multi-step-web-tests"></a>Testes na Web com diversas etapas
Você pode monitorar um cenário que envolve uma sequência de URLs. Por exemplo, se estiver monitorando um site de vendas, você poderá testar se adicionar itens ao carrinho de compras funciona corretamente.

> [!NOTE]
> Há uma cobrança para testes na Web de várias etapas. [Esquema de preços](https://azure.microsoft.com/pricing/details/application-insights/).
> 

Para criar um teste com várias etapas, grave o cenário usando o Visual Studio Enterprise e carregue a gravação no Application Insights. O Application Insights reproduz o cenário em intervalos e verifica as respostas.

> [!NOTE]
> * Não é possível usar funções codificadas nem loops nos seus testes. O teste deve estar completamente incluso no script. webtest. No entanto, você pode usar plug-ins-padrão.
> * Somente os caracteres em inglês têm suporte nos testes da Web de várias etapas. Se você usar o Visual Studio em outros idiomas, atualize o arquivo de definição de teste da Web para traduzir/excluir caracteres que não são inglês.
>

#### <a name="1-record-a-scenario"></a>1. Registrar um cenário
Use o Visual Studio Enterprise para registrar uma sessão da Web.

1. Crie um projeto de teste de desempenho na Web.

    ![No Visual Studio Enterprise Edition, crie um projeto do modelo de Teste de carga e desempenho na Web.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

   * *Não consegue ver o modelo de Teste de carga e desempenho na Web?* - Feche o Visual Studio Enterprise. Abra o **Instalador do Visual Studio** para modificar a instalação do Visual Studio Enterprise. Em **Componentes Individuais**, selecione **Ferramentas de teste de carga e desempenho na Web**.

2. Abra o arquivo .webtest e inicie a gravação.

    ![Abra o arquivo .webtest e clique em Registrar.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Siga as ações do usuário que você deseja simular em seu teste: abra seu site, adicione um produto ao carrinho e assim por diante. Em seguida, interrompa seu teste.

    ![O gravador de teste da Web é executado no Internet Explorer.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    Não crie um cenário longo. Há um limite de 100 etapas e 2 minutos.
4. Edite o teste para:

   * Adicionar validações para verificar o texto recebido e os códigos de resposta.
   * Remover todas as interações supérfluas. Você também pode remover solicitações dependentes de imagens, ou de sites de anúncio ou rastreamento.

     Lembre-se de que você só pode editar o script de teste — não é possível adicionar código personalizado ou chamar outros testes da web. Não insira loops no teste. Você pode usar plug-ins de teste da Web padrão.
5. Execute o teste no Visual Studio para confirmar que ele funciona.

    O executor do teste na Web abre um navegador da Web e repete as ações gravadas por você. Verifique se ele funciona conforme o esperado.

    ![No Visual Studio, abra o arquivo .webtest e clique em Executar.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2. Carregar o teste na Web para o Application Insights
1. No portal do Application Insights na folha disponibilidade, clique em Adicionar teste.

    ![Na folha de disponibilidade, escolha Adicionar teste.](./media/monitor-web-app-availability/3addtest-web.png)
2. Selecione o teste com várias etapas e carregue o arquivo .webtest.

    Defina os locais de teste, a frequência e os parâmetros de alerta da mesma forma que para testes de ping.

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>Conectando a hora e números aleatórios em seu teste de várias etapas
Suponha que você está testando uma ferramenta que obtém dados dependentes de tempo, como estoques de um feed externo. Quando grava seu teste na Web você deve usar horários específicos, definindo-os, todavia, como parâmetros do teste, StartTime e EndTime.

![Um teste da Web com parâmetros.](./media/monitor-web-app-availability/appinsights-72webtest-parameters.png)

Quando você executa o teste, o ideal é que EndTime seja sempre a hora atual e StartTime seja o horário de 15 minutos atrás.

Plug-ins de teste da Web fornecem uma maneira de gerar tempos parametrizados.

1. Adicione um plug-in de teste na Web para cada valor de parâmetro variável desejado. Na barra de ferramentas de teste da Web, escolha **Adicionar Plug-in de Teste na Web**.

    ![Escolha Adicionar plug-ins de teste da Web e selecione um tipo.](./media/monitor-web-app-availability/appinsights-72webtest-plugins.png)

    Neste exemplo, usamos duas instâncias do plug-in de Data e Hora. É uma instância de "15 minutos atrás" e outra de "agora".
2. Abra as propriedades de cada plug-in. Atribua um nome e configure-o para usar a hora atual. Para um deles, defina Add Minutes = -15.

    ![Definir nome, Usar hora atual e Adicionar minutos.](./media/monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. Nos parâmetros de teste na Web, use {{nome do plug-in}} para fazer referência a um nome de plug-in.

    ![No parâmetro de teste, use {{nome do plug-in}}.](./media/monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Agora, carregue seu teste no portal. Ele usa os valores dinâmicos em todas as execuções do teste.


## <a name="monitor"></a>Ver os resultados do teste de disponibilidade

A guia Visão geral mostra a taxa de sucesso dos testes, enquanto a guia Detalhes mostra o gráfico de dispersão e a grade de detalhes específicos.

Após alguns minutos, clique em **Atualizar** para ver os resultados de teste.

![Dispersão na folha de detalhes](./media/monitor-web-app-availability/4refresh.png)

O gráfico de dispersão mostra exemplos dos resultados de teste que têm detalhes de etapas do teste de diagnóstico. O mecanismo de teste armazena detalhes de diagnóstico para testes com falhas. Para testes bem-sucedidos, detalhes de diagnóstico são armazenados para um subconjunto das execuções. Passe o mouse sobre qualquer um dos pontos verde/vermelho para ver o carimbo de data/hora, duração, localização e nome do teste. Clique em qualquer ponto no gráfico de dispersão para ver os detalhes do resultado do teste.  

Selecione um teste específico e uma localização, ou reduza o período de tempo para ver mais resultados em todo o período de tempo de interesse. Use o Search Explorer para ver os resultados de todas as execuções ou usar consultas de análise para executar relatórios personalizados sobre esses dados.

Além dos resultados brutos, existem duas métricas de disponibilidade no Metrics Explorer: 

1. Disponibilidade: percentual dos testes que foram bem-sucedidos, em todas as execuções de teste. 
2. Duração do teste: duração média em todas as execuções de teste.

Você pode aplicar filtros de localização e nome de teste para analisar tendências de um teste e/ou localização específica.

## <a name="edit"></a>Como inspecionar e editar testes

Na guia detalhes, em um teste específico, selecione as reticências à direita para editar, desabilitar temporariamente, excluir ou baixar o teste da web. Ele pode levar até 20 minutos para propagar as alterações de configuração.

Selecione **Exibir detalhes do teste** de um teste específico para ver seu gráfico de dispersão e detalhes de local de teste específico.

![Exibir detalhes do teste, editar e desabilitar um teste da Web](./media/monitor-web-app-availability/5viewdetails.png)

Você talvez queira desabilitar testes de disponibilidade ou as regras de alerta associadas a eles, enquanto você estiver fazendo a manutenção de seu serviço.

![Desabilitar um teste da web](./media/monitor-web-app-availability/6disable.png)
![Editar teste](./media/monitor-web-app-availability/8edittest.png)

## <a name="failures"></a>Se você encontrar falhas
Clique em um ponto vermelho.

![Clique em um ponto vermelho](./media/monitor-web-app-availability/open-instance-3.png)

De um resultado do teste de disponibilidade, você pode ver os detalhes de transações em todos os componentes. Aqui, você pode ver:

* Inspecionar a resposta recebida do servidor.
* Diagnosticar falha na telemetria do lado do servidor correlacionado coletada durante o processamento do teste de disponibilidade com falha.
* Registrar um problema ou um item de trabalho no Git ou no Azure Boards para controlar o problema. O bug conterá um link para este evento.
* Abrir o resultado do teste na Web no Visual Studio.

Saiba mais sobre a experiência de diagnóstico de transação de ponta a ponta [aqui](../../azure-monitor/app/transaction-diagnostics.md).

Clique na linha de exceção para ver os detalhes da exceção do lado servidor que causou a falha no teste de disponibilidade sintético. Você também pode obter o [instantâneo de depuração](../../azure-monitor/app/snapshot-debugger.md) para diagnóstico mais rico em nível de código.

![Diagnóstico do lado do servidor](./media/monitor-web-app-availability/open-instance-4.png)

## <a name="alerts"></a> Alertas de disponibilidade
Você pode ter os seguintes tipos de regras de alerta em dados de disponibilidade usando a experiência de alertas clássicos:
1. X fora do relatório de falhas em um período de tempo de locais de Y
2. Descartes de percentual agregado de disponibilidade em um limite
3. A duração média de teste aumenta além do limite

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Alertar sobre X fora de locais de Y relatando falhas
O X fora de locais de Y regra de alerta está habilitada por padrão na [experiência de novos alertas unificados](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), quando você cria um novo teste de disponibilidade. Você pode recusá-la selecionando a opção "clássica" ou optar por desabilitar a regra de alerta.

![Criar experiência](./media/monitor-web-app-availability/appinsights-71webtestUpload.png)

> [!NOTE]
>  com os [novos alertas unificados](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), as preferências de notificação e a gravidade de regra de alerta com [grupos de ação](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **deve ser** configurada no experiência de alertas. Sem as etapas a seguir, você só receberá notificações no portal.

1. Depois de salvar o teste de disponibilidade, na guia Detalhes, clique nas reticências do teste que acabou de criar. Clique em "Editar alerta".
![Editar depois de salvar](./media/monitor-web-app-availability/9editalert.png)

2. Defina o nível de severidade desejados, a descrição da regra e mais importante, o grupo de ação que tem as preferências de notificação que você deseja usar para essa regra de alerta.
![Editar depois de salvar](./media/monitor-web-app-availability/10editalert.png)


> [!NOTE]
> * Configure os grupos de ação para receber notificações quando o alerta for disparado, seguindo as etapas acima. Sem essa etapa, você só receberá notificações no portal quando a regra dispara.
>

### <a name="alert-on-availability-metrics"></a>Alertar sobre métricas de disponibilidade
Usando os [novos alertas unificadas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), você pode alertar sobre a disponibilidade de agregação segmentada e métricas de duração de teste:

1. Selecione um recurso do Application Insights na experiência de métricas e selecione uma métrica de disponibilidade:  ![Seleção de métricas de disponibilidade](./media/monitor-web-app-availability/selectmetric.png)

2. Configure alertas de opção do menu levara você para a nova experiência de onde você pode selecionar testes específicos ou locais para configurar a regra de alerta no. Você também pode configurar os grupos de ação para esta regra de alerta aqui.
    ![Configuração de alertas de disponibilidade](./media/monitor-web-app-availability/availabilitymetricalert.png)

### <a name="alert-on-custom-analytics-queries"></a>Alertar sobre consultas de análises personalizadas
Usando [novos alertas unificados](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), você pode alertar sobre [consultas de log personalizado](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Com consultas personalizadas, você pode alertar sobre qualquer critério arbitrário que ajuda você a obter o sinal de mais confiável dos problemas de disponibilidade. Isso também é aplicável principalmente, se você estiver enviando resultados personalizados de disponibilidade usando o SDK TrackAvailability. 

> [!Tip]
> * As métricas sobre dados de disponibilidade incluem resultados disponibilidade personalizado que você pode estar enviando chamando nosso SDK TrackAvailability. Você pode usar os alertas de suporte a métricas para alertar sobre resultados de disponibilidade personalizado.
>

## <a name="dealing-with-sign-in"></a>Lidando com a entrada
Se os usuários entrarem em seu aplicativo, você terá várias opções para simular entradas para poder testar as páginas por trás da entrada. A abordagem usada dependerá do tipo de segurança fornecida pelo aplicativo.

Em todos os casos, você deve criar uma conta no aplicativo apenas para fins de teste. Se possível, restrinja as permissões da conta de teste para que não haja possibilidade de que os testes na Web afetem usuários reais.

### <a name="simple-username-and-password"></a>Senha e nome de usuário simples
Grave um teste na Web da maneira usual. Exclua os cookies primeiro.

### <a name="saml-authentication"></a>Autenticação de SAML
Use o plug-in do SAML que está disponível para testes na Web.

### <a name="client-secret"></a>Segredo do cliente
Se seu aplicativo tiver uma rota de entrada que envolva um segredo do cliente, use-a. O AAD (Azure Active Directory) é um exemplo de um serviço que fornece uma entrada de segredo do cliente. No AAD, o segredo do cliente é a Chave do Aplicativo.

Aqui está um teste na Web de exemplo de um aplicativo Web que usa uma chave de aplicativo:

![Exemplo de segredo do cliente](./media/monitor-web-app-availability/110.png)

1. Obtenha o token do AAD usando o segredo do cliente (AppKey).
2. Extraia o token de portador da resposta.
3. Chame a API usando o token de portador no cabeçalho de autorização.

Verifique se o teste na Web é um cliente real, ou seja, se ele tem seu próprio aplicativo no AAD, e use seu clientId + appkey. O serviço que está sendo testado também tem seu próprio aplicativo no AAD: o URI appID desse aplicativo é refletido no teste na Web no campo "recurso".

### <a name="open-authentication"></a>Autenticação Aberta
Um exemplo de autenticação aberta é entrar com sua conta da Microsoft ou do Google. Muitos aplicativos que usam OAuth fornecem a alternativa de segredo do cliente. Portanto, sua primeira tática deve ser investigar essa possibilidade.

Se o teste tiver de entrar usando OAuth, a abordagem geral será:

* Use uma ferramenta como o Fiddler para examinar o tráfego entre o navegador da web, o site de autenticação e seu aplicativo.
* Executar duas ou mais entradas usando computadores ou navegadores diferentes ou em longos intervalos (para permitir que os tokens expirem).
* Ao comparar sessões diferentes, identifique o token passado de volta ao site de autenticação, que será então passado para o servidor de aplicativos após a entrada.
* Registre um teste na Web usando o Visual Studio.
* Parametrize os tokens, definindo o parâmetro quando o token for retornado do autenticador e usando-o na consulta ao site.
  (O Visual Studio tenta parametrizar o teste, mas não parametriza os tokens corretamente.)

## <a name="performance-tests"></a>Testes de desempenho
Você pode executar um teste de carga em seu site. Como o teste de disponibilidade, você pode enviar solicitações simples ou solicitações de várias etapas de nossos pontos em todo o mundo. Diferentemente de um teste de disponibilidade, muitas solicitações são enviadas, simulando vários usuários simultâneos.

Em **Configurar**, acesse **Teste de desempenho** e clique em novo para criar um teste.

![Criar novo Teste de desempenho](./media/monitor-web-app-availability/11new-performance-test.png)

Quando o teste for concluído, você verá os tempos de resposta e as taxas de êxito.

![Resultados do teste de desempenho](./media/monitor-web-app-availability/12performance-test.png)

> [!TIP]
> Para observar os efeitos de um teste de desempenho, use [Fluxo ao vivo](../../azure-monitor/app/live-stream.md) e [Criador de perfil](../../azure-monitor/app/profiler.md).
>

## <a name="automation"></a>Automação
* [Use os scripts do PowerShell para configurar um teste de disponibilidade](../../azure-monitor/app/powershell.md#add-an-availability-test) automaticamente.
* Configure um [webhook](../../azure-monitor/platform/alerts-webhooks.md) , que é chamado quando um alerta é gerado.

## <a name="qna"></a> PERGUNTAS FREQUENTES

* *Site parece certo, mas vejo falhas de teste? Por que Application Insights está me alerta?*

    * O teste apresenta "Analisar solicitações dependentes" habilitadas? Isso resulta em uma verificação estrita em recursos, como scripts, imagens, etc. Esses tipos de falhas podem não ser notados em um navegador. Verifique todas as imagens, scripts, folhas de estilos e outros arquivos carregados pela página. Se qualquer um deles falhar, o teste será relatado como falha, mesmo se a página html principal carregar com êxito. Para dessensibilizar o teste para essas falhas de recurso, simplesmente desmarque "Analisar Solicitações Dependentes" da configuração do teste. 

    * Para reduzir a probabilidade de ruído de blips de rede transitória etc., verifique se a configuração "Habilitar novas tentativas para falhas de teste" está marcada. Você também pode testar em mais locais e gerenciar adequadamente o limite de regra de alerta para evitar problemas específicos de local que estão causando alertas desnecessários.

    * Clique em qualquer um dos pontos vermelhos com a experiência de disponibilidade ou qualquer falha de disponibilidade do Gerenciador de pesquisa para ver os detalhes do motivo pelo relatamos a falha. O resultado do teste, juntamente com a telemetria do lado do servidor correlacionado (se habilitado) deve ajudar a entender por que o teste falhou. Causas comuns dos problemas transitórios são problemas de rede ou conexão. 

    * O tempo limite acabou? Podemos cancelar testes após 2 minutos. Se seu ping ou teste de várias etapas demorar mais do que dois minutos, relataremos como falha. Considere dividir o teste em várias partes que podem ser concluídas em durações menores.

    * Todos os locais relataram falha ou apenas alguns deles? Se apenas alguns relataram falhas, pode ser devido a problemas de rede de CDN. Novamente, clicando nos pontos vermelhos deve ajudar a entender por que o local relatou falhas.

* *Eu não recebi um email quando o alerta disparou ou foi resolvido ou ambos?*

    Verifique a configuração de alertas clássicos para confirmar que seu e-mail está listado diretamente, ou você está usando uma lista de distribuição está configurada para receber notificações. Se for, em seguida, verifique a configuração da lista de distribuição para confirmar que ela possa receber e-mails externos. Também verifique se o administrador do sistema pode ter quaisquer políticas configuradas que podem causar esse problema.

* *Não recebi a notificação de webhook?*

    Verifique se o aplicativo recebe a notificação de webhook está disponível e processa com êxito as solicitações de webhook. Consulte [isso](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) para obter mais informações.

* *Falha de teste intermitente com um erro de violação do protocolo?*

    O erro ("violação de protocolo... CR deve ser seguido por LF"), indica um problema com o servidor (ou dependências). Isso acontece quando cabeçalhos malformados são definidos na resposta. Pode ser causado por balanceadores de carga ou CDNs. Especificamente, talvez alguns cabeçalhos não estejam usando CRLF para indicar o fim da linha, o que viola a especificação de HTTP e, portanto, causa falha na validação do nível de WebRequest .NET. Inspecione a resposta para encontrar cabeçalhos que possam estar em violação.
    
    Observação: A URL pode não falhar em navegadores que têm uma validação reduzida dos cabeçalhos HTTP. Consulte esta postagem de blog para obter uma explicação detalhada do problema: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  
    
* *Não vejo qualquer telemetria do lado servidor relacionado a falhas no teste de diagnóstico?*
    
    Se você o Application Insights está configurado para seu aplicativo do lado do servidor, talvez seja porque a [amostragem](../../azure-monitor/app/sampling.md) está em operação. Selecione um resultado de disponibilidade diferente.

* *Posso chamar o código através do meu teste na Web?*

    Não. As etapas do teste devem estar no arquivo .webtest. E não é possível chamar outros testes da Web nem usar loops. Porém, há vários plug-ins que podem ser úteis.

* *Há suporte para HTTPS?*

    Damos suporte a TLS 1.1 e TLS 1.2. Atualmente, não verificamos os erros de certificado HTTPS.  

* *Há diferença entre "testes na Web" e "testes de disponibilidade"?*

    Os dois termos podem ser consultados de modo intercambiável. Testes de disponibilidade é um termo mais genérico que inclui os testes de ping de URL individuais, além dos testes na Web de várias etapas.
    
* *Eu gostaria de usar testes de disponibilidade em nosso servidor interno que é executado por trás de um firewall.*

    Há duas soluções possíveis:
    
    * Configure o firewall para permitir as solicitações de entrada dos [endereços IP de nossos agentes de teste da Web](../../azure-monitor/app/ip-addresses.md).
    * Escreva seu próprio código para testar periodicamente o servidor interno. Execute o código como um processo em segundo plano em um servidor de teste por trás do firewall. O processo de teste pode enviar seus resultados para o Application Insights usando a API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) no pacote SDK principal. Isso requer que o servidor de teste tenha acesso de saída para o ponto de extremidade de ingestão do Application Insights, mas é um risco de segurança muito menor do que a alternativa de permitir as solicitações de entrada. Os resultados não serão exibidos nas folhas de testes na Web de disponibilidade, mas aparecerão como resultados de disponibilidade no Gerenciador de Análise, Pesquisa e Métrica.

* *Falha de carregamento de um teste na Web de várias etapas*

    Alguns motivos para isso acontecer:
    * Há um limite de tamanho de 300 K.
    * Não há suporte para loops.
    * Não há suporte para referências a outros testes na Web.
    * Não há suporte para fontes de dados.

* *O teste de várias etapas não foi concluído*

    Há um limite de 100 solicitações por teste. Além disso, o teste será interrompido se for executado por mais de dois minutos.

* *Como executar um teste com certificados de cliente*

    Não há suporte para isso, infelizmente.

## <a name="who-receives-the-classic-alert-notifications"></a>Quem recebe as notificações de alerta (clássicas)?

Esta seção só se aplica aos alertas clássicos e ajudará você a otimizar suas notificações de alerta para fazer com que somente os destinatários desejados recebam notificações. Para saber mais sobre a diferença entre [alertas clássicos](../platform/alerts-classic.overview.md) e a nova experiência de alertas, confira o [artigo sobre visão geral de alertas](../platform/alerts-overview.md). Para controlar a notificação de alerta na nova experiência de alertas, use [grupos de ação](../platform/action-groups.md).

* Recomendamos o uso de destinatários específicos para notificações de alertas clássicos.

* Para alertas de falhas de X entre Y locais, a opção de caixa de seleção **em massa/em grupo**, se habilitada, envia para os usuários com funções de administrador/coadministrador.  Basicamente _todos_ os administradores da _assinatura_ receberão notificações.

* Para alertas sobre métrica do Application Insights (ou qualquer métrica do Application Insights), a opção de caixa de seleção **em massa/em grupo**, se habilitada, envia para os usuários com funções de leitor, colaborador ou proprietário na assinatura. Na verdade, _todos_ os usuários com acesso à assinatura do recurso do Application Insights fazem parte do escopo e receberão notificações. 

> [!NOTE]
> Se você estiver usando a opção de caixa de seleção **em massa/grupo** e desabilitá-la, não poderá reverter a alteração.

Use a nova experiência de alerta/alertas quase em tempo real caso precise notificar usuários com base em suas funções. Com os [grupos de ação](../platform/action-groups.md), você pode configurar notificações por email para os usuários que tenham qualquer uma das funções de proprietário/colaborador/leitor (não combinadas como uma única opção).



## <a name="next"></a>Próximas etapas
[Pesquisar logs de diagnóstico][diagnostic]

[Solução de problemas][qna]

[Endereços IP de agentes de teste Web](../../azure-monitor/app/ip-addresses.md)

<!--Link references-->

[azure-availability]: ../../insights-create-web-tests.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
