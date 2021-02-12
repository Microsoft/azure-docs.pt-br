---
title: Perguntas frequentes sobre Gerenciamento de API do Azure | Microsoft Docs
description: Conheça as respostas a perguntas frequentes (FAQ), padrões e práticas recomendadas no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: eea3c8525d31a3ca551e9cbc7d21d7dde163b5cc
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697978"
---
# <a name="azure-api-management-faqs"></a>Perguntas frequentes sobre Gerenciamento de API do Azure
Obtenha as respostas a perguntas comuns, padrões e práticas recomendadas do Gerenciamento de API do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="frequently-asked-questions"></a>Perguntas frequentes
* [O que significa quando um recurso está em visualização?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Como proteger a conexão entre o gateway de Gerenciamento de API e meus serviços de back-end?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Como copiar minha instância do serviço de Gerenciamento de API para uma nova instância?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Posso gerenciar minha instância de Gerenciamento de API por meio de programação?](#can-i-manage-my-api-management-instance-programmatically)
* [Como adicionar um usuário ao grupo Administradores?](#how-do-i-add-a-user-to-the-administrators-group)
* [Por que a política que desejo adicionar não está disponível no editor de política?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Como configurar vários ambientes em uma única API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Pode usar SOAP com Gerenciamento de API?](#can-i-use-soap-with-api-management)
* [Posso configurar um servidor de autorização OAUth 2.0 com segurança ADFS?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Que método de roteamento o Gerenciamento de API usa quando implantado em vários locais geográficos?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Pode usar um modelo do Azure Resource Manager para criar uma instância do serviço de Gerenciamento de API?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Posso usar um certificado TLS/SSL autoassinado para um back-end?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Por que recebo uma falha de autenticação quando tento clonar um repositório GIT?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [O Gerenciamento de API funciona com o Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Por que exigimos uma sub-rede dedicada em Resource Manager tipo VNETs quando o Gerenciamento de API é implantado nelas?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Qual é o tamanho mínimo de sub-rede necessário ao implantar o Gerenciamento de API em uma VNET?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Posso mover um serviço de Gerenciamento de API de uma assinatura para outra?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Há restrições ou problemas conhecidos com a importação da minha API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>O que significa quando um recurso está em visualização?
Quando um recurso está em visualização, isso significa que estamos buscando ativamente comentários sobre como o recurso está funcionando para você. Um recurso no modo de visualização está funcionalmente completo, mas é possível que façamos uma alteração importante em resposta aos comentários dos clientes. É recomendável que você não dependa de um recurso que está na visualização em seu ambiente de produção.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Como proteger a conexão entre o gateway de Gerenciamento de API e meus serviços de back-end?
Você tem várias opções para proteger a conexão entre o gateway de Gerenciamento de API e os serviços de back-end. Você pode:

* Use a autenticação básica HTTP. Para obter mais informações, consulte [Importar e publicar sua primeira API](import-and-publish.md).
* Use a autenticação mútua TLS, conforme descrito em [como proteger serviços de back-end usando a autenticação de certificado de cliente no gerenciamento de API do Azure](api-management-howto-mutual-certificates.md).
* Use a filtragem de IP em seu serviço de back-end. Em todas as camadas do gerenciamento de API com exceção da camada de consumo, o endereço IP do gateway permanece constante, com algumas limitações descritas no [artigo de documentação de IP](api-management-howto-ip-addresses.md).
* Conecte sua instância de Gerenciamento de API a uma Rede Virtual do Azure.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Como copiar minha instância do serviço de Gerenciamento de API para uma nova instância?
Você tem várias opções para copiar uma instância de Gerenciamento de API para uma nova instância. Você pode:

* Use a função de backup e restauração no Gerenciamento de API. Para saber mais, confira [Como implementar a recuperação de desastre usando restauração e backup de serviço no Gerenciamento de API no Azure](api-management-howto-disaster-recovery-backup-restore.md).
* Criar seu próprio recurso de backup e restauração usando a [API REST de Gerenciamento de API](/rest/api/apimanagement/). Use a API REST para salvar e restaurar as entidades da instância do serviço que você deseja.
* Baixe a configuração de serviço usando o Git e carregue-a em uma nova instância. Para obter mais informações, confira [Saiba como salvar e definir a configuração do seu serviço de Gerenciamento de API usando o Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Posso gerenciar minha instância de Gerenciamento de API por meio de programação?
Sim, você pode gerenciar o Gerenciamento de API de forma programática, usando:

* A [API REST de Gerenciamento de API](/rest/api/apimanagement/)
* O [SDK da Biblioteca de Gerenciamento do Serviço Microsoft Azure ApiManagement](https://aka.ms/apimsdk).
* Os cmdlets do PowerShell [Implantação de serviço](/powershell/module/wds) e [Gerenciamento de serviço](/powershell/azure/servicemanagement/overview).

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Como adicionar um usuário ao grupo Administradores?
Os grupos de administradores são um grupo de sistemas imutáveis. Os administradores de assinatura do Azure são membros deste grupo. Você não pode adicionar um usuário a este grupo. Consulte [como criar e usar grupos para gerenciar contas de desenvolvedor no gerenciamento de API do Azure](./api-management-howto-create-groups.md) para obter mais informações.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Por que a política que desejo adicionar não está disponível no editor de política?
Se a política que você deseja adicionar aparecer esmaecida ou sombreada no editor de política, verifique se você está no escopo correto para a política. Cada declaração de política foi projetada para uso nas seções de política e escopos específicas. Para examinar as seções de política e escopos de uma política, confira a seção de uso da política em [Políticas de Gerenciamento de API](./api-management-policies.md).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Como configurar vários ambientes em uma única API?
Para configurar vários ambientes, por exemplo, um ambiente de teste e um ambiente de produção, em uma única API, você tem duas opções. Você pode:

* Hospede APIs diferentes no mesmo locatário.
* Hospede as mesmas APIs em locatários diferentes.

### <a name="can-i-use-soap-with-api-management"></a>Pode usar SOAP com Gerenciamento de API?
O suporte a [Passagem SOAP](https://azure.microsoft.com/blog/soap-pass-through/) agora está disponível. Os administradores podem importar o WSDL do serviço SOAP e o Gerenciamento de API do Azure criará um front-end SOAP. Documentação do portal de desenvolvedor, console de teste, políticas e análise estão disponíveis para serviços SOAP.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Posso configurar um servidor de autorização OAUth 2.0 com segurança ADFS?
Para saber como configurar um servidor de autorização OAuth 2.0 com a segurança do AD FS (Serviços de Federação do Active Directory), confira [Usar ADFS no Gerenciamento de API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Que método de roteamento o Gerenciamento de API usa quando implantado em vários locais geográficos?
O Gerenciamento de API usa o [método de roteamento de tráfego de desempenho](../traffic-manager/traffic-manager-routing-methods.md#performance) em implantações em vários locais geográficos. O tráfego é roteado para o gateway de API mais próximo. Se uma região ficar offline, o tráfego de entrada será automaticamente roteado para o gateway mais próximo. Saiba mais sobre os métodos de roteamentos em [Métodos de roteamento do Gerenciador de Tráfego](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Pode usar um modelo do Azure Resource Manager para criar uma instância do serviço de Gerenciamento de API?
Sim. Consulte os modelos de início rápido do [serviço de gerenciamento de API do Azure](https://aka.ms/apimtemplate) .

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Posso usar um certificado TLS/SSL autoassinado para um back-end?
Sim. Isso pode ser feito usando o PowerShell ou enviando diretamente à API. Isso desabilitará a validação da cadeia de certificados e permitirá que você use certificados autoassinados ou assinados de forma privada ao se comunicar do Gerenciamento de API com os serviços de back-end.

#### <a name="powershell-method"></a>Método com o PowerShell ####
Use os [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) cmdlets do PowerShell (para novo back-end) ou [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (para back-end existente) e defina o `-SkipCertificateChainValidation` parâmetro como `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Método de atualização direta da API ####
1. Criar uma entidade [back-end](/rest/api/apimanagement/) usando o Gerenciamento de API.     
2. Defina a propriedade **skipCertificateChainValidation** como **true**.     
3. Se você não quiser mais permitir certificados autoassinados, exclua a entidade de back-end ou defina a propriedade **skipCertificateChainValidation** como **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Por que obtenho uma falha de autenticação ao tentar clonar um repositório Git?
Se usar o Gerenciador de Credenciais do Git ou se estiver tentando clonar um repositório Git usando o Visual Studio, você poderá encontrar um problema conhecido com a caixa de diálogo de credenciais do Windows. A caixa de diálogo limita o comprimento da senha a 127 caracteres e trunca a senha gerada pelo Microsoft. Estamos trabalhando para encurtar a senha. Por enquanto, use Git Bash para clonar o repositório Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>O Gerenciamento de API funciona com o Azure ExpressRoute?
Sim. O Gerenciamento de API funciona com o Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Por que exigimos uma sub-rede dedicada em Resource Manager tipo VNETs quando o Gerenciamento de API é implantado nelas?
O requisito de sub-rede dedicado para o Gerenciamento de API vem do fato de ser desenvolvido no modelo de implantação Clássico (camada PAAS V1). Embora possamos implantar em uma VNET do Resource Manager (camada V2), isso tem consequências. O modelo de implantação Clássico no Azure não é rigorosamente acoplado ao modelo do Resource Manager e, portanto, se você criar um recurso na camada V2, a camada V1 não o reconhecerá e isso acarretará problemas, como o Gerenciamento de API tentar usar um IP que já está alocado a uma NIC (desenvolvido em V2).
Para saber mais sobre a diferença dos modelos do Resource Manager e Clássico no Azure, consulte [diferença nos modelos de implantação](../azure-resource-manager/management/deployment-models.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Qual é o tamanho mínimo de sub-rede necessário ao implantar o Gerenciamento de API em uma VNET?
O tamanho mínimo de sub-rede necessário para implantar o Gerenciamento de API é [/29](../virtual-network/virtual-networks-faq.md#configuration), que é o tamanho mínimo de sub-rede que o Azure oferece suporte.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Posso mover um serviço de Gerenciamento de API de uma assinatura para outra?
Sim. Para saber como, confira [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Há restrições ou problemas conhecidos com a importação da minha API?
[Restrições e problemas conhecidos](api-management-api-import-restrictions.md) para formatos Open API (Swagger), WSDL e WADL.
