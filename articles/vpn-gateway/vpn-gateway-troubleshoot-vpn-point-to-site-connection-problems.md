---
title: "Solucionar problemas de conexão de ponto a site do Azure | Microsoft Docs"
description: "Saiba como solucionar problemas de conexão de ponto a site."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2017
ms.author: genli
ms.openlocfilehash: 76ab1600903705aad7f18f48f41cb7119c3c09bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Solução de problemas: problemas de conexão de ponto a site do Azure

Este artigo lista os problemas comuns de conexão de ponto a site que podem ocorrer. Também discute as possíveis causas e soluções para esses problemas.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Erro de cliente VPN: não foi possível encontrar um certificado

### <a name="symptom"></a>Sintoma

Quando você tenta conectar-se à rede virtual do Azure usando o cliente VPN, recebe a seguinte mensagem de erro:

**Não foi possível encontrar um certificado que possa ser usado com este protocolo EAP. (Erro 798)**

### <a name="cause"></a>Causa

Esse problema ocorrerá se o certificado do cliente estiver ausente em **Certificates – Current User\Personal\Certificates**.

### <a name="solution"></a>Solução

Para resolver esse problema, siga estas etapas:

1. Verifique se os certificados abaixo estão no local correto:

    | Certificado | Local padrão |
    | ------------- | ------------- |
    | AzureClient.pfx  | Current User\Personal\Certificates |
    | Azuregateway-*GUID*.cloudapp.net  | Current User\Trusted Root Certification Authorities|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Local Computer\Trusted Root Certification Authorities|

2. Vá para Usuários\<UserName>AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID>, instale manualmente o certificado (arquivo *.cer) no repositório do computador e do usuário.

Para saber mais sobre como instalar o certificado do cliente, confira [Gerar e exportar certificados para conexões ponto a site](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Quando você importar o certificado do cliente, não selecione a opção **Habilitar a proteção de chave privada forte**.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Erro de cliente VPN: a mensagem recebida foi inesperada ou formatada incorretamente

### <a name="symptom"></a>Sintoma

Quando você tenta conectar-se à rede virtual do Azure usando o cliente VPN, recebe a seguinte mensagem de erro:

**A mensagem recebida era inesperada ou estava formatada incorretamente. (Erro 0x80090326)**

### <a name="cause"></a>Causa

Esse problema ocorre se a chave pública do certificado raiz não é carregada para o gateway de VPN do Azure. Também poderá ocorrer se a chave estiver corrompida ou expirada.

### <a name="solution"></a>Solução

Para resolver esse problema, verifique o status do certificado raiz no portal do Azure para ver se ele foi revogado. Se não foi revogado, tente excluir o certificado raiz e carregá-lo novamente. Para saber mais, confira [Criar certificados](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Erro de cliente VPN: uma cadeia de certificados foi processada, mas fechada 

### <a name="symptom"></a>Sintoma 

Quando você tenta conectar-se à rede virtual do Azure usando o cliente VPN, recebe a seguinte mensagem de erro:

**Uma cadeia de certificados foi processada, mas terminou em um certificado raiz em que o provedor de confiabilidade não confia.**

### <a name="solution"></a>Solução

1. Verifique se os certificados abaixo estão no local correto:

    | Certificado | Local padrão |
    | ------------- | ------------- |
    | AzureClient.pfx  | Current User\Personal\Certificates |
    | Azuregateway-*GUID*.cloudapp.net  | Current User\Trusted Root Certification Authorities|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Local Computer\Trusted Root Certification Authorities|

2. Se os certificados estiverem no local, tente excluir os certificados e reinstalá-los. O certificado **azuregateway-*GUID*.cloudapp.net** está no pacote de configuração de cliente VPN que você baixou do portal do Azure. Você pode usar arquivadores para extrair os arquivos do pacote.

## <a name="file-download-error-target-uri-is-not-specified"></a>Erro no download do arquivo: o URI de destino não foi especificado

### <a name="symptom"></a>Sintoma

Você vê a seguinte mensagem de erro:

**Erro de download do arquivo. O URI de destino não foi especificado.**

### <a name="cause"></a>Causa 

Esse problema ocorre devido ao tipo de gateway incorreto. 

### <a name="solution"></a>Solução

O tipo de gateway de VPN deve ser **VPN**, enquanto o tipo de VPN deve ser **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>Erro de cliente VPN: falha de script personalizado do VPN Azure 

### <a name="symptom"></a>Sintoma

Quando você tenta conectar-se à rede virtual do Azure usando o cliente VPN, recebe a seguinte mensagem de erro:

**Falha de script personalizado (para atualizar sua tabela de roteamento). (Erro 8007026f)**

### <a name="cause"></a>Causa

Esse problema poderá ocorrer se você estiver tentando abrir a conexão VPN site a ponto usando um atalho.

### <a name="solution"></a>Solução 

Abra o pacote VPN diretamente em vez de abri-lo pelo atalho.

## <a name="cannot-install-the-vpn-client"></a>Não é possível instalar o cliente VPN

### <a name="cause"></a>Causa 

Um certificado adicional é necessário para confiar no gateway de VPN da sua rede virtual. O certificado está incluído no pacote de configuração de cliente VPN que é gerado no portal do Azure.

### <a name="solution"></a>Solução

Extraia o pacote de configuração do cliente VPN e localize o arquivo .cer. Para instalar o certificado, siga estas etapas:

1. Abra mmc.exe.
2. Adicione o snap-in **Certificados**.
3. Selecione a conta **Computador** para o computador local.
4. Clique com botão direito do mouse no nó **Autoridades de Certificação Confiáveis**. Clique em **All-Task** > **Import** e navegue até o arquivo .cer que você extraiu do pacote de configuração do cliente VPN.
5. Reinicie o computador. 
6. Tente instalar o cliente VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Erro de portal do Azure: falha ao salvar o gateway de VPN e os dados são inválidos

### <a name="symptom"></a>Sintoma

Quando você tenta salvar as alterações do gateway de VPN no portal do Azure, recebe a seguinte mensagem de erro:

**Falha ao salvar o gateway de rede virtual &lt;*nome do gateway*&gt;. Os dados para o certificado &lt;*ID do certificado*&gt; são inválidos.**

### <a name="cause"></a>Causa 

Esse problema poderá ocorrer se a chave pública do certificado raiz que você carregou contiver caracteres inválidos, como espaço.

### <a name="solution"></a>Solução

Verifique se os dados no certificado não contêm caracteres inválidos, como quebras de linha (retornos de carro). O valor inteiro deve estar em uma linha longa. O texto abaixo é um exemplo de certificado:

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Erro do portal do Azure: falha ao salvar gateway de VPN e o nome do recurso é inválido

### <a name="symptom"></a>Sintoma

Quando você tenta salvar as alterações do gateway de VPN no portal do Azure, recebe a seguinte mensagem de erro: 

**Falha ao salvar o gateway de rede virtual &lt;*nome do gateway*&gt;. Nome do recurso &lt;*o nome do certificado que você tenta carregar*&gt; é inválido**.

### <a name="cause"></a>Causa

Esse problema ocorre porque o nome do certificado contém um caractere inválido, como um espaço. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Erro 503 de portal do Azure: erro de download do arquivo do pacote VPN

### <a name="symptom"></a>Sintoma

Ao tentar baixar o pacote de configuração de cliente VPN, você recebe a seguinte mensagem de erro:

**Falha ao baixar o arquivo. Detalhes do erro: erro 503. O servidor está ocupado.**
 
### <a name="solution"></a>Solução

Esse erro pode ser causado por um problema de rede temporário. Tente baixar o pacote VPN novamente após alguns minutos.

## <a name="azure-vpn-gateway-upgrade-all-p2s-clients-are-unable-to-connect"></a>Atualização de Gateway de VPN do Azure: nenhum dos clientes P2S consegue se conectar

### <a name="cause"></a>Causa

Se o certificado passou de 50% de seu tempo de vida, o certificado é substituído.

### <a name="solution"></a>Solução

Para resolver esse problema, crie e redistribua os novos certificados para os clientes VPN. 

## <a name="too-many-vpn-clients-connected-at-once"></a>Muitos clientes VPN conectados ao mesmo tempo

Para cada gateway de VPN, o número máximo de conexões permitidas é de 128. Você pode ver o número total de clientes conectados no portal do Azure.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>A VPN ponto a site adiciona incorretamente uma rota para 10.0.0.0/8 à tabela de rotas

### <a name="symptom"></a>Sintoma

Quando você disca a conexão VPN no cliente ponto a site, o cliente VPN deve adicionar uma rota para a rede virtual do Azure. O serviço auxiliar de IP deve adicionar uma rota para a sub-rede dos clientes VPN. 

O intervalo de cliente VPN pertence a uma sub-rede menor de 10.0.0.0/8, como 10.0.12.0/24. Em vez de uma rota para 10.0.12.0/24, é adicionada uma rota para 10.0.0.0/8 que tem prioridade mais alta. 

Essa rota incorreta interrompe a conectividade com outras redes locais que podem pertencer a outra sub-rede dentro no intervalo 10.0.0.0/8, como 10.50.0.0/24, que não tenham uma rota específica definida. 

### <a name="cause"></a>Causa

Esse comportamento ocorre por padrão para clientes do Windows. Quando o cliente usa o protocolo PPP IPCP, ele obtém o endereço IP para a interface de túnel do servidor (gateway de VPN, neste caso). No entanto, devido a uma limitação no protocolo, o cliente não tem a máscara de sub-rede. Como não há nenhuma outra forma de obtê-la, o cliente tenta adivinhar a máscara de sub-rede com base na classe do endereço IP da interface do túnel. 

Portanto, uma rota é adicionada com base no seguinte mapeamento estático: 

Se o endereço pertence à classe A--> aplicar /8

Se o endereço pertencer à classe B --> aplicar /16

Se o endereço pertence à classe C--> aplicar /24

## <a name="vpn-client-cannot-access-network-file-shares"></a>O cliente VPN não pode acessar compartilhamentos de arquivos de rede

### <a name="symptom"></a>Sintoma

O cliente VPN foi conectado à rede virtual do Azure. No entanto, o cliente não pode acessar compartilhamentos de rede.

### <a name="cause"></a>Causa

O protocolo SMB é usado para acesso de compartilhamento de arquivos. Quando a conexão é iniciada, o cliente VPN adiciona as credenciais da sessão e a falha ocorre. Depois que a conexão é estabelecida, o cliente é forçado a usar as credenciais de cache para a autenticação Kerberos. Esse processo inicia consultas para o Centro de Distribuição de Chaves (um controlador de domínio) para obter um token. Uma vez que o cliente conecta-se pela Internet, ele pode não conseguir alcançar o controlador de domínio. Portanto, o cliente não pode fazer failover do Kerberos para NTLM. 

A única vez em que o cliente é solicitado a fornecer uma credencial é quando ele tem um certificado válido (com SAN = UPN) emitido pelo domínio ao qual ele está associado. O cliente também deve estar fisicamente conectado à rede de domínio. Nesse caso, o cliente tenta usar o certificado e busca o controlador de domínio. Então o Centro de distribuição de chaves retorna um erro de "KDC_ERR_C_PRINCIPAL_UNKNOWN". O cliente é forçado a fazer failover para NTLM. 

### <a name="solution"></a>Solução

Para contornar o problema, desabilite o cache de credenciais de domínio da seguinte subchave do registro: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Não é possível localizar a conexão VPN ponto a site no Windows após a reinstalação do cliente VPN

### <a name="symptom"></a>Sintoma

Você remove a conexão VPN ponto a site e reinstala o cliente VPN. Nessa situação, a conexão VPN não foi configurada com êxito. Você não vê a conexão VPN nas configurações **Conexões de rede** do Windows.

### <a name="solution"></a>Solução

Para resolver o problema, exclua os arquivos de configuração de cliente VPN antigos de **C:\Users\TheUserName\AppData\Roaming\Microsoft\Network\Connections** e execute novamente o instalador do cliente VPN.
