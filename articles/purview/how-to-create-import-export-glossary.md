---
title: Como criar, importar e exportar os termos do glossário
description: Saiba como criar, importar e exportar os termos do glossário no Azure alcance.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 6f7f481ae0e0c75b14d894080f791161346cd93f
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952398"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>Como criar, importar e exportar os termos do glossário

Este artigo descreve como criar um termo de Glossário de negócios no catálogo de dados do Azure alcance e importar e exportar termos do glossário usando arquivos. csv.

## <a name="create-a-new-term"></a>Criar um novo termo

Para criar um novo termo do glossário, execute as seguintes etapas:

1. Selecione o ícone de Glossário no painel de navegação esquerdo na home page ir para a página de lista de termos.

2. Na página **termos do glossário** , selecione **+ novo termo**. Uma página é aberta com o modelo **padrão do sistema** selecionado. Escolha o modelo com o qual você deseja criar o termo do glossário e selecione **continuar**.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="Captura de tela da criação do novo termo." border="true":::

3. Dê um nome a seu novo termo, que deve ser exclusivo no catálogo. O nome do termo diferencia maiúsculas de minúsculas, o que significa que você pode ter um termo chamado **amostra** e **exemplo** no catálogo.

4. Adicione uma **definição**.

5. Defina o **status** do termo. Novos termos padrão para status de **rascunho** .

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-options.png" alt-text="Captura de tela das opções de status." border="true":::

   Esses marcadores de status são metadados associados ao termo. No momento, você pode definir o seguinte status em cada termo:

   - **Rascunho**: este termo ainda não foi implementado oficialmente.
   - **Aprovado**: este termo é oficial/padrão/aprovado.
   - **Expirado**: este termo não deve mais ser usado.
   - **Alerta**: este termo precisa de atenção.

6. Adicione **recursos** e **acrônimo**. Se o termo fizer parte da hierarquia, você poderá adicionar termos pai no **pai** na guia Visão geral.

7. Adicione **sinônimos** e **Termos relacionados** na guia relacionado.

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="Captura de tela do novo termo > guia relacionado." border="true":::

8. Opcionalmente, selecione a guia **contatos** para adicionar especialistas e administradores ao seu termo.

9. Selecione **criar** para criar seu termo.

## <a name="import-terms-into-the-glossary"></a>Importar termos para o Glossário

O catálogo de dados do Azure alcance fornece um arquivo template. csv para você importar seus termos em seu Glossário.

Você pode importar os termos no catálogo. Os termos duplicados no arquivo serão substituídos.

Observe que os nomes de termo diferenciam maiúsculas de minúsculas. Por exemplo, `Sample` e `saMple` podem existir no mesmo Glossário.

### <a name="to-import-terms-follow-these-steps"></a>Para importar os termos, siga estas etapas

1. Quando estiver na página **termos do glossário** , selecione **importar termos**.

2. A página modelo de termo é aberta. Corresponder o modelo de termo ao tipo de. CSV que você deseja importar.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="Captura de tela da página termos do glossário, botão importar termos.":::

3. Baixe o modelo CSV e use-o para inserir seus termos que você gostaria de adicionar.

   > [!Important]
   > O sistema só dá suporte à importação de colunas que estão disponíveis no modelo. O modelo "padrão do sistema" terá todos os atributos padrão.
   > No entanto, os modelos de termo personalizado terão atributos prontos para uso e atributos personalizados adicionais definidos no modelo. Portanto, o. O arquivo CSV difere do número total de colunas e nomes de colunas, dependendo do modelo de termo selecionado. Você também pode examinar o arquivo em busca de problemas após o carregamento.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="Captura de tela da página termos do glossário, selecione Arquivo para importação.":::

4. Depois de concluir o preenchimento do arquivo. csv, selecione o arquivo a ser importado e selecione **OK**.

5. O sistema carregará o arquivo e adicionará todos os termos ao seu catálogo.
 
   > [!Important]
   > O endereço de email para administradores e especialistas deve ser o endereço principal do usuário do grupo do AAD. O email alternativo, o nome principal do usuário e os emails não-AAD ainda não têm suporte. 

## <a name="export-terms-from-glossary-with-custom-attributes"></a>Exportar termos do glossário com atributos personalizados

Você deve ser capaz de exportar termos do glossário, contanto que os termos selecionados pertençam ao mesmo termo modelo.

1. Quando você estiver no Glossário, por padrão, o botão **Exportar** será desabilitado. Depois de selecionar os termos que você deseja exportar, o botão **Exportar** será habilitado se os termos selecionados pertencerem ao mesmo modelo.

2. Selecione **Exportar** para baixar os termos selecionados.

 > [!Important]
   > Se os termos em uma hierarquia pertencerem a modelos de termo diferentes, você precisará dividi-los em diferentes. Arquivos CSV para importação. Além disso, a atualização de um pai de um termo não tem suporte no momento usando o processo de importação.


## <a name="next-steps"></a>Próximas etapas

Siga o [tutorial: criar e importar os termos do glossário](tutorial-import-create-glossary-terms.md) para saber mais.
