---
title: Exportação contínua de telemetria do Application Insights | Microsoft Docs
description: Exportar dados de uso e diagnóstico para armazenamento no Microsoft Azure e baixá-los de lá.
ms.topic: conceptual
ms.date: 02/19/2021
ms.custom: references_regions
ms.openlocfilehash: e7831123834df9186310453106c50261373160ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737028"
---
# <a name="export-telemetry-from-application-insights"></a>Exportar telemetria do Application Insights
Deseja manter a telemetria por mais tempo que o período de retenção padrão? Ou processá-la de alguma forma especializada? Exportação contínua é ideal para isso. Os eventos que você vê no portal do Application Insights podem ser exportados para armazenamento no Microsoft Azure no formato JSON. A partir daí, você pode baixar os dados e gravar qualquer código que precisar para processá-los.  

> [!IMPORTANT]
> A exportação contínua foi preterida. [Migre para um recurso de Application insights baseado em espaço de trabalho](convert-classic-resource.md) para usar [as configurações de diagnóstico](#diagnostic-settings-based-export) para exportar a telemetria.

> [!NOTE]
> A exportação contínua só é compatível com recursos clássicos do Application Insights. Os [recursos baseados em espaço de trabalho do Application Insights](./create-workspace-resource.md) devem usar as [configurações de diagnóstico](./create-workspace-resource.md#export-telemetry).
>

Antes de configurar a exportação contínua, há algumas alternativas que você talvez queira considerar:

* O botão Exportar na parte superior de uma métrica ou da guia de pesquisa permite transferir tabelas e gráficos para uma planilha do Excel.

* O [Analytics](../logs/log-query-overview.md) fornece uma linguagem de consulta eficiente para telemetria. Ele também pode exportar os resultados.
* Se desejar [explorar seus dados no Power BI](./export-power-bi.md), é possível fazer isso sem usar a Exportação Contínua.
* A [API REST de acesso a dados](https://dev.applicationinsights.io/) permite que você acesse a telemetria programaticamente.
* Você também pode acessar a configuração de [exportação contínua por meio do PowerShell](/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

Depois que a exportação contínua copia os dados para o armazenamento (onde eles podem permanecer pelo tempo desejado), eles ainda ficam disponíveis no Application Insights pelo [período de retenção](./data-retention-privacy.md) normal.

## <a name="supported-regions"></a>Regiões com Suporte

A exportação contínua tem suporte nas seguintes regiões:

* Sudeste Asiático
* Canadá Central
* Índia Central
* Norte da Europa
* Sul do Reino Unido
* Leste da Austrália
* Japan East
* Coreia Central
* França Central
* Leste da Ásia
* Oeste dos EUA
* Centro dos EUA
* Leste dos EUA 2
* Centro-Sul dos Estados Unidos
* Oeste dos EUA 2
* Norte da África do Sul
* Centro-Norte dos EUA
* Brazil South
* Norte da Suíça
* Australia Southeast
* Oeste do Reino Unido
* Centro-Oeste da Alemanha
* Oeste da Suíça
* Austrália Central 2
* EAU Central
* Sudeste do Brasil
* Austrália Central
* Norte dos EAU
* Leste da Noruega
* Oeste do Japão

> [!NOTE]
> Os aplicativos já configurados no **Europa Ocidental** e no **leste dos EUA** têm suporte, mas não há suporte para a integração de novos aplicativos nessas regiões.

## <a name="continuous-export-advanced-storage-configuration"></a>Configuração de armazenamento avançado de exportação contínua

A exportação contínua **não oferece suporte** aos seguintes recursos/configurações do armazenamento do Azure:

* O uso de [firewalls de VNET/Armazenamento do Azure](../../storage/common/storage-network-security.md) em conjunto com o armazenamento de blobs do Azure.

* [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

## <a name="create-a-continuous-export"></a><a name="setup"></a> Criar uma Exportação Contínua

> [!NOTE]
> Um aplicativo não pode exportar mais de 3 TB de dados por dia. Se mais de 3 TB por dia for exportado, a exportação será desabilitada. Para exportar sem um limite, use a [exportação baseada em configurações de diagnóstico](#diagnostic-settings-based-export).

1. No recurso do Application Insights do seu aplicativo, em Configurar à esquerda, abra Exportação Contínua e selecione **Adicionar**:

2. Escolha a telemetria de tipos de dados que você deseja exportar.

3. Crie ou selecione uma [Conta de armazenamento do Azure](../../storage/common/storage-introduction.md) onde você deseja armazenar os dados. Para obter mais informações sobre as opções de preços de armazenamento, visite a [página de preços oficial](https://azure.microsoft.com/pricing/details/storage/).

     Clique em Adicionar, Destino de exportação, Conta de armazenamento e, em seguida, crie um novo repositório ou escolha um repositório existente.

    > [!Warning]
    > Por padrão, o local de armazenamento será definido como a mesma região geográfica que seu recurso Application Insights. Armazenar em uma região diferente poderá incorrer em encargos de transferência.

4. Crie ou selecione um contêiner no armazenamento.

> [!NOTE]
> Depois de criar sua exportação, os dados recentemente ingeridos começarão a fluir para o armazenamento de BLOBs do Azure. A exportação contínua transmitirá apenas a nova telemetria que é criada/ingerida após a habilitação da exportação contínua. Nenhum dado existente antes de habilitar a exportação contínua será exportado e não haverá maneira com suporte para exportar retroativamente os dados criados antes do uso da exportação contínua.

Pode haver um atraso de aproximadamente uma hora antes de os dados aparecem no armazenamento.

Quando a primeira exportação for concluída, você encontrará uma estrutura semelhante à seguinte em seu contêiner de armazenamento de blobs do Azure: (Isso vai variar dependendo dos dados que você está coletando.)

|Nome | Descrição |
|:----|:------|
| [Disponibilidade](export-data-model.md#availability) | Relata os [testes de disponibilidade na Web](./monitor-web-app-availability.md).  |
| [Evento](export-data-model.md#events) | Eventos personalizados gerados por [TrackEvent()](./api-custom-events-metrics.md#trackevent). 
| [Exceções](export-data-model.md#exceptions) |[Exceções](./asp-net-exceptions.md) do relatório no servidor e no navegador.
| [Mensagens](export-data-model.md#trace-messages) | Enviado por [TrackTrace](./api-custom-events-metrics.md#tracktrace) e pelos [adaptadores de log](./asp-net-trace-logs.md).
| [Métricas](export-data-model.md#metrics) | Gerado por chamadas de API de métrica.
| [PerformanceCounters](export-data-model.md) | Contadores de desempenho coletados pelo Application Insights.
| [Solicitações](export-data-model.md#requests)| Enviado por [TrackRequest](./api-custom-events-metrics.md#trackrequest). Os módulos padrão usam isso para indicar o tempo de resposta do servidor, medido no servidor.| 

### <a name="to-edit-continuous-export"></a>Para editar a exportação contínua

Clique em exportação contínua e selecione a conta de armazenamento a ser editada.

### <a name="to-stop-continuous-export"></a>Para interromper a exportação contínua

Para interromper a exportação, clique em Desabilitar. Quando você clicar em Habilitar novamente, a exportação será reiniciada com novos dados. Você não obterá os dados recebidos no portal enquanto a exportação estava desabilitada.

Para interromper a exportação permanentemente, basta excluí-la. Isso não exclui seus dados do armazenamento.

### <a name="cant-add-or-change-an-export"></a>Não consegue adicionar nem alterar uma exportação?
* Para adicionar ou alterar exportações, você precisa de direitos de acesso de Proprietário, Colaborador ou Colaborador do Application Insights. [Saiba mais sobre as funções][roles].

## <a name="what-events-do-you-get"></a><a name="analyze"></a> Quais eventos você recebe?
Os dados exportados são a telemetria bruta que recebemos de seu aplicativo, exceto que adicionamos dados de localização, que calculamos por meio do endereço IP do cliente.

Dados que foram descartados por [amostragem](./sampling.md) não são incluídos nos dados exportados.

Outras métricas calculadas não são incluídas. Por exemplo, nós não exportamos a utilização média de CPU, mas exportamos a telemetria bruta por meio da qual a média é computada.

Os dados também incluem os resultados de todos os [testes da Web de disponibilidade](./monitor-web-app-availability.md) que você configurou.

> [!NOTE]
> **Amostragem.** Se seu aplicativo enviar muitos dados, a funcionalidade de amostragem poderá operar e enviar apenas uma parte da telemetria gerada. [Saiba mais sobre amostragem.](./sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a> Inspecionar os dados
Você pode inspecionar o armazenamento diretamente no portal. Clique em Início no menu à extrema esquerda, na parte superior, que diz "Serviços do Azure", selecione **Contas de armazenamento** e defina o nome da conta de armazenamento; na página Visão geral, selecione **Blobs** em Serviços e, por fim, selecione o nome do contêiner.

Para inspecionar o armazenamento do Azure no Visual Studio, abra **Exibir** e **Cloud Explorer**. (Se você não tiver esse comando de menu, será necessário instalar o SDK do Azure: Abra a caixa de diálogo **Novo Projeto**, expanda Visual C#/Cloud e escolha **Obter Microsoft Azure SDK para .NET**.)

Quando você abrir o armazenamento de blob, verá um contêiner com um conjunto de arquivos de blob. O URI de cada arquivo deriva o nome do recurso Application Insights, da chave de instrumentação e do tipo/data/hora de telemetria. (O nome do recurso está todo em letras minúsculas e a chave de instrumentação omite traços.)

![Inspecione o repositório de blob com uma ferramenta adequada](./media/export-telemetry/04-data.png)

A data e hora são em formato UTC, e referentes a quando a telemetria foi depositada no repositório - não à hora em que essa telemetria foi gerada. Então, se você escrever código para baixar os dados, ele pode percorrer os dados linearmente.

Veja o formato do caminho:

```console
$"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"
```

Where

* `blobCreationTimeUtc` é a hora em que o blob foi criado no armazenamento de preparo interno
* `blobDeliveryTimeUtc` é a hora em que o blob foi copiado para o armazenamento de destino de exportação

## <a name="data-format"></a><a name="format"></a> Formato dos dados
* Cada blob é um arquivo de texto que contém várias linhas separadas por “ \n”. Ele contém a telemetria processada durante um período de tempo de aproximadamente metade um minuto.
* Cada linha representa um ponto de dados de telemetria como uma solicitação ou uma exibição de página.
* Cada linha é um documento JSON não formatado. Se você quiser exibir as linhas, abra o blob no Visual Studio e escolha **Editar**  >    >  **arquivo de formato** avançado:

   ![Veja a telemetria com uma ferramenta adequada](./media/export-telemetry/06-json.png)

As durações de tempo são em tiques, onde 10.000 tiques = 1 ms. Por exemplo, esses valores mostram um tempo de 1 ms para enviar uma solicitação do navegador, 3 ms recebê-la e 1,8 s para processar a página no navegador:

```json
"sendRequest": {"value": 10000.0},
"receiveRequest": {"value": 30000.0},
"clientProcess": {"value": 17970000.0}
```

[Referência de modelo de dados detalhados para os tipos de propriedades e valores.](export-data-model.md)

## <a name="processing-the-data"></a>Processamento dos dados
Em pequena escala, você pode escrever um código para extrair e separar seus dados, lê-los em uma planilha e assim por diante. Por exemplo:

```csharp
private IEnumerable<T> DeserializeMany<T>(string folderName)
{
   var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
   foreach (var file in files)
   {
      using (var fileReader = File.OpenText(file))
      {
         string fileContent = fileReader.ReadToEnd();
         IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
         foreach (var entity in entities)
         {
            yield return JsonConvert.DeserializeObject<T>(entity);
         }
      }
   }
}
```

Para obter um exemplo de código maior, consulte [Usar uma função de trabalho][exportasa].

## <a name="delete-your-old-data"></a><a name="delete"></a>Excluir dados antigos
Você é responsável por gerenciar a capacidade de armazenamento e excluir dados antigos, se necessário.

## <a name="if-you-regenerate-your-storage-key"></a>Se você regenerar sua chave de armazenamento...
Se você alterar a chave para seu armazenamento, a exportação contínua deixará de funcionar. Você verá uma notificação em sua conta do Azure.

Abra a guia Exportação Contínua e edite sua exportação. Edite o destino de exportação, mas mantenha o mesmo armazenamento selecionado. Clique em OK para confirmar.

A exportação contínua será reiniciada.

## <a name="export-samples"></a>Exemplos de exportação

* [Exportar para o SQL usando o Stream Analytics][exportasa]
* [Exemplo do Stream Analytics 2](export-stream-analytics.md)

Em escalas maiores, considere usar o [HDInsight](https://azure.microsoft.com/services/hdinsight/) - clusters de Hadoop na nuvem. O HDInsight fornece várias tecnologias para gerenciar e analisar Big Data, e você pode usá-lo para processar dados que foram exportados do Application Insights.

## <a name="q--a"></a>Perguntas e respostas
* *Mas tudo o que eu quero é um download único de um gráfico.*  

    Sim, você pode fazer isso. Na parte superior da guia, clique em **Exportar Dados**.
* *Eu configuro uma exportação, mas não há nenhum dado no meu repositório.*

    O Application Insights recebeu qualquer telemetria do seu aplicativo desde que você configurou a exportação? Você receberá apenas novos dados.
* *Eu tentei configurar uma exportação, mas o acesso foi negado*

    Se a conta pertence à sua organização, você precisa ser membro do grupo de proprietários ou do grupo de colaboradores.
* *Eu posso exportar diretamente para meu próprio repositório local?*

    Não, infelizmente. Nosso mecanismo de exportação funciona apenas com o armazenamento do Azure no momento.  
* *Há qualquer limite para a quantidade de dados que você coloca em meu repositório?*

    Não. Continuaremos a enviar dados por push até que você exclua a exportação. Interromperemos o envio se atingirmos os limites externos para o armazenamento de blob, mas são limites enormes. Cabe a você controlar a quantidade de armazenamento que usa.  
* *Quantos blobs devo ver no armazenamento?*

  * Para cada tipo de dados selecionado para exportação, um novo blob é criado a cada minuto (se os dados estiverem disponíveis).
  * Além disso, para aplicativos com tráfego intenso, são alocadas unidades de partição adicionais. Nesse caso, cada unidade cria um blob a cada minuto.
* *Eu regenerei a chave para o meu armazenamento ou alterei o nome do contêiner, e agora a exportação não funciona.*

    Edite a exportação e abra a guia Destino de exportação. Deixe o mesmo armazenamento de antes selecionado e clique em OK para confirmar. A exportação será reiniciada. Se a alteração foi realizada nos últimos dias, você não perderá dados.
* *Posso pausar a exportação?*

    Sim. Clique em Desabilitar.

## <a name="code-samples"></a>Exemplos de código

* [Exemplo do Stream Analytics](export-stream-analytics.md)
* [Exportar para o SQL usando o Stream Analytics][exportasa]
* [Referência de modelo de dados detalhados para os tipos de propriedades e valores.](export-data-model.md)

## <a name="diagnostic-settings-based-export"></a>Exportação baseada em configurações de diagnóstico

A exportação baseada em configurações de diagnóstico usa um esquema diferente do que a exportação contínua. Ele também dá suporte a recursos que a exportação contínua não gosta de:

* Contas de armazenamento do Azure com vnet, firewalls e links privados.
* Exportar para o Hub de eventos.

Para migrar para a exportação baseada em configurações de diagnóstico:

1. Desabilite a exportação contínua atual.
2. [Migre o aplicativo para o baseado em espaço de trabalho](convert-classic-resource.md).
3. [Habilite a exportação de configurações de diagnóstico](create-workspace-resource.md#export-telemetry). Selecione **configurações de diagnóstico > adicionar configuração de diagnóstico** de dentro de seu recurso de Application insights.

<!--Link references-->

[exportasa]: ./code-sample-export-sql-stream-analytics.md
[roles]: ./resources-roles-access-control.md

