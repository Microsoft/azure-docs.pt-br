---
title: Usando o Axinom para fornecer licenças Widevine aos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo descreve como é possível usar os serviços de mídia do Azure (AMS) para oferecer um fluxo dinamicamente é criptografado pelo AMS com os DRMs do PlayReady e Widevine. A licença do PlayReady vem do servidor de licenças do PlayReady dos serviços de mídia e a licença do Widevine é fornecida pelo servidor de licença Axinom.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan;Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 6714beae690e23c686fc08b88e93044ae3901c89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61245014"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Usando o Axinom para fornecer licenças Widevine para os Serviços de Mídia do Azure 
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Visão geral
O AMS (Serviços de Mídia do Azure) adicionou a proteção dinâmica do Google Widevine (veja o [blog de Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) para obter detalhes). Além disso, o Player de Mídia do Azure (AMP) também adicionou suporte Widevine (consulte o [documento AMP](https://amp.azure.net/libs/amp/latest/docs/) para obter detalhes). Isso é uma grande conquista de streaming de conteúdo DASH protegido por CENC com DRM multinativo (PlayReady e Widevine) em navegadores modernos equipados com MSE e EME.

A partir da versão 3.5.2 do SDK do .NET dos Serviços de Mídia, os Serviços de Mídia permitem que você configure um modelo de licença do Widevine e obtenha licenças do Widevine. Você também pode usar os seguintes parceiros do AMS para ajudá-lo a fornecer licenças Widevine: [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Este artigo descreve como integrar e testar o servidor de licença Widevine gerenciado pelo Axinom. Especificamente, ele abrange:  

* A configuração dinâmica da Criptografia Comum com multi-DRM (PlayReady e Widevine) com URLs de aquisição da licença correspondente;
* A geração de um token JWT para atender aos requisitos do servidor de licença;
* Desenvolvendo um aplicativo do Player de Mídia do Azure que lida com a aquisição de licenças com autenticação de token JWT;

O sistema completo e o fluxo de chave de conteúdo, a ID da chave, a propagação de chave, o token JTW e suas declarações podem ser melhor descritos pelo diagrama a seguir:

![DASH e CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Proteção do conteúdo
Para configurar a proteção dinâmica e a política de entrega de chaves, confira o blog de Mingfei: [Como configurar o empacotamento Widevine com os Serviços de Mídia do Azure](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Você pode configurar a proteção CENC dinâmica com multi-DRM para o streaming DASH com o seguinte:

1. Proteção do PlayReady para o Microsoft Edge e o IE11, que pode ter uma restrição de autorização de token. A política restrita do token deve ser acompanhada por um token emitido por um Secure Token Service (STS), como o Active Directory do Azure.
2. Proteção do Widevine para o Chrome, pode exigir autenticação de token com um token emitido pelo outro STS. 

Consulte a seção [Geração de tokens JWT](media-services-axinom-integration.md#jwt-token-generation) para saber por que o Azure Active Directory não pode ser usado como um STS para o servidor de licença Widevine da Axinom.

### <a name="considerations"></a>Considerações
1. Você deve usar a propagação da chave especificada da Axinom (8888000000000000000000000000000000000000) e sua ID de chave gerada ou selecionada para gerar a chave de conteúdo para a configuração do serviço de entrega de chave. O servidor de licença Axinom emite todas as licenças com as chaves de conteúdo com base na mesma propagação de chave, que é válida para teste e produção.
2. A URL de aquisição de licença de Widevine para testes: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). HTTP e HTTS são permitidos.

## <a name="azure-media-player-preparation"></a>Preparação do Player de Mídia do Azure
O AMP v1.4.0 oferece suporte à reprodução de conteúdo AMS dinamicamente empacotado com o PlayReady e o Widevine DRM.
Se o servidor de licença Widevine não exigir autenticação de token, não haverá nada mais que deva ser feito para testar um conteúdo DASH protegido pelo Widevine. Por exemplo, a equipe AMP apresenta um [exemplo](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html) simples, em que você pode vê-lo funcionando no Microsoft Edge e no IE11 com o PlayReady e o Chrome com Widevine.
O servidor de licença do Widevine fornecido pela Axinom requer autenticação de token JWT. O token JWT precisa ser enviado com uma solicitação de licença por meio de um cabeçalho HTTP “X-AxDRM-Message”. Para essa finalidade, você precisa adicionar o seguinte javascript à página da Web que esteja hospedando o AMP antes de configurar a fonte:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

O restante do código AMP é a API AMP padrão como descrito no documento AMP [aqui](https://amp.azure.net/libs/amp/latest/docs/).

O javascript acima para a configuração do cabeçalho de autorização personalizado ainda é uma abordagem a curto prazo antes que a abordagem oficial a longo prazo seja lançada no AMP.

## <a name="jwt-token-generation"></a>Geração de tokens JWT
O servidor de licença Widevine da Axinom para testes requer a autenticação do token JWT. Além disso, uma das declarações no token de JWT é de um tipo de objeto complexo em vez de um tipo de dados primitivo.

Infelizmente, o AD do Azure só pode emitir tokens JWT com tipos primitivos. Da mesma forma, a API do .NET Framework (System.IdentityModel.Tokens.SecurityTokenHandler e JwtPayload) só permite que você insira o tipo de objeto complexo como declarações. No entanto, as declarações ainda são serializadas como cadeia de caracteres. Portanto, não podemos usar qualquer um dos dois para a geração do token JWT para a solicitação de licença Widevine.

O [pacote NuGet JWT](https://www.nuget.org/packages/JWT) de John Sheehan atende às necessidades e, portanto, usaremos esse pacote Nuget.

A seguir, o código para gerar o token JWT com as declarações necessárias como exigido pelo servidor de licença Widevine da Axinom para testes:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;

    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };

                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

                return token;
            }

            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }

                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }

                return HexAsBytes;
            }

        }  

    }  

Servidor de licença Widevine da Axinom

    <add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Considerações
1. Embora o serviço de entrega de licenças do PlayReady da AMS exija "Bearer=" antes de um token de autenticação, o servidor de licença Widevine da Axinom não o utiliza.
2. A chave de comunicação Axinom é usada como chave de assinatura. Observe que a chave é uma cadeia de caracteres hexadecimal, mas deve ser tratada como uma série de bytes e não como uma cadeia de caracteres durante a codificação. Isso é obtido pelo método ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Recuperando a ID da chave
Talvez você tenha notado que uma ID da chave é necessária no código para a geração de um token JWT. Como o token JWT precisa estar pronto antes do carregamento do player AMP, será necessário recuperar a ID da chave para gerar o token JWT.

É claro que há várias maneiras de obter a ID da chave. Por exemplo, uma pode armazenar ID da chave junto com os metadados de conteúdo em um banco de dados. Ou você pode recuperar a ID da chave do arquivo MPD (Media Presentation Description) DASH. O código a seguir é para a última opção.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }

        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();

        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }

        return key_id;
    }

## <a name="summary"></a>Resumo
Com a mais recente adição de suporte Widevine à Proteção de Conteúdo dos Serviços de Mídia do Azure e ao Player de Mídia do Azure, somos capazes de implementar o streaming de DASH mais DRM multinativo (PlayReady + Widevine) com o serviço de licença PlayReady no AMS e o servidor de licença Widevine da Axinom para os seguintes navegadores modernos:

* Chrome
* Microsoft Edge no Windows 10
* IE 11 no Windows 8.1 e no Windows 10
* O Firefox (Área de Trabalho) e o Safari no Mac (sem ser o iOS) também têm suporte via Silverlight e a mesma URL com o Player de Mídia do Azure

Os parâmetros a seguir são necessários na mini-solução que aproveita o servidor de licença Widevine da Axinom. Com exceção da ID da chave, os outros parâmetros são fornecidos pela Axinom com base na configuração do servidor Widevine.

| Parâmetro | Como ele é usado |
| --- | --- |
| ID da chave de comunicação |Deve ser incluído como valor de declaração "com_key_id" no token JWT (consulte [esta](media-services-axinom-integration.md#jwt-token-generation) seção). |
| Chave de comunicação |Deve ser usado como a chave de assinatura de token JWT (consulte [esta](media-services-axinom-integration.md#jwt-token-generation) seção). |
| Semente de chave |Deve ser usada para gerar a chave de conteúdo com qualquer ID de chave de conteúdo fornecida (consulte [esta](media-services-axinom-integration.md#content-protection) seção). |
| URL de aquisição de licença de Widevine |Deve ser usada na configuração de política de entrega de ativos para streaming de DASH (consulte [esta](media-services-axinom-integration.md#content-protection) seção). |
| ID de chave de conteúdo |Deve ser incluída como parte do valor da declaração da Mensagem de Qualificação do token JWT (consulte [esta](media-services-axinom-integration.md#jwt-token-generation) seção). |

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Agradecimentos
Gostaríamos de agradecer às seguintes pessoas que contribuíram para a criação deste documento: Kristjan Jõgi da Axinom, Mingfei Yan e Amit Rajput.

