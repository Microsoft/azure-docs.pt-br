---
title: Solucionar problemas de seus testes de disponibilidade do Azure Application Insights | Microsoft Docs
description: Solucionar problemas de testes da web no Azure Application Insights. Obtenha alertas se um site fica indisponível ou responde lentamente.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 87bc87d7d105d581f0143e87044fb0337c0fd7f6
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305131"
---
# <a name="troubleshooting"></a>solução de problemas

Este artigo ajudará você a solucionar problemas comuns que podem ocorrer ao usar o monitoramento de disponibilidade.

## <a name="ssltls-errors"></a>Erros SSL/TLS

|Sintoma/mensagem de erro| Possíveis causas|
|--------|------|
|Não foi possível criar o canal seguro de SSL/TLS  | Versão do SSL. Há suporte para apenas TLS 1.0, 1.1 e 1.2. **Não há suporte para o SSLv3.**
|Camada de registro TLSv1.2: Alerta (nível: Fatal, descrição: Bad Record MAC)| Consulte StackExchange thread para [saber](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake).
|URL que falha é uma CDN (Content Delivery Network) | Isso pode ser causado por uma configuração incorreta em sua CDN |  

### <a name="possible-workaround"></a>Solução alternativa possível

* Se as URLs que estão enfrentando o problema estão sempre a recursos dependentes, é recomendável desabilitar **analisar solicitações dependentes** para o teste da web.

## <a name="test-fails-only-from-certain-locations"></a>Teste falhar apenas de determinados locais

|Sintoma/mensagem de erro| Possíveis causas|
|----|---------|
|Uma tentativa de conexão falhou porque a parte conectada não respondeu corretamente após um período de tempo  | Agentes de teste em determinados locais estão sendo bloqueados por um firewall.|
|    |Redirecionamento de determinados endereços IP está ocorrendo via (balanceadores de carga, gerenciadores de tráfego geográfico, rota expressa do Azure). 
|    |Se usar o Azure ExpressRoute, há cenários em que os pacotes podem ser descartados em casos em que [roteamento assimétrico ocorre](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing).|

## <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Falha no teste de intermitente com um erro de violação de protocolo

|Sintoma/mensagem de erro| Possíveis causas|
|----|---------|
CR de violação de protocolo deve ser seguido por LF | Isso ocorre quando cabeçalhos malformados são detectados. Especificamente, alguns cabeçalhos talvez não estejam usando CRLF para indicar o final da linha, o que viola a especificação de HTTP e, portanto, falhará a validação no nível de WebRequest .NET.
 || Isso também pode ser causado por balanceadores de carga ou CDNs.

> [!NOTE]
> A URL pode não falhar em navegadores que têm uma validação reduzida dos cabeçalhos HTTP. Consulte esta postagem de blog para obter uma explicação detalhada do problema: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Perguntas frequentes sobre a solução de problemas

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Site parece okey, mas vejo falhas de teste? Por que Application Insights está me alertas?

   * O teste tem **analisar solicitações dependentes** habilitado? Isso resulta em uma verificação estrita em recursos, como scripts, imagens, etc. Esses tipos de falhas podem não ser notados em um navegador. Verifique todas as imagens, scripts, folhas de estilos e outros arquivos carregados pela página. Se qualquer um deles falhar, o teste é relatado como falha, mesmo se a página HTML principal carrega sem problemas. Para dessensibilizar o teste para essas falhas de recurso, simplesmente desmarque a opção de analisar solicitações dependentes da configuração do teste.

   * Para reduzir a probabilidade de ruído de blips de rede transitória etc., certifique-se habilitar tentativas para falhas de teste de configuração é verificada. Você também pode testar em mais locais e gerenciar adequadamente o limite de regra de alerta para evitar problemas específicos de local que estão causando alertas desnecessários.

   * Clique em qualquer um dos pontos vermelhos com a experiência de disponibilidade ou qualquer falha de disponibilidade do Gerenciador de pesquisa para ver os detalhes do motivo pelo relatamos a falha. O resultado do teste, juntamente com a telemetria do lado do servidor correlacionado (se habilitado) deve ajudar a entender por que o teste falhou. Causas comuns dos problemas transitórios são problemas de rede ou conexão.

   * O tempo limite acabou? Podemos cancelar testes após 2 minutos. Se seu ping ou teste de várias etapas demorar mais do que dois minutos, relataremos como falha. Considere dividir o teste em várias partes que podem ser concluídas em durações menores.

   * Todos os locais relataram falha ou apenas alguns deles? Se apenas alguns relataram falhas, pode ser devido a problemas de rede de CDN. Novamente, clicando nos pontos vermelhos deve ajudar a entender por que o local relatou falhas.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Eu não recebeu um email quando o alerta disparado ou resolvido ou ambos?

Verifique a configuração de alertas clássicos para confirmar que seu e-mail está listado diretamente, ou você está usando uma lista de distribuição está configurada para receber notificações. Se for, em seguida, verifique a configuração da lista de distribuição para confirmar que ela possa receber e-mails externos. Também verifique se o administrador do sistema pode ter quaisquer políticas configuradas que podem causar esse problema.

### <a name="i-did-not-receive-the-webhook-notification"></a>Não recebi a notificação de webhook?

Verifique se o aplicativo recebe a notificação de webhook está disponível e processa com êxito as solicitações de webhook. Consulte [isso](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) para obter mais informações.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Falha no teste de intermitente com um erro de violação de protocolo?

O erro ("violação de protocolo... CR deve ser seguido por LF"), indica um problema com o servidor (ou dependências). Isso acontece quando cabeçalhos malformados são definidos na resposta. Pode ser causado por balanceadores de carga ou CDNs. Especificamente, alguns cabeçalhos talvez não estejam usando CRLF para indicar o final da linha, o que viola a especificação de HTTP e, portanto, falha na validação no nível de WebRequest .NET. Inspecione a resposta para encontrar cabeçalhos, que podem estar em violação.

> [!NOTE]
> A URL pode não falhar em navegadores que têm uma validação reduzida dos cabeçalhos HTTP. Consulte esta postagem de blog para obter uma explicação detalhada do problema: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Não vejo todas relacionadas a telemetria do lado do servidor para diagnosticar falhas de teste? *

Se você o Application Insights está configurado para seu aplicativo do lado do servidor, talvez seja porque a [amostragem](../../azure-monitor/app/sampling.md) está em operação. Selecione um resultado de disponibilidade diferente.

### <a name="can-i-call-code-from-my-web-test"></a>Pode chamar o código do meu teste na web?

Não. As etapas do teste devem estar no arquivo .webtest. E não é possível chamar outros testes da Web nem usar loops. Porém, há vários plug-ins que podem ser úteis.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Há uma diferença entre "testes na web" e "testes de disponibilidade"?

Os dois termos podem ser consultados de modo intercambiável. Testes de disponibilidade é um termo mais genérico que inclui os testes de ping de URL individuais, além dos testes na Web de várias etapas.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Eu gostaria de usar testes de disponibilidade em nosso servidor interno que é executado atrás de um firewall.

   Há duas soluções possíveis:

   * Configure o firewall para permitir as solicitações de entrada dos [endereços IP de nossos agentes de teste da Web](../../azure-monitor/app/ip-addresses.md).
   * Escreva seu próprio código para testar periodicamente o servidor interno. Execute o código como um processo em segundo plano em um servidor de teste por trás do firewall. O processo de teste pode enviar seus resultados para o Application Insights usando a API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) no pacote SDK principal. Isso requer que o servidor de teste tenha acesso de saída para o ponto de extremidade de ingestão do Application Insights, mas é um risco de segurança muito menor do que a alternativa de permitir as solicitações de entrada. Os resultados não serão exibidos nas folhas de testes na Web de disponibilidade, mas aparecerão como resultados de disponibilidade no Gerenciador de Análise, Pesquisa e Métrica.

### <a name="uploading-a-multi-step-web-test-fails"></a>Carregamento de um teste da web de várias etapas falhar

Alguns motivos para isso acontecer:
   * Há um limite de tamanho de 300 K.
   * Não há suporte para loops.
   * Não há suporte para referências a outros testes na Web.
   * Não há suporte para fontes de dados.

### <a name="my-multi-step-test-doesnt-complete"></a>Não é meu teste de várias etapas concluída

Há um limite de 100 solicitações por teste. Além disso, o teste será interrompido se for executado por mais de dois minutos.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Como executar um teste com certificados de cliente?

Não há suporte para esse recurso no momento.

## <a name="who-receives-the-classic-alert-notifications"></a>Quem recebe as notificações de alerta (clássicas)?

Esta seção só se aplica aos alertas clássicos e ajudará você a otimizar suas notificações de alerta para fazer com que somente os destinatários desejados recebam notificações. Para saber mais sobre a diferença entre [alertas clássicos](../platform/alerts-classic.overview.md) e a nova experiência de alertas, confira o [artigo sobre visão geral de alertas](../platform/alerts-overview.md). Para controlar a notificação de alerta na nova experiência de alertas, use [grupos de ação](../platform/action-groups.md).

* Recomendamos o uso de destinatários específicos para notificações de alertas clássicos.

* Para alertas de falhas de X entre Y locais, a opção de caixa de seleção **em massa/em grupo**, se habilitada, envia para os usuários com funções de administrador/coadministrador.  Basicamente _todos_ os administradores da _assinatura_ receberão notificações.

* Para alertas de métricas de disponibilidade a **em massa/grupo** opção da caixa de seleção se habilitada, envia para os usuários com funções de leitor, colaborador ou proprietário na assinatura. Na verdade, _todos_ os usuários com acesso à assinatura do recurso do Application Insights fazem parte do escopo e receberão notificações. 

> [!NOTE]
> Se você estiver usando a opção de caixa de seleção **em massa/grupo** e desabilitá-la, não poderá reverter a alteração.

Use a nova experiência de alerta/alertas quase em tempo real caso precise notificar usuários com base em suas funções. Com os [grupos de ação](../platform/action-groups.md), você pode configurar notificações por email para os usuários que tenham qualquer uma das funções de proprietário/colaborador/leitor (não combinadas como uma única opção).

## <a name="next-steps"></a>Próximas etapas

* [Teste da web de várias etapa](availability-multistep.md)
* [Testes de ping de URL](monitor-web-app-availability.md)
