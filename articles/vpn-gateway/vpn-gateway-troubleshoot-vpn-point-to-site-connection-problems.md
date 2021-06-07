---
title: Solucionar problemas de conexão de ponto a site do Azure
titleSuffix: Azure VPN Gateway
description: Saiba como solucionar problemas e resolver problemas comuns de conexão de ponto a site e outros problemas e erros de rede privada virtual.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: c316aaf02979008b9d2ebc691d54c0fb95a5a52d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994852"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Solução de problemas: problemas de conexão de ponto a site do Azure

Este artigo lista os problemas comuns de conexão de ponto a site que podem ocorrer. Também discute as possíveis causas e soluções para esses problemas.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Erro de cliente VPN: não foi possível encontrar um certificado

### <a name="symptom"></a>Sintoma

Quando você tenta conectar-se à rede virtual do Azure usando o cliente VPN, recebe a seguinte mensagem de erro:

**Não foi possível encontrar um certificado que possa ser usado com esse protocolo de autenticação extensível. (Erro 798)**

### <a name="cause"></a>Causa

Esse problema ocorrerá se o certificado do cliente estiver ausente em **Certificates – Current User\Personal\Certificates**.

### <a name="solution"></a>Solução

Para resolver esse problema, siga estas etapas:

1. Abra o Gerenciador de Certificados: Clique em **Iniciar**, digite **gerenciar certificados do computador** e, em seguida, clique em **gerenciar certificados do computador** no resultado da pesquisa.

2. Verifique se os certificados abaixo estão no local correto:

    | Certificado | Location |
    | ------------- | ------------- |
    | AzureClient.pfx  | Current User\Personal\Certificates |
    | AzureRoot. cer    | Local Computer\Trusted Root Certification Authorities|

3. Vá para C:\Users \<UserName> \AppData\Roaming\Microsoft\Network\Connections\Cm \<GUID> , instale manualmente o certificado (arquivo *. cer) no repositório do usuário e do computador.

Para saber mais sobre como instalar o certificado do cliente, confira [Gerar e exportar certificados para conexões ponto a site](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Quando você importar o certificado do cliente, não selecione a opção **Habilitar a proteção de chave privada forte**.

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>A conexão de rede entre o computador e o servidor VPN não pôde ser estabelecida porque o servidor remoto não está respondendo

### <a name="symptom"></a>Sintoma

Ao tentar e conectar-se a um gateway de rede virtual do Azure usando IKEv2 no Windows, você obtém a seguinte mensagem de erro:

**A conexão de rede entre o computador e o servidor VPN não pôde ser estabelecida porque o servidor remoto não está respondendo**

### <a name="cause"></a>Causa
 
 O problema ocorre se a versão do Windows não tiver suporte para fragmentação de IKE
 
### <a name="solution"></a>Solução

O IKEv2 tem suporte no Windows 10 e Server 2016. No entanto, para usar IKEv2, você deve instalar as atualizações e definir um valor de chave do Registro localmente. Versões do sistema operacional anteriores ao Windows 10 não têm suporte e só podem usar SSTP.

Para preparar o Windows 10 ou Server 2016 para IKEv2:

1. Instale a atualização.

   | Versão do SO | Data | Número/Link |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10, versão 1607 | 17 de janeiro de 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10, versão 1703 | 17 de janeiro de 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 Versão 1709 | 22 de março de 2018 | [BDC4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. Defina o valor da chave do Registro. Crie ou defina `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` REG_DWORD chave no registro como 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Erro de cliente VPN: a mensagem recebida foi inesperada ou formatada incorretamente

### <a name="symptom"></a>Sintoma

Quando você tenta conectar-se à rede virtual do Azure usando o cliente VPN, recebe a seguinte mensagem de erro:

**A mensagem recebida era inesperada ou formatada incorretamente. (Erro 0x80090326)**

### <a name="cause"></a>Causa

Esse problema ocorre se uma das seguintes condições for verdadeira:

- O uso de UDR (rotas definidas pelo usuário) com a rota padrão na sub-rede de gateway está configurado incorretamente.
- A chave pública do certificado raiz não é carregada para o gateway de VPN do Azure. 
- A chave está corrompida ou expirada.

### <a name="solution"></a>Solução

Para resolver esse problema, siga estas etapas:

1. Remova a UDR da sub-rede de Gateway. Certifique-se de que a UDR encaminha todo o tráfego corretamente.
2. Verifique o status do certificado raiz no Portal do Azure para ver se ele foi revogado. Se não foi revogado, tente excluir o certificado raiz e carregá-lo novamente. Para saber mais, confira [Criar certificados](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Erro de cliente VPN: uma cadeia de certificados foi processada, mas fechada 

### <a name="symptom"></a>Sintoma 

Quando você tenta conectar-se à rede virtual do Azure usando o cliente VPN, recebe a seguinte mensagem de erro:

**Uma cadeia de certificados foi processada, mas terminou em um certificado raiz em que o provedor de confiabilidade não confia.**

### <a name="solution"></a>Solução

1. Verifique se os certificados abaixo estão no local correto:

    | Certificado | Location |
    | ------------- | ------------- |
    | AzureClient.pfx  | Current User\Personal\Certificates |
    | Azuregateway-*GUID*.cloudapp.net  | Current User\Trusted Root Certification Authorities|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Local Computer\Trusted Root Certification Authorities|

2. Se os certificados estiverem no local, tente excluir os certificados e reinstalá-los. O certificado **azuregateway-*GUID*.cloudapp.net** está no pacote de configuração de cliente VPN que você baixou do Portal do Azure. Você pode usar arquivadores para extrair os arquivos do pacote.

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

**Falha no script personalizado (para atualizar sua tabela de roteamento). (Erro 8007026f)**

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
4. Clique com botão direito do mouse no nó **Autoridades de Certificação Confiáveis**. Clique em **tudo-**  >  **importar** tarefa e navegue até o arquivo. cer extraído do pacote de configuração do cliente VPN.
5. Reinicie o computador. 
6. Tente instalar o cliente VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Erro de portal do Azure: falha ao salvar o gateway de VPN e os dados são inválidos

### <a name="symptom"></a>Sintoma

Quando você tenta salvar as alterações do gateway de VPN no portal do Azure, recebe a seguinte mensagem de erro:

**Falha ao salvar o nome do gateway de gateway de rede virtual &lt;  &gt; . Os dados da &lt; *ID do certificado* de certificado &gt; são inválidos.**

### <a name="cause"></a>Causa 

Esse problema poderá ocorrer se a chave pública do certificado raiz que você carregou contiver caracteres inválidos, como espaço.

### <a name="solution"></a>Solução

Verifique se os dados no certificado não contêm caracteres inválidos, como quebras de linha (retornos de carro). O valor inteiro deve estar em uma linha longa. O texto abaixo é um exemplo de certificado:

```text
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
```

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Erro do portal do Azure: falha ao salvar gateway de VPN e o nome do recurso é inválido

### <a name="symptom"></a>Sintoma

Quando você tenta salvar as alterações do gateway de VPN no portal do Azure, recebe a seguinte mensagem de erro: 

**Falha ao salvar o nome do gateway de gateway de rede virtual &lt;  &gt; . Nome do recurso &lt; *nome do certificado que você tentar carregar* &gt; é inválido**.

### <a name="cause"></a>Causa

Esse problema ocorre porque o nome do certificado contém um caractere inválido, como um espaço. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Erro 503 de portal do Azure: erro de download do arquivo do pacote VPN

### <a name="symptom"></a>Sintoma

Ao tentar baixar o pacote de configuração de cliente VPN, você recebe a seguinte mensagem de erro:

**Falha ao baixar o arquivo. Detalhes do erro: erro 503. O servidor está ocupado.**
 
### <a name="solution"></a>Solução

Esse erro pode ser causado por um problema de rede temporário. Tente baixar o pacote VPN novamente após alguns minutos.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Atualização de Gateway de VPN do Azure: nenhum dos clientes ponto a site consegue conectar-se

### <a name="cause"></a>Causa

Se o certificado passou de 50% de seu tempo de vida, o certificado é substituído.

### <a name="solution"></a>Solução

Para resolver esse problema, Baixe novamente e reimplante o pacote ponto a site em todos os clientes.

## <a name="too-many-vpn-clients-connected-at-once"></a>Muitos clientes VPN conectados ao mesmo tempo

O número máximo de conexões permitidas é atingido. Você pode ver o número total de clientes conectados no portal do Azure.

## <a name="vpn-client-cannot-access-network-file-shares"></a>O cliente VPN não pode acessar compartilhamentos de arquivos de rede

### <a name="symptom"></a>Sintoma

O cliente VPN foi conectado à rede virtual do Azure. No entanto, o cliente não pode acessar compartilhamentos de rede.

### <a name="cause"></a>Causa

O protocolo SMB é usado para acesso de compartilhamento de arquivos. Quando a conexão é iniciada, o cliente VPN adiciona as credenciais da sessão e a falha ocorre. Depois que a conexão é estabelecida, o cliente é forçado a usar as credenciais de cache para a autenticação Kerberos. Esse processo inicia consultas para o Centro de Distribuição de Chaves (um controlador de domínio) para obter um token. Uma vez que o cliente conecta-se pela Internet, ele pode não conseguir alcançar o controlador de domínio. Portanto, o cliente não pode fazer failover do Kerberos para NTLM. 

A única vez em que o cliente é solicitado a fornecer uma credencial é quando ele tem um certificado válido (com SAN = UPN) emitido pelo domínio ao qual ele está associado. O cliente também deve estar fisicamente conectado à rede de domínio. Nesse caso, o cliente tenta usar o certificado e busca o controlador de domínio. Então o Centro de distribuição de chaves retorna um erro de "KDC_ERR_C_PRINCIPAL_UNKNOWN". O cliente é forçado a fazer failover para NTLM. 

### <a name="solution"></a>Solução

Para contornar o problema, desabilite o cache de credenciais de domínio da seguinte subchave do registro: 

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1`


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Não é possível localizar a conexão VPN ponto a site no Windows após a reinstalação do cliente VPN

### <a name="symptom"></a>Sintoma

Você remove a conexão VPN ponto a site e reinstala o cliente VPN. Nessa situação, a conexão VPN não foi configurada com êxito. Você não vê a conexão VPN nas configurações **Conexões de rede** do Windows.

### <a name="solution"></a>Solução

Para resolver o problema, exclua os arquivos de configuração de cliente VPN antigos de **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections \<VirtualNetworkId>** e execute o instalador do cliente VPN novamente.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>O cliente VPN ponto a site não pode resolver o FQDN dos recursos no domínio local

### <a name="symptom"></a>Sintoma

Quando o cliente se conecta ao Azure usando a conexão VPN ponto-a-site, ele não pode resolver o FQDN dos recursos em seu domínio local.

### <a name="cause"></a>Causa

O cliente VPN ponto a site normalmente usa servidores DNS do Azure configurados na rede virtual do Azure. Os servidores DNS do Azure têm precedência sobre os servidores DNS locais que são configurados no cliente (a menos que a métrica da interface Ethernet seja menor), de modo que todas as consultas DNS são enviadas para os servidores DNS do Azure. Se os servidores DNS do Azure não tiverem os registros dos recursos locais, a consulta falhará.

### <a name="solution"></a>Solução

Para resolver o problema, verifique se os servidores DNS do Azure que são usados em uma rede virtual do Azure podem resolver os registros DNS para recursos locais. Para fazer isso, você pode usar encaminhadores DNS ou encaminhadores condicionais. Para obter mais informações, consulte [Resolução de nome usando seu próprio servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>A conexão VPN ponto a site é estabelecida, mas ainda não é possível se conectar aos recursos do Azure 

### <a name="cause"></a>Causa

Esse problema poderá ocorrer se o cliente VPN não obtiver as rotas do gateway de VPN do Azure.

### <a name="solution"></a>Solução

Para resolver esse problema, [redefina o gateway de VPN do Azure](./reset-gateway.md). Para verificar se as novas rotas estão sendo usadas, os clientes VPN ponto a site devem ser baixados novamente após o emparelhamento da rede virtual ter sido configurado com êxito.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Erro: "A função de revogação não pôde verificar a revogação porque o servidor de revogação estava offline. (Erro 0x80092013)"

### <a name="causes"></a>Causas
Essa mensagem de erro ocorre se o cliente não puder acessar http://crl3.digicert.com/ssca-sha2-g1.crl e http://crl4.digicert.com/ssca-sha2-g1.crl.  A verificação de revogação requer acesso a esses dois sites.  Esse problema geralmente acontece em clientes que têm o servidor proxy configurado. Em alguns ambientes, se as solicitações não forem realizadas por meio do servidor proxy, elas serão negadas no Firewall do Edge.

### <a name="solution"></a>Solução

Verifique as configurações do servidor proxy, certifique-se de que o cliente pode acessar http://crl3.digicert.com/ssca-sha2-g1.crl e http://crl4.digicert.com/ssca-sha2-g1.crl.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>Erro de cliente VPN: A conexão foi impedida devido a uma política configurada no servidor RAS/VPN. (Erro 812)

### <a name="cause"></a>Causa

Esse erro ocorre se o servidor RADIUS usado para autenticar o cliente VPN tiver configurações incorretas ou se Gateway do Azure não puder alcançar o servidor Radius.

### <a name="solution"></a>Solução

Verifique se o servidor RADIUS está configurado corretamente. Para obter mais informações, consulte [integrar a autenticação RADIUS com o Azure AD servidor de autenticação multifator](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Erro 405" ao baixar o certificado raiz do Gateway de VPN

### <a name="cause"></a>Causa

O certificado raiz não foi instalado. O certificado raiz está instalado no repositório **Certificados confiáveis**.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>Erro de cliente VPN: a conexão remota não foi feita porque a tentativa de túneis VPN falhou. (Erro 800) 

### <a name="cause"></a>Causa

O driver NIC está desatualizado.

### <a name="solution"></a>Solução

Atualize o driver NIC:

1. Clique em **Iniciar**, digite **Gerenciador de Dispositivos** e selecione-o na lista de resultados. Se você for solicitado para uma senha de administrador ou confirmação, digite a senha ou forneça a confirmação.
2. Nas categorias **Adaptadores de rede**, localize o NIC a ser atualizado.  
3. Clique duas vezes no nome do dispositivo, selecione **Atualizar Driver**, selecione **Pesquisar automaticamente software de driver atualizado**.
4. Se o Windows não localizar um novo driver, você poderá tentar procurar um no site do fabricante do dispositivo e seguir as instruções.
5. Reinicie o computador e tente novamente a conexão.

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>Erro de cliente VPN: conexão VPN Dial-up <VPN Connection Name> , status = a plataforma VPN não disparava a conexão

Você também pode ver o seguinte erro em Visualizador de Eventos de RasClient: "o usuário <User> discado uma conexão chamada <VPN Connection Name> que falhou. O código de erro retornado na falha é 1460. "

### <a name="cause"></a>Causa

O cliente VPN do Azure não tem a permissão de aplicativo "aplicativos em segundo plano" habilitada nas configurações do aplicativo para Windows.

### <a name="solution"></a>Solução

1. No Windows, acesse configurações-> privacidade-> aplicativos em segundo plano
2. Alterne a opção "permitir que os aplicativos sejam executados em segundo plano" em

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Erro: 'erro no download do arquivo O URI de destino não foi especificado'

### <a name="cause"></a>Causa

Isso é causado por um tipo de gateway incorreto configurado.

### <a name="solution"></a>Solução

O tipo de gateway de VPN do Azure deve ser VPN e o tipo de VPN deve ser **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>O instalador do pacote VPN não foi concluído

### <a name="cause"></a>Causa

Esse problema pode ser causado pelas instalações anteriores do cliente VPN. 

### <a name="solution"></a>Solução

Exclua os arquivos de configuração de cliente VPN antigos de **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections \<VirtualNetworkId>** e execute o instalador de cliente VPN novamente. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>O cliente VPN hiberna ou suspende após algum tempo

### <a name="solution"></a>Solução

Verifique as configurações de suspensão e hibernação no computador em que o cliente VPN está executando.