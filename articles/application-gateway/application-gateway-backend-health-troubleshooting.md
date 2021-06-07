---
title: Solucionar problemas de integridade de back-end no Gateway de Aplicativo do Azure
description: Descreve como solucionar problemas de integridade de back-end para o Gateway de Aplicativo do Azure
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 95b74e5fc6c5d2c09ff04b3f14e920ae675ab6e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592748"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Solucionar problemas de integridade de back-end no Gateway de Aplicativo
==================================================

<a name="overview"></a>Visão geral
--------

Por padrão, o Gateway de Aplicativo do Azure investiga os servidores back-end para verificar seu status de integridade e se eles estão prontos para atender a solicitações. Os usuários também podem criar investigações personalizadas para mencionar o nome do host, o caminho a ser investigado e os códigos de status a serem aceitos como íntegros. Em cada caso, se o servidor back-end não responder com êxito, o Gateway de Aplicativo marcará o servidor como Não íntegro e interromperá o encaminhamento de solicitações ao servidor. Depois que o servidor começa a responder com êxito, o Gateway de Aplicativo retoma o encaminhamento das solicitações.

### <a name="how-to-check-backend-health"></a>Como verificar a integridade do back-end

Para verificar a integridade do pool de back-end, você pode usar a página **Integridade de Back-end** no portal do Azure. Ou você pode usar o [Azure PowerShell](/powershell/module/az.network/get-azapplicationgatewaybackendhealth), a [CLI](/cli/azure/network/application-gateway#az-network-application-gateway-show-backend-health) ou a [API REST](/rest/api/application-gateway/applicationgateways/backendhealth).

O status recuperado por qualquer um desses métodos pode ser um dos seguintes:

- Healthy

- Não Íntegro

- Unknown (desconhecido)

Se o status de integridade de back-end de um servidor for Íntegro, significa que o Gateway de Aplicativo encaminhará as solicitações para esse servidor. Mas se o status da integridade de back-end de todos os servidores em um pool de back-end for Não íntegro ou Desconhecido, talvez você tenha problemas ao tentar acessar aplicativos. Este artigo descreve os sintomas, a causa e a resolução de cada um dos erros mostrados.

<a name="backend-health-status-unhealthy"></a>Status de integridade de back-end: Não Íntegro
-------------------------------

Se o status da integridade do back-end for Não íntegro, a exibição do portal será semelhante à captura de tela a seguir:

![Status da integridade de back-end do Gateway de Aplicativo - Não íntegro](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Ou, se você estiver usando uma consulta do Azure PowerShell, CLI ou API REST, obterá uma resposta semelhante à seguinte:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Depois de você receber um status de servidor de back-end Não íntegro para todos os servidores em um pool de back-end, as solicitações não serão encaminhadas para os servidores e o Gateway de Aplicativo retornará um erro "502 Gateway Incorreto" ao cliente solicitante. Para solucionar esse problema, verifique a coluna **Detalhes** na guia **Integridade de Back-end**.

A mensagem exibida na coluna **Detalhes** fornece mais informações detalhadas sobre o problema e, com base nelas, você pode começar a solucionar o problema.

> [!NOTE]
> A solicitação de investigação padrão é enviada no formato \<protocol\> ://127.0.0.1: \<port\> /. Por exemplo, http://127.0.0.1:80 para uma investigação http na porta 80. Somente os códigos de status HTTP de 200 a 399 são considerados íntegros. O protocolo e a porta de destino são herdados das configurações de HTTP. Se você quiser que o Gateway de Aplicativo investigue um protocolo, nome do host ou caminho diferente e reconheça um código de status diferente como Íntegro, configure uma investigação personalizada e associe-a às configurações de HTTP.

<a name="error-messages"></a>Mensagens de erro
------------------------
#### <a name="backend-server-timeout"></a>Tempo limite do servidor back-end

**Mensagem:** o tempo gasto pelo back-end para responder à investigação de integridade do gateway\' de aplicativo é maior que o tempo limite na configuração da investigação.

**Causa:** depois que o Gateway de Aplicativo envia uma solicitação de investigação HTTP(S) ao servidor back-end, ele aguarda uma resposta do servidor back-end por um período configurado. Se o servidor back-end não responder dentro do período configurado (o valor do tempo limite), ele será marcado como Não íntegro até que comece a responder novamente dentro do período de tempo limite configurado.

**Resolução:** verifique por que o servidor ou aplicativo back-end não está respondendo dentro do período de tempo limite configurado e verifique também as dependências do aplicativo. Por exemplo, verifique se o banco de dados tem algum problema que possa desencadear um atraso na resposta. Se você estiver ciente do comportamento do aplicativo e ele responder apenas após o valor de tempo limite, aumente o valor de tempo limite nas configurações personalizadas da investigação. Você deve ter uma investigação personalizada para alterar o valor de tempo limite. Para obter informações sobre como configurar uma investigação personalizada, [confira a página de documentação](./application-gateway-create-probe-portal.md).

Para aumentar o valor de tempo limite, siga estas etapas:

1.  Acesse o servidor back-end diretamente e verifique o tempo necessário para que o servidor responda nessa página. É possível usar qualquer ferramenta para acessar o servidor back-end, incluindo um navegador com ferramentas de desenvolvedor.

1.  Depois de descobrir o tempo necessário para que o aplicativo responda, selecione a guia **Investigações de Integridade** e, em seguida, selecione a investigação associada às suas configurações de HTTP.

1.  Insira qualquer valor de tempo limite maior que o tempo de resposta do aplicativo, em segundos.

1.  Salve as configurações de investigação personalizadas e verifique se a integridade do back-end é mostrada como íntegra agora.

#### <a name="dns-resolution-error"></a>Erro de resolução de DNS

**Mensagem:** o Gateway de Aplicativo não conseguiu criar uma investigação para este back-end. Isso geralmente acontece quando o FQDN do back-end não foi inserido corretamente. 

**Causa:** se o pool de back-end for do tipo Endereço IP/FQDN ou Serviço de Aplicativo, o Gateway de Aplicativo será determinado para o endereço IP do FQDN inserido pelo DNS (Sistema de Nomes de Domínio) (personalizado ou padrão do Azure) e tentará se conectar ao servidor na porta TCP mencionada nas configurações de HTTP. Mas se essa mensagem for exibida, ela sugere que o Gateway de Aplicativo não conseguiu determinar com êxito o endereço IP do FQDN inserido.

**Resolução:**

1.  verifique se o FQDN inserido no pool de back-end está correto e se é de domínio público e tente determiná-lo a partir do computador local.

1.  Se for possível determinar o endereço IP, pode haver algo errado com a configuração de DNS na rede virtual.

1.  Verifique se a rede virtual está configurada com um servidor DNS personalizado. Se estiver, verifique porque o servidor DNS não determina o endereço IP do FQDN especificado.

1.  Se você estiver usando o DNS padrão do Azure, verifique com o seu registrador de nomes de domínio se o registro A ou o mapeamento do registro CNAME adequado foi concluído.

1.  Se o domínio for privado ou interno, tente determiná-lo a partir de uma VM na mesma rede virtual. Se for possível determiná-lo, reinicie o Gateway de Aplicativo e verifique novamente. Para reiniciar o Gateway de Aplicativo, é necessário [parar](/powershell/module/azurerm.network/stop-azurermapplicationgateway) e [iniciar](/powershell/module/azurerm.network/start-azurermapplicationgateway) usando os comandos do PowerShell descritos nesses recursos vinculados.

#### <a name="tcp-connect-error"></a>Erro de conexão TCP

**Mensagem:** o Gateway de Aplicativo não pôde se conectar ao back-end.
Verifique se o back-end responde na porta usada para a investigação.
Verifique também se algum NSG/UDR/firewall está bloqueando o acesso ao IP e à porta desse back-end

**Causa:** após a fase de resolução do DNS, o Gateway de Aplicativo tenta se conectar ao servidor back-end na porta TCP configurada nas configurações de HTTP. Se o Gateway de Aplicativo não puder estabelecer uma sessão TCP na porta especificada, o status da investigação passará a Não íntegro com esta mensagem.

**Solução:** se você receber esse erro, siga estas etapas:

1.  Verifique se você pode se conectar ao servidor back-end na porta mencionada nas configurações de HTTP usando um navegador ou o PowerShell. Por exemplo, execute o seguinte comando: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Se a porta mencionada não for a porta desejada, insira o número da porta correta para que o Gateway de Aplicativo se conecte ao servidor back-end

1.  Se também não for possível se conectar a partir do seu computador local, então:

    a.  Verifique as configurações do NSG (grupo de segurança de rede) do adaptador de rede e da sub-rede do servidor back-end e se são permitidas conexões de entrada com a porta configurada. Se este não for o caso, crie uma nova regra para permitir as conexões. Para saber como criar regras de NSG, [confira a página de documentação](../virtual-network/tutorial-filter-network-traffic.md#create-security-rules).

    b.  Verifique se as configurações de NSG da sub-rede do Gateway de Aplicativo permitem tráfego de saída público e privado, para que uma conexão possa ser estabelecida. Verifique a página de documentação fornecida na etapa 3a para saber mais sobre como criar regras NSG.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Verifique as configurações de UDR (rotas definidas pelo usuário) do Gateway de Aplicativo e a sub-rede do servidor back-end para quaisquer anomalias de roteamento. Verifique se o UDR não está direcionando o tráfego para longe da sub-rede de back-end. Por exemplo, verifique se há rotas para dispositivos virtuais em rede ou rotas padrão anunciadas para a sub-rede do Gateway de Aplicativo via Azure ExpressRoute e/ou VPN.

    d.  Para verificar as rotas e regras em vigor para um adaptador de rede, você pode usar os seguintes comandos do PowerShell:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Se você não encontrar problemas com NSG ou UDR, verifique no servidor back-end se há problemas relacionados a aplicativos que estão impedindo os clientes de estabelecer uma sessão TCP nas portas configuradas. Alguns pontos a verificar:

    a.  Abra um prompt de comando (Win + R-\> cmd), insira `netstat` e selecione Enter.

    b.  Verifique se o servidor está escutando na porta configurada. Por exemplo:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Se não estiver escutando na porta configurada, verifique as configurações do servidor Web. Por exemplo: associações de site no IIS, bloco de servidor no NGINX e host virtual no Apache.

    d.  Verifique as configurações do firewall do sistema operacional para garantir que o tráfego de entrada para a porta seja permitido.

#### <a name="http-status-code-mismatch"></a>Incompatibilidade de código de status HTTP

**Mensagem:** o código de status da resposta HTTP do back-end\' não corresponde à configuração da investigação. Esperado:{HTTPStatusCode0} Recebido:{HTTPStatusCode1}.

**Causa:** depois que a conexão TCP for estabelecida e um handshake TLS for concluído (se o TLS estiver ativado), o Gateway de Aplicativo enviará a investigação como uma solicitação HTTP GET para o servidor back-end. Conforme descrito anteriormente, a investigação padrão será \<protocol\> ://127.0.0.1: \<port\> /, e considerará os códigos de status de resposta no Rage 200 a 399 como íntegro. Se o servidor retornar qualquer outro código de status, ele será marcado como Não íntegro nesta mensagem.

**Solução:** dependendo do código de resposta do servidor back-end, você pode executar as seguintes etapas. Alguns dos códigos de status comuns estão listados aqui:

| **Erro** | **Ações** |
| --- | --- |
| Incompatibilidade de código de status de investigação: Recebido 401 | Verifique se o servidor back-end requer autenticação. As investigações do gateway de aplicativo não podem passar credenciais para autenticação. Permita que o código de status \"HTTP 401\" em investigação corresponda ou investigue um caminho em que o servidor não exija autenticação. | |
| Incompatibilidade de código de status de investigação: Recebido 403 | Acesso proibido. Verifique se o acesso ao caminho é permitido no servidor back-end. | |
| Incompatibilidade de código de status de investigação: Recebido 404 | Página não encontrada. Verifique se o caminho do nome do host está acessível no servidor back-end. Altere o nome do host ou o parâmetro do caminho para um valor acessível. | |
| Incompatibilidade de código de status de investigação: Recebido 405 | As solicitações de investigação do Gateway de Aplicativo usam o método HTTP GET. Verifique se o seu servidor permite esse método. | |
| Incompatibilidade de código de status de investigação: Recebido 500 | Erro interno do servidor. Verifique a integridade do servidor de back-end e se os serviços estão em execução. | |
| Incompatibilidade de código de status de investigação: Recebido 503 | Serviço indisponível. Verifique a integridade do servidor de back-end e se os serviços estão em execução. | |

Ou, se você acredita que a resposta é legítima e deseja que o Gateway de Aplicativo aceite outros códigos de status como Íntegros, é possível criar uma investigação personalizada. Essa abordagem é útil em situações em que o site de back-end precisa de autenticação. Como as solicitações de investigação não carregam credenciais de usuário, elas falharão e um código de status HTTP 401 será retornado pelo servidor back-end.

Para criar uma investigação personalizada, siga [estas etapas](./application-gateway-create-probe-portal.md).

#### <a name="http-response-body-mismatch"></a>Incompatibilidade no corpo da resposta HTTP

**Mensagem:** o corpo da resposta HTTP do back-end\' não corresponde à configuração de investigação. O corpo da resposta recebida não contém {cadeia de caracteres}.

**Causa:** Ao criar uma investigação personalizada, você tem a opção de marcar um servidor back-end como Íntegro, combinando com uma cadeia de caracteres do corpo da resposta. Por exemplo, você pode configurar o Gateway de Aplicativo para aceitar "não autorizado" como uma cadeia de caracteres a ser correspondida. Se a resposta do servidor back-end para a solicitação de investigação contiver a sequência **não autorizada**, ela será marcada como Íntegra. Caso contrário, ela será marcada como Não íntegra com esta mensagem.

**Solução:** Para resolver esse problema, siga estas etapas:

1.  Acesse o servidor back-end localmente ou a partir de um computador cliente no caminho da investigação e verifique o corpo da resposta.

1.  Verifique se o corpo da resposta na configuração de investigação personalizada do Gateway de Aplicativo corresponde ao que está configurado.

1.  Se não houver correspondência, altere a configuração da investigação para que ela tenha o valor correto da cadeia de caracteres a ser aceito.

Saiba mais sobre a [correspondência de investigação do Gateway de Aplicativo](./application-gateway-probe-overview.md#probe-matching).

>[!NOTE]
> Com relação a todas as mensagens de erro relacionadas ao TLS, para saber mais sobre o comportamento do SNI e as diferenças entre os SKUs v1 e v2, confira a página [Visão geral do TLS](ssl-overview.md).


#### <a name="backend-server-certificate-invalid-ca"></a>AC inválida do certificado do servidor back-end

**Mensagem:** O certificado do servidor usado pelo back-end não é assinado por uma CA (Autoridade de Certificação) bem conhecida. Permita o back-end no gateway de aplicativo carregando o certificado raiz do certificado do servidor usado pelo back-end.

**Causa:** O SSL de ponta a ponta com o Gateway de Aplicativo v2 exige que o certificado do servidor de back-end seja verificado para considerar o servidor íntegro.
Para que um certificado TLS/SSL seja confiável, o certificado do servidor back-end deve ser emitido por uma autoridade de certificação incluída no armazenamento confiável do Gateway de Aplicativo. Se o certificado não tiver sido emitido por uma autoridade de certificação confiável (por exemplo, certificados autoassinados), os usuários deverão carregar o certificado do emissor para o Gateway de Aplicativo.

**Solução:** Siga estas etapas para exportar e fazer upload do certificado raiz confiável para o Gateway de Aplicativo. (Essas etapas são para clientes Windows.)

1.  Entre no computador em que seu aplicativo está hospedado.

1.  Selecione Win+R ou clique com o botão direito do mouse no botão **Iniciar** e selecione **Executar**.

1.  Insira `certmgr.msc` e selecione Enter. Você também pode procurar pelo Gerenciador de Certificados no menu **Iniciar**.

1.  Localize o certificado, normalmente em `\Certificates - Current User\\Personal\\Certificates\`, e abra-o.

1.  Selecione o certificado raiz e, em seguida, selecione **Exibir Certificado**.

1.  Nas propriedades do certificado, selecione a guia **Detalhes**.

1.  Na guia **Detalhes**, selecione a opção **Copiar para Arquivo** e salve o arquivo no formato X.509 (.CER) codificado em Base-64.

1.  Abra a página **Configurações** de HTTP do Gateway de Aplicativo no portal do Azure.

1. Abra as configurações de HTTP, selecione **Adicionar Certificado** e localize o arquivo de certificado que você acabou de salvar.

1. Selecione **Salvar** para salvar as configurações de HTTP.

Como alternativa, você pode exportar o certificado raiz de um computador cliente acessando diretamente o servidor (ignorando o Gateway de Aplicativo) por meio do navegador e exportando o certificado raiz do navegador.

Para saber mais sobre como extrair e fazer upload de certificados raiz confiáveis no Gateway de Aplicativo, confira [Exportar certificado raiz confiável (para v2 SKU)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Incompatibilidade de certificado raiz confiável

**Mensagem:** o certificado raiz do certificado do servidor usado pelo back-end não corresponde ao certificado raiz confiável adicionado ao gateway de aplicativo. Certifique-se de adicionar o certificado raiz correto para permitir alist de back-end.

**Causa:** O SSL de ponta a ponta com o Gateway de Aplicativo v2 exige que o certificado do servidor de back-end seja verificado para considerar o servidor íntegro.
Para que um certificado TLS/SSL seja confiável, o certificado do servidor back-end deve ser emitido por uma autoridade de certificação incluída no armazenamento confiável do Gateway de Aplicativo. Se o certificado não tiver sido emitido por uma autoridade de certificação confiável (por exemplo, certificados autoassinados), os usuários deverão carregar o certificado do emissor para o Gateway de Aplicativo.

O certificado carregado para as configurações de HTTP do Gateway de Aplicativo deve corresponder ao certificado raiz do certificado do servidor back-end.

**Solução:** Se você receber essa mensagem de erro, há uma incompatibilidade entre o certificado que foi carregado no Gateway de Aplicativo e o que foi carregado no servidor back-end.

Siga as etapas de 1 a 11 no método anterior para carregar o certificado raiz confiável correto no Gateway de Aplicativo.

Para saber mais sobre como extrair e fazer upload de certificados raiz confiáveis no Gateway de Aplicativo, confira [Exportar certificado raiz confiável (para v2 SKU)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Este erro também pode ocorrer se o servidor back-end não trocar a cadeia completa do certificado, incluindo a Raiz > Intermediário (se aplicável) > Folha durante o handshake TLS. Para verificar, você pode usar os comandos OpenSSL de qualquer cliente e conectar-se ao servidor back-end usando as configurações definidas na investigação do Gateway de Aplicativo.

Por exemplo:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Se a saída não mostrar a cadeia completa do certificado que está sendo retornado, exporte o certificado novamente com a cadeia completa, incluindo o certificado raiz. Configure esse certificado em seu servidor de back-end. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>Nome comum inválido do certificado de back-end (CN)

**Mensagem:** o CN (nome comum) do certificado de back-end não corresponde ao cabeçalho do host da investigação.

**Causa:** o Gateway de Aplicativo verifica se o nome de host especificado na configuração de HTTP de back-end corresponde à configuração do CN apresentado pelo certificado TLS/SSL do servidor back-end. Esse é o comportamento Standard_v2 e WAF_v2 SKU (V2). A Indicação de Nome de Servidor (SNI) dos SKUs padrão e WAF (v1) é definida como o FQDN no endereço do pool de back-end. Para saber mais sobre o comportamento do SNI e as diferenças entre os SKUs v1 e v2, confira [Visão geral da terminação TLS e TLS de ponta a ponta com o Gateway de Aplicativo](ssl-overview.md).

No SKU v2, se houver uma investigação padrão (nenhuma análise personalizada foi configurada e associada), o SNI será definido a partir do nome do host mencionado nas configurações de HTTP. Ou, se "Escolher nome do host do endereço de back-end" for mencionado nas configurações de HTTP, onde o pool de endereços de back-end contém um FQDN válido, essa configuração será aplicada.

Se houver uma investigação personalizada associada às configurações de HTTP, o SNI será definido do nome de host mencionado na configuração da investigação personalizada. Ou, se **Escolher nome do host nas configurações HTTP de back-end** for selecionado na investigação customizada, o SNI será definido a partir do nome do host mencionado nas configurações de HTTP.

Se **Escolher nome do host do endereço de back-end** estiver definido nas configurações de HTTP, o conjunto de endereços de back-end deverá conter um FQDN válido.

Se você receber essa mensagem de erro, o CN de certificado de back-end não corresponde ao nome do host configurado na investigação personalizada ou nas configurações de HTTP (caso **Escolher nome do host nas configurações HTTP de back-end** seja selecionado). Se você estiver usando uma investigação padrão, o nome do host será definido como **127.0.0.1**. Se esse não for um valor desejado, você deverá criar uma investigação personalizada e associá-la às configurações de HTTP.

**Solução:**

Para resolver o problema, siga estas etapas.

Para Windows:

1.  Entre no computador em que seu aplicativo está hospedado.

1.  Selecione Win+R ou clique com o botão direito do mouse no botão **Iniciar** e selecione **Executar**.

1.  Digite **certmgr. msc** e selecione Enter. Você também pode procurar pelo Gerenciador de Certificados no menu **Iniciar**.

1.  Localize o certificado (normalmente em `\Certificates - Current User\\Personal\\Certificates`) e abra o certificado.

1.  Na guia **Detalhes**, verifique o **Assunto** do certificado.

1.  Verifique o CN do certificado a partir dos detalhes e inserir o mesmo no campo nome do host da investigação personalizada ou nas configurações de HTTP (se **Escolher nome do host nas configurações HTTP de back-end** estiver selecionado). Se esse não for o nome do host desejado para o seu site, você deve obter um certificado para esse domínio ou inserir o nome do host correto na investigação customizada ou na configuração de HTTP.

Para Linux usando OpenSSL:

1.  Execute este comando no OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Nas propriedades exibidas, localize o CN do certificado e insira o mesmo no campo nome do host das configurações de http. Se esse não for o nome do host desejado para o seu site, você deve obter um certificado para esse domínio ou inserir o nome do host correto na investigação customizada ou na configuração de HTTP.

#### <a name="backend-certificate-is-invalid"></a>O certificado de back-end é inválido

**Mensagem:** O certificado de back-end é inválido. A data atual não está dentro do intervalo de datas \"Válido de\" e \"Válido até\" no certificado.

**Causa:** todo certificado é fornecido com um intervalo de validade e a conexão HTTPS não será segura, a menos que o certificado TLS/SSL do servidor seja válido. Os dados atuais devem estar dentro do intervalo **válido de** e **válido até**. Caso contrário, o certificado é considerado inválido e isso criará um problema de segurança no qual o Gateway de Aplicativo marcará o servidor back-end como Não íntegro.

**Solução:** Se o certificado TLS/SSL tiver expirado, renove-o com seu fornecedor e atualize as configurações do servidor com o novo certificado. Se for um certificado autoassinado, você deverá gerar um certificado válido e carregar o certificado raiz nas configurações de HTTP do Gateway de Aplicativo. Para fazer isso, siga estas etapas:

1.  Abra as configurações de HTTP do Gateway de Aplicativo no portal.

1.  Selecione a configuração que possui o certificado expirado, selecione **Adicionar Certificado** e abra o novo arquivo de certificado.

1.  Remova o certificado antigo usando o ícone **Excluir** ao lado do certificado e selecione **Salvar**.

#### <a name="certificate-verification-failed"></a>Falha na verificação do certificado

**Mensagem:** não foi possível verificar a validade do certificado de back-end. Para descobrir o motivo, verifique o diagnóstico do OpenSSL para obter a mensagem associada ao código de erro {errorCode}

**Causa:** esse erro ocorre quando o gateway de aplicativo não pode verificar a validade do certificado.

**Solução:** para resolver esse problema, verifique se o certificado no seu servidor foi criado corretamente. Por exemplo, você pode usar o [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) para verificar o certificado e suas propriedades e tentar recarregar o certificado nas configurações de HTTP do Gateway de Aplicativo.

<a name="backend-health-status-unknown"></a>Status de integridade de back-end: desconhecido
-------------------------------
Se o status da integridade do back-end for Desconhecido, a exibição do portal será semelhante à captura de tela a seguir:

![Status da integridade de back-end do Gateway de Aplicativo - Desconhecido](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Esse comportamento pode ocorrer por uma ou mais das seguintes razões:

1.  O NSG na sub-rede do Gateway de Aplicativo está bloqueando o acesso de entrada às portas 65503-65534 (v1 SKU) ou 65200-65535 (v2 SKU) da "Internet".
1.  O UDR na sub-rede do Gateway de Aplicativo é definido como a rota padrão (0.0.0.0/0) e o próximo salto não é especificado como "Internet".
1.  A rota padrão é anunciada por uma conexão ExpressRoute/VPN à rede virtual através do BGP.
1.  O servidor DNS personalizado está configurado em uma rede virtual que não pode resolver nomes de domínio público.
1.  O Gateway de Aplicativo está em um estado Não Íntegro.

**Solução:**

1.  Verifique se o NSG está bloqueando o acesso às portas 65503-65534 (v1 SKU) ou 65200-65535 (v2 SKU) da **Internet**:

    a.  Na guia **Visão Geral** do Gateway de Aplicativo, selecione o link **Rede Virtual/Sub-rede**.

    b.  Na guia **Sub-redes** da sua rede virtual, selecione a sub-rede na qual o Gateway de Aplicativo foi implantado.

    c.  Verifique se algum NSG está configurado.

    d.  Se um NSG estiver configurado, procure esse recurso NSG na guia **Pesquisar** ou em **Todos os Recursos**.

    e.  Na seção **Regras de Entrada**, adicione uma regra de entrada para permitir o intervalo de porta de destino 65503-65534 para SKU v1 ou 65200-65535 v2 SKU com a **Fonte** definida como **Qualquer** ou **Internet**.

    f.  Selecione **Salvar** e verifique se você pode exibir o back-end como Íntegro. Como alternativa, você pode fazer isso por meio do [PowerShell/CLI](../virtual-network/manage-network-security-group.md).

1.  Verifique se o UDR tem uma rota padrão (0.0.0.0/0) com o próximo salto não definido como **Internet**:
    
    a.  Siga as etapas 1a e 1b para determinar sua sub-rede.

    b.  Verifique se há qualquer UDR configurado. Se houver, pesquise o recurso na barra de pesquisa ou em **Todos os Recursos**.

    c.  Verifique se há alguma rota padrão (0.0.0.0/0) com o próximo salto não definido como **Internet**. Se a configuração for **Solução de Virtualização** ou **Gateway de Rede Virtual**, você deverá certificar-se de que sua solução de virtualização ou o dispositivo local possa rotear corretamente o pacote de volta para o destino da Internet sem modificar o pacote.

    d.  Caso contrário, altere o próximo salto para **Internet**, selecione **Salvar** e verifique a integridade do back-end.

1.  Rota padrão anunciada pela conexão ExpressRoute/VPN à rede virtual através do BGP:

    a.  Se você tem uma conexão ExpressRoute/VPN com a rede virtual por BGP e está anunciando uma rota padrão, verifique se o pacote é roteado de volta ao destino da Internet sem modificá-lo. Você pode verificar usando a opção **Solução de Problemas de Conexão** no portal do Gateway de Aplicativo.

    b.  Escolha o destino manualmente como qualquer endereço IP roteável da Internet, como 1.1.1.1. Defina a porta de destino como qualquer uma, e verifique a conectividade.

    c.  Se o próximo salto for o gateway de rede virtual, pode haver uma rota padrão anunciada pelo ExpressRoute ou VPN.

1.  Se houver um servidor DNS personalizado configurado na rede virtual, verifique se o servidor (ou servidores) pode determinar domínios públicos. A resolução de nomes de domínio público pode ser necessária em cenários em que o Gateway de Aplicativo deve acessar domínios externos como servidores OCSP ou verificar o status de revogação do certificado.

1.  Para verificar se o Gateway de Aplicativo está íntegro e em execução, acesse a opção **Resource Health** no portal e verifique se o estado é **Íntegro**. No caso de um estado **Não Íntegro** ou **Degradado**, entre em [contato com o suporte](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Próximas etapas
----------

Saiba mais sobre [Diagnóstico e log do Gateway de Aplicativo](./application-gateway-diagnostics.md).
