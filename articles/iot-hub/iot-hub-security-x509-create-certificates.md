---
title: Como usar o PowerShell para criar certificados X.509 | Microsoft Docs
description: "Como usar o PowerShell para criar certificados X.509 localmente e habilitar a segurança baseada em X.509 em seu Hub IoT do Azure em um ambiente simulado."
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: dkshir
ms.openlocfilehash: 31f94686fed376fbeda2ccdcbc5ed001bcda8126
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>Scripts do PowerShell para gerenciar certificados X.509 assinados pela AC

A segurança baseada em certificado X.509 no Hub IoT exige que você comece com uma [cadeia de certificados X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), que inclui o certificado raiz, bem como os certificados intermediários até o certificado de folha. Esse guia *Prático* o orienta através de scripts do PowerShell de exemplo que usam [OpenSSL](https://www.openssl.org/) para criar e assinar certificados X.509. Recomendamos que você use este guia apenas para experimentação, pois muitas dessas etapas ocorrerão durante o processo de fabricação do mundo real. Você pode usar esses certificados para simular a segurança em seu Hub IoT do Azure usando a *Autenticação de Certificado X.509*. As etapas neste guia criam certificados localmente no seu computador Windows. 

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial presume que você adquiriu os binários do OpenSSL. Você pode
    - baixar o código-fonte do OpenSSL e criar os binários em seu computador ou 
    - baixar e instalar todos os [binários do OpenSSL de terceiros](https://wiki.openssl.org/index.php/Binaries), por exemplo, [deste projeto no SourceForge](https://sourceforge.net/projects/openssl/).

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>Criar certificados X.509
As etapas a seguir mostram um exemplo de como criar certificados raiz X.509 localmente. 

1. Abra uma janela do PowerShell como *Administrador*. 
2. Navegue até seu diretório de trabalho. Execute o script a seguir para definir as variáveis globais. 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertSubject         = "CN=Azure IoT Root CA"
    $_intermediateCertSubject = "CN=Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. Execute o script a seguir que copia os binários do OpenSSL para seu diretório de trabalho e define as variáveis de ambiente:

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. Em seguida, execute o script a seguir que pesquisa se um certificado pelo *Nome da entidade* especificado já está instalado e se o OpenSSL está configurado corretamente em seu computador:
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    Se tudo estiver configurado corretamente, você deverá ver uma mensagem de "Êxito". 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>Criar cadeia de certificados X.509
Crie uma cadeia de certificados com uma AC raiz, por exemplo, "CN = Azure IoT Root CA" que este exemplo usa, executando o seguinte script do PowerShell. Esse script também atualiza o repositório de certificados do sistema operacional Windows e cria arquivos de certificado no diretório de trabalho. 
    1. O script a seguir cria uma função do PowerShell para criar um certificado autoassinado, para um determinado *Nome da Entidade* e para a autoridade de autenticação. 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$subjectName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$subjectName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. A seguinte função do PowerShell cria certificados X.509 intermediários usando a função anterior, além dos binários do OpenSSL. 
    ```PowerShell
    function New-CAIntermediateCert([string]$subjectName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($subjectName + ".cer")
        $newCert = New-CASelfsignedCertificate $subjectName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. A seguinte função do PowerShell cria a cadeia de certificados X.509. Leia [Cadeias de certificados](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification) para obter mais informações.
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    Esse script cria um arquivo chamado *RootCA.cer* no diretório de trabalho. 
    4. Por fim, use as funções do PowerShell acima para criar a cadeia de certificados X.509, executando o comando `New-CACertChain` na janela do PowerShell. 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>Prova de posse do seu certificado de autoridade de certificação X.509

Esse script executa o fluxo de *Prova de posse* para o seu certificado X.509. 

Na janela do PowerShell na área de trabalho, execute o seguinte código:
    ```PowerShell
    function New-CAVerificationCert([string]$requestedSubjectName)
    {
        $cnRequestedSubjectName = ("CN={0}" -f $requestedSubjectName)
        $verifyRequestedFileName = ".\verifyCert4.cer"
        $rootCACert = Get-CACertBySubjectName $_rootCertSubject
        Write-Host "Using Signing Cert:::" 
        Write-Host $rootCACert
    
        $verifyCert = New-CASelfsignedCertificate $cnRequestedSubjectName $rootCACert $false

        Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
        if (-not (Test-Path $verifyRequestedFileName))
        {
            throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
        }
    
        Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnRequestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
    }
    New-CAVerificationCert "<your verification code>"
    ```
   Isso cria um certificado com o nome da entidade determinada, assinado pela AC, como um arquivo chamado *VerifyCert4.cer* no diretório de trabalho. Esse arquivo de certificado ajudará a validar com o Hub IoT se você tem a permissão de autenticação (ou seja, a chave privada) dessa AC.


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>Criar um certificado X.509 de folha para seu dispositivo

Esta seção demonstra que você pode usar um script do PowerShell que cria um certificado de dispositivo de folha e a cadeia de certificados correspondente. 

Na janela do PowerShell no computador local, execute o script a seguir para criar um certificado de X.509 assinado pela AC para este dispositivo:
    ```PowerShell
    function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
    {
        $cnNewDeviceSubjectName = ("CN={0}" -f $deviceName)
        $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
        $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
        $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
        $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
    
        $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

        $newDeviceCertPfx = New-CASelfSignedCertificate $cnNewDeviceSubjectName $signingCert $false
    
        $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

        # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
        Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
        if (-not (Test-Path $newDevicePfxFileName))
        {
            throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
        }

        # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
        Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
        openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

        # Convert the PEM to get formats we can process
        if ($useEcc -eq $true)
        {
            openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
        }
        else
        {
            openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
        }
        openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
        Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
    }
    ```
   Em seguida, execute `New-CADevice "<yourTestDevice>"` na janela do PowerShell, usando o nome amigável que você usou para criar o seu dispositivo. Quando solicitado a fornecer a senha da chave privada da AC, digite "123". Isso cria um arquivo _<yourTestDevice>.pfx_ no diretório de trabalho.

