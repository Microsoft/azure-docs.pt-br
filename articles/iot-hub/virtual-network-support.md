---
title: Suporte ao Hub IoT do Azure para redes virtuais
description: Como usar o padrão de conectividade de redes virtuais com o Hub IoT
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jlian
ms.openlocfilehash: 1e28c7767868904fb20ae6d27c6aea9e7077eb62
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630286"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>Suporte do Hub IoT para redes virtuais com Link Privado e Identidade Gerenciada

Por padrão, os nomes de host do Hub IoT são mapeados para um ponto de extremidade público com um endereço IP roteado publicamente pela Internet. Diferentes clientes compartilham esse ponto de extremidade público do Hub IoT e dispositivos IoT em redes de longa distância e redes locais podem acessá-lo.

![Ponto de extremidade público do Hub IoT](./media/virtual-network-support/public-endpoint.png)

Os recursos do Hub IoT, incluindo [roteamento de mensagens](./iot-hub-devguide-messages-d2c.md), [carregamento de arquivos](./iot-hub-devguide-file-upload.md) e [importação/exportação de dispositivos em massa](./iot-hub-bulk-identity-mgmt.md) também exigem conectividade do Hub IoT para um recurso do Azure de propriedade do cliente sobre seu ponto de extremidade público. Esses caminhos de conectividade compõem coletivamente o tráfego de saída do Hub IoT para os recursos do cliente.

Convém restringir a conectividade aos recursos do Azure (incluindo o Hub IoT) por meio de uma VNet que você possui e opera. Esses motivos incluem:

* A introdução ao isolamento de rede para o Hub IoT, impedindo a exposição da conectividade à Internet pública.

* A habilitação de uma experiência de conectividade privada de seus ativos de rede local, garantindo que seus dados e tráfego sejam transmitidos diretamente para a rede de backbone do Azure.

* Impedir ataques de vazamento de redes locais confidenciais. 

* Seguindo os padrões estabelecidos de conectividade de todo o Azure usando [pontos de extremidade privados](../private-link/private-endpoint-overview.md).

Este artigo descreve como atingir essas metas usando [Link Privado do Azure](../private-link/private-link-overview.md) para conectividade de entrada ao Hub IoT e usando exceção de serviços confiáveis da Microsoft para a conectividade de saída do Hub IoT para outros recursos do Azure.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Conectividade de entrada para Hub IoT usando Link Privado do Azure

Um ponto de extremidade privado é um endereço IP privado alocado dentro de uma VNet de propriedade do cliente por meio da qual um recurso do Azure pode ser acessado. Por meio do Link Privado do Azure, você pode configurar um ponto de extremidade privado para o Hub IoT para permitir que os serviços dentro de sua VNet alcancem o Hub IoT sem exigir que o tráfego seja enviado ao ponto de extremidade público do Hub IoT. Da mesma forma, seus dispositivos locais podem usar emparelhamento da [Rede Virtual Privada (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou do [ExpressRoute](https://azure.microsoft.com/services/expressroute/) para obter conectividade com sua VNet e seu Hub IoT (por meio de seu ponto de extremidade privado). Como resultado, você pode restringir ou bloquear completamente a conectividade com os pontos de extremidade públicos do seu hub IoT usando o [filtro IP do Hub IOT](./iot-hub-ip-filtering.md) ou [a alternância de acesso à rede pública](iot-hub-public-network-access.md). Essa abordagem mantém a conectividade com o seu Hub usando o ponto de extremidade privado para dispositivos. O foco principal dessa configuração é para dispositivos dentro de uma rede local. Essa configuração não é recomendada para dispositivos implantados em uma rede de longa distância.

![Engress de rede virtual do Hub IoT](./media/virtual-network-support/virtual-network-ingress.png)

Antes de continuar, verifique se os seguintes pré-requisitos foram atendidos:

* Você [criou uma VNet do Azure](../virtual-network/quick-create-portal.md) com uma sub-rede na qual o ponto de extremidade privado será criado.

* Para dispositivos que operam em redes locais, configure o emparelhamento da [Rede Virtual Privada (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou do [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privado em sua VNet do Azure.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Configurar um ponto de extremidade privado para a entrada do Hub IoT

O ponto de extremidade privado funciona para APIs de dispositivo do Hub IoT (como mensagens do dispositivo para a nuvem), bem como APIs de serviço (como criar e atualizar dispositivos).

1. No portal do Azure, selecione **Rede**, **Conexões de ponto de extremidade privado** e clique no **+ Ponto de extremidade privado**.

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Captura de tela que mostra onde adicionar um ponto de extremidade privado para o Hub IoT":::

1. Forneça a assinatura, o grupo de recursos, o nome e a região para criar o novo ponto de extremidade privado. Idealmente, o ponto de extremidade privado deve ser criado na mesma região que o hub.

1. Clique em **Avançar: Recurso**  e forneça a assinatura do recurso do Hub IoT e selecione **“Microsoft.Devices/IotHubs”** como tipo de recurso, o nome do Hub IoT como **recurso** e **iotHub**  como sub-fonte de destino.

1. Clique em **Avançar: Configuração** e forneça sua rede virtual e sub-rede para criar o ponto de extremidade privado. Selecione a opção para integrar-se à zona DNS privada do Azure, se desejado.

1. Clique em **Avançar: Marcas** e, opcionalmente, forneça qualquer marca para o seu recurso.

1. Clique em **Revisar + criar** para criar seu recurso de link privado.

### <a name="built-in-event-hub-compatible-endpoint"></a>Ponto de extremidade compatível com o Hub de eventos interno 

O [ponto de extremidade compatível com o Hub de eventos interno](iot-hub-devguide-messages-read-builtin.md) também pode ser acessado por meio do ponto de extremidade privado. Quando o link privado estiver configurado, você deverá ver uma conexão de ponto de extremidade particular adicional para o ponto de extremidade interno. É aquele com `servicebus.windows.net` no FQDN.

:::image type="content" source="media/virtual-network-support/private-built-in-endpoint.png" alt-text="Imagem mostrando dois pontos de extremidade privados, considerando cada link privado do Hub IoT":::

O [filtro IP](iot-hub-ip-filtering.md) do Hub IOT pode, opcionalmente, controlar o acesso público ao ponto de extremidade interno. 

Para bloquear completamente o acesso à rede pública ao seu hub IoT, [desative o acesso à rede pública](iot-hub-public-network-access.md) ou use o filtro IP para bloquear todo o IP e selecione a opção para aplicar regras ao ponto de extremidade interno.

### <a name="pricing-for-private-link"></a>Preços para o Link Privado

Para obter detalhes de preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Conectividade de saída do Hub IoT para outros recursos do Azure

O Hub IoT pode se conectar ao armazenamento de blobs do Azure, hub de eventos, recursos do barramento de serviço para [roteamento de mensagens](./iot-hub-devguide-messages-d2c.md), [carregamento de arquivos](./iot-hub-devguide-file-upload.md) e [importação/exportação de dispositivos em massa](./iot-hub-bulk-identity-mgmt.md) sobre o ponto de extremidade público dos recursos. A associação do seu recurso a uma VNet bloqueia a conectividade com o recurso por padrão. Como resultado, essa configuração impede que o Hub IoT trabalhe enviando dados para seus recursos. Para corrigir esse problema, habilite a conectividade do recurso de Hub IoT para sua conta de armazenamento, hub de eventos ou recursos do barramento de serviço por meio da opção **serviço confiável da Microsoft**.

### <a name="turn-on-managed-identity-for-iot-hub"></a>Ativar a identidade gerenciada para Hub IoT

Para permitir que outros serviços localizem seu hub IoT como um serviço confiável da Microsoft, ele deve ter uma identidade gerenciada atribuída pelo sistema.

1. Vá para **Identidade** no portal do Hub IoT

1. Em **Status**, selecione **Ativado** e, em seguida, clique em **Salvar**.

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="Captura de tela que mostra como ativar a identidade gerenciada para o Hub IoT":::

Para usar CLI do Azure para ativar a identidade gerenciada:

```azurecli-interactive
az iot hub update --name <iot-hub-resource-name> --set identity.type="SystemAssigned"
```

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>Atribuir identidade gerenciada ao Hub IoT no momento da criação usando o modelo ARM

Para atribuir identidade gerenciada ao Hub IoT no tempo de provisionamento de recursos, use o modelo ARM abaixo. Esse modelo de ARM tem dois recursos necessários e ambos precisam ser implantados antes de criar outros recursos como o `Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups` . 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "createIotHub",
      "dependsOn": [
        "[resourceId('Microsoft.Devices/IotHubs', '<provide-a-valid-resource-name>')]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

Depois de substituir os valores do recurso `name` , `location` `SKU.name` e `SKU.tier` , você pode usar CLI do Azure para implantar o recurso em um grupo de recursos existente usando:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Depois que o recurso for criado, você poderá recuperar a identidade de serviço gerenciada atribuída ao seu hub usando CLI do Azure:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

### <a name="pricing-for-managed-identity"></a>Preço para identidade gerenciada

O recurso confiável de exceção de serviços de terceiros da Microsoft é gratuito. Os preços das contas de armazenamento provisionadas, dos hubs de eventos ou dos recursos do barramento de serviço são aplicados separadamente.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Conectividade de saída para pontos de extremidade da conta de armazenamento para roteamento

O Hub IoT pode rotear mensagens para uma conta de armazenamento de propriedade do cliente. Para permitir que a funcionalidade de roteamento acesse uma conta de armazenamento enquanto as restrições do firewall estiverem em vigor, o Hub IoT precisa ter uma [identidade gerenciada](#turn-on-managed-identity-for-iot-hub). Depois que uma identidade gerenciada for provisionada, siga as etapas abaixo para conceder permissão de RBAC do Azure à identidade de recurso do hub para acessar sua conta de armazenamento.

1. No portal do Azure, vá até a guia **Controle de acesso (IAM)** da conta de armazenamento e clique em **Adicionar** na seção **Adicionar uma atribuição de função**.

2. Selecione **Colaborador de dados de blobs de armazenamento** ([*não* Colaborador ou Colaborador da Conta de Armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) como **função**, **usuário, grupo ou entidade de serviço do Azure AD** como **Atribuindo acesso a** e selecione o nome do recurso do Hub IoT na lista suspensa. Clique no botão **Salvar** .

3. Vá até a guia **Firewalls e redes virtuais** na sua conta de armazenamento e habilite a opção **Permitir acesso a partir das redes selecionadas**. Na lista **Exceções**, marque a caixa **Permitir que serviços confiáveis da Microsoft acessem esta conta de armazenamento**. Clique no botão **Salvar** .

4. Na página de recursos do seu Hub IoT, vá até a guia **Roteamento de mensagens**.

5. Vá para a seção **Pontos de extremidade personalizados** e clique em **Adicionar**. Selecione **Armazenamento** para o tipo de ponto de extremidade.

6. Na página que aparece, forneça um nome para o ponto de extremidade, selecione o contêiner que você pretende usar no armazenamento de blobs, forneça a codificação e o formato de nome de arquivo. Selecione **baseado em identidade** como o **tipo de autenticação** para o ponto de extremidade de armazenamento. Selecione o botão **Criar**.

Agora seu ponto de extremidade de armazenamento personalizado está configurado para usar a identidade atribuída do sistema do hub e tem permissão para acessar seu recurso de armazenamento, apesar das restrições do firewall. Agora você pode usar esse ponto de extremidade para configurar uma regra de roteamento.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Conectividade de saída para pontos de extremidade dos hubs de eventos para roteamento

O Hub IoT pode ser configurado para rotear mensagens para um namespace de hubs de eventos de propriedade do cliente. Para permitir que a funcionalidade de roteamento acesse um recurso de hubs de eventos enquanto houver restrições de firewall, o Hub IoT precisa ter uma identidade gerenciada. Depois que uma identidade gerenciada for criada, siga as etapas abaixo para conceder permissão de RBAC do Azure para a identidade de recurso do hub para acessar seus hubs de eventos.

1. No portal do Azure, vá até a guia **Controle de acesso (IAM)** dos hubs de eventos e clique em **Adicionar** na seção **Adicionar uma atribuição de função**.

2. Selecione **Remetente de dados dos hubs de eventos** como **função**, **usuário, grupo ou entidade de serviço do Azure AD** como **Atribuindo acesso a** e selecione o nome do recurso do Hub IoT na lista suspensa. Clique no botão **Salvar** .

3. Vá até a guia **Firewalls e redes virtuais** nos seus hubs de eventos e habilite a opção **Permitir acesso a partir das redes selecionadas**. Na lista **Exceções**, marque a caixa **Permitir que serviços confiáveis da Microsoft acessem hubs de eventos**. Clique no botão **Salvar** .

4. Na página de recursos do seu Hub IoT, vá até a guia **Roteamento de mensagens**.

5. Vá para a seção **Pontos de extremidade personalizados** e clique em **Adicionar**. Selecione **Hubs de eventos** para o tipo de ponto de extremidade.

6. Na página que aparece, forneça um nome para o ponto de extremidade, selecione o namespace e a instância dos hubs de eventos. Selecione **baseado em identidade** como o **tipo de autenticação** e clique no botão **criar** .

Agora seu ponto de extremidade dos hubs de eventos está configurado para usar a identidade atribuída do sistema do hub e tem permissão para acessar seus hubs de eventos, apesar das restrições do firewall. Agora você pode usar esse ponto de extremidade para configurar uma regra de roteamento.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Conectividade de saída para pontos de extremidade de barramento de serviço para roteamento

O Hub IoT pode ser configurado para rotear mensagens para um namespace de barramento de serviço de propriedade do cliente. Para permitir que a funcionalidade de roteamento acesse um recurso de barramento de serviço enquanto houver restrições de firewall, o Hub IoT precisa ter uma identidade gerenciada. Depois que uma identidade gerenciada for provisionada, siga as etapas abaixo para conceder permissão de RBAC do Azure à identidade de recurso do hub para acessar o barramento de serviço.

1. No portal do Azure, vá até a guia **Controle de acesso (IAM)** do barramento de serviço e clique em **Adicionar** na seção **Adicionar uma atribuição de função**.

2. Selecione **Remetente de dados do barramento de serviço** como **função**, **usuário, grupo ou entidade de serviço do Azure AD** como **Atribuindo acesso a** e selecione o nome do recurso do Hub IoT na lista suspensa. Clique no botão **Salvar** .

3. Vá até a guia **Firewalls e redes virtuais** no seu barramento de serviço e habilite a opção **Permitir acesso a partir das redes selecionadas**. Na lista **Exceções**, marque a caixa **Permitir que serviços confiáveis da Microsoft acessem este barramento de serviço**. Clique no botão **Salvar** .

4. Na página de recursos do seu Hub IoT, vá até a guia **Roteamento de mensagens**.

5. Vá para a seção **Pontos de extremidade personalizados** e clique em **Adicionar**. Selecione **Fila do barramento de serviço** ou **Tópico do Barramento de Serviço** (conforme aplicável) como o tipo de ponto de extremidade.

6. Na página que aparece, forneça um nome para o ponto de extremidade, selecione o namespace e a fila ou tópico do barramento de serviço (conforme aplicável). Selecione **baseado em identidade** como o **tipo de autenticação** e clique no botão **criar** .

Agora seu ponto de extremidade do barramento de serviço está configurado para usar a identidade atribuída do sistema do hub e tem permissão para acessar seu barramento de serviço, apesar das restrições do firewall. Agora você pode usar esse ponto de extremidade para configurar uma regra de roteamento.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Conectividade de saída para contas de armazenamento para carregamento de arquivos

O recurso de carregamento de arquivo do Hub IoT permite que dispositivos carreguem arquivos para uma conta de armazenamento de propriedade do cliente. Para permitir que o carregamento do arquivo funcione, os dispositivos e o Hub IoT precisam ter conectividade com a conta de armazenamento. Se a conta de armazenamento tiver restrições de firewall, os dispositivos precisarão usar qualquer um dos mecanismos de conta de armazenamento com suporte (incluindo [pontos de extremidade privados](../private-link/tutorial-private-endpoint-storage-portal.md), [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) ou [configuração de firewall direto](../storage/common/storage-network-security.md)) para obter conectividade. Da mesma forma, se a conta de armazenamento tiver restrições de firewall, o Hub IoT precisará ser configurado para acessar o recurso de armazenamento por meio da exceção de serviços confiáveis da Microsoft. Para esse fim, o Hub IoT deve ter uma identidade gerenciada. Depois que uma identidade gerenciada for provisionada, siga as etapas abaixo para conceder permissão de RBAC do Azure à identidade de recurso do hub para acessar sua conta de armazenamento.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

1. No portal do Azure, vá até a guia **Controle de acesso (IAM)** da conta de armazenamento e clique em **Adicionar** na seção **Adicionar uma atribuição de função**.

2. Selecione **Colaborador de dados de blobs de armazenamento** ([*não* Colaborador ou Colaborador da Conta de Armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) como **função**, **usuário, grupo ou entidade de serviço do Azure AD** como **Atribuindo acesso a** e selecione o nome do recurso do Hub IoT na lista suspensa. Clique no botão **Salvar** .

3. Vá até a guia **Firewalls e redes virtuais** na sua conta de armazenamento e habilite a opção **Permitir acesso a partir das redes selecionadas**. Na lista **Exceções**, marque a caixa **Permitir que serviços confiáveis da Microsoft acessem esta conta de armazenamento**. Clique no botão **Salvar** .

4. Na página de recursos do seu Hub IoT, vá até a guia **Carregamento de arquivos**.

5. Na página que aparece, selecione o contêiner que você pretende usar em seu armazenamento de blobs, defina as **Configurações de notificação de arquivo**, **TTL do SAS**, **TTL padrão** e a **Contagem de entrega máxima** conforme desejado. Selecione **baseado em identidade** como o **tipo de autenticação** para o ponto de extremidade de armazenamento. Selecione o botão **Criar**. Se você receber um erro nesta etapa, defina temporariamente sua conta de armazenamento para permitir o acesso de **todas as redes** e tente novamente. Você pode configurar o firewall na conta de armazenamento depois que a configuração de carregamento de arquivo for concluída.

Agora seu ponto de extremidade de armazenamento para carregamento de arquivo está configurado para usar a identidade atribuída do sistema do hub e tem permissão para acessar seu recurso de armazenamento, apesar das restrições do firewall.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Conectividade de saída para contas de armazenamento para importação/exportação de dispositivos em massa

O Hub IoT tem suporte para a funcionalidade de [importação/exportação](./iot-hub-bulk-identity-mgmt.md) de informações dos dispositivos em massa de/para um blob de armazenamento fornecido pelo cliente. Para permitir que o recurso de importação/exportação em massa funcione, os dispositivos e o Hub IoT precisam ter conectividade com a conta de armazenamento.

Essa funcionalidade requer conectividade do Hub IoT à conta de armazenamento. Para acessar um recurso de barramento de serviço enquanto houver restrições de firewall, o Hub IoT precisa ter uma identidade gerenciada. Depois que uma identidade gerenciada for provisionada, siga as etapas abaixo para conceder permissão de RBAC do Azure à identidade de recurso do hub para acessar o barramento de serviço.

1. No portal do Azure, vá até a guia **Controle de acesso (IAM)** da conta de armazenamento e clique em **Adicionar** na seção **Adicionar uma atribuição de função**.

2. Selecione **Colaborador de dados de blobs de armazenamento** ([*não* Colaborador ou Colaborador da Conta de Armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) como **função**, **usuário, grupo ou entidade de serviço do Azure AD** como **Atribuindo acesso a** e selecione o nome do recurso do Hub IoT na lista suspensa. Clique no botão **Salvar** .

3. Vá até a guia **Firewalls e redes virtuais** na sua conta de armazenamento e habilite a opção **Permitir acesso a partir das redes selecionadas**. Na lista **Exceções**, marque a caixa **Permitir que serviços confiáveis da Microsoft acessem esta conta de armazenamento**. Clique no botão **Salvar** .

Agora você pode usar as APIs REST do IoT do Azure para [criar trabalhos de importação/exportação](/rest/api/iothub/service/jobs/getimportexportjobs) para obter informações sobre como usar a funcionalidade de importação/exportação em massa. Você precisará fornecer o `storageAuthenticationType="identityBased"` no corpo da solicitação e usar `inputBlobContainerUri="https://..."` e `outputBlobContainerUri="https://..."` como as URLs de entrada e saída da sua conta de armazenamento, respectivamente.

Os SDKs do Hub IoT do Azure também dão suporte a essa funcionalidade no gerenciador de registro do cliente de serviço. O snippet de código de C# a seguir mostra como iniciar um trabalho de importação ou exportação usando o SDK de C#.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```

Para usar esta versão dos SDKs do IoT do Azure com suporte de rede virtual para C# , Java e Node.js:

1. Crie uma variável de ambiente chamada `EnableStorageIdentity` e defina seu valor como `1`.

2. Baixe o SDK:  [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
Para o Python, faça o download da nossa versão limitada do GitHub.

1. Vá para a [Página de versão do GitHub](https://aka.ms/vnetpythonsdk).

2. Faça o download do arquivo a seguir, que você encontrará na parte inferior da página de versão sob o cabeçalho chamado **ativos**.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Abra um terminal e vá até a pasta com o arquivo baixado.

4. Execute o seguinte comando para instalar o SDK do Python Service com suporte para redes virtuais:
    > pip install ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Próximas etapas

Use os links abaixo para saber mais sobre os recursos do Hub IoT:

* [Roteamento de mensagem](./iot-hub-devguide-messages-d2c.md)
* [Carregamento de arquivos](./iot-hub-devguide-file-upload.md)
* [Importação/exportação de dispositivos em massa](./iot-hub-bulk-identity-mgmt.md)
