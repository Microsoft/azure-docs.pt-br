---
title: "Adicionar um certificado ao repositório Java CA | Microsoft Docs"
description: "Saiba como adicionar um certificado de autoridade de certificação (AC) para o armazenamento de certificado CA (cacerts) do Java para serviço Twilio ou barramento de serviço do Azure."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: d3699b0a-835c-43fb-844d-9c25344e5cda
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: b6e1a305e19415ab1c4b4c208dac98ad1e2689c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Adicionar um certificado ao repositório de certificados Java CA
As etapas a seguir mostram como adicionar um certificado de autoridade de certificado (CA) para o armazenamento de certificados (cacerts) da autoridade de certificação de Java. O exemplo usado é para o certificado de autoridade de certificação exigido pelo serviço Twilio. Informações fornecidas posteriormente no tópico descrevem como instalar o certificado de autoridade de certificação para o Barramento de Serviço do Azure. 

Você pode usar o keytool para adicionar o certificado de autoridade de certificação antes de compactar seu JDK e adicionar a sua pasta **approot** do projeto do Azure ou executar uma tarefa de inicialização do Azure que usa keytool para adicionar o certificado. Este exemplo assume que você irá adicionar um certificado de autoridade de certificação antes do JDK ser compactado. Além disso, um certificado de autoridade de certificação específico será usado no exemplo, mas as etapas para obter um certificado de autoridade de certificação diferente e importar no repositório cacerts seriam semelhantes.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>Para adicionar um certificado ao repositório cacerts
1. Em um prompt de comando de Administrador definido como sua pasta **jdk\jre\lib\security** do JDK, execute o seguinte para ver quais certificados estão instalados:
   
    `keytool -list -keystore cacerts`
   
    Você será solicitado a digitar a senha de armazenamento. A senha padrão é **changeit**. (Se você quiser alterar a senha, consulte a documentação do keytool em <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.) Este exemplo assume que o certificado com impressão digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 não está listado e que você deseja importar (esse certificado específico é necessário para o serviço Twilio API).
2. Obtenha o certificado da lista de certificados em [Certificados de raiz de GeoTrust](http://www.geotrust.com/resources/root-certificates/). Clique com botão direito no link do certificado com o número de série 35:DE:F4:CF e salve na pasta **jdk\jre\lib\security**. Para fins deste exemplo, ele foi salvo em um arquivo chamado **Equifax\_Secure\_Certificate\_Authority.cer**.
3. Importe o certificado por meio do seguinte comando:
   
    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`
   
    Quando solicitado para confiar nesse certificado, se o certificado tiver impressão digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4, responda digitando **y**.
4. Execute o seguinte comando para garantir que o certificado de CA foi importado com êxito:
   
    `keytool -list -keystore cacerts`
5. Compacte o JDK e adicione a sua pasta **approot** do projeto do Azure.

Para obter mais informações sobre o keytool, consulte <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Certificados de Raiz do Azure
Os aplicativos que usam os serviços do Azure (por exemplo, o Service Bus do Azure) precisam confiar no certificado de Baltimore CyberTrust Root. (A partir de 15 de abril de 2013, o Azure começou migrando do GTE CyberTrust Global Root para o Baltimore CyberTrust Root. Essa migração levou vários meses para ser concluída.)

O certificado Baltimore já pode estar instalado no seu repositório cacerts, portanto, lembre-se de executar o comando **keytool-list** primeiro para ver se ele já existe.

Se você precisar adicionar o Baltimore CyberTrust Root, ele possui número de série 02:00:00:b9 e impressão digital SHA1 d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74. Ele pode ser baixado do <https://cacert.omniroot.com/bc2025.crt>, salvo em um arquivo local com extensão **.cer**, e importado usando o **keytool** conforme mostrado acima.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre os certificados raiz usados pelo Azure, consulte [Migração de Certificados raiz do Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

Para saber mais sobre Java, veja [Centro de desenvolvedores do Java](/java/azure).

