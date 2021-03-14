---
title: Certificados da versão prévia do Firewall do Azure Premium
description: Para configurar corretamente a inspeção TLS no Azure firewall Premium Preview, você deve configurar e instalar certificados de autoridade de certificação intermediários.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: 47ebc752dedd72bbdedc02908911f1686584acda
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615492"
---
# <a name="azure-firewall-premium-preview-certificates"></a>Certificados da versão prévia do Firewall do Azure Premium 

> [!IMPORTANT]
> O firewall Premium do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Para configurar corretamente a inspeção de TLS do Azure firewall Premium Preview, você deve fornecer um certificado de autoridade de certificação intermediário válido e depositá-lo no cofre de chaves do Azure.

## <a name="certificates-used-by-azure-firewall-premium-preview"></a>Certificados usados pelo Azure firewall Premium Preview

Há três tipos de certificados usados em uma implantação típica:

- **Certificado de autoridade de certificação intermediário (certificado de autoridade de certificação)**

   Uma autoridade de certificação (CA) é uma organização confiável para assinar certificados digitais. Uma autoridade de certificação verifica a identidade e a legitimidade de uma empresa ou individual solicitando um certificado. Se a verificação for bem-sucedida, a AC emitirá um certificado assinado. Quando o servidor apresenta o certificado para o cliente (por exemplo, seu navegador da Web) durante um handshake de SSL/TLS, o cliente tenta verificar a assinatura em uma lista de assinantes *válidos conhecidos* . Os navegadores da Web normalmente são fornecidos com listas de CAs que eles confiam implicitamente para identificar hosts. Se a autoridade não estiver na lista, como ocorre com alguns sites que assinam seus próprios certificados, o navegador alertará o usuário de que o certificado não está assinado por uma autoridade reconhecida e solicitará ao usuário se deseja continuar a comunicação com o site não verificado.

- **Certificado do servidor (certificado do site)**

   Um certificado associado a um nome de domínio específico. Se um site tiver um certificado válido, significa que uma autoridade de certificação executou etapas para verificar se o endereço da Web realmente pertence a essa organização. Quando você digita uma URL ou segue um link para um site seguro, seu navegador verifica o certificado para as seguintes características:
   - O endereço do site corresponde ao endereço no certificado.
   - O certificado é assinado por uma autoridade de certificação que o navegador reconhece como uma autoridade *confiável* .
   
   Ocasionalmente, os usuários podem se conectar a um servidor com um certificado não confiável. O Firewall do Azure removerá a conexão como se o servidor encerrasse a conexão.

- **Certificado de autoridade de certificação raiz (certificado raiz)**

   Uma autoridade de certificação pode emitir vários certificados na forma de uma estrutura de árvore. Um certificado raiz é o certificado mais alto da árvore.

A visualização Premium do firewall do Azure pode interceptar o tráfego HTTP/S de saída e gerar automaticamente um certificado de servidor para o `www.website.com` . Esse certificado é gerado usando o certificado de autoridade de certificação intermediário que você fornece. Os aplicativos cliente e navegador do usuário final devem confiar no certificado da autoridade de certificação raiz da sua organização ou no certificado de autoridade de certificação intermediário para que esse procedimento funcione. 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="Processo de certificado":::

## <a name="intermediate-ca-certificate-requirements"></a>Requisitos de certificado de AC intermediário

Verifique se o seu certificado de autoridade de certificação está em conformidade com os seguintes requisitos:

- Quando implantado como um segredo de Key Vault, você deve usar o PKCS12 com um certificado e uma chave privada sem senha.

- Ele deve ser um único certificado e não deve incluir toda a cadeia de certificados.  

- Ele deve ser válido por um ano.  

- Ele deve ser uma chave privada RSA com tamanho mínimo de 4096 bytes.  

- Ele deve ter a `KeyUsage` extensão marcada como crítica com o `KeyCertSign` sinalizador (RFC 5280; 4.2.1.3 Key Usage).

- Ele deve ter a `BasicContraints` extensão marcada como crítica (RFC 5280; 4.2.1.9 Basic Constraints).  

- O `CA` sinalizador deve ser definido como true.

- O comprimento do caminho deve ser maior ou igual a um.

## <a name="azure-key-vault"></a>Cofre de Chave do Azure

[Azure Key Vault](../key-vault/general/overview.md) é um repositório de segredos gerenciado por plataforma que você pode usar para proteger segredos, chaves e certificados TLS/SSL. O firewall Premium do Azure dá suporte à integração com o Key Vault para certificados de servidor anexados a uma política de firewall.
 
Para configurar o cofre de chaves:

- Você precisa importar um certificado existente com seu par de chaves para o cofre de chaves. 
- Como alternativa, você também pode usar um segredo do Key Vault que é armazenado como um arquivo PFX codificado por senha, de base 64.  Um arquivo PFX é um certificado digital que contém a chave privada e a chave pública.
- É recomendável usar uma importação de certificado de autoridade de certificação, pois ela permite que você configure um alerta com base na data de expiração do certificado.
- Depois de importar um certificado ou um segredo, você precisa definir políticas de acesso no cofre de chaves para permitir que a identidade receba acesso ao certificado/segredo.
- O certificado de autoridade de certificação fornecido precisa ser confiável para sua carga de trabalho do Azure. Verifique se eles foram implantados corretamente.

Você pode criar ou reutilizar uma identidade gerenciada atribuída pelo usuário existente, que o Firewall do Azure usa para recuperar certificados de Key Vault em seu nome. Para obter mais informações, consulte [o que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md) 

## <a name="configure-a-certificate-in-your-policy"></a>Configurar um certificado em sua política

Para configurar um certificado de autoridade de certificação na política Premium do firewall, selecione sua política e selecione **inspeção TLS (versão prévia)**. Selecione **habilitado** na página **inspeção TLS** . Em seguida, selecione seu certificado de autoridade de certificação em Azure Key Vault, conforme mostrado na figura a seguir:

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Diagrama de visão geral do firewall do Azure Premium":::
 
> [!IMPORTANT]
> Para ver e configurar um certificado do portal do Azure, você deve adicionar sua conta de usuário do Azure à política de acesso de Key Vault. Dê à sua conta de usuário **obter** e **listar** **permissões secretas**.
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Política de acesso de Azure Key Vault":::


## <a name="create-your-own-self-signed-ca-certificate"></a>Criar seu próprio certificado de autoridade de certificação autoassinado

Para ajudá-lo a testar e verificar a inspeção de TLS, você pode usar os scripts a seguir para criar sua própria AC raiz autoassinada e autoridade de certificação intermediária.

> [!IMPORTANT]
> Para produção, você deve usar sua PKI corporativa para criar um certificado de autoridade de certificação intermediário. Uma PKI corporativa aproveita a infraestrutura existente e manipula a distribuição da AC raiz para todos os computadores de ponto de extremidade. Para obter mais informações, consulte [implantar e configurar certificados de autoridade de certificação corporativa para a versão prévia do firewall do Azure](premium-deploy-certificates-enterprise-ca.md).

Há duas versões deste script:
- um script bash `cert.sh` 
- um script do PowerShell `cert.ps1` 

 Além disso, os dois scripts usam o `openssl.cnf` arquivo de configuração. Para usar os scripts, copie o conteúdo de `openssl.cnf` e `cert.sh` ou `cert.ps1` para o computador local.

Os scripts geram os seguintes arquivos:
- rootCA. CRT/rootCA. chave-certificado público da AC raiz e chave privada.
- interCA. CRT/interCA. chave-certificado público da CA intermediária e chave privada
- pacote de interCA. pfx-intermediário da CA intermediária que será usado pelo firewall

> [!IMPORTANT]
> rootCA. Key deve ser armazenada em um local offline seguro. Os scripts geram um certificado com validade de 1024 dias.
> Os scripts exigem binários OpenSSL instalados em seu computador local. Para obter mais informações, confira https://www.openssl.org/
> 
Depois que os certificados forem criados, implante-os nos seguintes locais:
- rootCA. CRT – implantar em computadores de ponto de extremidade (somente certificado público).
- interCA. pfx-importar como certificado em um Key Vault e atribuir à política de firewall.

### <a name="opensslcnf"></a>**OpenSSL. cnf**
```
[ req ]
default_bits        = 4096
distinguished_name  = req_distinguished_name
string_mask         = utf8only
default_md          = sha512

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name
localityName                    = Locality Name
0.organizationName              = Organization Name
organizationalUnitName          = Organizational Unit Name
commonName                      = Common Name
emailAddress                    = Email Address

[ rootCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ interCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:1
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ server_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:false
keyUsage = critical, digitalSignature
extendedKeyUsage = serverAuth
```

###  <a name="bash-script---certsh"></a>Script bash-cert.sh 
```bash
#!/bin/bash

# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 1024 -out rootCA.crt -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA" -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA"

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 1024 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password "pass:"

echo ""
echo "================"
echo "Successfully generated root and intermediate CA certificates"
echo "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
echo "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
echo "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
echo "================"
```

### <a name="powershell---certps1"></a>PowerShell-cert.ps1
```powershell
# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 3650 -out rootCA.crt -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA' -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA'

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 3650 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password 'pass:'

Write-Host ""
Write-Host "================"
Write-Host "Successfully generated root and intermediate CA certificates"
Write-Host "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
Write-Host "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
Write-Host "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
Write-Host "================"

```

## <a name="troubleshooting"></a>Solução de problemas

Se seu certificado de autoridade de certificação for válido, mas você não puder acessar FQDNs ou URLs sob a inspeção TLS, verifique os seguintes itens:

- Verifique se o certificado do servidor Web é válido.  

- Verifique se o certificado de autoridade de certificação raiz está instalado no sistema operacional do cliente.  

- Verifique se o navegador ou o cliente HTTPS contém um certificado raiz válido. O Firefox e alguns outros navegadores podem ter políticas de certificação especiais.  

- Verifique se o tipo de destino da URL na regra de aplicativo abrange o caminho correto e quaisquer outros hiperlinks inseridos na página HTML de destino. Você pode usar curingas para uma cobertura fácil de todo o caminho de URL necessário.  


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre os recursos do firewall do Azure Premium](premium-features.md)
