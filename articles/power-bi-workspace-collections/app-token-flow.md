---
title: Autenticando e autorizando com Coleções de Workspaces do Power BI | Microsoft Docs
description: Autenticando e autorizando com Coleções de Workspaces do Power BI.
services: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 713c56904769c133272db4fb65f8b596ab66804b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672499"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Autenticando e autorizando com Coleções de Workspaces do Power BI

As Coleções de Workspaces do Power BI usam **Chaves** e **Tokens de Aplicativo** para autenticação e autorização em vez de usar a autenticação explícita de usuário final. Nesse modelo, seu aplicativo gerencia a autenticação e autorização para os usuários finais. Quando necessário, seu aplicativo cria e envia os tokens de aplicativo que dizem ao nosso serviço para renderizar o relatório solicitado. Esse design não requer que o aplicativo use o Azure Active Directory para autorização e autenticação de usuário, embora você possa fazer isso.

> [!IMPORTANT]
> As Coleções de Workspaces do Power BI foram preteridas e só estarão disponíveis até junho de 2018 ou conforme a indicação do seu contrato. Recomendamos planejar a migração para o Power BI Embedded a fim de evitar interrupções em seu aplicativo. Para saber mais sobre como migrar seus dados para o Power BI Embedded, confira [Como migrar o conteúdo das Coleções de Workspaces do Power BI para o Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Duas maneiras de autenticar

**Chave** – você pode usar chaves para todas as chamadas à API de REST das Coleções de Workspaces do Power BI. As chaves podem ser encontradas no **Portal do Microsoft Azure** selecionando **Todas as configurações** e **Chaves de acesso**. Sempre trate sua chave como se fosse uma senha. Essas chaves têm permissões para fazer qualquer chamada à API REST em uma coleção de workspaces específica.

Para usar uma chave em uma chamada REST, adicione o seguinte cabeçalho de autorização:

    Authorization: AppKey {your key}

**Token de aplicativo** - tokens de aplicativo são usados para todas as solicitações de inserção. Eles são projetados para serem executados no lado do cliente. O token está restrito a um único relatório e é uma melhor prática definir um tempo de expiração.

Os tokens de aplicativo são um JWT (Token Web JSON) assinado por uma das suas chaves.

O token de seu aplicativo pode conter as seguintes declarações:

| Declaração | DESCRIÇÃO |    
| --- | --- |
| **ver** |A versão do token do aplicativo. A versão atual é 0.2.0. |
| **aud** |O destinatário pretendido do token. Para o uso de Coleções de Workspaces do Power BI: *https:\//analysis.windows.net/powerbi/api*. |
| **iss** |Uma cadeia de caracteres que indica o aplicativo que emitiu o token. |
| **type** |O tipo de token de aplicativo que está sendo criado. O único tipo com suporte atualmente é **incorporar**. |
| **wcn** |Nome da coleção de workspaces para o qual o token foi emitido. |
| **wid** |ID do workspace para o qual o token foi emitido. |
| **rid** |ID ddo relatório para o qual o token foi emitido. |
| **nome de usuário** (opcional) |Usado com RLS, o nome de usuário é uma cadeia de caracteres que pode ser usada para ajudar a identificar o usuário ao aplicar regras RLS. |
| **funções** (opcional) |Uma cadeia de caracteres que contém as funções a selecionar ao aplicar regras de segurança em nível de linha. Ao transmitir mais de uma função, elas deverão ser transmitidas como uma matriz de cadeia de caracteres. |
| **SCP** (opcional) |Uma cadeia de caracteres que contém os escopos de permissões. Ao transmitir mais de uma função, elas deverão ser transmitidas como uma matriz de cadeia de caracteres. |
| **exp** (opcional) |Indica a hora em que o token expira. O valor deve ser transmitido como carimbos de data/hora do Unix. |
| **nbf** (opcional) |Indica a hora em que o token começa a valer. O valor deve ser transmitido como carimbos de data/hora do Unix. |

Um token de aplicativo de exemplo é semelhante a:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Quando decodificado, é semelhante a:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

Há métodos disponíveis dentro dos SDKs que facilitam a criação de tokens de aplicativo. Por exemplo, para .NET, você pode examinar a classe [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) e os métodos [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN).

Para o SDK do .NET, você pode consultar [Scopes](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Escopos

Ao usar os tokens de inserção, convém restringir o uso de recursos aos quais você concede acesso. Por esse motivo, você pode gerar um token com as permissões no escopo.

A seguir estão os escopos disponíveis para as Coleções de Workspaces do Power BI.

|Escopo|DESCRIÇÃO|
|---|---|
|Dataset.Read|Fornece permissão para ler o conjunto de dados especificado.|
|Dataset.Write|Fornece permissão para gravar o conjunto de dados especificado.|
|Dataset.ReadWrite|Fornece permissão para leitura e gravação do conjunto de dados especificado.|
|Report.Read|Fornece permissão para exibir o relatório especificado.|
|Report.ReadWrite|Fornece permissão para exibir e editar o relatório especificado.|
|Workspace.Report.Create|Fornece permissão para criar um novo relatório no workspace especificado.|
|Workspace.Report.Copy|Fornece permissão para clonar um relatório existente no workspace especificado.|

Você pode fornecer vários escopos usando um espaço entre os escopos semelhante ao que segue.

```csharp
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Declarações necessárias - escopos**

scp: {scopesClaim} scopesClaim pode ser uma cadeia de caracteres ou uma matriz de cadeias de caracteres, observando as permissões permitidas para recursos do workspace (relatório, conjunto de dados, etc.)

Um token decodificado, com escopos definidos, seria semelhante a:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>Operações e escopos

|Operação|Recurso de destino|Permissões de token|
|---|---|---|
|Criar (na memória) um novo relatório com base em um conjunto de dados.|Conjunto de dados|Dataset.Read|
|Criar (na memória) um novo relatório com base em um conjunto de dados e salvar o relatório.|Conjunto de dados|* Dataset.Read<br>* Workspace.Report.Create|
|Exibir e explorar/Editar (na memória) um relatório existente. Report.Read implica Dataset.Read. Report.Read não permite o salvamento de edições.|Relatório|Report.Read|
|Editar e salvar um relatório existente.|Relatório|Report.ReadWrite|
|Salvar uma cópia de um relatório (Salvar como).|Relatório|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Como funciona o fluxo
1. Copie as chaves de API para o seu aplicativo. Você pode obter as chaves no **Portal do Azure**.
   
    ![Onde encontrar as chaves de API no Portal do Azure](media/get-started-sample/azure-portal.png)
1. O token faz a asserção de uma declaração e tem uma data de validade.
   
    ![Fluxo do token de aplicativo – o token faz a asserção de declaração](media/get-started-sample/token-2.png)
1. O token é assinado com uma chave de acesso de API.
   
    ![Fluxo do token de aplicativo – o token é assinado](media/get-started-sample/token-3.png)
1. Solicitações do usuário para exibir um relatório.
   
    ![Fluxo do token de aplicativo – o usuário faz a solicitação para exibir um relatório](media/get-started-sample/token-4.png)
1. O token é validado com uma chave de acesso de API.
   
   ![Fluxo do token de aplicativo – o token é validado](media/get-started-sample/token-5.png)
1. As Coleções de Workspaces do Power BI enviam um relatório para o usuário.
   
   ![Fluxo do token de aplicativo – o serviço envia o relatório para o usuário](media/get-started-sample/token-6.png)

Após as **Coleções de Workspaces do Power BI** enviarem um relatório para o usuário, o usuário pode exibir o relatório em seu aplicativo personalizado. Por exemplo, se você importou o [exemplo de PBIX Analisando Dados de Vendas](https://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), o aplicativo Web de exemplo teria essa aparência:

![Exemplo de relatório inserido no aplicativo](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Consulte também

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Introdução ao exemplo de Coleções de Workspaces do Microsoft Power BI](get-started-sample.md)  
[Cenários comuns das Coleções de Workspaces do Microsoft Power BI](scenarios.md)  
[Introdução às Coleções de Workspaces do Microsoft Power BI](get-started.md)  
[Repositório de Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)

Mais perguntas? [Experimentar a comunidade do Power BI](https://community.powerbi.com/)
