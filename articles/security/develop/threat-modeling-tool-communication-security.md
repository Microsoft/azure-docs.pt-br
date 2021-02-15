---
title: Segurança de comunicação para o Microsoft Threat Modeling Tool
titleSuffix: Azure
description: Saiba mais sobre a mitigação de ameaças de segurança de comunicação expostas no Threat Modeling Tool. Veja informações de mitigação e exiba exemplos de código.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: d9a4eabf37101622ac69ae05f3bec232fb8d2fe6
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517522"
---
# <a name="security-frame-communication-security--mitigations"></a>Estrutura de segurança: Segurança de comunicações | Atenuações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Hub de Eventos do Azure** | <ul><li>[Proteger comunicações para o Hub de Eventos usando SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Verificar os privilégios da conta de serviço e verificar se os serviços personalizados ou as páginas ASP.NET respeitam a segurança do CRM](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Usar o gateway de gerenciamento de dados ao conectar SQL Server locais a Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Servidor de identidade** | <ul><li>[Verifique se todo o tráfego para o servidor de identidade está por conexão HTTPS](#identity-https)</li></ul> |
| **Aplicativo Web** | <ul><li>[Verifique os certificados X. 509 usados para autenticar conexões SSL, TLS e DTLS](#x509-ssltls)</li><li>[Configurar o certificado TLS/SSL para o domínio personalizado no serviço de Azure App](#ssl-appservice)</li><li>[Forçar todo o tráfego para o Serviço de Aplicativo do Azure pela conexão HTTPS](#appservice-https)</li><li>[Habilitar HSTS (Segurança de Transporte Estrito HTTP)](#http-hsts)</li></ul> |
| **Backup de banco de dados** | <ul><li>[Garantir a criptografia de conexão do SQL Server e a validação de certificado](#sqlserver-validation)</li><li>[Forçar a comunicação criptografada com o SQL Server](#encrypted-sqlserver)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Garantir que a comunicação com o armazenamento do Azure seja por HTTPS](#comm-storage)</li><li>[Validar o hash MD5 depois de baixar o blob se o HTTPS não puder ser habilitado](#md5-https)</li><li>[Usar o cliente compatível com SMB 3,0 para garantir a criptografia de dados em trânsito para compartilhamentos de arquivos do Azure](#smb-shares)</li></ul> |
| **Cliente móvel** | <ul><li>[Implementar a anexação de certificado](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Habilitar HTTPS - proteger o canal de transporte](#https-transport)</li><li>[WCF: definir o nível de proteção da segurança de mensagens como EncryptAndSign](#message-protection)</li><li>[WCF: use uma conta com privilégios mínimos para executar o serviço WCF](#least-account-wcf)</li></ul> |
| **API da Web** | <ul><li>[Forçar todo o tráfego para APIs Web pela conexão HTTPS](#webapi-https)</li></ul> |
| **Cache Redis do Azure** | <ul><li>[Garantir que a comunicação com o cache do Azure para Redis seja por TLS](#redis-ssl)</li></ul> |
| **Gateway de Campo de IoT** | <ul><li>[Proteger dispositivo para comunicações do Gateway de Campo](#device-field)</li></ul> |
| **Gateway de Nuvem IoT** | <ul><li>[Proteger o dispositivo para comunicações do Gateway de Nuvem usando SSL/TLS](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Proteger comunicações para o Hub de Eventos usando SSL/TLS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e segurança dos Hubs de Eventos](../../event-hubs/authenticate-shared-access-signature.md) |
| **Etapas** | Proteja conexões AMQP ou HTTP com o Hub de Eventos usando SSL/TLS |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Verificar se os privilégios da conta do serviço e verificar se os serviços ou páginas ASP.NET personalizados respeitam a segurança do CRM

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Verifique se os privilégios da conta do serviço e verifique se os serviços ou páginas ASP.NET personalizados respeitam a segurança do CRM. |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Usar o gateway de gerenciamento de dados ao conectar SQL Server locais a Azure Data Factory

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Fábrica de dados do Azure | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Tipos de serviço vinculados-Azure e local |
| **Referências**              |[Movendo dados entre o local e o Azure data Factory](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-gateway), o [Gateway de gerenciamento de dados](../../data-factory/v1/data-factory-data-management-gateway.md) |
| **Etapas** | <p>A ferramenta Gateway de Gerenciamento de Dados (DMG) é necessária para conectar fontes de dados protegidas por um firewall ou pela rede corporativa.</p><ol><li>Blindar a máquina isola a ferramenta DMG e evita que programas com defeito danifiquem ou espionem o computador da fonte de dados. Por exemplo, instalando das atualizações mais recentes, habilitando o mínimo necessário de portas, provisionando contas controladas, habilitando a auditoria, habilitando a criptografia de disco, etc.</li><li>A chave do Gateway de Dados precisa ser trocada em intervalos frequentes ou sempre que a senha da conta do serviço do DMG for renovada.</li><li>O tráfego de dados pelo serviço de vinculação deve ser criptografado.</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Garantir que todo o tráfego para o servidor de identidade passe pela conexão HTTPS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidade | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [IdentityServer3 - chaves, assinaturas e criptografia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 - implantação](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Etapas** | Por padrão, o IdentityServer exige que todas as conexões de entrada sejam feitas via HTTPS. É absolutamente obrigatório que a comunicação com IdentityServer seja feita apenas transportes protegidos. Há alguns cenários de implantação, como o descarregamento de TLS, onde esse requisito pode ser relaxado. Consulte a página de implantação do Servidor de identidade indicada nas referências para obter mais informações. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Verificar os certificados x. 509 usados para autenticar conexões SSL, TLS e DTLS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Os aplicativos que usam SSL, TLS ou DTLS devem verificar os certificados X.509 das entidades a que se conectarem. Isso inclui a verificação dos certificados para:</p><ul><li>Nome de domínio</li><li>Datas de validade (datas de início e vencimento)</li><li>Status de revogação</li><li>Uso (por exemplo, autenticação de servidor para servidores de autenticação de cliente para clientes)</li><li>Cadeia de confiança (os certificados devem estar vinculados a uma autoridade de certificação (CA) raiz de confiança da plataforma ou serem configurados explicitamente pelo administrador)</li><li>O comprimento da chave pública do certificado deve ser maior que 2.048 bits</li><li>Algoritmo de hash deve ser SHA256 e superior |

## <a name="configure-tlsssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>Configurar o certificado TLS/SSL para o domínio personalizado no serviço de Azure App

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Tipo de ambiente - Azure |
| **Referências**              | [Habilitar HTTPS para um aplicativo no Serviço de Aplicativo do Azure](../../app-service/configure-ssl-bindings.md) |
| **Etapas** | Por padrão, o Azure já habilita o HTTPS para todos os aplicativos com um certificado curinga para o domínio *.azurewebsites.net. No entanto, assim como todos os domínios curinga, ele não é tão seguro quanto usar um domínio personalizado com seu próprio certificado. [Consulte este artigo](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). É recomendável habilitar o TLS para o domínio personalizado no qual o aplicativo implantado será acessado|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Forçar todo o tráfego para o Serviço de Aplicativo do Azure pela conexão HTTPS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Tipo de ambiente - Azure |
| **Referências**              | [Impor HTTPS no Serviço de Aplicativo do Azure](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Etapas** | <p>Embora os Serviços de Aplicativos do Azure já habilite o HTTPS com um certificado curinga para o domínio *.azurewebsites.net, ele não impõe o HTPPS. Os visitantes podem continuar acessando o aplicativo com o HTTP, possibilitando que a segurança do aplicativo seja comprometida, por isso o HTTPS deve ser explicitamente imposto. Os aplicativos do ASP.NET MVC devem usar o [filtro RequireHttps](/dotnet/api/system.web.mvc.requirehttpsattribute) que obriga uma solicitação HTTP desprotegida a ser reenviada pelo HTTPS.</p><p>O módulo de Reescrita de URL, incluído no Serviço de Aplicativo do Azure, também pode ser usado para impor o HTTPS. Esse módulo permite que desenvolvedores definam as regras aplicadas às solicitações recebidas antes que elas cheguem ao aplicativo. As regras de Reescrita de URL são definidas em um arquivo web.config, que fica armazenado na raiz do aplicativo.</p>|

### <a name="example"></a>Exemplo
O exemplo abaixo contém uma regra básica de Reescrita de URL que força todo o tráfego de entrada a usar o protocolo HTTPS.
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Essa regra funciona retornando um código de status de protocolo HTTP 301 (redirecionamento permanente) quando o usuário solicita uma página usando o protocolo HTTP. O 301 redireciona a solicitação para a mesma URL solicitada pelo visitante, mas substitui a parte do protocolo HTTP da solicitação com o protocolo HTTPS. Por exemplo, `HTTP://contoso.com` seria redirecionado para `HTTPS://contoso.com`. 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>Habilitar HSTS (Segurança de Transporte Estrito HTTP)

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Folha de dados do OWASP sobre Segurança de Transporte Estrito HTTP](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) |
| **Etapas** | <p>A Segurança de Transporte Estrito HTTP (HSTS) é um aperfeiçoamento de segurança opcional, que é especificado por um aplicativo Web por meio de um cabeçalho de resposta especial. Depois que um navegador em conformidade recebe o cabeçalho, esse navegador impede que todas as comunicações sejam enviadas pelo HTTP para o domínio especificado e envia todas as comunicações pelo HTTPS. Ele também impede cliques via HTTPS em prompts de navegadores.</p><p>Para implementar o HSTS, o cabeçalho de resposta a seguir deve ser configurado para um site globalmente, seja no código ou na configuração. Strict-Transport-Security: Max-age = 300; includeSubDomains HSTS aborda as seguintes ameaças:</p><ul><li>O usuário marca ou digita `https://example.com` e fica sujeito a um ataque man-in-the-middle: a HSTS automaticamente redireciona as solicitações de HTTP para HTTPS para o domínio de destino</li><li>O aplicativo da Web que se destina a ser puramente HTTPS acaba contendo links HTTP ou envia pelo HTTP: a HSTS automaticamente redireciona as solicitações de HTTP para HTTPS para o domínio de destino.</li><li>Um ataque MITM tenta interceptar o tráfego de um usuário utilizando um certificado inválido e espera que o usuário aceite o certificado incorreto: a HSTS não permite que um usuário substitua a mensagem de certificado inválido.</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Garantir a validação de certificado e a criptografia da conexão do SQL Server

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | SQL Azure  |
| **Atributos**              | Versão do SQL - V12 |
| **Referências**              | [Práticas recomendadas para escrever cadeias de caracteres de conexão seguras para o banco de dados SQL](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Etapas** | <p>Todas as comunicações entre o banco de dados SQL e um aplicativo cliente são criptografadas usando TLS (segurança de camada de transporte), anteriormente conhecido como protocolo SSL (SSL), em todos os momentos. O banco de dados SQL não dá suporte a conexões não criptografadas. Para validar certificados com o código do aplicativo ou as ferramentas, solicite explicitamente uma conexão criptografada e não confie nos certificados do servidor. Se o código ou as ferramentas do aplicativo não solicitam uma conexão criptografada, eles continuarão receberão conexões criptografadas.</p><p>No entanto, eles não podem validar certificados de servidor e, portanto, ficarão suscetíveis a ataques de "man in the middle". Para validar certificados com o código de aplicativo ADO.NET, defina `Encrypt=True` e `TrustServerCertificate=False` na cadeia de conexão de banco de dados. Para validar certificados por meio do SQL Server Management Studio, abra a caixa de diálogo Conectar ao Servidor. Na guia Propriedades da Conexão, clique em Criptografar conexão.</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Forçar a comunicação criptografada para o SQL Server

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | OnPrem |
| **Atributos**              | Versão do SQL - MsSQL2016, Versão do SQL - MsSQL2012, Versão do SQL - MsSQL2014 |
| **Referências**              | [Habilitar conexões criptografadas com o Mecanismo de Banco de Dados](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)  |
| **Etapas** | A habilitação da criptografia TLS aumenta a segurança dos dados transmitidos entre redes entre instâncias de SQL Server e aplicativos. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Garantir que as comunicações para o Armazenamento do Azure sejam feitas via HTTPS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Criptografia no nível do transporte do Armazenamento do Azure – usando HTTPS](../../storage/blobs/security-recommendations.md#networking) |
| **Etapas** | Para garantir a segurança dos dados do Armazenamento do Azure que estão em trânsito, use sempre o protocolo HTTPS ao chamar APIs REST ou acessar objetos no armazenamento. Além disso, as Assinaturas de Acesso Compartilhado, que podem ser usadas para delegar acesso a objetos do Armazenamento do Azure, incluem uma opção para especificar que apenas o protocolo HTTPS seja utilizado com as Assinaturas de Acesso Compartilhado, garantindo que qualquer pessoa que envie links com tokens SAS usará o protocolo adequado.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>Validar o hash MD5 depois de baixar o blob se o HTTPS não puder ser habilitado

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | StorageType - Blob |
| **Referências**              | [Visão geral da MD5 do Blob do Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Etapas** | <p>O serviço Blob do Microsoft Azure fornece mecanismos para garantir a integridade dos dados no aplicativo e nas camadas de transporte. Se, por algum motivo, você precisar usar o HTTP em vez do HTTPS e estiver trabalhando com blobs de bloco, você poderá usar a verificação MD5 para ajudar a verificar a integridade dos blobs que estão sendo transferidos.</p><p>Isso ajudará na proteção contra erros na camada de rede/transporte, mas não necessariamente contra ataques de intermediários. Se você puder usar HTTPS, que fornece segurança em nível de transporte, o uso da verificação MD5 será redundante e desnecessário.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Usar um cliente compatível com SMB 3.0 para garantir a criptografia de dados em trânsito para os compartilhamentos de arquivos do Azure

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Tipo de armazenamento - Arquivo |
| **Referências**              | [Armazenamento de Arquivos do Azure](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Suporte do Armazenamento de Arquivos do Azure para SMB em clientes Windows](../../storage/files/storage-dotnet-how-to-use-files.md#understanding-the-net-apis) |
| **Etapas** | O Armazenamento de Arquivos do Azure é compatível com HTTPS quando se usa a API REST, mas é mais frequentemente usado como um compartilhamento de arquivos SMB conectado a uma VM. O SMB 2.1 não é compatível com a criptografia, de modo que as conexões só são permitidas dentro da mesma região no Azure. No entanto, o SMB 3.0 é compatível com a criptografia e pode ser usado com o Windows Server 2012 R2, o Windows 8, o Windows 8.1 e o Windows 10, permitindo o acesso entre regiões e até mesmo o acesso à área de trabalho. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Implementar a anexação de certificado

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico, Windows Phone |
| **Atributos**              | N/D  |
| **Referências**              | [Anexação de chave pública e certificado](https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning) |
| **Etapas** | <p>A anexação de certificado protege contra ataques MITM (Man-In-The-Middle). A anexação é o processo de associar um host com sua chave pública ou seu certificado X509 esperado. Depois que um certificado ou uma chave pública forem conhecidas ou vistos para um host, o certificado ou a chave pública serão associados ou “anexados” ao host. </p><p>Assim, quando um adversário tenta realizar um ataque de MITM TLS, durante o handshake de TLS, a chave do servidor do invasor será diferente da chave do certificado fixado e a solicitação será descartada, impedindo que a fixação do certificado MITM possa ser obtida com a implementação do `ServerCertificateValidationCallback` delegado do ServicePointManager.</p>|

### <a name="example"></a>Exemplo
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>Habilitar HTTPS - proteger o canal de transporte

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Etapas** | A configuração do aplicativo deve garantir que o HTTPS seja usado para todos os acessos a informações confidenciais.<ul><li>**EXPLICAÇÃO:** se um aplicativo contiver informações confidenciais e não usar a criptografia no nível da mensagem, ele só poderá se comunicar por meio de um canal de transporte criptografado.</li><li>**RECOMENDAÇÕES:** verifique se o transporte HTTP está desabilitado e habilite o transporte HTTPS em vez disso. Por exemplo, substitua `<httpTransport/>` pela marcação `<httpsTransport/>`. Não confie em uma configuração de rede (firewall) para garantir que o aplicativo seja acessado apenas por um canal seguro. Do ponto de vista filosófico, o aplicativo não deve depender da rede para garantir sua segurança.</li></ul><p>Do ponto de vista prático, as pessoas responsáveis pela segurança da rede nem sempre monitoram os requisitos de segurança do aplicativo à medida que eles evoluem.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: definir o nível de proteção da segurança de mensagens como EncryptAndSign

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff650862(v=pandp.10)) |
| **Etapas** | <ul><li>**EXPLICAÇÃO:** quando o nível de proteção estiver definido como "none", a proteção de mensagem será desabilitada. A confidencialidade e a integridade dos dados só são conquistadas com o nível apropriado de configuração.</li><li>**RECOMMENDATIONS**<ul><li>`Mode=None` - desabilita a proteção de mensagem</li><li>`Mode=Sign` - assina, mas não criptografa a mensagem (deve ser usada quando a integridade dos dados for importante)</li><li>`Mode=EncryptAndSign` - assina e criptografa a mensagem</li></ul></li></ul><p>Considere desativar a criptografia e apenas assinar uma mensagem quando você só precisar validar a integridade das informações sem se preocupar com sua confidencialidade. Isso pode ser útil para operações ou contratos de serviço nos quais você precisa validar o remetente original, mas que não transmitem dados confidenciais. Ao reduzir o nível de proteção, tenha cuidado para que a mensagem não contenha dados pessoais.</p>|

### <a name="example"></a>Exemplo
A configuração do serviço e a operação de apenas assinar a mensagem são mostradas nos exemplos aabaixo. Exemplo de contrato de serviço de `ProtectionLevel.Sign`: o exemplo abaixo mostra o uso de ProtectionLevel.Sign no nível do contrato de serviço. 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Exemplo
Exemplo de contrato de operação de `ProtectionLevel.Sign` (para um controle granular): veja abaixo um exemplo do uso de `ProtectionLevel.Sign` no nível do contrato de operação.

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: use uma conta com privilégios mínimos para executar o serviço WCF

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff648826(v=pandp.10)) |
| **Etapas** | <ul><li>**EXPLICAÇÃO:** não execute serviços do WCF usando contas de administrador ou com privilégios altos. Se os serviços forem comprometidos, isso pode ter um impacto alto.</li><li>**RECOMENDAÇÕES:** use uma conta com privilégios mínimos para hospedar o serviço WCF, porque isso reduz a superfície de ataque do aplicativo e os potenciais danos causados se houver um ataque. Se a conta do serviço exigir direitos de acesso adicionais nos recursos de infraestrutura, como MSMQ, o log de eventos, contadores de desempenho e o sistema de arquivos, as permissões apropriadas para esses recursos devem ser concedidas, para que o serviço WCF seja executado êxito.</li></ul><p>Se o serviço precisar acessar recursos específicos em nome do chamador original, use a representação e a delegação, para que a identidade do chamador passe por uma verificação de autorização de downstream. Em um cenário de desenvolvimento, use a conta de serviço da rede local, que é uma conta interna especial com privilégios limitados. Em um cenário de produção, crie uma conta de serviço de domínio personalizada com privilégios mínimos.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Forçar todo o tráfego para APIs Web pela conexão HTTPS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Impondo o SSL em um controlador de API da Web](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Etapas** | Se um aplicativo tiver uma associação HTTPS e HTTP, os clientes poderão usar o HTTP para acessar o site. Para impedir isso, use um filtro de ação para garantir que as solicitações para APIs protegidas serão sempre feitas via HTTPS.|

### <a name="example"></a>Exemplo 
O código a seguir mostra um filtro de autenticação de API Web que verifica o TLS: 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Adicione este filtro a qualquer ação da API Web que exija TLS: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-tls"></a><a id="redis-ssl"></a>Garantir que a comunicação com o cache do Azure para Redis seja por TLS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cache Redis do Azure | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Suporte do Azure Redis TLS](../../azure-cache-for-redis/cache-faq.md) |
| **Etapas** | O servidor Redis não dá suporte ao TLS pronto para uso, mas o cache do Azure para Redis. Se você estiver se conectando ao Cache do Azure para Redis, e seu cliente oferecer suporte a TLS, como StackExchange.Redis, você deverá usar TLS. Por padrão, a porta não TLS está desabilitada para o novo cache do Azure para instâncias de Redis. Certifique-se de que os padrões seguros não sejam alterados, a menos que haja uma dependência no suporte a TLS para clientes Redis. |

Observe que o Redis foi projetado para ser acessado por clientes confiáveis em ambientes confiáveis. Isso significa que normalmente não é uma boa ideia expor a instância do Redis diretamente à Internet ou, de uma forma geral, a um ambiente onde clientes não confiáveis podem acessar diretamente o soquete UNIX ou a porta TCP do Redis. 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Proteger dispositivo para comunicações do Gateway de Campo

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Campo de IoT | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Para dispositivos baseados em IP, o protocolo de comunicação poderia ser encapsulado em um canal SSL/TLS para proteger os dados em trânsito. Para outros protocolos que não oferecem suporte a SSL/TLS, verifique se há versões protegidas do protocolo que fornecem segurança na camada de transporte ou de mensagens. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Proteger o dispositivo para comunicações do Gateway de Nuvem usando SSL/TLS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Nuvem IoT | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Escolha seu protocolo de comunicação](../../iot-hub/iot-hub-devguide.md) |
| **Etapas** | Proteja os protocolos HTTP/AMQP ou MQTT usando SSL/TLS. |