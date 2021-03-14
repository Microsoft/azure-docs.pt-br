---
title: Segurança de serviços cognitivas do Azure
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre as várias considerações de segurança para o uso de serviços cognitivas.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: erhopf
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 66de81387fe27bd2a81ebcfeab7d86aac7031ff1
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425287"
---
# <a name="azure-cognitive-services-security"></a>Segurança de serviços cognitivas do Azure

A segurança deve ser considerada uma prioridade principal ao desenvolver qualquer e todos os aplicativos. Com o início de aplicativos habilitados para inteligência artificial, a segurança é ainda mais importante. Neste artigo, vários aspectos da segurança dos serviços cognitivas do Azure são descritos, como o uso da segurança da camada de transporte, autenticação, configuração segura de dados confidenciais e Sistema de Proteção de Dados do Cliente para acesso a dados do cliente.

## <a name="transport-layer-security-tls"></a>Protocolo TLS

Todos os pontos de extremidade de serviços cognitivas expostos por HTTP impõem o TLS 1,2. Com um protocolo de segurança imposto, os consumidores que tentam chamar um ponto de extremidade de serviços cognitivas devem aderir a estas diretrizes:

* O sistema operacional do cliente (SO) precisa dar suporte a TLS 1,2
* A linguagem (e a plataforma) usada para fazer a chamada HTTP precisar especificar o TLS 1,2 como parte da solicitação
  * Dependendo do idioma e da plataforma, a especificação do TLS é feita implicitamente ou explicitamente

Para usuários do .NET, considere as <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">práticas recomendadas de segurança da camada de transporte </a>.

## <a name="authentication"></a>Autenticação

Ao discutir a autenticação, há várias concepções comuns. A autenticação e a autorização geralmente são confusas entre si. A identidade também é um componente importante na segurança. Uma identidade é uma coleção de informações sobre uma <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">entidade de segurança </a>. Os IdP (provedores de identidade) fornecem identidades aos serviços de autenticação. A autenticação é o ato de verificar a identidade de um usuário. Autorização é a especificação de direitos de acesso e privilégios para recursos para uma determinada identidade. Várias ofertas de serviços cognitivas incluem o Azure RBAC (controle de acesso baseado em função do Azure). O RBAC do Azure poderia ser usado para simplificar parte da cerimônia envolvida com o gerenciamento manual de entidades. Para obter mais detalhes, consulte [controle de acesso baseado em função do Azure para recursos do Azure](../role-based-access-control/overview.md).

Para obter mais informações sobre autenticação com chaves de assinatura, tokens de acesso e Azure Active Directory (AAD), consulte <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">autenticar solicitações para serviços cognitivas do Azure</a>.

## <a name="environment-variables-and-application-configuration"></a>Variáveis de ambiente e configuração de aplicativo

As variáveis de ambiente são pares de nome-valor, armazenadas em um ambiente específico. Uma alternativa mais segura ao uso de valores codificados para dados confidenciais é usar variáveis de ambiente. Os valores codificados são inseguros e devem ser evitados.

> [!CAUTION]
> Não **use valores** codificados para dados confidenciais, fazendo isso é uma grande vulnerabilidade de segurança.

> [!NOTE]
> Embora as variáveis de ambiente sejam armazenadas em texto sem formatação, elas são isoladas em um ambiente. Se um ambiente for comprometido, também serão as variáveis com o ambiente.

### <a name="set-environment-variable"></a>Definir variável de ambiente

Para definir variáveis de ambiente, use um dos comandos a seguir, em que o `ENVIRONMENT_VARIABLE_KEY` é a chave nomeada e `value` é o valor armazenado na variável de ambiente.

# <a name="command-line"></a>[Linha de comando](#tab/command-line)

Crie e atribua a variável de ambiente persistente, dado o valor.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

Em uma nova instância do **prompt de comando**, leia a variável de ambiente.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Crie e atribua a variável de ambiente persistente, dado o valor.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

Em uma nova instância do **Windows PowerShell**, leia a variável de ambiente.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Crie e atribua a variável de ambiente persistente, dado o valor.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

Em uma nova instância do **bash**, leia a variável de ambiente.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> Depois de definir uma variável de ambiente, reinicie o IDE (ambiente de desenvolvimento integrado) para garantir que as variáveis de ambiente adicionadas recentemente estejam disponíveis.

### <a name="get-environment-variable"></a>Obter variável de ambiente

Para obter uma variável de ambiente, ele deve ser lido na memória. Dependendo do idioma que você estiver usando, considere os trechos de código a seguir. Esses trechos de código demonstram como obter a variável de ambiente dada ao `ENVIRONMENT_VARIABLE_KEY` e atribuir a uma variável chamada `value` .

# <a name="c"></a>[C#](#tab/csharp)

Para obter mais informações, <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` </a>consulte.

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

Para obter mais informações, <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` </a>consulte.

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

Para obter mais informações, <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` </a>consulte.

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

Para obter mais informações, <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` </a>consulte.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Para obter mais informações, <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` </a>consulte.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

Para obter mais informações, <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` </a>consulte.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>Sistema de Proteção de Dados do Cliente

[Sistema de proteção de dados do cliente para Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md) fornece uma interface para os clientes revisarem e aprovarem ou rejeitarem solicitações de acesso a dados do cliente. Ele é usado quando um engenheiro da Microsoft precisa acessar os dados do cliente durante uma solicitação de suporte. Para obter informações sobre como Sistema de Proteção de Dados do Cliente solicitações são iniciadas, controladas e armazenadas para revisões e auditorias posteriores, consulte [sistema de proteção de dados do cliente](../security/fundamentals/customer-lockbox-overview.md). 

Sistema de Proteção de Dados do Cliente está disponível para este serviço cognitiva:

* Tradutor

Para os serviços a seguir, os engenheiros da Microsoft não acessarão nenhum dado do cliente na camada E0: 

* Reconhecimento Vocal
* Face
* Content Moderator
* Personalizador

> [!IMPORTANT]
> Para o **reconhecedor de formulário**, os engenheiros da Microsoft não acessarão dados de clientes em recursos criados após 10 de julho de 2020.

Para solicitar a capacidade de usar o SKU E0, preencha e envie este [formulário de solicitação](https://aka.ms/cogsvc-cmk). Levará aproximadamente 3-5 dias úteis para que o status da solicitação seja reproduzido. Dependendo da demanda, você pode ser colocado em uma fila e aprovado, pois o espaço se torna disponível. Depois de aprovado para usar o SKU E0 com LUIS, você precisará criar um novo recurso do portal do Azure e selecionar E0 como o tipo de preço. Os usuários não poderão atualizar do F0 para o novo SKU E0.

No momento, o serviço de fala não oferece suporte a Sistema de Proteção de Dados do Cliente. No entanto, os dados do cliente podem ser armazenados usando o BYOS (Traga seu próprio armazenamento), permitindo que você obtenha controles de dados semelhantes para Sistema de Proteção de Dados do Cliente. Tenha em mente que os dados do serviço de fala permanecem e são processados na região em que o recurso de fala foi criado. Isso se aplica a quaisquer dados em repouso e dados em trânsito. Ao usar recursos de personalização, como Fala Personalizada e voz personalizada, todos os dados do cliente são transferidos, armazenados e processados na mesma região em que seu BYOS (se usado) e o recurso de serviço de fala residem.

> [!IMPORTANT]
> A Microsoft **não** usa dados do cliente para melhorar seus modelos de fala. Além disso, se o log do ponto de extremidade estiver desabilitado e nenhuma personalização for usada, nenhum dado do cliente será armazenado. 

## <a name="next-steps"></a>Próximas etapas

* Explore os vários [Serviços cognitivas](./what-are-cognitive-services.md)
* Saiba mais sobre [redes virtuais de serviços cognitivas](cognitive-services-virtual-networks.md)