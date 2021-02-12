---
title: Proxy do SMART on FHIR do Azure Active Directory
description: Este tutorial descreve como usar um proxy para habilitar aplicativos SMART on FHIR com a API do Azure para FHIR.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 04/02/2019
ms.openlocfilehash: 2e13a9fc32964781dda07e5534e5cab79868ddf0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995532"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>Tutorial: Proxy do SMART on FHIR do Azure Active Directory

O [SMART on FHIR](https://docs.smarthealthit.org/) é um conjunto de especificações abertas para integrar aplicativos de parceiros com servidores FHIR e sistemas de registros médicos eletrônicos que têm interfaces FHIR. Uma das principais finalidades das especificações é descrever como um aplicativo deve descobrir pontos de extremidade de autenticação para um servidor FHIR e iniciar uma sequência de autenticação. 

A autenticação é baseada em OAuth2. Porém, como o SMART on FHIR usa as convenções de nomenclatura de parâmetro que não são compatíveis imediatamente com o Azure AD (Azure Active Directory), a API do Azure para FHIR tem um proxy do SMART on FHIR do Azure AD interno que habilita um subconjunto das sequências de inicialização do SMART on FHIR. Especificamente, o proxy habilita a [sequência de inicialização do EHR](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence).

Este tutorial descreve como usar o proxy para habilitar aplicativos SMART on FHIR com a API do Azure para FHIR.

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância da API do Azure para FHIR
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>Configurar registros do Azure AD

O SMART on FHIR requer que `Audience` tenha um URI de identificador igual ao URI do serviço FHIR. A configuração padrão da API do Azure para FHIR usa um valor `Audience` de `https://azurehealthcareapis.com`. No entanto, você também pode definir um valor correspondente à URL específica do serviço FHIR (por exemplo `https://MYFHIRAPI.azurehealthcareapis.com`). Isso é necessário ao trabalhar com o proxy do SMART on FHIR.

Você também precisará de um registro de aplicativo cliente. A maioria dos aplicativos SMART on FHIR é composta por aplicativos JavaScript de página única. Portanto, você deve seguir as instruções para configurar um [aplicativo cliente público do Azure AD](register-public-azure-ad-client-app.md).

Depois de concluir essas etapas, você deverá ter:

- Um servidor FHIR com o público RGE definido como `https://MYFHIRAPI.azurehealthcareapis.com`, em que `MYFHIRAPI` é o nome da instância da API do Azure para FHIR.
- Um registro de aplicativo cliente público. Anote a ID do aplicativo para esse aplicativo cliente.

## <a name="enable-the-smart-on-fhir-proxy"></a>Habilitar o proxy do SMART on FHIR

Habilite o proxy do SMART on FHIR nas configurações de **Autenticação** para a instância da API do Azure para FHIR marcando a caixa de seleção **proxy do SMART on FHIR**:

![Seleções para habilitar o proxy SMART on FHIR](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>Habilitar CORS

Como a maioria dos aplicativos SMART on FHIR é composta por aplicativos JavaScript de página única, você precisa [habilitar o CORS (compartilhamento de recursos entre origens)](configure-cross-origin-resource-sharing.md) para a API do Azure para FHIR:

![Seleções para habilitar CORS](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>Configurar a URL de resposta

O proxy do SMART on FHIR atua como um intermediário entre o aplicativo SMART on FHIR e o Azure AD. A resposta de autenticação (o código de autenticação) deve ir para o proxy do SMART on FHIR, em vez do próprio aplicativo. O proxy então encaminha a resposta para o aplicativo. 

Devido a essa retransmissão de duas etapas do código de autenticação, você precisa definir a URL de resposta (retorno de chamada) para seu aplicativo cliente do Azure AD para uma URL que seja uma combinação da URL de resposta para o proxy do SMART on FHIR e a URL de resposta para o aplicativo SMART on FHIR. A URL de resposta combinada tem este formato:

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

Nessa resposta, `aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA` é uma versão segura de URL, codificada em base64 da URL de resposta para o aplicativo SMART on FHIR. Para o inicializador de aplicativos SMART on FHIR, quando o aplicativo está em execução localmente, a URL de resposta é `https://localhost:5001/sampleapp/index.html`. 

Você pode gerar a URL de resposta combinada usando um script como este:

```PowerShell
$replyUrl = "https://localhost:5001/sampleapp/index.html"
$fhirServerUrl = "https://MYFHIRAPI.azurewebsites.net"
$bytes = [System.Text.Encoding]::UTF8.GetBytes($ReplyUrl)
$encodedText = [Convert]::ToBase64String($bytes)
$encodedText = $encodedText.TrimEnd('=');
$encodedText = $encodedText.Replace('/','_');
$encodedText = $encodedText.Replace('+','-');

$newReplyUrl = $FhirServerUrl.TrimEnd('/') + "/AadSmartOnFhirProxy/callback/" + $encodedText
```

Adicione a URL de resposta ao aplicativo cliente público que você criou anteriormente para o Azure AD:

![URL de resposta configurada para o cliente público](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>Obter um paciente de teste

Para testar a API do Azure para FHIR e o proxy do SMART on FHIR, você precisará ter pelo menos um paciente no banco de dados. Se você ainda não tiver interagido com a API e não tiver dados no banco de dados, siga o [tutorial do Postman da API do FHIR](access-fhir-postman-tutorial.md) para carregar um paciente. Anote a ID de um paciente específico.

## <a name="download-the-smart-on-fhir-app-launcher"></a>Baixe o inicializador de aplicativos SMART on FHIR

O [servidor FHIR para o repositório do Azure](https://github.com/Microsoft/fhir-server) de software livre inclui um inicializador de aplicativos SMART on FHIR simples e um aplicativo de exemplo SMART on FHIR. Neste tutorial, use esse iniciador do SMART on FHIR localmente para testar a instalação.

Você pode clonar o repositório do GitHub e ir para o aplicativo usando estes comandos:

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

O aplicativo precisa de algumas definições de configuração, que podem ser feitas em `appsettings.json`:

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

Recomendamos o uso do recurso `dotnet user-secrets`:

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

Use este comando para executar o aplicativo:

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>Testar o proxy do SMART on FHIR

Depois de iniciar o inicializador de aplicativos SMART on FHIR, você pode apontar seu navegador para `https://localhost:5001`, em que deverá ver a tela a seguir:

![Inicializador de aplicativos SMART on FHIR](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

Ao inserir informações de **Paciente**, **Encontrado** ou **Profissional**, você observará que o **Contexto de inicialização** é atualizado. Quando você está usando a API do Azure para FHIR, o contexto de inicialização é simplesmente um documento JSON que contém informações sobre pacientes, profissionais e muito mais. Esse contexto de inicialização é codificado em Base64 e passado para o aplicativo SMART on FHIR como o parâmetro de consulta `launch`. De acordo com a especificação do SMART on FHIR, essa variável é opaca para o aplicativo SMART on FHIR e passada para o provedor de identidade. 

O proxy do SMART on FHIR usa essas informações para preencher campos na resposta do token. O aplicativo SMART on FHIR *pode* usar esses campos para controlar para qual paciente ele solicita dados e como ele renderiza o aplicativo na tela. O proxy do SMART on FHIR dá suporte aos seguintes campos:

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

Esses campos destinam-se a fornecer orientação para o aplicativo, mas não transmitem nenhuma informação de segurança. Um aplicativo SMART on FHIR pode ignorá-los.

Observe que o inicializador de aplicativos SMART on FHIR atualiza as informações da **URL de inicialização** na parte inferior da página. Selecione **Iniciar** para iniciar o aplicativo de exemplo e você verá algo semelhante a este exemplo:

![Aplicativo SMART on FHIR](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

Inspecione a resposta do token para ver como os campos de contexto de inicialização são passados para o aplicativo.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou o proxy do SMART on FHIR do Azure Active Directory. Para explorar o uso de aplicativos SMART on FHIR com a API do Azure para FHIR e o servidor do FHIR de software livre para o Azure, acesse o repositório de exemplos de servidor do FHIR no GitHub:

>[!div class="nextstepaction"]
>[Exemplos de servidor FHIR](https://github.com/Microsoft/fhir-server-samples)
