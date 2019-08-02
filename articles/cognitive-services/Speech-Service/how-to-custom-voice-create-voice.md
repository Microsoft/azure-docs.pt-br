---
title: Criar um serviço de fala de voz personalizado
titleSuffix: Azure Cognitive Services
description: Quando estiver pronto para carregar seus dados, vá para o portal de voz personalizado. Crie ou selecione um projeto de voz personalizado. O projeto deve compartilhar o idioma/a localidade correto e as propriedades do gênero como os dados que você pretende usar para o treinamento de voz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 0fdc58ba54c63ba7dd6b74f56aa91e9c2b3c0936
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562826"
---
# <a name="create-a-custom-voice"></a>Criar uma voz personalizada

Em [preparar dados para voz personalizada](how-to-custom-voice-prepare-data.md), descrevemos os diferentes tipos de dados que você pode usar para treinar uma voz personalizada e os requisitos de formato diferentes. Depois de preparar seus dados, você pode começar a carregá-los no [portal de voz personalizado](https://aka.ms/custom-voice-portal)ou por meio da API de treinamento de voz personalizada. Aqui, descrevemos as etapas de treinamento de uma voz personalizada por meio do Portal.

> [!NOTE]
> Esta página pressupõe que você leu [introdução à voz personalizada](how-to-custom-voice.md) e [preparar dados para voz personalizada](how-to-custom-voice-prepare-data.md)e criou um projeto de voz personalizado.

Verifique os idiomas com suporte para personalizar voz: [idioma para personalização](language-support.md#customization).

## <a name="upload-your-datasets"></a>Upload de conjunto de dados

Quando estiver pronto para carregar seus dados, vá para o [portal de voz personalizado](https://aka.ms/custom-voice-portal). Crie ou selecione um projeto de voz personalizado. O projeto deve compartilhar o idioma/a localidade correto e as propriedades do gênero como os dados que você pretende usar para o treinamento de voz. Por exemplo, selecione `en-GB` se as gravações de áudio que você tem são feitas em inglês com um acento do Reino Unido.

Vá para a guia **dados** e clique em **carregar dados**. No assistente, selecione o tipo de dados correto que corresponde ao que você preparou.

Cada conjunto de dados que você carrega deve atender aos requisitos para o tipo de dado que você escolher. É importante formatar corretamente os dados antes que eles sejam carregados. Isso garante que os dados serão processados com precisão pelo serviço de voz personalizado. Acesse [preparar dados para voz personalizada](how-to-custom-voice-prepare-data.md) e verifique se os dados foram formatados de forma correta.

> [!NOTE]
> Os usuários da assinatura gratuita (F0) podem carregar dois conjuntos de os simultaneamente. Assinatura padrão (S0) os usuários podem carregar cinco conjuntos de os simultaneamente. Se o limite for alcançado, aguarde até que pelo menos um dos conjuntos de dados conclua a importação. Em seguida, tente novamente.

> [!NOTE]
> O número máximo de conjuntos de usuários que podem ser importados por assinatura é de 10 arquivos. zip para usuários de assinatura gratuita (F0) e 500 para usuários de assinatura padrão (S0).

Os conjuntos de valores são validados automaticamente quando você pressiona o botão carregar. A validação de dados inclui uma série de verificações nos arquivos de áudio para verificar o formato, o tamanho e a taxa de amostragem do arquivo. Corrija os erros se houver e envie novamente. Quando a solicitação de importação de dados for iniciada com êxito, você deverá ver uma entrada na tabela de dados que corresponde ao DataSet que você acabou de carregar.

A tabela a seguir mostra os estados de processamento dos conjuntos de dados importados:

| Estado | Significado |
| ----- | ------- |
| Processando | Seu conjunto de seus foi recebido e está sendo processado. |
| Bem-sucedido | Seu conjunto de um foi validado e agora pode ser usado para criar um modelo de voz. |
| Falhou | O conjunto de dados falhou durante o processamento devido a vários motivos, por exemplo, erros de arquivo, problemas de dados ou problemas de rede. |

Após a conclusão da validação, você poderá ver o número total de declarações correspondentes para cada um de seus conjuntos de os na coluna **declarações** . Se o tipo de dados selecionado exigir segmentação de áudio de longo prazo, essa coluna refletirá apenas o declarações que segmentamos para você com base em suas transcrições ou por meio do serviço de transcrição de fala. Você pode baixar ainda mais o conjunto de informações validado para exibir os resultados detalhados do declarações importado com êxito e suas transcrições de mapeamento. Dica: a segmentação de áudio longo pode levar mais de uma hora para concluir o processamento de dados.

Para conjuntos de resultados en-US e zh-CN, você pode baixar um relatório para verificar as pontuações de pronúncia e o nível de ruído para cada uma de suas gravações. A pontuação de pronúncia varia de 0 a 100. Uma pontuação abaixo de 70 normalmente indica um erro de fala ou uma incompatibilidade com o script. Um sotaque pesado pode reduzir a pontuação de pronúncia e afetar a voz digital gerada.

Uma SNR (relação de sinal de ruído) superior indica menor ruído no áudio. Normalmente, é possível alcançar uma SNR de 50+ com gravação em estúdios profissionais. O áudio com uma SNR abaixo de 20 pode resultar em ruído óbvio na voz gerada.

Considere regravar quaisquer enunciados com pontuações baixas de pronúncia ou relações de sinal de ruído ruins. Caso não possa regravar, existe a possibilidade de excluir esses enunciados do conjunto de dados.

## <a name="build-your-custom-voice-model"></a>Crie seu modelo de voz personalizado

Depois que o conjunto de seus conjuntos de um for validado, você poderá usá-lo para criar seu modelo de voz personalizado.

1.  Navegue até a **conversão de texto em fala > treinamento de > de voz personalizado**.

2.  Clique em **treinar modelo**.

3.  Em seguida, insira um **nome** e uma **Descrição** para ajudá-lo a identificar esse modelo.

    Escolha um nome com cuidado. O nome que você inserir aqui será o nome utilizado para especificar a voz na solicitação de síntese de fala como parte da entrada SSML. Somente letras, números e alguns caracteres de pontuação, como-, \_e (', ') são permitidos. Use nomes diferentes para modelos de voz diferentes.

    Um uso comum do campo **Descrição** é registrar os nomes dos conjuntos de dados que foram usados para criar o modelo.

4.  Na página **selecionar dados de treinamento** , escolha um ou vários conjuntos de dados que você gostaria de usar para treinamento. Verifique o número de declarações antes de enviá-los. Você pode começar com qualquer número de declarações para modelos de voz en-US e zh-CN. Para outras localidades, você deve selecionar mais de 2.000 declarações para poder treinar uma voz.

    > [!NOTE]
    > Nomes de áudio duplicados serão removidos do treinamento. Verifique se os conjuntos de valores selecionados não contêm os mesmos nomes de áudio em vários arquivos. zip.

    > [!TIP]
    > O uso dos conjuntos de valores do mesmo palestrante é necessário para os resultados de qualidade. Quando os conjuntos de valores que você enviou para treinamento contêm um número total de menos de 6.000 declarações distintos, você treinará seu modelo de voz por meio da técnica de síntese paramétricas estatística. No caso em que os dados de treinamento excedem um número total de 6.000 declarações distintos, você iniciará um processo de treinamento com a técnica de síntese de concatenação. Normalmente, a tecnologia de concatenação pode resultar em resultados de voz mais naturais e de maior fidelidade. [Entre em contato com a equipe de voz personalizada](mailto:speechsupport@microsoft.com) se desejar treinar um modelo com a tecnologia de TTS do neural mais recente que pode produzir uma voz digital equivalente às [vozes neurais](language-support.md#neural-voices)disponíveis publicamente.

5.  Clique em **treinar** para começar a criar seu modelo de voz.

A tabela de treinamento exibe uma nova entrada que corresponde a esse modelo recém-criado. A tabela também exibe o status: Processamento, com êxito, com falha.

O status mostrado reflete o processo de conversão do conjunto de seus conjuntos de um modelo de voz, como mostrado aqui.

| Estado | Significado |
| ----- | ------- |
| Processando | Seu modelo de voz está sendo criado. |
| Bem-sucedido | Seu modelo de voz foi criado e pode ser implantado. |
| Falhou | O seu modelo de voz falhou no treinamento devido a vários motivos, por exemplo, problemas de dados ou problemas de rede não vistos. |

O tempo de treinamento varia dependendo do volume de dados de áudio processados. Intervalos de tempo típicos variam de aproximadamente 30 minutos para centenas de enunciados a 40 horas para 20.000 enunciados. Quando o treinamento do modelo for bem-sucedido, você poderá começar a testá-lo.

> [!NOTE]
> Os usuários da assinatura gratuita (F0) podem treinar uma fonte de voz simultaneamente. Assinatura padrão (S0) os usuários podem treinar três vozes simultaneamente. Se o limite for alcançado, aguarde até que pelo menos uma das fontes de voz termine o treinamento; em seguida, tente novamente.

> [!NOTE]
> O número máximo de modelos de voz com permissão para ser treinado por assinatura é de 10 modelos para usuários de assinatura gratuita (F0) e 100 para usuários de assinatura padrão (S0).

## <a name="test-your-voice-model"></a>Testar seu modelo de voz

Depois que a fonte de voz for compilada com êxito, você poderá testá-la antes de implantá-la para uso.

1.  Navegue até a **conversão de texto em fala > teste de > de voz personalizado**.

2.  Clique em **Adicionar teste**.

3.  Selecione um ou vários modelos que você gostaria de testar.

4.  Forneça o texto que você deseja que as voz (s) falem. Se você tiver selecionado para testar vários modelos ao mesmo tempo, o mesmo texto será usado para os testes de modelos diferentes.

    > [!NOTE]
    > O idioma do texto deve ser o mesmo idioma da fonte de voz. Somente modelos treinados com êxito podem ser testados. Somente texto sem formatação tem suporte nesta etapa.

5.  Clique em **Criar**.

Depois de enviar sua solicitação de teste, você retornará para a página de teste. A tabela agora inclui uma entrada que corresponde à nova solicitação e a coluna de status. Pode demorar alguns minutos para a sintetização de voz. Quando a coluna status for **bem-sucedida**, você poderá reproduzir o áudio ou baixar a entrada de texto (um arquivo. txt) e a saída de áudio (um arquivo. wav) e ainda mais Audition o último para obter qualidade.

Você também pode encontrar os resultados do teste na página de detalhes de cada modelo selecionado para teste. Vá para a guia **treinamento** e clique no nome do modelo para inserir a página de detalhes do modelo.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Criar e usar um ponto de extremidade de voz personalizado

Depois de criar e testar com êxito o modelo de voz, implante-o em um ponto de extremidade personalizado de Conversão de Texto em Fala. Em seguida, você usará esse ponto de extremidade no lugar do ponto de extremidade comum ao fazer solicitações de Conversão de Texto em Fala por meio de API REST. Seu ponto de extremidade personalizado pode ser chamado apenas pela assinatura que você usou para implantar a fonte.

Para criar um novo ponto de extremidade de voz personalizado, acesse **conversão de texto em fala > voz personalizada > implantação**. Selecione **Adicionar ponto de extremidade** e insira um **nome** e uma **Descrição** para o ponto de extremidade personalizado. Em seguida, selecione o modelo de voz personalizado que você deseja associar a esse ponto de extremidade.

Depois de clicar no botão **Adicionar** , na tabela ponto de extremidade, você verá uma entrada para o novo ponto de extremidade. Pode demorar alguns minutos para instanciar um novo ponto de extremidade. Quando o status da implantação for **Com êxito**, o ponto de extremidade estará pronto para uso.

> [!NOTE]
> Os usuários da assinatura gratuita (F0) podem ter apenas um modelo implantado. Assinatura padrão (S0) os usuários podem criar até 50 pontos de extremidade, cada um com sua própria voz personalizada.

> [!NOTE]
> Para usar sua voz personalizada, você deve especificar o nome do modelo de voz, usar o URI personalizado diretamente em uma solicitação HTTP e usar a mesma assinatura para passar pela autenticação do serviço TTS.

Depois que o ponto de extremidade for implantado, o nome do ponto de extremidade aparecerá como um link. Clique no link para exibir informações específicas para seu ponto de extremidade, como a chave do ponto de extremidade, a URL do ponto de extremidade e o código de exemplo.

O teste online do ponto de extremidade também está disponível no portal de voz personalizada. Para testar seu ponto de extremidade, escolha **verificar ponto de extremidade** na página de **detalhes do ponto de extremidade** . A página de teste do ponto de extremidade é exibida. Insira o texto a ser falado (no formato de texto sem formatação ou [SSML](speech-synthesis-markup.md) na caixa de texto. Para ouvir o texto falado na fonte de voz personalizada, selecione **Reproduzir**. Este recurso de teste será cobrado em relação ao uso personalizado de síntese de fala.

O ponto de extremidade personalizado é funcionalmente idêntico ao ponto de extremidade padrão utilizado para solicitações de Conversão de Texto em Fala. Consulte [API REST](rest-text-to-speech.md) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

* [Orienta Registre seus exemplos de voz](record-custom-voice-samples.md)
* [Referência de texto para Speech API](rest-text-to-speech.md)
