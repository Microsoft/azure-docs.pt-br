---
title: Migrar workspace e projetos do Hub do Microsoft Translator? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo explica como migrar seu espaço de trabalho de Hub e projetos para o tradutor personalizado de serviços cognitivas do Azure.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 89658ce88b4f1ac9d5bacac7bd45511b4aa0a1be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895701"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrar workspace e projetos do Hub para o Tradutor Personalizado

É possível migrar o workspace e os projetos do [Hub do Microsoft Translator](https://hub.microsofttranslator.com/) facilmente para o Tradutor Personalizado. A migração é iniciada no Hub da Microsoft selecionando um workspace ou um projeto, selecionando um workspace no Tradutor Personalizado e, em seguida, selecionando os treinamentos que você deseja transferir. Depois que a migração é iniciada, as configurações de treinamento selecionadas serão transferidas com todos os documentos relevantes. Modelos implantados são treinados e podem ser implantados automaticamente após a conclusão.

Estas ações são executadas durante a migração:
* Todos os documentos e definições de projeto terão seus nomes transferidos com a adição de "hub_" prefixado para o nome. Os dados de teste e ajuste gerados automaticamente serão denominados hub_systemtune_\<modelid> ou hub_systemtest_\<modelid>.
* Os treinamentos que estavam no estado implantado quando a migração ocorrer serão treinados automaticamente usando os documentos do treinamento do Hub. Esse treinamento não será cobrado na sua assinatura. Se a implantação automática foi selecionada para a migração, o modelo treinado será implantado após a conclusão. Custos de hospedagem normais serão aplicados.
* Os treinamentos migrados que não estavam no estado implantado serão colocados no estado de rascunho migrado. Nesse estado, você terá a opção de treinar de um modelo com a definição de migrada, mas serão aplicados os encargos de treinamento regulares.
* A qualquer momento, a pontuação de BLEU migrada do treinamento de Hub pode ser encontrada na página TrainingDetails do modelo no cabeçalho "Pontuação Bleu no Hub MT".

> [!Note] 
> Para que um treinamento tenha sucesso, o tradutor personalizado requer um mínimo de 10.000 frases extraídas exclusivas. O tradutor personalizado não pode conduzir um treinamento com menos do que o [mínimo sugerido](./sentence-alignment.md#suggested-minimum-number-of-sentences).

## <a name="find-custom-translator-workspace-id"></a>Encontrar a ID do workspace do Tradutor Personalizado

Para migrar o workspace do [Hub do Microsoft Translator](https://hub.microsofttranslator.com/), será necessária uma ID do workspace de destino no Tradutor Personalizado. O workspace de destino no Tradutor Personalizado é onde todos os workspaces e projetos do Hub deverão ser migrados.

Você encontrará a ID do workspace de destino na página Configurações do Tradutor Personalizado:

1. Acesse a página "Configuração" no portal do Tradutor Personalizado.

2. Você encontrará a ID do workspace na seção Informações Básicas.

    ![Como localizar a ID do workspace de destino](media/how-to/how-to-find-destination-ws-id.png)

3. Mantenha a ID do workspace de destino para consultar durante o processo de migração.

## <a name="migrate-a-project"></a>Migrar um projeto

Se você quiser migrar os projetos seletivamente, o Hub do Microsoft Translator oferecerá essa capacidade.

Para migrar um projeto:

1. Entre no Hub do Microsoft Translator.

2. Vá para a página "Projetos".

3. Clique no link "Migrar" do projeto apropriado.

    ![Captura de tela que realça o botão migrar do projeto selecionado.](media/how-to/how-to-migrate-from-hub.png)

4. Ao pressionar o link de migração, você verá um formulário permitindo:
   * Especificar o workspace que deseja transferir para o Tradutor Personalizado
   * Indicar se deseja transferir todos os treinamentos bem-sucedidos ou apenas os treinamentos implantados. Por padrão, todos os treinamentos bem-sucedidos serão transferidos.
   * Indicar se deseja que o treinamento seja implantado automaticamente após a conclusão. Por padrão, o treinamento não será implantado automaticamente após a conclusão.

5. Clique em "Enviar Solicitação".

## <a name="migrate-a-workspace"></a>Migrar um workspace

Além de migrar um único projeto, você também poderá migrar todos os projetos com treinamentos bem-sucedidos em um workspace. Isso fará com que cada projeto no workspace seja avaliado como se o link de migração tivesse sido pressionado. Esse recurso é adequado para usuários com vários projetos que desejam migrar todos eles para o Tradutor Personalizado com as mesmas configurações. Uma migração de workspace pode ser iniciada na página Configurações do Hub de Tradução.

Para migrar um workspace:

1. Entre no Hub do Microsoft Translator.

2. Acesse a página "Configurações".

3. Na página &quot;Configurações&quot;, clique em &quot;Migrar dados do workspace para o Tradutor Personalizado&quot;.

    ![Captura de tela que realça a opção migrar dados do espaço de trabalho para o tradutor personalizado.](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Na próxima página, selecione uma destas duas opções:

    a. Apenas os treinamentos implantados: selecionar essa opção migrará apenas os sistemas implantados e documentos relacionados.

    b. Todos os treinamentos com êxito: selecionar essa opção migrará todos os treinamentos e documentos relacionados.

    c. Insira a ID do workspace de destino no Tradutor Personalizado.

    ![Como migrar do Hub](media/how-to/how-to-migrate-from-hub-screen.png)

5. Clique em Enviar Solicitação.

## <a name="migration-history"></a>Histórico de migração

Quando tiver solicitado a migração de espaço de trabalho/projeto do Hub, você encontrará o histórico de migração na página Configurações do tradutor personalizado.

Para exibir o histórico de migração, siga estas etapas:

1. Acesse a página "Configuração" no portal do Tradutor Personalizado.

2. Na seção Histórico de Migração da página Configurações, clique em Histórico de Migração.

    ![Histórico de migração](media/how-to/how-to-migration-history.png)

A página Histórico de Migração exibe as seguintes informações como resumo para cada migração solicitada.

1. Migrado por: nome e email do usuário que enviou essa solicitação de migração

2. Migrado em: carimbo de data/hora da migração

3. Projetos: número de projetos solicitados para migração vs. número de projetos migrados com êxito.

4. Treinamentos: número de treinamentos solicitados para migração vs. número de treinamentos migrados com êxito.

5. Documentos: o número de documentos solicitados para migração vs. número de documentos migrados com êxito.

    ![Detalhes do histórico de migração](media/how-to/how-to-migration-history-details.png)

Se quiser um relatório de migração mais detalhado sobre os projetos, treinamentos e documentos, você terá a opção de exportar os detalhes como CSV.

## <a name="implementation-notes"></a>Notas da implementação
* Sistemas com pares de idiomas ainda não disponíveis no Tradutor personalizado só estarão disponíveis para acessar dados ou Desimplantar por meio de um tradutor personalizado. Esses projetos serão marcados como "indisponíveis" na página projetos. À medida que habilitarmos novos pares de idiomas com o tradutor personalizado, os projetos ficarão ativos para treinar e implantar. 
* A migração de um projeto do Hub para o Tradutor Personalizado não terá nenhum impacto sobre os treinamentos ou projetos do Hub. Não podemos excluir projetos ou documentos do Hub durante uma migração e não podemos desfazer a implantação de modelos.
* Só é possível migrar uma vez por projeto. Se você precisar repetir uma migração em um projeto, entre em contato conosco.
* O tradutor personalizado dá suporte a pares de idiomas NMT de e para inglês. [Veja a lista completa de idiomas com suporte](../language-support.md#customization). O Hub não exige que os modelos de linha de base e, portanto, dá suporte a várias linguagens de milhar. Você pode migrar um par linguístico sem suporte; no entanto, faremos apenas a migração de documentos e as definições do projeto. Não poderemos treinar o novo modelo. Além disso, esses documentos e projetos serão exibidos como inativos para indicar que não podem ser usados no momento. Caso o suporte a esses projetos e/ou documentos seja adicionado, eles ficarão ativos e poderão ser treinados.
* Atualmente, o Tradutor Personalizado não dá suporte a dados de treinamento monolíngues. Assim como em pares linguísticos sem suporte, você poderá migrar documentos monolíngues, mas eles aparecerão como inativos até que passem a receber suporte.
* O Tradutor Personalizado requer 10 mil sentenças paralelas para treinamento. O Hub da Microsoft pode treinar com um conjunto menor de dados. Se um treinamento for migrado e não atender a esse requisito, ele não será treinado.

## <a name="custom-translator-versus-hub"></a>Tradutor Personalizado versus Hub

Esta tabela compara os recursos entre o Microsoft Translator Hub e o conversor personalizado.

| Recurso | Hub | Tradutor personalizado |
| ------- | :-: | :---------------: |
| Status do recurso de personalização    | Disponibilidade geral    | Disponibilidade geral |
| Versão da API de texto    | V2     | V3  |
| Personalização de SMT    | Sim    | Não |
| Personalização de NMT    | Não    | Sim |
| Nova personalização de serviços de Fala unificados    | Não    | Sim |
| Sem rastreamento | Sim | Sim |

## <a name="new-languages"></a>Novos idiomas

Se você for uma comunidade ou organização trabalhando na criação de um novo sistema de linguagem para o tradutor, acesse [custommt@microsoft.com](mailto:custommt@microsoft.com) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

- [Treinar um modelo](how-to-train-model.md).
- Comece a usar seu modelo de tradução personalizado implantado por meio do [Tradutor v3](../reference/v3-0-translate.md?tabs=curl).