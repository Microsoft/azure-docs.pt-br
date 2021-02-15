---
title: Criar rastreamentos de desempenho do lado do cliente
description: Práticas recomendadas para a criação de perfil de desempenho do lado do cliente usando o WPF
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1d4ce68bdda5fbc3dfdb7396141289a58dab5bd1
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204088"
---
# <a name="create-client-side-performance-traces"></a>Criar rastreamentos de desempenho do lado do cliente

Há muitas razões pelas quais o desempenho da renderização remota do Azure pode não ser tão bom quanto o desejado. Além do desempenho de renderização puro no servidor de nuvem, especialmente a qualidade da conexão de rede tem uma influência significativa sobre a experiência. Para criar o perfil do desempenho do servidor, consulte o capítulo [consultas de desempenho do lado do servidor](../overview/features/performance-queries.md).

Este capítulo se concentra em como identificar possíveis afunilamentos do lado do cliente por meio do *:::no-loc text="performance traces":::* .

## <a name="getting-started"></a>Introdução

Se você for novo na funcionalidade do Windows :::no-loc text="performance tracing"::: , esta seção irá mencionar os termos e aplicativos mais fundamentais para você começar.

### <a name="installation"></a>Instalação

Os aplicativos usados para fazer o rastreamento com ARR são ferramentas de uso geral que podem ser usadas para todo o desenvolvimento do Windows. Eles são fornecidos por meio do [Kit de ferramentas de desempenho do Windows](/windows-hardware/test/wpt/). Para obter esse kit de ferramentas, baixe o [Kit de avaliação e implantação do Windows](/windows-hardware/get-started/adk-install).

### <a name="terminology"></a>Terminologia

Ao procurar informações sobre rastreamentos de desempenho, você será inevitavelmente em uma série de termos. Os mais importantes são:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

O **ETW** representa a [corrida de **T**de ventilação **E**saídas para **W**](/windows/win32/etw/about-event-tracing). É simplesmente o nome abrangente para o recurso de rastreamento eficiente no nível do kernel que é incorporado ao Windows. Ele é chamado de rastreamento de *eventos* , porque os aplicativos que dão suporte ao ETW emitirão eventos para registrar ações que podem ajudar a rastrear problemas de desempenho. Por padrão, o sistema operacional já emite eventos para coisas como acessos a disco, opções de tarefas e tal. Aplicativos como o ARR também emitem eventos personalizados, por exemplo, sobre quadros descartados, retardo de rede etc.

**ETL** significa **E**& vento **T**Race **L**ogging. Isso simplesmente significa que um rastreamento foi coletado (registrado) e, portanto, normalmente é usado como a extensão de arquivo para arquivos que armazenam os dados de rastreamento. Assim, quando você faz um rastreamento, normalmente terá um \* arquivo. etl posteriormente.

**WPR** significa [ **W**erenciador **R**ecorder **P**v](/windows-hardware/test/wpt/windows-performance-recorder) e é o nome do aplicativo que inicia e interrompe a gravação de rastreamentos de eventos. WPR usa um arquivo de perfil ( \* . wprp) que configura quais eventos exatos registrar em log. Esse `wprp` arquivo é fornecido com o SDK do arr. Ao fazer rastreamentos em um PC desktop, você pode iniciar o WPR diretamente. Ao fazer um rastreamento no HoloLens, você normalmente passa pela interface da Web.

O **WPA** significa [ **W**erenciador **a** **P**nalyzer](/windows-hardware/test/wpt/windows-performance-analyzer) e é o nome do aplicativo de GUI usado para abrir \* arquivos. ETL e examinar os dados para identificar problemas de desempenho. O WPA permite que você classifique dados por vários critérios, exiba os dados de várias maneiras, aprofunde-se em detalhes e correlacione informações.

Embora os rastreamentos de ETL possam ser criados em qualquer dispositivo Windows (PC local, HoloLens, servidor de nuvem, etc.), eles são normalmente salvos em disco e analisados com WPA em um PC desktop. Os arquivos ETL podem ser enviados a outros desenvolvedores para que eles tenham uma aparência. No entanto, lembre-se de que informações confidenciais, como caminhos de arquivo e endereços IP, podem ser capturadas em rastreamentos de ETL. Você pode usar o ETW de duas maneiras: para gravar rastreamentos ou para analisar rastreamentos. A gravação de rastreamentos é direta e requer configuração mínima. A análise de rastreamentos por outro lado requer uma compreensão razoável da ferramenta WPA e do problema que você está investigando. O material geral para o aprendizado de WPA será fornecido abaixo, bem como diretrizes sobre como interpretar rastreamentos específicos do ARR.

## <a name="recording-a-trace-on-a-local-pc"></a>Gravando um rastreamento em um PC local

Para identificar problemas de desempenho de ARR, você deve preferir fazer um rastreamento diretamente em um HoloLens, pois essa é a única maneira de obter um instantâneo das verdadeiras características de desempenho. No entanto, se você quiser especificamente fazer um rastreamento sem as restrições de desempenho do HoloLens ou se apenas quiser aprender a usar o WPA e não precisar de um rastreamento realista, aqui está como fazer isso.

### <a name="wpr-configuration"></a>Configuração do WPR

1. Inicie o [:::no-loc text="Windows Performance Recorder":::](/windows-hardware/test/wpt/windows-performance-recorder) no *menu iniciar*.
1. Expandir **mais opções**
1. Clique em **adicionar perfis...**
1. Selecione o arquivo *AzureRemoteRenderingNetworkProfiling. wprp*. Você pode encontrar esse arquivo no SDK do ARR em *Tools/ETLProfiles*.
   O perfil agora será listado em WPR em *medidas personalizadas*. Verifique se ele é o único perfil habilitado.
1. Expandir a *triagem de primeiro nível*:
    * Se tudo o que você deseja fazer é capturar um rastreamento rápido dos eventos de rede do ARR, **desabilite** essa opção.
    * Se você precisar correlacionar eventos de rede ARR com outras características do sistema, como uso de CPU ou memória, **habilite** essa opção.
    * Se você habilitar essa opção, o rastreamento provavelmente terá vários gigabytes de tamanho e levará muito tempo para salvar e abrir no WPA.

Depois, a configuração do WPR deve ter esta aparência:

![Configuração do WPR](./media/wpr.png)

### <a name="recording"></a>Gravação

Clique em **Iniciar** para iniciar a gravação de um rastreamento. Você pode iniciar e parar a gravação a qualquer momento; Você não precisa fechar seu aplicativo antes de fazer isso. Como você pode ver, não é necessário especificar qual aplicativo rastrear, uma vez que o ETW sempre registrará um rastreamento para todo o sistema. O `wprp` arquivo especifica quais tipos de eventos registrar.

Clique em **salvar** para interromper a gravação e especificar onde armazenar o arquivo ETL.

Agora você tem um arquivo ETL que pode ser aberto diretamente no WPA ou enviado para outra pessoa.

## <a name="recording-a-trace-on-a-hololens"></a>Gravando um rastreamento em um HoloLens

Para registrar um rastreamento em um HoloLens, inicialize seu dispositivo e insira seu endereço IP em um navegador para abrir o portal do *dispositivo*.

![Portal de Dispositivos](./media/wpr-hl.png)

1. À esquerda, navegue até *desempenho > rastreamento de desempenho*.
1. Selecionar **perfis personalizados**
1. Selecione **:::no-loc text="Browse...":::**
1. Selecione o arquivo *AzureRemoteRenderingNetworkProfiling. wprp*. Você pode encontrar esse arquivo no SDK do ARR em *Tools/ETLProfiles*.
1. Clique em **Iniciar rastreamento**
1. Agora, o HoloLens está gravando um rastreamento. Certifique-se de disparar os problemas de desempenho que você deseja investigar. Em seguida, clique em **parar rastreamento**.
1. O rastreamento será listado na parte inferior da página da Web. Clique no ícone de disco no lado direito para baixar o arquivo ETL.

Agora você tem um arquivo ETL que pode ser aberto diretamente no WPA ou enviado para outra pessoa.

## <a name="analyzing-traces-with-wpa"></a>Analisando rastreamentos com WPA

### <a name="wpa-basics"></a>Noções básicas de WPA

O analisador de desempenho do Windows é a ferramenta padrão para abrir arquivos ETL e inspecionar os rastreamentos. Uma explicação de como o WPA funciona está fora do escopo deste artigo. Para começar, observe estes recursos:

* Assista aos [vídeos introdutórios](/windows-hardware/test/wpt/windows-performance-analyzer) para obter uma primeira visão geral.
* O próprio WPA tem uma guia *introdução* , que explica as etapas comuns. Observe os tópicos disponíveis. Especialmente em "exibir dados", você obtém uma breve introdução sobre como criar grafos para dados específicos.
* No entanto, há informações excelentes [sobre esse site](https://randomascii.wordpress.com/2015/09/24/etw-central/), nem todas são relevantes para iniciantes.

### <a name="graphing-data"></a>Dados de gráfico

Para começar a usar o rastreamento do ARR, é bom saber as seguintes partes.

![Grafo de desempenho](./media/wpa-graph.png)

A imagem acima mostra uma tabela de dados de rastreamento e uma representação de gráfico dos mesmos dados.

Na tabela na parte inferior, observe a barra amarela (ouro) e a barra azul. Você pode arrastar essas barras e colocá-las em qualquer posição.

Todas as **colunas à esquerda da barra amarela** são interpretadas como **chaves**. As chaves são usadas para estruturar a árvore na janela superior esquerda. Aqui, temos duas colunas de *chave* , "nome do provedor" e "nome da tarefa". Consequentemente, a estrutura de árvore na janela superior esquerda tem dois níveis de profundidade. Se você reordenar as colunas ou adicionar ou remover colunas da área de chave, a estrutura no modo de exibição de árvore será alterada.

As **colunas à direita da barra azul** são usadas para a **exibição do grafo** na janela superior direita. Na maioria das vezes, apenas a primeira coluna é usada, mas alguns modos de grafo exigem várias colunas de dados. Para que os grafos de linha funcionem, o *modo de agregação* nessa coluna deve ser definido. Use ' AVG ' ou ' Max '. O modo de agregação é usado para determinar o valor do grafo em um determinado pixel, quando um pixel cobre um intervalo com vários eventos. Isso pode ser observado Configurando a agregação para ' Sum ' e, em seguida, ampliando e reduzindo o zoom.

As colunas no meio não têm nenhum significado especial.

![Modo de exibição de eventos](./media/wpa-event-view.png)

No *Editor de exibição de eventos genéricos* , você pode configurar todas as colunas a serem exibidas, o modo de agregação, classificação e quais colunas são usadas como chaves ou para o gráfico. No exemplo acima, o **campo 2** está habilitado e o campo 3-6 está desabilitado. O campo 2 normalmente é o primeiro campo de *dados personalizado* de um evento ETW e, portanto, para eventos de Arr "FrameStatistics", que representam algum valor de latência de rede. Habilite outras colunas de "campo" para ver outros valores desse evento.

### <a name="presets"></a>Predefinições

Para analisar corretamente um rastreamento, você precisará descobrir seu próprio fluxo de trabalho e a exibição de dados preferenciais. No entanto, para poder obter uma visão geral rápida sobre os eventos específicos do ARR, incluímos o perfil da plataforma de proteção de software do Windows e predefinem os arquivos na pasta *Tools/ETLProfiles*. Para carregar um perfil completo, selecione *perfis > aplicar...* na barra de menus do WPA ou abra o *painel meus predefinições* (*janela > minhas predefinições*) e selecione *importar*. O primeiro configurará uma configuração WPA completa como na imagem abaixo. O último fará apenas as predefinições para as várias configurações de exibição disponíveis e permitirá que você abra rapidamente uma exibição para examinar uma parte específica dos dados de evento ARR.

![Predefinições](./media/wpa-arr-trace.png)

A imagem acima mostra exibições de vários eventos específicos do ARR, além de uma exibição da utilização geral da CPU.

## <a name="next-steps"></a>Próximas etapas

* [Consultas de desempenho no lado do servidor](../overview/features/performance-queries.md)