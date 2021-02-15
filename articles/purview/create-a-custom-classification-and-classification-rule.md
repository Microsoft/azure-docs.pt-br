---
title: Criar uma regra de classificação e classificação personalizada (versão prévia)
description: Este artigo descreve como você pode criar classificações personalizadas para definir tipos de dados em seu espaço de dados que são exclusivos para sua organização. Ele também descreve a criação de regras de classificação personalizadas que permitem encontrar dados especificados em todo o seu espaço de dados.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/5/2021
ms.openlocfilehash: 3cc29e0bd806ab76c4980128df5a89761e465fe7
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988381"
---
# <a name="custom-classifications-in-azure-purview"></a>Classificações personalizadas no Azure alcance 

Este artigo descreve como você pode criar classificações personalizadas para definir tipos de dados em seu espaço de dados que são exclusivos para sua organização. Ele também descreve a criação de regras de classificação personalizadas que permitem encontrar dados especificados em todo o seu espaço de dados.

## <a name="default-classifications"></a>Classificações padrão

O catálogo de dados do Azure alcance fornece um grande conjunto de classificações padrão que representam tipos de dados pessoais típicos que você pode ter em seu espaço de dados.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="selecionar classificação" border="true":::

Você também pode criar classificações personalizadas se qualquer uma das classificações padrão não atender às suas necessidades.

## <a name="steps-to-create-a-custom-classification"></a>Etapas para criar uma classificação personalizada

Para criar uma classificação personalizada, faça o seguinte:

1. No catálogo, selecione **centro de gerenciamento** no menu à esquerda.

2. Selecione **classificações** em **Gerenciamento de metadados**.

3. Selecionar **+ novo**

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="Nova classificação" border="true":::

O painel **Adicionar nova classificação** é aberto, onde você pode dar um nome e uma descrição à sua classificação. É uma boa prática usar uma Convenção de espaçamento de nome, como `your company name.classification name` .
As classificações de sistema da Microsoft são agrupadas sob o `MICROSOFT.` namespace reservado. Um exemplo é **Microsoft. Instituições. Digamos. \_ \_ número** do seguro social.

O nome da sua classificação deve começar com uma letra seguida por uma sequência de letras, números e pontos (.) ou caracteres de sublinhado.
Nenhum espaço é permitido. Conforme você digita, a UX gera automaticamente um nome amigável. Esse nome amigável é o que os usuários veem quando você os aplica a um ativo no catálogo.

Para manter o nome curto, o sistema cria o nome amigável com base na seguinte lógica:

- Todos os dois últimos segmentos do namespace são cortados.

- A capitalização é ajustada para que a primeira letra de cada palavra seja capitalizada. Todas as outras letras são convertidas em minúsculas.

- Todos os sublinhados ( \_ ) são substituídos por espaços.

Por exemplo, se você tiver nomeado seu CONTOSO.HR de classificação **. \_ID do funcionário**, o nome amigável é armazenado no sistema como **hr. ID do funcionário**.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

Selecione **OK** e a nova classificação será adicionada à sua lista de classificação.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="Classificação personalizada" border="true":::

A seleção da classificação na lista abre a página detalhes da classificação. Aqui, você encontra todos os detalhes sobre a classificação.
Esses detalhes incluem a contagem de quantas instâncias existem, o nome formal, as regras de classificação associadas (se houver) e o nome do proprietário.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="Selecionar classificação" border="true":::

## <a name="custom-classification-rules"></a>Regras de classificação personalizada

O serviço de catálogo fornece um conjunto de regras de classificação padrão, que são usadas pelo verificador para detectar automaticamente determinados tipos de dados. Você também pode adicionar suas próprias regras de classificação personalizadas para detectar outros tipos de dados que você pode estar interessado em Localizar em seu espaço de dados. Esse recurso pode ser muito eficiente quando você \' tenta novamente localizar dados dentro de seu estado de dados.

Por exemplo, digamos \' que uma empresa chamada contoso tenha IDs de funcionário padronizadas em toda a empresa com a palavra \" Employee \" seguida por um GUID para criar Employee {GUID}. Por exemplo, uma instância de uma ID de funcionário é semelhante a EMPLOYEE9c55c474-9996-420C-a285-0d0fc23f1f55.

A Contoso pode configurar o sistema de verificação para localizar instâncias dessas IDs criando uma regra de classificação personalizada. Eles podem fornecer uma expressão regular que corresponda ao padrão de dados, nesse caso, `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$` . Opcionalmente, se os dados geralmente estiverem em uma coluna que eles conhecem o nome, como ID de funcionário \_ ou EmployeeID, eles poderão adicionar uma expressão regular de padrão de coluna para tornar a verificação ainda mais precisa. Um Regex de exemplo é \_ EmployeeID de ID de funcionário \| .

O sistema de verificação pode usar essa regra para examinar os dados reais na coluna e o nome da coluna para tentar identificar todas as instâncias de onde o padrão de ID de funcionário é encontrado.

## <a name="steps-to-create-a-custom-classification-rule"></a>Etapas para criar uma regra de classificação personalizada

Para criar uma regra de classificação personalizada:

1. Crie uma classificação personalizada seguindo as instruções na seção acima. Você adicionará essa classificação personalizada na configuração da regra de classificação para que o sistema a aplique quando encontrar uma correspondência na coluna.

2. Selecione o ícone **centro de gerenciamento** .

3. Selecione a seção **regras de classificações** .

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classificationrules.png" alt-text="Bloco de regras de classificação" border="true":::

4. Selecione **Novo**.

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/newclassificationrule.png" alt-text="Adicionar nova regra de classificação" border="true":::

5. A caixa de diálogo **nova regra de classificação** é aberta. Preencha as informações de configuração para sua nova regra.

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/createclassificationrule.png" alt-text="Criar nova regra de classificação" border="true":::

|Campo     |Descrição  |
|---------|---------|
|Nome   |    Obrigatórios. O máximo é de 100 caracteres.    |
|Descrição      |Opcional. O máximo é de 256 caracteres.    |
|Nome da classificação    | Obrigatório. Selecione o nome da classificação na lista suspensa para instruir o verificador a aplicá-lo se uma correspondência for encontrada.        |
|Estado   |  Obrigatório. As opções estão habilitadas ou desabilitadas. Habilitado é o padrão.    |
|Padrão de dados    |Opcional. Uma expressão regular que representa os dados armazenados no campo de dados. O limite é muito grande. No exemplo anterior, os padrões de dados são testados para uma ID de funcionário que é literalmente a palavra `Employee{GUID}` .  |
|Padrão de coluna    |Opcional. Uma expressão regular que representa os nomes de coluna que você deseja corresponder. O limite é muito grande.          |

Em **padrão de dados**, há duas opções:

- **Limite de correspondência distinta**: o número total de valores de dados distintos que precisam ser encontrados em uma coluna antes que o verificador execute o padrão de dados nele. O valor sugerido é 8. Esse valor pode ser ajustado manualmente em um intervalo de 2 a 32. O sistema requer esse valor para garantir que a coluna contenha dados suficientes para que o verificador classifique com precisão. Por exemplo, uma coluna que contém várias linhas que contêm o valor 1 não será classificada. As colunas que contêm uma linha com um valor e o restante das linhas têm valores nulos também não serão classificadas. Se você especificar vários padrões, esse valor se aplicará a cada um deles.

- **Limite mínimo de correspondência**: você pode usar essa configuração para definir o percentual mínimo de correspondências de valor de dados em uma coluna que deve ser encontrada pelo verificador para que a classificação seja aplicada. O valor sugerido é 60%. Você precisa ter cuidado com essa configuração. Se você reduzir o nível abaixo de 60%, poderá introduzir classificações de falsos positivos em seu catálogo. Se você especificar vários padrões de dados, essa configuração será desabilitada e o valor será corrigido em 60%.

## <a name="next-steps"></a>Próximas etapas

Agora que você criou sua regra de classificação, ela está pronta para ser adicionada a um conjunto de regras de verificação para que sua verificação use a regra durante a verificação. Para obter mais informações, consulte [criar um conjunto de regras de verificação](create-a-scan-rule-set.md).
