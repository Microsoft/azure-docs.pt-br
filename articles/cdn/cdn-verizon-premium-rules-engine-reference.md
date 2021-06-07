---
title: Referência do mecanismo de regras CDN do Azure | Microsoft Docs
description: Documentação de referência para recursos e condições de correspondência do mecanismo de regras da CDN do Azure.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 295bc0a20a547bf944f48af6711b18af34571b02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91362573"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Referência do mecanismo de regras da CDN do Azure da Verizon Premium

Este artigo lista as descrições detalhadas dos recursos e condições de correspondência disponíveis para o mecanismo de regras da [CDN (Rede de Distribuição de Conteúdo)](cdn-verizon-premium-rules-engine.md).

O mecanismo de regras foi projetado para ser a autoridade final sobre como os tipos específicos de solicitações são processados pela CDN.

**Usos comuns**:

- Substituir ou definir uma política de cache personalizada.
- Proteger ou negar solicitações de conteúdo confidencial.
- Solicitações de redirecionamento.
- Armazenar dados de log personalizados.
## <a name="key-concepts"></a>Principais conceitos
Os principais conceitos para configurar o mecanismo de regras são descritos abaixo.
### <a name="draft"></a>Rascunho
O rascunho de uma política consiste em uma ou mais regras destinadas a identificar solicitações e o conjunto de ações que serão aplicadas a elas. Um rascunho é um trabalho em andamento que permite atualizações frequentes da configuração sem afetar o tráfego do site. Quando um rascunho estiver pronto para ser finalizado, ele deverá ser convertido em uma política somente leitura.

### <a name="rule"></a>Regra
Uma regra identifica um ou mais tipos de solicitações e o conjunto de ações que serão aplicadas a elas.

Ele consiste em: 

- Um conjunto de expressões condicionais que definem a lógica pela qual as solicitações são identificadas.
- Um conjunto de condições de correspondência que definem os critérios usados para identificar solicitações.
- Um conjunto de recursos que definem como a CDN lidará com as solicitações acima.
Esses elementos são identificados na ilustração a seguir.

![Captura de tela com rótulos mostra a expressão condicional, a correspondência e os recursos de uma regra.](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>Política
Uma política, que consiste em um conjunto de regras somente leitura, fornece os meios para:

- Criar, armazenar e gerenciar diversas variantes de suas regras.
- Reverter para uma versão previamente implantada.
- Prepare regras específicas de eventos com antecedência (por exemplo, uma regra que redirecione o tráfego como resultado de uma manutenção com origem no cliente).

> [!NOTE]
> Embora apenas uma única política por ambiente seja permitida, as políticas podem ser implantadas conforme necessário.

### <a name="deploy-request"></a>Solicitação de implantação
Uma solicitação de implantação fornece um procedimento simples e otimizado pelo qual uma política pode ser aplicada rapidamente ao ambiente de preparação ou produção. Um histórico das solicitações de implantação é fornecido para facilitar o controle das alterações aplicadas a esses ambientes.

> [!NOTE]
> Somente solicitações que não passam por nosso sistema de detecção de erros e validação automatizada exigirão revisão e aprovação manual.

### <a name="rule-precedence"></a>Precedência de regra
As regras contidas em uma política são normalmente processadas na ordem em que são listadas (ou seja, de cima para baixo). Se a solicitação corresponder a regras conflitantes, a última regra a ser processada terá precedência.

### <a name="policy-deployment-workflow"></a>Fluxo de trabalho de implantação da política
O fluxo de trabalho pelo qual uma política pode ser aplicada ao ambiente de produção ou de preparo é ilustrado abaixo.

![Fluxo de trabalho de implantação da política](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|Etapa |Descrição |
|---------|---------|
|[Criar rascunho](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    Um rascunho consiste em um conjunto de regras que definem como as solicitações para seu conteúdo devem ser tratadas pela CDN.     |
|Bloquear rascunho   |     Depois que um rascunho for finalizado, ele deverá ser bloqueado e convertido em uma política somente leitura.    |
|[Enviar solicitação de implantação](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> Uma solicitação de implantação permite que uma política seja aplicada a um tráfego de teste ou de produção.</br> <br>Envie uma solicitação de implantação para o ambiente de preparo ou de produção.</br>     |
|Revisão da solicitação de implantação   |    <br>Uma solicitação de implantação passa por uma validação automatizada e por uma detecção de erro.</br><br>Embora a maioria das solicitações de implantação seja aprovada automaticamente, a revisão manual é necessária no caso de políticas mais complexas.</br>   |
|Implantação de política ([Preparo](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging))   |  <br> Após a aprovação de uma solicitação de implantação para o ambiente de preparo, uma política será aplicada a esse ambiente. Esse ambiente permite que uma política seja testada em relação ao tráfego do site de simulação.</br><br>Depois que a política estiver pronta para ser aplicada ao tráfego do site ativo, uma nova solicitação de implantação para o ambiente de produção deverá ser enviada.</br>      |
|Implantação de política ([Produção](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti))   |  Após a aprovação de uma solicitação de implantação para o ambiente de produção, uma política será aplicada a esse ambiente. Esse ambiente permite que uma política atue como a autoridade final para determinar como a CDN deve lidar com o tráfego ativo.     |
## <a name="syntax"></a>Sintaxe

A maneira como os caracteres especiais são tratados varia de acordo com a forma como um recurso ou condição de correspondência manipula valores de texto. Uma condição de correspondência ou recurso pode interpretar o texto de uma das seguintes maneiras:

- [**Valores literais**](#literal-values)
- [**Valores de caractere curinga**](#wildcard-values)
- [**Expressões regulares**](#regular-expressions)

### <a name="literal-values"></a>Valores literais

O texto que é interpretado como um valor literal trata todos os caracteres especiais, com exceção do símbolo %, como parte do valor que deve ser correspondido. Em outras palavras, uma condição de correspondência literal definida para `\'*'\` será satisfeita somente quando esse valor exato (ou seja, `\'*'\`) for localizado.

Um símbolo de porcentagem é usado para indicar a codificação de URL (por exemplo, `%20`).

### <a name="wildcard-values"></a>Valores de caractere curinga

O texto que é interpretado como um valor de caractere curinga atribui significado adicional a caracteres especiais. A tabela a seguir descreve como o seguinte conjunto de caracteres é interpretado:

Caractere | Descrição
----------|------------
\ | Uma barra invertida é usada para funcionar como escape para qualquer um dos caracteres especificados nesta tabela. Uma barra invertida deve ser especificada diretamente antes do caractere especial que deve ter escape.<br/>Por exemplo, a seguinte sintaxe ignora um asterisco: `\*`
% | Um símbolo de porcentagem é usado para indicar a codificação de URL (por exemplo, `%20`).
\* | Um asterisco é um caractere curinga que representa um ou mais caracteres.
Space | Um caractere de espaço indica que uma condição de correspondência pode ser atendida por qualquer um dos valores especificados ou padrões.
'valor' | As aspas simples não têm significado especial. No entanto, um conjunto de aspas simples é usado para indicar que um valor deve ser tratado como um valor literal. Ele pode ser usado das seguintes maneiras:<br><br/>- Permite que uma condição de correspondência seja satisfeita sempre que o valor especificado corresponder a qualquer parte do valor de comparação.  Por exemplo, `'ma'` corresponderia a qualquer uma das seguintes cadeias de caracteres: <br/><br/>/business/**ma** rathon/asset.htm<br/>**ma** p.gif<br/>/business/template.**ma** p<br /><br />- Permite que um caractere especial seja especificado como um caractere literal. Por exemplo, é possível especificar um caractere de espaço literal ao incluir um caractere de espaço dentro de um conjunto de aspas simples (ou seja, `' '` ou `'sample value'`).<br/>- Permite a especificação de um valor em branco. Especifique um valor em branco, especificando um conjunto de aspas simples (ou seja, '').<br /><br/>**Importante:**<br/>- Se o valor especificado não contiver um curinga, então ele será automaticamente considerado como um valor literal, o que significa que não será necessário especificar um conjunto de aspas simples.<br/>- Se uma barra invertida não funcionar como escape para outro caractere nesta tabela, ela será ignorada quando for especificada dentro de um conjunto de aspas simples.<br/>- Outra maneira de especificar um caractere especial como um caractere literal é isolá-lo, usando uma barra invertida (ou seja, `\`).

### <a name="regular-expressions"></a>Expressões regulares

Expressões regulares definem um padrão que é pesquisado dentro de um valor de texto. A notação de expressão regular define significados específicos para uma variedade de símbolos. A tabela a seguir indica como os caracteres especiais são tratados por condições de correspondência e recursos que dão suporte a expressões regulares.

Caractere especial | Descrição
------------------|------------
\ | Uma barra invertida escapa ao caractere que o segue, fazendo com que esse caractere seja tratado como um valor literal em vez de assumir o significado de expressão regular. Por exemplo, a seguinte sintaxe ignora um asterisco: `\*`
% | O significado de um símbolo de porcentagem depende de seu uso.<br/><br/> `%{HTTPVariable}`: Essa sintaxe identifica uma variável HTTP.<br/>`%{HTTPVariable%Pattern}`: Essa sintaxe usa um símbolo de porcentagem para identificar uma variável HTTP e como um delimitador.<br />`\%`: Escapar de um símbolo de porcentagem permite que ele seja usado como um valor literal, ou para indicar codificação de URL (por exemplo, `\%20`).
\* | Um asterisco permite que o caractere precedente corresponda a zero ou mais vezes.
Space | Normalmente, um caractere de espaço é tratado como um caractere literal.
'valor' | Aspas simples são tratadas como caracteres literais. Um conjunto de aspas simples não tem significado especial.

As condições de correspondência e os recursos que permitem expressões regulares aceitam padrões definidos por PCRE (expressões regulares compatíveis com Perl).



## <a name="next-steps"></a>Próximas etapas

- [Condições de correspondência do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Expressões condicionais do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Recursos do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Substitua o comportamento HTTP usando o mecanismo de regras](cdn-verizon-premium-rules-engine.md)
- [Visão geral da CDN do Azure](cdn-overview.md)
