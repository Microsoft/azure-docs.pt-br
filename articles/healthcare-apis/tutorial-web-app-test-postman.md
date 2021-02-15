---
title: Tutorial de aplicativo Web – Teste de conexão à API do Azure para FHIR
description: O tutorial apresenta um exemplo de implantação de um aplicativo Web simples. Esta seção do tutorial explica como testar a conexão com o servidor FHIR com o Postman
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 1c64468a2e420734ca51a5b9308bb52e13712c51
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023305"
---
# <a name="testing-the-fhir-api"></a>Como testar a API do FHIR
Nas duas etapas anteriores, você implantou a API do Azure para FHIR e registrou seu aplicativo cliente. Agora você está pronto para testar se a API do Azure para FHIR está configurada com o aplicativo cliente. 

## <a name="retrieve-capability-statement"></a>Recuperar declaração de capacidade
Primeiro, obteremos a declaração de capacidade para sua API do Azure para FHIR. 
1. Abra o Postman
1. Recupere a declaração de capacidade fazendo GET https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/metadata. Na imagem abaixo, o nome do servidor FHIR é **fhirserver**.

![Declaração de capacidade](media/tutorial-web-app/postman-capability-statement.png)

Em seguida, tentaremos recuperar um paciente. Para recuperar um paciente, insira GET https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/Patient. Você receberá um erro 401 Não autorizado. Esse erro ocorre porque você não comprovou que deve ter acesso aos dados do paciente.

## <a name="get-patient-from-fhir-server"></a>Obter paciente do servidor do FHIR
![Paciente com falha](media/tutorial-web-app/postman-patient-authorization-failed.png)

Para obter acesso, você precisa de um token de acesso.
1. No Postman, selecione **Autorização** e defina o tipo como **OAuth2.0**
1. Selecione **Obter Novo Token de Acesso**
1. Preencha os campos e selecione **Token de Solicitação**. Abaixo, você pode ver os valores de cada campo deste tutorial.

|Campo                |Valor                                                               |
|---------------------|--------------------------------------------------------------------|
|Nome do Token           |Um nome para o token                                               |
|Tipo de concessão           |Código de Autorização                                                  |
|URL de retorno de chamada         |https://www.getpostman.com/oauth2/callback                          |
|URL de autenticação             |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/?resource=https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com|
|URL do Token de Acesso     |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/token|
|ID do Cliente            |A ID do cliente que você copiou nas etapas anteriores             |
|Segredo do cliente        |\<BLANK>                                                            |
|Escopo                |\<BLANK>                                                            |
|Estado                |1234                                                                |
|Autenticação de cliente|Enviar credenciais do cliente no corpo                                     |

4. Entre com suas credenciais e selecione **Aceitar**
1. Role para baixo no resultado e selecione **Usar Token**
1. Selecione **Enviar** novamente na parte superior e, desta vez, você deverá obter um resultado ![Paciente com Sucesso](media/tutorial-web-app/postman-patient-authorization-success.png)

## <a name="post-patient-into-fhir-server"></a>Poste o paciente no servidor do FHIR
Agora que tem acesso, você pode criar um paciente. Aqui está um exemplo de um paciente simples que você pode adicionar ao servidor do FHIR. Insira o código abaixo na seção **Corpo** do Postman.

``` json
    {
        "resourceType": "Patient",
        "active": true,
        "name": [
            {
                "use": "official",
                "family": "Kirk",
                "given": [
                    "James",
                    "Tiberious"
                ]
            },
            {
                "use": "usual",
                "given": [
                    "Jim"
                ]
            }
        ],
        "gender": "male",
        "birthDate": "1960-12-25"
    }
```
Esta ação POST criará um paciente em seu servidor do FHIR com o nome James Tiberious Kirk.
![Postar Paciente](media/tutorial-web-app/postman-post-patient.png)

Se você executar a etapa GET acima para recuperar um paciente novamente, verá James Tiberious Kirk listado na saída.

## <a name="troubleshooting-access-issues"></a>Solução de problemas de acesso
Se você tiver problemas durante qualquer uma dessas etapas, examine os documentos que reunimos sobre o Azure Active Directory e a API do Azure para FHIR. 

* [Azure AD e API do Azure para FHIR](azure-ad-hcapi.md) – esse documento descreve alguns dos princípios básicos do Azure Active Directory e como ele interage com a API do Azure para FHIR.
* [Validação do token de acesso](azure-ad-hcapi-token-validation.md) – esse guia de instruções passo a passo apresenta detalhes mais específicos sobre a validação de token de acesso e as etapas a serem executadas para resolver problemas de acesso.

## <a name="next-steps"></a>Próximas etapas
Agora que você pode se conectar com êxito ao seu aplicativo cliente, está pronto para escrever seu aplicativo Web.

>[!div class="nextstepaction"]
>[Escrever um aplicativo Web](tutorial-web-app-write-web-app.md)



