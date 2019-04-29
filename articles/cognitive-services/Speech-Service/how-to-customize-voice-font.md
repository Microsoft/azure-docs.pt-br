---
title: Como criar uma fonte de voz personalizada
titlesuffix: Azure Cognitive Services
description: Este artigo é uma visão geral da personalização de voz de Conversão de Texto em Fala que permite criar uma voz de marca exclusiva e reconhecível.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: panosper
ms.openlocfilehash: 24b98ce8cd2c587f0d39390954eb8a64747ca2ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653935"
---
# <a name="creating-custom-voice-fonts"></a>Criar fontes de voz personalizadas

A personalização de voz TTS (Conversão de Texto em Fala) permite que você crie uma voz exclusiva e reconhecível para sua marca: uma: *fonte de voz.*

Para criar a fonte de voz, você faz uma gravação em estúdio e carrega os scripts associados como os dados de treinamento. Em seguida, o serviço cria um modelo de voz exclusivo ajustado para a gravação. É possível usar essa fonte de voz para a sintetização de voz.

Você pode começar com uma pequena quantidade de dados para uma prova de conceito. Mas quanto mais dados você fornecer, mais natural e profissional será a voz.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma conta do Azure e uma assinatura do serviço de Fala. [Crie uma](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started), se ainda não tiver. Conecte sua assinatura ao portal de Voz Personalizada, conforme mostrado aqui.

1. Entre no [portal de Voz Personalizada](https://customvoice.ai) usando a mesma conta Microsoft utilizada para solicitar o acesso.

2. Sob o nome da conta, no canto superior direito, acesse **Assinaturas**.

    ![Assinaturas](media/custom-voice/subscriptions.png)

3. Na página Assinaturas, escolha **Conectar assinatura existente**. Os Serviços de Fala dão suporte a diferentes regiões. Verifique a região onde sua chave de assinatura foi criada e certifique-se de conectar sua chave ao subportal correto.  

4. Cole a chave de assinatura na tabela, conforme mostrado no exemplo a seguir. Cada assinatura tem duas chaves; você pode usar qualquer uma delas.

     ![Adicionar assinatura](media/custom-voice/add-subscription.png)

Está tudo pronto!

## <a name="prepare-recordings-and-transcripts"></a>Preparar gravações e transcrições

Um conjunto de dados de treinamento de voz consiste em um conjunto de arquivos de áudio, junto com um arquivo de texto que contém as transcrições dos arquivos de áudio.

Você pode preparar esses arquivos de duas maneiras. Escreva um script e leia-o para o ator de voz ou use áudio disponível publicamente e transcreva-o para texto. No último caso, edite os erros de fluência dos arquivos de áudio, como "um" e outros sons de preenchimento, gagueira, palavras murmuradas ou pronunciamentos incorretos.

Para produzir uma fonte de voz boa, faça as gravações em uma sala silenciosa com um microfone de alta qualidade. Volume consistente, velocidade de fala, densidade de fala e expressivos maneirismos da fala são essenciais para compilar uma excelente voz digital.

Para criar uma voz para uso de produção, é recomendável utilizar um estúdio de gravação profissional e um talento de voz. Para mais informações, consulte [Como gravar amostras de voz para uma voz personalizada](record-custom-voice-samples.md).

### <a name="audio-files"></a>Arquivos de áudio

Cada arquivo de áudio deve conter uma única expressão (por exemplo, uma única sentença ou um único turno de um sistema de diálogo). Todos os arquivos devem estar no mesmo idioma. (Não há suporte para vozes personalizadas multilíngues.) Os arquivos de áudio também devem ter um nome de arquivo numérico exclusivo com a extensão do nome do arquivo `.wav`.

Arquivos de áudio devem ser preparados conforme a seguir. Outros formatos não são têm suporte e serão rejeitados.

| **Propriedade** | **Valor** |
| ------------ | --------- |
| Formato de arquivo  | RIFF (.wav)|
| Taxa de amostragem| pelo menos 16.000 Hz |
| Formato de exemplo| PCM, 16 bits |
| Nome do arquivo    | Numérico, com extensão `.wav` |
| Formato de arquivo| .zip      |
| Tamanho máximo de arquivo|200 MB|

> [!NOTE]
> Arquivos .wav com uma taxa de amostragem menor que 16.000 Hertz serão rejeitados. Se um arquivo .zip contiver ondas com taxas de amostragem diferentes, apenas aquelas iguais ou superiores a 16.000 Hz serão importadas.
> Atualmente, o portal importa arquivos .zip de até 200 MB. No entanto, vários arquivos podem ser enviados. O número máximo de conjuntos de dados permitidos é de 10 arquivos .zip para usuários de assinatura gratuita e 50 para usuários de assinatura padrão.

### <a name="transcripts"></a>Transcrições

O arquivo de transcrição é um arquivo de texto sem formatação (ANSI, UTF-8, UTF-8-BOM, UTF-16-LE ou UTF-16-BE). Cada linha do arquivo de transcrição deve ter o nome de um arquivo de áudio, seguido por um caractere de tabulação (ponto de código 9) e, finalmente, a transcrição. Nenhuma linha em branco é permitida.

Por exemplo: 

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

O sistema de voz personalizada normaliza as transcrições convertendo o texto em minúscula e removendo a pontuação estranha. É importante que as transcrições sejam transcrições 100% exatas das gravações de áudio correspondentes.

> [!TIP]
> Ao compilar vozes de Conversão de Texto em Fala de produção, selecione enunciados (ou escreva scripts) que levem em conta a cobertura e eficiência fonética. Tendo problemas para a obtenção dos resultados você deseja? [Entre em contato com a equipe de voz personalizada](mailto:speechsupport@microsoft.com) para descobrir mais sobre ter nos consulte.

## <a name="upload-your-datasets"></a>Upload de conjunto de dados

Após preparar o arquivo de áudio e transcrições, carregue os arquivos por meio do [portal de serviço de Voz Personalizada](https://customvoice.ai).

> [!NOTE]
> Os conjuntos de dados não poderão ser editados após o upload. Caso tenha esquecido de incluir transcrições de alguns dos arquivos de áudio, por exemplo, ou se escolher acidentalmente o sexo errado, carregue todo o conjunto de dados novamente. Verifique o conjunto de dados e as configurações antes de iniciar o upload.

1. Entre no portal.

2. Na página principal, em **Voz personalizada**, selecione **Dados**.   

    A tabela **Minha voz** será exibida. Estará vazia se você ainda não carregou nenhum conjunto de dados de voz.

3. Para abrir a página e fazer upload de um novo conjunto de dados, selecione **Importar dados**.

    ![Importar dados de voz](media/custom-voice/import-voice-data.png)

4. Digite um nome e uma descrição nos campos fornecidos.

5. Selecione o local para as fontes de voz. Verifique se as informações de localidade correspondem ao idioma dos dados de gravação e dos scripts.

6. Selecione o sexo do falante cuja voz está usando.

7. Selecione o script e os arquivos de áudio para upload.

8. Selecione **Importar** para carregar os dados. Para conjuntos de dados maiores, a importação pode demorar vários minutos.

> [!NOTE]
> Usuários de assinatura gratuita podem fazer upload de dois conjuntos de dados de cada vez. Usuários de assinatura padrão podem fazer upload de cinco conjuntos de dados simultaneamente. Se o limite for alcançado, aguarde até que pelo menos um dos conjuntos de dados conclua a importação. Em seguida, tente novamente.

Quando o upload estiver concluído, a tabela **Meus dados de voz** será exibida novamente. Você deverá ver uma entrada que corresponda ao conjunto de dados cujo upload acabou de fazer.

Os conjuntos de dados serão validados automaticamente após o upload. A validação de dados inclui uma série de verificações nos arquivos de áudio para verificar o formato, o tamanho e a taxa de amostragem do arquivo. As verificações nos arquivos de transcrição verificam o formato do arquivo e fazem alguma normalização de texto. Os enunciados são transcritos usando o reconhecimento de fala. Em seguida, o texto resultante é comparado com a transcrição fornecida.

![Meus Dados de Voz](media/custom-voice/my-voice-data.png)

A tabela a seguir mostra os estados de processamento dos conjuntos de dados importados:

| Estado | Significado
| ----- | -------
| `NotStarted` | O conjunto de dados foi recebido e está na fila para processamento.
| `Running` | O conjunto de dados está sendo validado.
| `Succeeded` | O conjunto de dados foi validado e agora pode ser usado para criar uma fonte de voz.

Após a conclusão da validação, você poderá ver o número total de enunciados correspondentes para cada um dos conjuntos de dados na coluna **Enunciado**.

É possível baixar um relatório para verificar as pontuações de pronúncia e o nível de ruído de cada uma das gravações. A pontuação de pronúncia varia de 0 a 100. Uma pontuação abaixo de 70 normalmente indica um erro de fala ou uma incompatibilidade com o script. Um sotaque pesado pode reduzir a pontuação de pronúncia e afetar a voz digital gerada.

Uma SNR (relação de sinal de ruído) superior indica menor ruído no áudio. Normalmente, é possível alcançar uma SNR de 50+ com gravação em estúdios profissionais. O áudio com uma SNR abaixo de 20 pode resultar em ruído óbvio na voz gerada.

Considere regravar quaisquer enunciados com pontuações baixas de pronúncia ou relações de sinal de ruído ruins. Caso não possa regravar, existe a possibilidade de excluir esses enunciados do conjunto de dados.

## <a name="build-your-voice-font"></a>Compilar a fonte de voz

Depois da validação do conjunto de dados, será possível utilizá-lo para criar a fonte de voz personalizada.

1.  No menu suspenso **Voz personalizada**, escolha **Modelos**.

    A tabela **Minhas fontes de voz** é exibida, listando todas as fontes de voz personalizadas já criadas.

1. Sob o título da tabela, selecione **Criar vozes**.

    A página para criar uma fonte de voz é exibida. A localidade atual é mostrada na primeira linha da tabela. Altere a localidade para criar uma voz em outro idioma. A localidade deve ser igual à dos conjuntos de dados que estão sendo usados para criar a voz.

1. Assim como fez durante o upload do conjunto de dados, insira um nome e uma descrição para ajudá-lo a identificar esse modelo.

    Escolha um nome com cuidado. O nome que você inserir aqui será o nome utilizado para especificar a voz na solicitação de síntese de fala como parte da entrada SSML. Apenas letras, números e alguns caracteres de pontuação, como `-`, `_` e `(', ')`, são permitidos.

    Um uso comum do campo **Descrição** é registrar os nomes dos conjuntos de dados que foram usados para criar o modelo.

1. Escolha o sexo da fonte de voz. Deve corresponder ao sexo do conjunto de dados.

1. Selecione o(s) conjunto(s) de dados que você quer usar para treinar a fonte de voz. Todos os conjuntos de dados utilizados devem ser do mesmo falante.

1. Clique em **Criar** para começar a criar a fonte de voz.

    ![Criar modelo](media/custom-voice/create-model.png)

O novo modelo aparece na tabela **Minhas fontes de voz**.

![Minhas Fontes de Voz](media/custom-voice/my-voice-fonts.png)

O status exibido reflete o processo de conversão do conjunto de dados em uma fonte de voz, conforme mostrado aqui.

| Estado | Significado
| ----- | -------
| `NotStarted` | A solicitação de criação de fontes de voz está na fila para processamento.
| `Running` | A fonte de voz está sendo criada.
| `Succeeded` | A fonte de voz foi criada e pode ser implantada.

O tempo de treinamento varia dependendo do volume de dados de áudio processados. Intervalos de tempo típicos variam de aproximadamente 30 minutos para centenas de enunciados a 40 horas para 20.000 enunciados.

> [!NOTE]
> Usuários de assinatura gratuita podem treinar uma fonte de voz de cada vez. Usuários de assinatura padrão podem treinar três vozes simultaneamente. Se o limite for alcançado, aguarde até que pelo menos uma das fontes de voz termine o treinamento; em seguida, tente novamente.

## <a name="test-your-voice-font"></a>Testar a fonte de voz

Depois que a fonte de voz for compilada com êxito, você poderá testá-la antes de implantá-la para uso. Na coluna **Operações**, selecione **Teste**. A página de teste é exibida para a fonte de voz selecionada. Se você ainda não enviou solicitações de teste para a voz, a tabela estará vazia.

Para exibir um menu pop-up para enviar solicitações de texto, selecione o botão **Teste com texto** sob o título da tabela. É possível enviar a solicitação de teste em texto sem formatação ou SSML. O tamanho máximo de entrada é de 1.024 caracteres, incluindo todas as marcas para solicitação SSML. O idioma do texto deve ser o mesmo idioma da fonte de voz.

Após preencher a caixa de texto e confirmar o modo de entrada, selecione **Sim** para enviar a solicitação de teste e retornar à página de teste. A tabela agora inclui uma entrada que corresponde à nova solicitação e a coluna de status. Pode demorar alguns minutos para a sintetização de voz. Quando a coluna de status informar **Com êxito**, será possível baixar a entrada de texto (um arquivo `.txt`) e a saída de áudio (um arquivo `.wav`) e auditar o último para qualidade.

## <a name="create-and-use-a-custom-endpoint"></a>Criar e usar um ponto de extremidade personalizado

Depois de criar e testar com êxito o modelo de voz, implante-o em um ponto de extremidade personalizado de Conversão de Texto em Fala. Em seguida, você usará esse ponto de extremidade no lugar do ponto de extremidade comum ao fazer solicitações de Conversão de Texto em Fala por meio de API REST. O ponto de extremidade personalizado pode ser chamado apenas pela assinatura usada para implantar a fonte.

Para criar um ponto de extremidade personalizado, escolha **Pontos de extremidade** no menu **Voz personalizada**, na parte superior da página. A página **Minhas vozes implantadas** será exibida, com a tabela de pontos de extremidade de voz personalizada atual, se houver. A localidade atual é refletida na primeira linha da tabela. Para criar uma implantação para um idioma diferente, altere a localidade exibida. (Deve corresponder à voz que você está implantando.)

Para criar um ponto de extremidade, selecione o botão **Implantar vozes**. Insira o nome e a descrição do ponto de extremidade personalizado.

No menu **Assinatura**, escolha a assinatura que você quer usar. Usuários de assinatura gratuita podem ter apenas um modelo implantado por vez. Usuários de assinatura padrão podem criar até 20 pontos de extremidade, cada um com a própria voz personalizada.

![Criar ponto de extremidade](media/custom-voice/create-endpoint.png)

Após selecionar o modelo a ser implantado, selecione **Criar**. A página **Minhas vozes implantadas** será exibida novamente, agora com uma entrada para o novo ponto de extremidade. Pode demorar alguns minutos para instanciar um novo ponto de extremidade. Quando o status da implantação for **Com êxito**, o ponto de extremidade estará pronto para uso.

![Minhas vozes implantadas](media/custom-voice/my-deployed-voices.png)

Quando o status da implantação for **Com Êxito**, o ponto de extremidade da fonte de voz implantada aparecerá na tabela **Minhas vozes implantadas**. É possível usar esse URI diretamente em uma solicitação HTTP.

O teste online do ponto de extremidade também está disponível no portal de voz personalizada. Para testar o ponto de extremidade, escolha **Teste de pontos de extremidade** no menu suspenso **Voz personalizada**. A página de teste do ponto de extremidade é exibida. Escolha uma voz personalizada implantada e digite o texto a ser falado (em texto sem formatação ou em formato SSML) na caixa de texto.

> [!NOTE]
> Ao usar SSML, a marca `<voice>` deve especificar o nome que você forneceu à voz personalizada ao criá-la. Se você enviar texto sem formatação, a voz personalizada será sempre usada.

Para ouvir o texto falado na fonte de voz personalizada, selecione **Reproduzir**.

![Teste do ponto de extremidade](media/custom-voice/endpoint-testing.png)

O ponto de extremidade personalizado é funcionalmente idêntico ao ponto de extremidade padrão utilizado para solicitações de Conversão de Texto em Fala. Consulte [API REST](rest-apis.md) para obter mais informações.

## <a name="language-support"></a>Suporte ao idioma

A personalização de voz está disponível nestes idiomas:

| Linguagem | Local |
|----------|--------|
| Chinês (continente) | zh-CN |
| Inglês (EUA) | en-US |
| Francês | fr-FR |
| Alemão | de-DE |
| Italiano | it-IT |

> [!NOTE]
> O treinamento de voz em francês, alemão e italiano começa com um conjunto de dados com mais de 2.000 enunciados. Modelos bilíngues chinês-inglês também são suportados com um conjunto de dados de mais de 2.000 enunciados.

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Grave suas amostras de voz](record-custom-voice-samples.md)
