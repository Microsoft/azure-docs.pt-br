---
title: "Como configurar a autenticação mútua TLS para Aplicativo Web"
description: "Saiba como configurar o aplicativo Web para usar a autenticação de certificado do cliente no TLS."
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: naziml
ms.openlocfilehash: db69852cffd1ff331ac4a640b04ea4360d00bf75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>Como configurar a autenticação mútua TLS para Aplicativo Web
## <a name="overview"></a>Visão geral
Você pode restringir o acesso ao aplicativo Web do Azure, permitindo diferentes tipos de autenticação para ele. Uma maneira de fazer isso é autenticar usando um certificado de cliente quando a solicitação for por TLS/SSL. Esse mecanismo é chamado de autenticação mútua TLS ou autenticação de certificado do cliente, e este artigo mostra detalhadamente como configurar o aplicativo Web para usar a autenticação de certificado do cliente.

> **Observação:** se acessar seu site via HTTP e não HTTPS, você não receberá nenhum certificado de cliente. Por isso, se seu aplicativo exigir certificados de cliente, você não deve permitir solicitações ao seu aplicativo via HTTP.
> 
> 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="configure-web-app-for-client-certificate-authentication"></a>Configurar o aplicativo Web para autenticação de certificado do cliente
Para que seu aplicativo web exija certificados do cliente, você precisa adicionar a definição de site clientCertEnabled para seu aplicativo Web e defini-lo como true. Atualmente, essa configuração não está disponível por meio da experiência de gerenciamento no Portal e será necessário usar a API REST para isso.

Você pode usar a [ferramenta ARMClient](https://github.com/projectkudu/ARMClient) para facilitar a chamada à API REST. Depois de fazer logon com a ferramenta, você precisará emitir o seguinte comando:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose

substituindo tudo que está entre {} pelas informações do seu aplicativo Web e criando um arquivo chamado enableclientcert.json com o seguinte conteúdo JSON:

    {
        "location": "My Web App Location",
        "properties": {
            "clientCertEnabled": true
        }
    }

Não se esqueça de alterar o valor de “location” em todo lugar que o aplicativo Web estiver localizado; por exemplo, região central do norte dos EUA ou oeste dos EUA.

Você também pode usar https://resources.azure.com para inverter a propriedade `clientCertEnabled` para `true`.

> **Observação:** se você executar o ARMClient no Powershell, precisará usar o símbolo @ como o caractere de escape para o arquivo JSON com um acento grave `.
> 
> 

## <a name="accessing-the-client-certificate-from-your-web-app"></a>Acessando o certificado do cliente do aplicativo Web
Se você estiver usando ASP.NET e configurar seu aplicativo para usar a autenticação de certificado de cliente, o certificado estará disponível por meio da propriedade **HttpRequest.ClientCertificate** . Para outras pilhas de aplicativo, o certificado do cliente estará disponível no seu aplicativo por meio de um valor codificado na base64 no cabeçalho da solicitação "X-ARR-ClientCert". O aplicativo pode criar um certificado a partir desse valor e, em seguida, usá-lo para fins de autenticação e autorização.

## <a name="special-considerations-for-certificate-validation"></a>Considerações especiais para validação de certificado
O certificado do cliente que é enviado ao aplicativo não passa por qualquer validação da plataforma de aplicativos da Web do Azure. Validar o certificado é de responsabilidade do aplicativo Web. Veja o exemplo de código ASP.NET que valida as propriedades do certificado para fins de autenticação.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
