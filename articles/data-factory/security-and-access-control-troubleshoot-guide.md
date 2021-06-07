---
title: Solucionar problemas de segurança e controle de acesso
description: Saiba como solucionar problemas de segurança e controle de acesso no Azure Data Factory.
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.author: lle
ms.openlocfilehash: 5e94ea989002d3d3c6d0e96123d5b8ddb5f078c3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568028"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Solucionar problemas Azure Data Factory segurança e controle de acesso

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas de segurança e controle de acesso no Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>Problema de conectividade na atividade de cópia do repositório de armazenamento de nuvem

#### <a name="symptoms"></a>Sintomas

Várias mensagens de erro podem ser retornadas quando ocorrem problemas de conectividade no repositório de armazenamento de origem ou de coletor.

#### <a name="cause"></a>Causa 

O problema geralmente é causado por um dos seguintes fatores:

* A configuração de proxy no nó do tempo de execução de integração (IR) auto-hospedado, se você estiver usando um IR de hospedagem interna.

* A configuração de firewall no nó IR auto-hospedado, se você estiver usando um IR de hospedagem interna.

* A configuração de firewall no repositório de armazenamento de nuvem.

#### <a name="resolution"></a>Resolução

* Para garantir que esse é um problema de conectividade, verifique os seguintes pontos:

   - O erro é gerado a partir dos conectores de origem ou de coletor.
   - A falha está no início da atividade de cópia.
   - A falha é consistente para Azure IR ou o IR auto-hospedado com um nó, pois pode ser uma falha aleatória em um IR de vários nós hospedados automaticamente se apenas alguns dos nós tiverem o problema.

* Se você estiver usando um **ir auto-hospedado**, verifique as configurações de proxy, firewall e rede, pois a conexão com o mesmo repositório de armazenamento poderá ter sucesso se você estiver usando um Azure ir. Para solucionar esse problema, consulte:

   * [Portas e firewalls de IR hospedados internamente](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Conector de Azure Data Lake Storage](./connector-azure-data-lake-store.md)
  
* Se você estiver usando uma **Azure ir**, tente desabilitar a configuração de firewall do repositório de armazenamento. Essa abordagem pode resolver os problemas nas duas situações a seguir:
  
   * [Azure ir endereços IP](./azure-integration-runtime-ip-addresses.md) não estão na lista de permissões.
   * O recurso *permitir que os serviços Microsoft confiáveis acessem este conta de armazenamento* está desativado para o [armazenamento de BLOBs do Azure](./connector-azure-blob-storage.md#supported-capabilities) e o [Azure data Lake Storage Gen 2](./connector-azure-data-lake-storage.md#supported-capabilities).
   * A configuração *permitir acesso aos serviços do Azure* não está habilitada para Azure data Lake Storage Gen1.

Se nenhum dos métodos anteriores funcionar, contate a Microsoft para obter ajuda.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Problema de chave de autenticação inválido ou vazio após o acesso à rede pública ser desabilitado

#### <a name="symptoms"></a>Sintomas

Depois de desabilitar o acesso à rede pública para Data Factory, o Integration Runtime de hospedagem interna gera o seguinte erro: "a chave de autenticação é inválida ou está vazia".

#### <a name="cause"></a>Causa

O problema é provavelmente causado por um problema de resolução de DNS (sistema de nomes de domínio), pois desabilitar a conectividade pública e estabelecer um ponto de extremidade privado impede a reconexão.

Para verificar se o FQDN (nome de domínio totalmente qualificado) do Data Factory é resolvido para o endereço IP público, faça o seguinte:

1. Confirme que você criou a VM (máquina virtual) do Azure na mesma rede virtual que o ponto de extremidade Data Factory privado.

2. Execute PsPing e ping da VM do Azure para o Data Factory FQDN:

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > Você deve especificar uma porta para o comando PsPing. A porta 443 é mostrada aqui, mas não é necessária.

3. Verifique se ambos os comandos são resolvidos para um Azure Data Factory IP público com base em uma região especificada. O IP deve estar no seguinte formato: `xxx.xxx.xxx.0`

#### <a name="resolution"></a>Resolução

Para resolver o problema, faça o seguinte:

- Como opção, gostaríamos de sugerir que você adicione manualmente um "link de rede virtual" na Data Factory "zona DNS de link privado". Para obter detalhes, consulte o [link privado do Azure para Azure data Factory](./data-factory-private-link.md#dns-changes-for-private-endpoints) artigo. A instrução é para configurar a zona DNS privada ou o servidor DNS personalizado para resolver o Data Factory FQDN para um endereço IP privado. 

- No entanto, se você não quiser configurar a zona DNS privada ou o servidor DNS personalizado, tente a seguinte solução temporária:

  1. Altere o arquivo de host no Windows e mapeie o IP privado (o Azure Data Factory ponto de extremidade privado) para o Azure Data Factory FQDN.
  
     Na VM do Azure, vá para `C:\Windows\System32\drivers\etc` e abra o arquivo de *host* no bloco de notas. Adicione a linha que mapeia o IP privado ao FQDN no final do arquivo e salve a alteração.
     
     ![Captura de tela do mapeamento do IP privado para o host.](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Execute novamente os mesmos comandos das etapas de verificação anteriores para verificar a resposta, que deve conter o IP privado.

  1. Registre novamente o tempo de execução de integração auto-hospedado e o problema deve ser resolvido.

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Não é possível registrar a chave de autenticação IR em VMs auto-hospedadas devido ao link privado

#### <a name="symptoms"></a>Sintomas

Você não pode registrar a chave de autenticação IR na VM auto-hospedada porque o link privado está habilitado. Você vê a seguinte mensagem de erro:

"Falha ao obter o token de serviço do serviço ADF com a chave * * * * * * * * * * * * * * * e o custo de tempo é: 0,1250079 segundo, o código de erro é: InvalidGatewayKey, ActivityId é: XXXXXXX e a mensagem de erro detalhada é que o endereço IP do cliente não é um IP privado válido, pois o data Factory não pôde acessar a rede pública, portanto, não é capaz de acessar a nuvem para fazer a conexão bem-sucedida."

#### <a name="cause"></a>Causa

O problema pode ser causado pela VM na qual você está tentando instalar o IR auto-hospedado. Para se conectar à nuvem, verifique se acesso à rede pública está habilitado.

#### <a name="resolution"></a>Resolução

**Solução 1**
 
Para resolver o problema, faça o seguinte:

1. Vá para a página [fábricas – atualização](/rest/api/datafactory/Factories/Update) .

1. No canto superior direito, selecione o botão **experimentar** .
1. Em **parâmetros**, conclua as informações necessárias. 
1. Em **corpo**, Cole a seguinte propriedade:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. Selecione **executar** para executar a função. 

1. Em **parâmetros**, conclua as informações necessárias. 

1. Em **corpo**, Cole a seguinte propriedade:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Selecione **executar** para executar a função. 
1. Confirme se o **código de resposta: 200** é exibido. A propriedade colada também deve ser exibida na definição de JSON.

1. Adicione a chave de autenticação IR novamente no Integration Runtime.

**Solução 2**

Para resolver o problema, vá para o [link privado do Azure para Azure data Factory](./data-factory-private-link.md).

Tente habilitar o acesso à rede pública na interface do usuário, conforme mostrado na seguinte captura de tela:

![Captura de tela do controle "habilitado" para "permitir acesso à rede pública" no painel de rede.](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

### <a name="adf-private-dns-zone-overrides-azure-resource-manager-dns-resolution-causing-not-found-error"></a>A zona DNS privada do ADF substitui Azure Resource Manager resolução DNS causando o erro ' não encontrado '

#### <a name="cause"></a>Causa
Tanto Azure Resource Manager quanto ADF estão usando a mesma zona privada criando um possível conflito no DNS privado do cliente com um cenário em que os registros de Azure Resource Manager não serão encontrados.

#### <a name="solution"></a>Solução
1. Localizar DNS privado zonas **privatelink.Azure.com** em portal do Azure.
![Captura de tela de localização de zonas de DNS privado.](media/security-access-control-troubleshoot-guide/private-dns-zones.png)
2. Verifique se há um **ADF** de registro a.
![Captura de tela de um registro.](media/security-access-control-troubleshoot-guide/a-record.png)
3.  Vá para **links de rede virtual**, exclua todos os registros.
![Captura de tela do link de rede virtual.](media/security-access-control-troubleshoot-guide/virtual-network-link.png)
4.  Navegue até o data factory em portal do Azure e recrie o ponto de extremidade privado para o portal de Azure Data Factory.
![Captura de tela da recriação do ponto de extremidade privado.](media/security-access-control-troubleshoot-guide/create-private-endpoint.png)
5.  Volte para DNS privado zonas e verifique se há uma nova zona DNS privada **privatelink.ADF.Azure.com**.
![Captura de tela do novo registro DNS.](media/security-access-control-troubleshoot-guide/check-dns-record.png)

### <a name="connection-error-in-public-endpoint"></a>Erro de conexão no ponto de extremidade público

#### <a name="symptoms"></a>Sintomas

Ao copiar dados com acesso público à conta de armazenamento de BLOBs do Azure, as execuções de pipeline aleatoriamente falham com o seguinte erro.

Por exemplo: o coletor de armazenamento de BLOBs do Azure estava usando Azure IR (VNet pública, não gerenciada) e a origem do banco de dados SQL do Azure estava usando o IR de VNet gerenciado. Ou origem/coletor, use o IR para rede virtual gerenciada somente com acesso público de armazenamento.

`
<LogProperties><Text>Invoke callback url with req:
"ErrorCode=UserErrorFailedToCreateAzureBlobContainer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Unable to create Azure Blob container. Endpoint: XXXXXXX/, Container Name: test.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.WindowsAzure.Storage.StorageException,Message=Unable to connect to the remote server,Source=Microsoft.WindowsAzure.Storage,''Type=System.Net.WebException,Message=Unable to connect to the remote server,Source=System,''Type=System.Net.Sockets.SocketException,Message=A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond public ip:443,Source=System,'","Details":null}}</Text></LogProperties>.
`

#### <a name="cause"></a>Causa

O ADF ainda pode usar o IR da VNet gerenciada, mas você pode encontrar esse erro porque o ponto de extremidade público para o armazenamento de BLOBs do Azure na VNet gerenciada não é confiável com base no resultado do teste, e o armazenamento de BLOBs do Azure e Azure Data Lake Gen2 não têm suporte para serem conectados por meio do ponto de extremidade público da rede virtual gerenciada pelo ADF de acordo [&](./managed-virtual-network-private-endpoint.md#outbound-communications-through-public-endpoint-from-adf-managed-virtual-network)com

#### <a name="solution"></a>Solução

- Ter um ponto de extremidade privado habilitado na origem e também o lado do coletor ao usar o IR da VNet gerenciada.
- Se você ainda quiser usar o ponto de extremidade público, poderá alternar para o IR público somente em vez de usar o IR da VNet gerenciada para a origem e o coletor. Mesmo que você alterne de volta para o IR público, o ADF ainda poderá usar o IR da VNet gerenciada se o IR da VNet gerenciada ainda estiver lá.

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente os seguintes recursos:

*  [Link privado para Data Factory](data-factory-private-link.md)
*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&uma página](/answers/topics/azure-data-factory.html)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)