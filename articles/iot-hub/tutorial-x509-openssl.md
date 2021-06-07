---
title: Tutorial – Usar o OpenSSL para criar certificados de teste X.509 para o Hub IoT do Azure | Microsoft Docs
description: Tutorial – Usar o OpenSSL para criar Certificados de Autoridades de Certificação e de dispositivo para o hub IoT do Azure
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 0843e5d3a5e91cb4acdf18ad6bdf6f4f0c214f72
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378288"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Tutorial: Como usar o OpenSSL para criar certificados de teste

Embora seja possível comprar certificados X.509 de uma autoridade de certificação confiável, criar uma hierarquia própria de certificados de teste ou usar certificados autoassinados é adequado para testar a autenticação de dispositivo do hub IoT. O exemplo a seguir usa o [OpenSSL](https://www.openssl.org/) e o [OpenSSL Cookbook](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) para criar uma AC (autoridade de certificação), uma AC subordinada e um certificado de dispositivo. Em seguida, o exemplo conecta a AC subordinada e o certificado de dispositivo a uma hierarquia de certificados. Isso é apresentado apenas para fins de exemplo.

## <a name="step-1---create-the-root-ca-directory-structure"></a>Etapa 1 – Criar a estrutura de diretório da AC raiz

Crie uma estrutura de diretório para a autoridade de certificação.

* O diretório **certs** armazena novos certificados.
* O diretório **db** é usado para o banco de dados de certificados.
* O diretório **private** armazena a chave privada da AC.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>Etapa 2 – Criar um arquivo de configuração de AC raiz

Antes de criar uma AC, crie um arquivo de configuração e salve-o como `rootca.conf` no diretório rootca.

```xml
[default]
name                     = rootca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Root CA"

[ca_default]
home                     = ../rootca 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = none
default_days             = 3650
default_crl_days         = 365
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>Etapa 3 – Criar uma AC raiz

Primeiro, gere a chave e a CSR (solicitação de assinatura de certificado) no diretório rootca.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Em seguida, crie um Certificado de Autoridade de Certificação autoassinado. A autoassinatura é adequada para fins de teste. Especifique as extensões de arquivo de configuração ca_ext na linha de comando. Elas indicam que o certificado se destina a uma AC raiz e que ele pode ser usado para assinar certificados e CRLs (listas de certificados revogados). Assine o certificado e confirme-o no banco de dados.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>Etapa 4 – Criar a estrutura de diretório da AC subordinada

Crie uma estrutura de diretório para a AC subordinada.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>Etapa 5 – Criar um arquivo de configuração de AC subordinada

Crie um arquivo de configuração e salve-o como subca.conf no diretório `subca`.

```bash
[default]
name                     = subca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Subordinate CA"

[ca_default]
home                     = . 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = copy 
default_days           
default_crl_days         = 90 
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash
```

## <a name="step-6---create-a-subordinate-ca"></a>Etapa 6 – Criar uma AC subordinada

Crie um número de série no arquivo `rootca/db/serial` para o certificado de AC subordinada.

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>Você precisa criar um número de série para cada certificado de AC subordinada e cada certificado de dispositivo criado. Certificados diferentes não podem ter o mesmo número de série.

Este exemplo mostra como criar uma AC subordinada ou de registro. Como você pode usar a AC raiz para assinar certificados, a criação de uma AC subordinada não é estritamente necessária. No entanto, ter uma AC subordinada simula as hierarquias de certificados do mundo real, nas quais a AC raiz é mantida offline e as CAs subordinadas emitem certificados do cliente.

Use o arquivo de configuração para gerar uma chave e uma CSR (solicitação de assinatura de certificado).

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

Envie a CSR para a AC raiz e use a AC raiz para emitir e assinar o certificado de AC subordinada. Especifique sub_ca_ext para a opção de extensões na linha de comando. As extensões indicam que o certificado se destina a uma AC que pode assinar certificados e CRLs (listas de certificados revogados). Quando solicitado, assine o certificado e confirme-o no banco de dados.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>Etapa 7 – Demonstrar a prova de posse

Agora, você tem um certificado de AC raiz e um certificado de AC subordinada. Você pode usar qualquer um para assinar certificados de dispositivo. Aquele que você escolher precisará ser carregado no Hub IoT. As etapas a seguir pressupõem que você esteja usando o certificado de AC subordinada. Para carregar e registrar o certificado de AC subordinada no Hub IoT:

1. No portal do Azure, navegue até o Hub IoT e selecione **Configurações > Certificados**.

1. Selecione **Adicionar** para adicionar o novo certificado de AC subordinada.

1. Insira um nome de exibição no campo **Nome do Certificado** e escolha o arquivo de certificado PEM criado anteriormente.

1. Clique em **Salvar**. Seu certificado será mostrado na lista de certificados com o status **Não verificado**. O processo de verificação provará que você é o proprietário do certificado.

   
1. Selecione o certificado para ver a caixa de diálogo **Detalhes do Certificado**.

1. Escolha **Gerar Código de Verificação**. Para obter mais informações, confira [Provar a posse de um Certificado de Autoridade de Certificação](tutorial-x509-prove-possession.md).

1. Copie o código de verificação para a área de transferência. É necessário definir o código de verificação como a entidade do certificado. Por exemplo, se o código de verificação for BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A, adicione-o como a entidade do certificado, conforme mostrado na etapa 9.

1. Gere uma chave privada.

  ```bash
    $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
  ```

9. Gere uma CSR (solicitação de assinatura de certificado) por meio da chave privada. Adicione o código de verificação como a entidade do certificado.

  ```bash
  openssl req -new -key pop.key -out pop.csr
  
    -----
    Country Name (2 letter code) [XX]:.
    State or Province Name (full name) []:.
    Locality Name (eg, city) [Default City]:.
    Organization Name (eg, company) [Default Company Ltd]:.
    Organizational Unit Name (eg, section) []:.
    Common Name (eg, your name or your server hostname) []:BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
 
  ```

10. Crie um certificado usando o arquivo de configuração da AC raiz e a CSR para o certificado de prova de posse.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

11. Selecione o novo certificado na exibição **Detalhes do Certificado**. Para encontrar o arquivo PEM, procure a pasta certs.

12. Depois que o certificado for carregado, selecione **Verificar**. O status do Certificado de Autoridade de Certificação será alterado para **Verificado**.

## <a name="step-8---create-a-device-in-your-iot-hub"></a>Etapa 8 – Criar um dispositivo no seu Hub IoT

Navegue até o Hub IoT no portal do Azure e crie uma identidade do dispositivo IoT com os seguintes valores:

1. Forneça a **ID do dispositivo** que corresponda ao nome da entidade dos certificados do dispositivo.

1. Selecione o tipo de autenticação **AC de X.509 Assinado**.

1. Clique em **Salvar**.

## <a name="step-9---create-a-client-device-certificate"></a>Etapa 9 – Criar um certificado de dispositivo do cliente

Para gerar um certificado do cliente, primeiro, você precisa gerar uma chave privada. Os comandos a seguir mostram como usar o OpenSSL para criar uma chave privada. Crie a chave no diretório subca.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Crie uma CSR (solicitação de assinatura de certificado) para a chave. Não é necessário inserir uma senha de desafio nem um nome de empresa opcional. No entanto, é necessário inserir a identificação do dispositivo no campo de nome comum.

```bash
openssl req -new -key device.key -out device.csr

-----
Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server hostname) []:`<your device ID>`
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

Verifique se a CSR é o que você espera.

```bash
openssl req -text -in device.csr -noout
```

Envie a CSR à AC subordinada para conectá-la à hierarquia de certificados. Especifique `client_ext` na opção `-extensions`. Observe que o `Basic Constraints` no certificado emitido indica que esse certificado não se destina a uma AC. Se você estiver assinando vários certificados, lembre-se de atualizar o número de série antes de gerar cada certificado usando o comando `rand -hex 16 > db/serial` do OpenSSL.

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Próximas etapas

Acesse [Como testar a autenticação de certificado](tutorial-x509-test-certificate.md) para determinar se o certificado pode autenticar seu dispositivo no Hub IoT.
