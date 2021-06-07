---
title: Configurar o cache conectado da Microsoft para atualização de dispositivo para o Hub IoT do Azure | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Visão geral do cache conectado da Microsoft para atualização de dispositivo para o Hub IoT do Azure
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 2903407f88b57a7be948cdeb0610e6d65df975b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661780"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Configurar o cache conectado da Microsoft para atualização de dispositivo para o Hub IoT do Azure

O cache conectado da Microsoft é implantado para Azure IoT Edge gateways como um módulo Azure IoT Edge. Assim como outros módulos Azure IoT Edge, as variáveis de ambiente de implantação de módulo MCC e as opções de criação de contêiner são usadas para configurar os módulos de cache conectados da Microsoft.  Esta seção define as variáveis de ambiente e as opções de criação de contêiner que são necessárias para um cliente implantar com êxito o módulo de cache conectado da Microsoft para uso pela atualização de dispositivo para o Hub IoT do Azure.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Detalhes de implantação do módulo Azure IoT Edge de cache conectado da Microsoft

A nomenclatura do módulo de cache conectado da Microsoft é a critério do administrador. Não há outras interações de módulo ou serviço que dependem do nome do módulo para comunicação. Além disso, a relação pai filho dos servidores de cache conectados da Microsoft não depende desse nome de módulo, mas sim o endereço IP ou FQDN do gateway de Azure IoT Edge que foi configurado como discutido anteriormente.

O cache conectado da Microsoft Azure IoT Edge variáveis de ambiente de módulo são usadas para passar informações de identidade de módulo básico e configurações de módulo funcional para o contêiner.

| Nome da variável                 | Formato do valor                           | Obrigatório/Opcional | Funcionalidade                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | GUID da ID de assinatura do Azure             | Obrigatório          | Essa é a chave do cliente, que fornece segurança<br>autenticação do nó de cache para otimização de entrega<br>. Necessário para que o módulo funcione. |
| CACHE_NODE_ID                 | GUID de ID de nó de cache                     | Obrigatório          | Identifica exclusivamente o cache conectado da Microsoft<br>nó para serviços de otimização de entrega. Necessário na ordem<br> para que o módulo funcione. |
| CUSTOMER_KEY                  | GUID da chave do cliente                     | Obrigatório          | Essa é a chave do cliente, que fornece segurança<br>autenticação do nó de cache para serviços de otimização de entrega.<br>Necessário para que o módulo funcione.|
| STORAGE_ *N* _SIZE_GB           | Em que N é o número de GB necessários   | Obrigatório          | Especifique até nove unidades para armazenar o conteúdo em cache e especifique<br>o espaço máximo em gigabytes a ser alocado para o conteúdo em cada unidade de cache. Exemplos:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>O número da unidade deve corresponder aos valores de associação da unidade de cache especificados<br>na opção criar contêiner, MicrosoftConnectedCache *N* valor|
| UPSTREAM_HOST                 | FQDN/IP                                | Opcional          | Esse valor pode especificar um upstream conectado à Microsoft<br>Nó de cache que atua como um proxy se o nó de cache conectado<br> está desconectado da Internet. Essa configuração é usada para dar suporte a<br> o cenário de IoT aninhado.<br>**Observação:** O cache conectado da Microsoft escuta na porta padrão http 80.|
| UPSTREAM_PROXY                | FQDN/IP: PORTA                           | Opcional          | O proxy da Internet de saída.<br>Isso também pode ser o proxy DMZ de OT se uma rede ISA 95. |
| CACHEABLE_CUSTOM_ *N* _HOST     | HOST/IP<br>FQDN                        | Opcional          | Necessário para dar suporte a repositórios de pacotes personalizados.<br>Os repositórios podem ser hospedados localmente ou na Internet.<br>Não há nenhum limite para o número de hosts personalizados que podem ser configurados.<br><br>Exemplos:<br>Nome = CACHEABLE_CUSTOM_1_HOST valor = packages.foo.com<br> Nome = CACHEABLE_CUSTOM_2_HOST valor = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | Opcional          | Necessário para dar suporte a repositórios de pacotes personalizados.<br>Esse valor pode ser usado como um alias e será usado pelo servidor de cache para fazer referência a<br>nomes DNS diferentes. Por exemplo, o nome do host de conteúdo do repositório pode ser packages.foo.com,<br>Mas, para regiões diferentes, pode haver um prefixo adicional que é adicionado ao nome do host<br>como westuscdn.packages.foo.com e eastuscdn.packages.foo.com.<br>Ao definir o alias canônico, você garante que o conteúdo não seja duplicado<br>para conteúdo proveniente do mesmo host, mas diferentes fontes CDN.<br>O formato do valor canônico não é importante, mas deve ser exclusivo para o host.<br>Pode ser mais fácil definir o valor para corresponder ao valor do host.<br><br>Exemplos baseados em exemplos de hosts personalizados acima:<br>Nome = CACHEABLE_CUSTOM_1_CANONICAL valor = foopackages<br> Nome = CACHEABLE_CUSTOM_2_CANONICAL valor = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | Verdadeiro ou Falso                          | Opcional          | Habilita a exibição do relatório de resumo na rede local ou na Internet.<br>O uso de uma chave de API (discutida posteriormente) é necessário para exibir o relatório de resumo se definido como true. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| Verdadeiro ou Falso                          | Opcional          | Habilita a exibição do relatório de resumo na rede local ou na Internet sem<br>uso da chave de API de qualquer dispositivo na rede. Use se você não quiser bloquear o acesso<br>para exibir dados de resumo do servidor de cache por meio do navegador. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Opções de criação de contêiner do módulo Azure IoT Edge de cache conectado da Microsoft

As opções de criação de contêiner para implantação de módulo MCC fornecem controle das configurações relacionadas ao armazenamento e às portas usadas pelo módulo MCC. Esta é a lista de variáveis criadas pelo contêiner necessárias usadas para implantar o MCC.

### <a name="container-to-host-os-drive-mappings"></a>Contêiner para hospedar mapeamentos de unidade do sistema operacional

Necessário para mapear o local de armazenamento do contêiner para o local de armazenamento no disco. < até nove locais podem ser especificados.

>[!Note]
>O número da unidade deve corresponder aos valores de associação da unidade de cache especificados na variável de ambiente STORAGE_ *N* _SIZE_GB valor, ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>Contêiner para hospedar mapeamentos de porta TCP

Esta opção especifica a porta http do computador externo que o MCC escuta para solicitações de conteúdo. O HostPort padrão é a porta 80 e outras portas não têm suporte no momento, pois o cliente ADU faz solicitações na porta 80 hoje. A porta TCP 8081 é a porta de contêiner interna na qual o MCC escuta e não pode ser alterada.

```markdown
8081/tcp": [
   {
       "HostPort": "80"
   }
]
```

### <a name="container-service-tcp-port-mappings"></a>Mapeamentos de porta TCP do serviço de contêiner

O módulo de cache conectado da Microsoft tem um serviço .NET Core, que é usado pelo mecanismo de cache para várias funções.

>[!Note]
>Para dar suporte à borda aninhada do Azure IoT, o HostPort não deve ser definido como 5000 porque o módulo de proxy do registro já está escutando na porta do host 5000.

```markdown
5000/tcp": [
   {
       "HostPort": "5001"
   }
]
```

## <a name="microsoft-connected-cache-summary-report"></a>Relatório de resumo do cache conectado da Microsoft

No momento, o relatório de resumo é a única maneira de um cliente exibir dados de cache para as instâncias de cache conectadas da Microsoft implantadas em gateways de Azure IoT Edge. O relatório é gerado em intervalos de 15 segundos e inclui estatísticas médias para o período, bem como estatísticas agregadas para o tempo de vida do módulo. As principais estatísticas nas quais os clientes se interessarão são:

* **hitBytes** -esta é a soma de bytes entregues diretamente do cache.
* **dissbytes** -esta é a soma de bytes entregues que o cache conectado da Microsoft teve de baixar da CDN para ver o cache.
* **eggressBytes** -esta é a soma de hitBytes e de suplantações incompletas e é o total de bytes entregues aos clientes.
* **hitRatioBytes** -esta é a taxa de HitBytes para egressBytes.  Se 100% de eggressBytes entregues em um período forem iguais a hitBytes, isso seria 1 por exemplo.

O relatório de resumo está disponível em `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` (consulte os detalhes da variável de ambiente abaixo para obter informações sobre a visibilidade desse relatório).
