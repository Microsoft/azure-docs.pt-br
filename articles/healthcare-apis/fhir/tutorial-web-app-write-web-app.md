---
title: Tutorial do aplicativo Web – escrever o aplicativo Web
description: Este tutorial apresenta um exemplo de implantação de um aplicativo Web simples. Esta seção do tutorial mostra como escrever o aplicativo Web.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bb9c206a17a11f0cf710ac4ee3ac7ccae0fa9eb9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017713"
---
# <a name="write-azure-web-application-to-read-fhir-data"></a>Escrever o aplicativo Web do Azure para ler dados do FHIR
Agora que você é capaz de se conectar ao seu servidor do FHIR e postar dados, está pronto para escrever um aplicativo Web que lerá os dados do FHIR. Nesta etapa final do tutorial, veremos como escrever e acessar o aplicativo Web.

## <a name="create-web-application"></a>Criar o aplicativo Web
No Azure, selecione **Criar um recurso** e **Aplicativo Web**. Dê a seu aplicativo Web qualquer nome que você tenha especificado o URI redirecionamento para seu aplicativo cliente ou volte e atualize o URI de redirecionamento com o novo nome. 

![Criar o Aplicativo Web](media/tutorial-web-app/create-web-app.png)

Quando o aplicativo Web estiver disponível, escolha **Ir para o recurso**. Selecione **Editor do Serviço de Aplicativo (versão prévia)** em Ferramentas de Desenvolvimento à direita e, em seguida, selecione **Ir**. Selecionar Ir abrirá o Editor do Serviço de Aplicativo. Clique com o botão direito do mouse no espaço cinza em *Explorar* e crie um arquivo chamado **index.html**.

Abaixo está o código que você pode inserir em **index.html**. Você precisará atualizar os seguintes itens:
* **clientId** – atualize com a ID do aplicativo cliente. Essa ID será a mesma recebida ao recuperar seu token
* **autoridade** – atualize com sua ID de locatário do Azure AD
* **FHIRendpoint** – atualize o FHIRendpoint para ter o nome do serviço FHIR
* **escopos** – atualize para refletir a URL completa de seu público-alvo

``` HTML

<!DOCTYPE html>
<html>

<head>
    <title>FHIR Patient browser sample app</title>
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
</head>

<body>
    <div class="leftContainer">
        <p id="WelcomeMessage">Welcome to the FHIR Patient browsing sample Application</p>
        <button id="SignIn" onclick="signIn()">Sign In</button>
    </div>

    <div id="patientTable">
    </div>

    <script>
        var msalConfig = {
            auth: {
                clientId: '<CLIENT-ID>',
                authority: "https://login.microsoftonline.com/<AZURE-AD-TENANT-ID>"
            },
            cache: {
                cacheLocation: "localStorage",
                storeAuthStateInCookie: true
            }
        }

        var FHIRConfig = {
            FHIRendpoint: "https://<FHIR-SERVER-NAME>.azurehealthcareapis.com"
        }
        var requestObj = {
            scopes: ["https://<FHIR-SERVER-NAME>.azurehealthcareapis.com/user_impersonation"]
        }

        function authRedirectCallBack(error, response) {
            if (error) {
                console.log(error);
            } else {
                if (response.tokenType === "access_token") {
                    callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, response.accessToken, FHIRCallback);
                }
            }
        }

        var myMSALObj = new Msal.UserAgentApplication(msalConfig);
        myMSALObj.handleRedirectCallback(authRedirectCallBack);

        function signIn() {
            myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
                showWelcomeMessage();
                acquireTokenPopupAndCallFHIRServer();
            }).catch(function (error) {
                console.log(error);
            })
        }

        function showWelcomeMessage() {
            var divWelcome = document.getElementById('WelcomeMessage');
            divWelcome.innerHTML = "Welcome " + myMSALObj.getAccount().userName + " to FHIR Patient Browsing App";
            var loginbutton = document.getElementById('SignIn');
            loginbutton.innerHTML = 'Sign Out';
            loginbutton.setAttribute('onclick', 'signOut()')
        }

        function signOut() {
            myMSALObj.logout();
        }

        function acquireTokenPopupAndCallFHIRServer() {
            myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
                callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
            }).catch(function (error) {
                console.log(error);
                if (requiresInteraction(error.errorCode)) {
                    myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                        callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
                    }).catch(function (error) {
                        console.log(error);
                    })
                }
            });
        }

        function callFHIRServer(theUrl, method, message, accessToken, callBack) {
            var xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200)
                    callBack(JSON.parse(this.responseText));
            }
            xmlHttp.open(method, theUrl, true);
            xmlHttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
            xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
            xmlHttp.send(message);
        }

        function FHIRCallback(data) {
            patientListHtml = '<ol>';
            data.entry.forEach(function(e) {
                patientListHtml += '<li>' + e.resource.name[0].family + ', ' + e.resource.name[0].given + ' (' + e.resource.id + ')';
            });
            patientListHtml += '</ol>';
            document.getElementById("patientTable").innerHTML = patientListHtml;
        }
    </script>
</body>

</html>
```

Daqui, você pode voltar para o recurso de aplicativo Web e abrir a URL encontrada na página Visão Geral. Faça logon para ver o paciente James Tiberious Kirk que você criou anteriormente.

## <a name="next-steps"></a>Próximas etapas
Você implantou com êxito a API do Azure para FHIR, registrou um aplicativo cliente público, testou o acesso e criou um pequeno aplicativo Web. Confira os recursos compatíveis da API do Azure para FHIR como uma próxima etapa.

>[!div class="nextstepaction"]
>[Recursos compatíveis](fhir-features-supported.md)





