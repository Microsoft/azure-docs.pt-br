---
title: Usar certificados com o Azure Stack Edge pro GPU | Microsoft Docs
description: Descreve o uso de certificados com Azure Stack dispositivo de GPU pro Edge, incluindo por que usar, quais tipos e como carregar certificados em seu dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 534870e6bd67b7aa5273289f3154a794a2b9bd22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519003"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Usar certificados com Azure Stack dispositivo de GPU pro Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve os tipos de certificados que podem ser instalados em seu dispositivo Azure Stack Edge pro. O artigo também inclui os detalhes de cada tipo de certificado, juntamente com o procedimento para instalar e identificar a data de validade.  

## <a name="about-certificates"></a>Sobre certificados

Um certificado fornece um link entre uma **chave pública** e uma entidade (como o nome de domínio) que foi **assinada** (verificada) por um terceiro confiável (como uma **autoridade de certificação**).  Um certificado fornece uma maneira conveniente de distribuir chaves de criptografia pública confiáveis. Assim, os certificados garantem que sua comunicação seja confiável e que você esteja enviando informações criptografadas para o servidor correto. 

Quando seu dispositivo Azure Stack Edge pro é inicialmente configurado, os certificados autoassinados são gerados automaticamente. Opcionalmente, você pode colocar seus próprios certificados. Há diretrizes que você precisa seguir se planeja trazer seus próprios certificados.

## <a name="types-of-certificates"></a>Tipos de certificados

Os vários tipos de certificados que são usados em seu dispositivo Azure Stack Edge pro são os seguintes: 
- Certificados de autenticação
    - AC Raiz
    - Intermediário

- Certificados de ponto de extremidade
    - Certificado do nó
    - Certificados de interface do usuário local
    - Azure Resource Manager certificados
    - Certificados de armazenamento de BLOBs
    - Certificados de dispositivo IoT
    <!--- WiFi certificates
    - VPN certificates-->

- Certificados de criptografia
    - Certificados de sessão de suporte

Cada um desses certificados é descrito em detalhes nas seções a seguir.

## <a name="signing-chain-certificates"></a>Certificados de cadeia de assinatura

Esses são os certificados para a autoridade que assina os certificados ou a autoridade de certificado de assinatura. 

### <a name="types"></a>Tipos

Esses certificados podem ser certificados raiz ou certificados intermediários. Os certificados raiz são sempre autoassinados (ou assinados por si só). Os certificados intermediários não são assinados automaticamente e assinados pela autoridade de assinatura.

### <a name="caveats"></a>Advertências

- Os certificados raiz devem ser certificados de cadeia de assinatura.
- Os certificados raiz podem ser carregados em seu dispositivo no seguinte formato: 
    - **Der** – elas estão disponíveis como uma `.cer` extensão de arquivo.
    - **Codificado em Base 64** – elas estão disponíveis como `.cer` extensão de arquivo.
    - **P7b** – esse formato é usado apenas para certificados de cadeia de assinatura que inclui os certificados raiz e intermediário.
- Os certificados de cadeia de assinatura são sempre carregados antes de você carregar quaisquer outros certificados.


## <a name="node-certificates"></a>Certificados de nó

<!--Your Azure Stack Edge Pro device could be a 1-node device or a 4-node device.--> Todos os nós no dispositivo estão constantemente se comunicando entre si e, portanto, precisam ter uma relação de confiança. Os certificados de nó fornecem uma maneira de estabelecer essa confiança. Os certificados de nó também entram em jogo quando você está se conectando ao nó do dispositivo usando uma sessão remota do PowerShell via HTTPS.

### <a name="caveats"></a>Advertências

- O certificado do nó deve ser fornecido em `.pfx` formato com uma chave privada que possa ser exportada. 
- Você pode criar e carregar um certificado de nó de curinga 1 ou 4 certificados de nó individuais. 
- Um certificado de nó deve ser alterado se o domínio DNS for alterado, mas o nome do dispositivo não for alterado. Se você estiver trazendo seu próprio certificado de nó, não poderá alterar o número de série do dispositivo. você só pode alterar o nome de domínio.
- Use a tabela a seguir para orientá-lo durante a criação de um certificado de nó.
   
    |Type |Nome da entidade (SN)  |SAN (nome alternativo da entidade)  |Exemplo de nome da entidade |
    |---------|---------|---------|---------|
    |Nó|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>Certificados de ponto de extremidade

Para qualquer ponto de extremidade que o dispositivo expõe, um certificado é necessário para a comunicação confiável. Os certificados de ponto de extremidade incluem aqueles necessários ao acessar o Azure Resource Manager e o armazenamento de BLOBs por meio das APIs REST. 

Quando você coloca um certificado assinado por conta própria, também precisa da cadeia de assinatura correspondente do certificado. Para a cadeia de assinatura, Azure Resource Manager e os certificados de blob no dispositivo, você precisará dos certificados correspondentes no computador cliente também para autenticar e se comunicar com o dispositivo.

### <a name="caveats"></a>Advertências

- Os certificados do ponto de extremidade precisam estar no `.pfx` formato com uma chave privada. A cadeia de assinatura deve ser do formato DER ( `.cer` extensão de arquivo). 
- Quando você traga seus próprios certificados de ponto de extremidade, eles podem ser como certificados individuais ou certificados de multidomínio. 
- Se você estiver trazendo uma cadeia de assinatura, o certificado de cadeia de assinatura deverá ser carregado antes de carregar um certificado de ponto de extremidade.
- Esses certificados devem ser alterados se o nome do dispositivo ou os nomes de domínio DNS forem alterados.
- Um certificado de ponto de extremidade curinga pode ser usado.
- As propriedades dos certificados de ponto de extremidade são semelhantes às de um certificado SSL típico. 
- Use a tabela a seguir ao criar um certificado de ponto de extremidade:

    |Type |Nome da entidade (SN)  |SAN (nome alternativo da entidade)  |Exemplo de nome da entidade |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Armazenamento de Blobs|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Certificado único de várias SANs para ambos os pontos de extremidade|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>Certificados de interface do usuário local

Você pode acessar a interface do usuário da Web local do seu dispositivo por meio de um navegador. Para garantir que essa comunicação seja segura, você pode carregar seu próprio certificado. 

### <a name="caveats"></a>Advertências

- O certificado de interface do usuário local também é carregado em um `.pfx` formato com uma chave privada que pode ser exportada.
- Depois de carregar o certificado de interface do usuário local, será necessário reiniciar o navegador e limpar o cache. Consulte as instruções específicas para seu navegador.

    |Type |Nome da entidade (SN)  |SAN (nome alternativo da entidade)  |Exemplo de nome da entidade |
    |---------|---------|---------|---------|
    |Interface do usuário local| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge certificados de dispositivo

Seu dispositivo Azure Stack Edge pro também é um dispositivo IoT com a computação habilitada por um dispositivo IoT Edge conectado a ele. Para qualquer comunicação segura entre este IoT Edge dispositivo e os dispositivos downstream que podem se conectar a ele, você também pode carregar IoT Edge certificados. 

O dispositivo tem certificados autoassinados que podem ser usados se você quiser usar apenas o cenário de computação com o dispositivo. Se o dispositivo Azure Stack Edge pro estiver conectado a dispositivos downstream, você precisará colocar seus próprios certificados.

Há três certificados IoT Edge que você precisa instalar para habilitar esta relação de confiança:

- **Autoridade de certificação raiz ou a autoridade de certificação do proprietário**
- **Autoridade de certificação do dispositivo** 
- **Certificado de chave do dispositivo**

### <a name="caveats"></a>Advertências

- Os certificados de IoT Edge são carregados no `.pem` formato. 


Para obter mais informações sobre certificados de IoT Edge, consulte [Azure IOT Edge detalhes do certificado](../iot-edge/iot-edge-certs.md#iot-edge-certificates).

## <a name="support-session-certificates"></a>Certificados de sessão de suporte

Se seu dispositivo Azure Stack Edge pro estiver enfrentando problemas e, em seguida, para solucionar esses problemas, uma sessão de suporte remoto do PowerShell poderá ser aberta no dispositivo. Para habilitar uma comunicação segura e criptografada nessa sessão de suporte, você pode carregar um certificado.

### <a name="caveats"></a>Advertências

- Verifique se o certificado correspondente `.pfx` com a chave privada está instalado no computador cliente usando a ferramenta de descriptografia.
- Verifique se o campo **uso de chave** do certificado não é **assinatura de certificado**. Para verificar isso, clique com o botão direito do mouse no certificado, escolha **abrir** e, na guia **detalhes** , localize **uso da chave**. 


### <a name="caveats"></a>Advertências

- O certificado de sessão de suporte deve ser fornecido como formato DER com uma `.cer` extensão.


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge Pro device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a>Criar certificados (opcional)

A seção a seguir descreve o procedimento para criar uma cadeia de assinatura e certificados de ponto de extremidade.

### <a name="certificate-workflow"></a>Fluxo de trabalho do certificado

Você terá uma maneira definida de criar os certificados para os dispositivos que estão operando em seu ambiente. Você pode usar os certificados fornecidos por seu administrador de ti. 

**Para fins de desenvolvimento ou teste apenas, você também pode usar o Windows PowerShell para criar certificados no sistema local.** Ao criar os certificados para o cliente, siga estas diretrizes:

1. Você pode criar qualquer um dos seguintes tipos de certificados:

    - Crie um único certificado válido para uso com um único nome de domínio totalmente qualificado (FQDN). Por exemplo, *mydomain.com*.
    - Crie um certificado curinga para proteger o nome de domínio principal e vários subdomínios também. Por exemplo, **. mydomain.com*.
    - Crie um certificado SAN (nome alternativo da entidade) que abrangerá vários nomes de domínio em um único certificado. 

2. Se você estiver trazendo seu próprio certificado, será necessário um certificado raiz para a cadeia de assinatura. Consulte as etapas para [criar certificados de cadeia de assinatura](#create-signing-chain-certificate).

3. Em seguida, você pode criar os certificados de ponto de extremidade para a interface do usuário local do dispositivo, do blob e do Azure Resource Manager. Você pode criar 3 certificados separados para o dispositivo, o blob e o Azure Resource Manager, ou pode criar um certificado para todos os 3 pontos de extremidade. Para obter etapas detalhadas, consulte [criar assinatura e certificados de ponto de extremidade](#create-signed-endpoint-certificates).

4. Se você estiver criando três certificados separados ou um certificado, especifique os nomes de entidade (SN) e os nomes alternativos da entidade (SAN) de acordo com as diretrizes fornecidas para cada tipo de certificado. 

### <a name="create-signing-chain-certificate"></a>Criar certificado de cadeia de assinatura

Crie esses certificados por meio do Windows PowerShell em execução no modo de administrador. **Os certificados criados dessa forma devem ser usados apenas para fins de desenvolvimento ou teste.**

O certificado de cadeia de assinatura precisa ser criado apenas uma vez. Os outros certificados de ponto de extremidade se referirão a este certificado para assinatura.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>Criar certificados de ponto de extremidade assinados

Crie esses certificados por meio do Windows PowerShell em execução no modo de administrador.

Nesses exemplos, os certificados de pontos de extremidade são criados para um dispositivo com:
    - Nome do dispositivo: `DBE-HWDC1T2`
    - Domínio DNS: `microsoftdatabox.com`

Substitua pelo nome e domínio DNS do seu dispositivo para criar certificados para seu dispositivo.
 
**Certificado de ponto de extremidade de BLOB**

Crie um certificado para o ponto de extremidade de BLOB em seu repositório pessoal.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Azure Resource Manager certificado de ponto de extremidade**

Crie um certificado para os pontos de extremidade de Azure Resource Manager em seu repositório pessoal.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certificado de interface do usuário da Web local do dispositivo**

Crie um certificado para a interface do usuário da Web local do dispositivo em seu repositório pessoal.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Único certificado de várias SANs para todos os pontos de extremidade**

Crie um único certificado para todos os pontos de extremidade em seu repositório pessoal.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

Depois que os certificados forem criados, a próxima etapa será carregar os certificados em seu dispositivo Azure Stack Edge pro


## <a name="upload-certificates"></a>Carregar certificados 

Os certificados que você criou para seu dispositivo, por padrão, residem no **repositório pessoal** do cliente. Esses certificados precisam ser exportados em seu cliente em arquivos de formato apropriados que possam ser carregados em seu dispositivo.

1. O certificado raiz deve ser exportado como formato DER com `.cer` extensão. Para obter etapas detalhadas, consulte [Exportar certificados como formato der](#export-certificates-as-der-format).
2. Os certificados de ponto de extremidade devem ser exportados como arquivos *. pfx* com chaves privadas. Para obter etapas detalhadas, consulte [Exportar certificados como arquivo *. pfx* com chaves privadas](#export-certificates-as-pfx-format-with-private-key). 
3. Os certificados raiz e de ponto de extremidade são carregados no dispositivo usando a opção **+ Adicionar certificado** na página certificados na interface do usuário da Web local. 

    1. Carregue os certificados raiz primeiro. Na interface do usuário da Web local, vá para **certificados > + adicionar certificado**.

        ![Adicionar certificado de cadeia de assinatura 1](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. Em seguida, carregue os certificados do ponto de extremidade. 

        ![Adicionar certificado de cadeia de assinatura 2](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        Escolha os arquivos de certificado no formato *. pfx* e insira a senha que você forneceu quando exportou o certificado. O certificado Azure Resource Manager pode levar alguns minutos para ser aplicado.

        Se a cadeia de assinatura não for atualizada primeiro e você tentar carregar os certificados de ponto de extremidade, você receberá um erro.

        ![Erro de aplicação de certificado](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        Volte e carregue o certificado da cadeia de assinatura e, em seguida, carregue e aplique os certificados do ponto de extremidade.

> [!IMPORTANT]
> Se o nome do dispositivo ou o domínio DNS forem alterados, novos certificados deverão ser criados. Os certificados de cliente e os certificados de dispositivo devem ser atualizados com o novo nome de dispositivo e domínio DNS. 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>Importar certificados no cliente que está acessando o dispositivo

Os certificados que você criou e carregou em seu dispositivo devem ser importados no cliente do Windows (acessando o dispositivo) no repositório de certificados apropriado.

1. O certificado raiz que você exportou como o DER agora deve ser importado nas **autoridades de certificação raiz confiáveis** no seu sistema cliente. Para obter etapas detalhadas, consulte [importar certificados para o repositório de autoridades de certificação raiz confiáveis](#import-certificates-as-der-format).

2. Os certificados de ponto de extremidade exportados como o `.pfx` devem ser exportados como der com a `.cer` extensão. Isso `.cer` é então importado no **repositório de certificados pessoal** do seu sistema. Para obter etapas detalhadas, consulte [importar certificados para o repositório de certificados pessoal](#import-certificates-as-der-format).

### <a name="import-certificates-as-der-format"></a>Importar certificados como formato DER

Para importar certificados em um cliente Windows, execute as seguintes etapas:

1. Clique com o botão direito do mouse no arquivo e selecione **Instalar certificado**. Essa ação inicia o Assistente para Importação de Certificados.

    ![Importar certificado 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. Em **local do repositório**, selecione **computador local** e, em seguida, selecione **Avançar**.

    ![Importar certificado 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. Selecione **Coloque todos os certificados no repositório a seguir** e, em seguida, selecione **procurar**. 

    - Para importar para o repositório pessoal, navegue até o repositório pessoal do seu host remoto e, em seguida, selecione **Avançar**.

        ![Importar certificado 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - Para importar para o repositório confiável, navegue até a autoridade de certificação raiz confiável e selecione **Avançar**.

        ![Importar certificado 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. Selecione **Concluir**. Uma mensagem para o efeito de que a importação foi bem-sucedida é exibida.

### <a name="export-certificates-as-pfx-format-with-private-key"></a>Exportar certificados como formato. pfx com chave privada

Execute as etapas a seguir para exportar um certificado SSL com uma chave privada em um computador Windows. 

> [!IMPORTANT]
> Execute estas etapas no mesmo computador que você usou para criar o certificado. 

1. Execute *certlm. msc* para iniciar o repositório de certificados do computador local.

1. Clique duas vezes na pasta **pessoal** e, em seguida, em **certificados**.

    ![Exportar certificado 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. Clique com o botão direito do mouse no certificado do qual você deseja fazer backup e escolha **todas as tarefas > exportar...**

    ![Exportar certificado 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. Siga o assistente para exportação de certificados para fazer backup de seu certificado em um arquivo. pfx.

    ![Exportar certificado 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. Escolha **Sim, exportar a chave privada**.

    ![Exportar certificado 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. Escolha **incluir todos os certificados no caminho do certificado, se possível**, **exporte todas as propriedades estendidas** e **habilite a privacidade do certificado**. 

    > [!IMPORTANT]
    > Não selecione a **opção Excluir chave privada se a exportação for bem-sucedida**.

    ![Exportar certificado 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. Insira uma senha que você vai lembrar. Confirme a senha. A senha protege a chave privada.

    ![Exportar certificado 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. Escolha salvar arquivo em um local definido.

    ![Exportar certificado 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. Selecione **Concluir**.

    ![Exportar certificado 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. Você recebe uma mensagem a exportação foi bem-sucedida. Selecione **OK**.

    ![Exportar certificado 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

O backup do arquivo. pfx agora é salvo no local selecionado e está pronto para ser movido ou armazenado para sua manutenção segura.


### <a name="export-certificates-as-der-format"></a>Exportar certificados como formato DER

1. Execute *certlm. msc* para iniciar o repositório de certificados do computador local.

1. No repositório de certificados pessoal, selecione o certificado raiz. Clique com o botão direito do mouse e selecione **todas as tarefas > exportar...**

    ![Exportar certificado DER 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. O assistente de certificado é aberto. Selecione o formato como **X. 509 binário codificado por der (. cer)**. Selecione **Avançar**.

    ![Exportar certificado DER 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. Procure e selecione o local onde você deseja exportar o arquivo de formato. cer.

    ![Exportar certificado DER 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. Selecione **Concluir**.

    ![Exportar certificado DER 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>Algoritmos de certificado com suporte

 Somente os certificados RSA (Rivest – Shamir-Adleman) têm suporte com seu dispositivo Azure Stack Edge pro. Não há suporte para certificados de ECDSA (algoritmo de assinatura digital) de curva elíptica.

 Os certificados que contêm uma chave pública RSA são chamados de certificados RSA. Certificados que contêm uma chave pública de criptografia de curva elíptica (ECC) são chamados de certificados de ECDSA (algoritmo de assinatura digital de curva elíptica). 


## <a name="view-certificate-expiry"></a>Exibir expiração do certificado

Se você colocar seus próprios certificados, os certificados expirarão normalmente em 1 ano ou 6 meses. Para exibir a data de validade em seu certificado, vá para a página **certificados** na interface do usuário da Web local do seu dispositivo. Se você selecionar um certificado específico, poderá exibir a data de validade em seu certificado.

<!--## Rotate certificates

Rotation of certificates is not implemented in this release. You are also not notified of the pending expiration date on your certificate. 

View the certificate expiration date on the **Certificates** page in the local web UI of your device. Once the certificate expiration is approaching, create and upload new certificates as per the detailed instructions in [Create and upload certificates]().-->

## <a name="next-steps"></a>Próximas etapas

[Implantar o dispositivo pro Edge Azure Stack](azure-stack-edge-gpu-deploy-prep.md)