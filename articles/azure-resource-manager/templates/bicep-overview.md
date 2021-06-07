---
title: Idioma bicep para modelos de Azure Resource Manager
description: Descreve a linguagem bicep para implantar a infraestrutura no Azure por meio de modelos de Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 74028c682b48a492c2e8f13bef538d1694370cbd
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955900"
---
# <a name="what-is-bicep-preview"></a>O que é o bicep (versão prévia)?

Bicep é uma linguagem para a implantação declarativa de recursos do Azure. Você pode usar bicep em vez de JSON para desenvolver seus modelos de Azure Resource Manager (modelos ARM). O bicep simplifica a experiência de criação fornecendo uma sintaxe concisa, melhor suporte à reutilização de código e à segurança de tipo aprimorada. Bicep é uma DSL (linguagem específica de domínio), o que significa que ele foi projetado para um determinado cenário ou domínio. Ele não é destinado a uma linguagem de programação geral para escrever aplicativos.

A sintaxe JSON para criar o modelo pode ser detalhada e exigir uma expressão complicada. O bicep melhora essa experiência sem perder nenhum dos recursos de um modelo JSON. É uma abstração transparente sobre o JSON para modelos de ARM. Cada arquivo bicep é compilado em um modelo ARM padrão. Os tipos de recursos, as versões de API e as propriedades que são válidas em um modelo ARM são válidas em um arquivo bicep. Há algumas [limitações conhecidas](#known-limitations) na versão atual.

O bicep está atualmente em versão prévia. Para acompanhar o status do trabalho, consulte o [repositório do projeto bicep](https://github.com/Azure/bicep).

Para saber mais sobre o bicep, consulte o vídeo a seguir.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Introdução

Para começar com o bicep, [Instale as ferramentas](bicep-install.md).

Depois de instalar as ferramentas, experimente o [tutorial do bicep](./bicep-tutorial-create-first-bicep.md). A série de tutoriais orienta você pela estrutura e pelos recursos do bicep. Você implanta arquivos bicep e converte um modelo ARM no arquivo bicep equivalente.

Para exibir arquivos JSON e bicep equivalentes lado a lado, consulte [bicep playground](https://aka.ms/bicepdemo).

Se você tiver um modelo ARM existente que deseja converter em bicep, consulte [convertendo modelos de ARM entre JSON e bicep](bicep-decompile.md).

## <a name="bicep-improvements"></a>Aprimoramentos do bicep

O bicep oferece uma sintaxe mais fácil e concisa quando comparado ao JSON equivalente. Você não usa `[...]` expressões. Em vez disso, você chama diretamente funções e obtém valores de parâmetros e variáveis. Você dá a cada recurso implantado um nome simbólico, o que torna mais fácil fazer referência a esse recurso em seu modelo.

Por exemplo, o JSON a seguir retorna um valor de saída de uma propriedade de recurso.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

A expressão de saída equivalente em bicep é mais fácil de escrever. O exemplo a seguir retorna a mesma propriedade usando o **publicIP** do nome simbólico para um recurso que é definido dentro do modelo:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Para obter uma comparação completa da sintaxe, consulte comparando [JSON e bicep para modelos](compare-template-syntax.md).

O bicep gerencia automaticamente as dependências entre os recursos. Você pode evitar `dependsOn` a configuração quando o nome simbólico de um recurso é usado em outra declaração de recurso.

Com o bicep, você pode dividir o projeto em vários módulos.

A estrutura do arquivo bicep é mais flexível do que o modelo JSON. Você pode declarar parâmetros, variáveis e saídas em qualquer lugar no arquivo. No JSON, você precisa declarar todos os parâmetros, variáveis e saídas dentro das seções correspondentes do modelo.

A extensão VS Code para bicep oferece validação avançada e IntelliSense. Por exemplo, você pode usar o IntelliSense da extensão para obter as propriedades de um recurso.

## <a name="known-limitations"></a>Limitações conhecidas

Os seguintes limites existem atualmente:

* Não é possível definir o modo ou o tamanho do lote em loops de cópia.
* Não é possível combinar loops e condições.
* Não há suporte para objetos de linha única e matrizes, como `['a', 'b', 'c']` ,.

## <a name="faq"></a>Perguntas frequentes

**Por que criar um novo idioma em vez de usar um existente?**

Você pode considerar o bicep como uma revisão para a linguagem de modelo ARM existente em vez de uma nova linguagem. A sintaxe foi alterada, mas a funcionalidade principal e o tempo de execução permanecem os mesmos.

Antes de desenvolver o bicep, consideramos o uso de uma linguagem de programação existente. Decidimos que nosso público-alvo acharia mais fácil aprender a bicep em vez de começar a usar outra linguagem.

**Por que não concentrar sua energia no Terraform ou outra infraestrutura de terceiros como ofertas de código?**

Usuários diferentes preferem linguagens de configuração e ferramentas diferentes. Queremos ter certeza de que todas essas ferramentas fornecem uma ótima experiência no Azure. Bicep faz parte desse esforço.

Se você estiver satisfeito usando Terraform, não há motivo para mudar. A Microsoft está comprometida em garantir que o Terraform no Azure seja o melhor possível.

Para clientes que selecionaram modelos de ARM, acreditamos que o bicep melhora a experiência de criação. O bicep também ajuda com a transição para clientes que não adotaram a infraestrutura como código.

**O é bicep somente para o Azure?**

Bicep é uma DSL concentrada na implantação de soluções completas no Azure. A reunião dessa meta requer o trabalho com algumas APIs que estão fora do Azure. Esperamos fornecer pontos de extensibilidade para esses cenários.

**O que acontece com meus modelos de ARM existentes?**

Eles continuam a funcionar exatamente como sempre têm. Você não precisa fazer nenhuma alteração. Continuaremos a dar suporte à linguagem JSON de modelo ARM subjacente. Os arquivos bicep são compilados em JSON e esse JSON é enviado para o Azure para implantação.

Quando estiver pronto, você poderá [converter os arquivos JSON em bicep](bicep-decompile.md).

## <a name="next-steps"></a>Próximas etapas

Introdução ao tutorial do [bicep](./bicep-tutorial-create-first-bicep.md).
