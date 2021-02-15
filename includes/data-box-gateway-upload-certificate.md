---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 27a4d775b8d88e02be69655e5adfc6155f867ef6
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581481"
---
Um certificado SSL adequado garante que você esteja enviando informações criptografadas para o servidor correto. Além da criptografia, o certificado também permite a autenticação. Você pode carregar seu próprio certificado SSL confiável por meio da interface do PowerShell do dispositivo.

1. [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. Use o `Set-HcsCertificate` cmdlet para carregar o certificado. Quando solicitado, forneça os seguintes parâmetros:

   - `CertificateFilePath` -Caminho para o compartilhamento que contém o arquivo de certificado no formato *. pfx* .
   - `CertificatePassword` -Uma senha usada para proteger o certificado.
   - `Credentials` -Username para acessar o compartilhamento que contém o certificado. Forneça a senha para o compartilhamento de rede quando solicitado.

     O exemplo a seguir mostra o uso desse cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```
