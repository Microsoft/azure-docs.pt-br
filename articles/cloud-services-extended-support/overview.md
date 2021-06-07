---
title: Sobre os serviços de nuvem do Azure (suporte estendido)
description: Saiba mais sobre os elementos filho do elemento de configuração de rede do arquivo de configuração de serviço, que especifica os valores de rede virtual e DNS.
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: ecf115b7ce902fcd8b50f0eca32ffda6ef47e068
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618467"
---
# <a name="about-azure-cloud-services-extended-support"></a>Sobre os serviços de nuvem do Azure (suporte estendido)

> [!IMPORTANT]
> No momento, os Serviços de Nuvem (suporte estendido) estão em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Serviços de nuvem (suporte estendido) é um novo modelo de implantação baseado em [Azure Resource Manager](../azure-resource-manager/management/overview.md) para o produto de [serviços de nuvem do Azure](https://azure.microsoft.com/services/cloud-services/) e está atualmente em visualização pública. Os serviços de nuvem (suporte estendido) têm o principal benefício de fornecer resiliência regional juntamente com a paridade de recursos com os serviços de nuvem do Azure implantados usando o Azure Service Manager. Ele também oferece alguns recursos de ARM, como RBAC (acesso baseado em função e controle), marcas, política e suporte a modelos de implantação.  

Com essa alteração, o modelo de implantação baseado no Azure Service Manager para serviços de nuvem será renomeado como [serviços de nuvem (clássico)](../cloud-services/cloud-services-choose-me.md). Você manterá a capacidade de criar e implantar rapidamente seus aplicativos e serviços na nuvem e na Web. Você poderá dimensionar sua infraestrutura de serviços de nuvem com base na demanda atual e garantir que o desempenho de seus aplicativos possa acompanhar enquanto reduz os custos simultaneamente.  

## <a name="what-does-not-change"></a>O que não é alterado 
- Você cria o código, define as configurações e implanta-o no Azure. O Azure configura o ambiente de computação, executa o código e, em seguida, monitora e mantém isso para você.
- Os serviços de nuvem (suporte estendido) também dão suporte a dois tipos de funções, [Web e trabalho](../cloud-services/cloud-services-choose-me.md). Não há nenhuma alteração no design, na arquitetura ou nos componentes das funções Web e de trabalho. 
- Os três componentes de um serviço de nuvem, a definição de serviço (. csdef), a configuração do serviço (. cscfg) e o pacote de serviço (. cspkg) são postergados e não há nenhuma alteração nos seus [formatos](cloud-services-model-and-package.md). 
- Nenhuma alteração é necessária para o código de tempo de execução, pois o plano de dados é o mesmo e o plano de controle só é alterado. 
- As versões do Azure GuestOS e as atualizações associadas estão alinhadas com os serviços de nuvem (clássicos)
- Processo de atualização subjacente com relação aos domínios de atualização, como a atualização prossegue, a reversão e as alterações de serviço permitidas durante uma atualização não são alteradas

## <a name="changes-in-deployment-model"></a>Alterações no modelo de implantação

Alterações mínimas são necessárias para os arquivos de configuração de serviço (. cscfg) e de definição de serviço (. csdef) para implantar serviços de nuvem (suporte estendido). Nenhuma alteração é necessária para o código de tempo de execução. No entanto, os scripts de implantação precisarão ser atualizados para chamar as novas APIs baseadas em Azure Resource Manager. 

:::image type="content" source="media/overview-image-1.png" alt-text="Imagem mostra a configuração do serviço de nuvem clássico com adição da seção de modelo. ":::

As principais diferenças entre os serviços de nuvem (clássicos) e os serviços de nuvem (suporte estendido) em relação à implantação são: 

- Azure Resource Manager implantações usam [modelos ARM](../azure-resource-manager/templates/overview.md) , que é um arquivo JavaScript Object Notation (JSON) que define a infraestrutura e a configuração do seu projeto. O modelo usa a sintaxe declarativa, que permite declarar o que você pretende implantar sem precisar gravar a sequência de comandos de programação para criá-lo. A configuração de serviço e o arquivo de definição de serviço precisam ser consistentes com o [modelo ARM](../azure-resource-manager/templates/overview.md) durante a implantação de serviços de nuvem (suporte estendido). Isso pode ser feito [criando manualmente o modelo ARM](deploy-template.md) ou usando o [PowerShell](deploy-powershell.md), o [portal](deploy-portal.md) e o [Visual Studio](deploy-visual-studio.md).  

- Os clientes devem usar [Azure Key Vault](../key-vault/general/overview.md) para [gerenciar certificados em serviços de nuvem (suporte estendido)](certificates-and-key-vault.md). Azure Key Vault permite armazenar e gerenciar com segurança as credenciais do aplicativo, como segredos, chaves e certificados em um repositório de nuvem central e seguro. Seus aplicativos podem se autenticar em Key Vault em tempo de execução para recuperar credenciais. 

- Todos os recursos implantados por meio do [Azure Resource Manager](../azure-resource-manager/templates/overview.md) devem estar dentro de uma rede virtual. As redes virtuais e sub-redes são criadas em Azure Resource Manager usando as APIs de Azure Resource Manager existentes e precisarão ser referenciadas na seção NetworkConfiguration do. cscfg ao implantar serviços de nuvem (suporte estendido).   

- Cada serviço de nuvem (suporte estendido) é uma implantação independente única. Os serviços de nuvem (suporte estendido) não oferecem suporte a vários slots em um único serviço de nuvem.  
    - O <sup>*</sup> recurso de permuta VIP pode ser usado para alternar entre dois serviços de nuvem (suporte estendido). Para testar e preparar uma nova versão de um serviço de nuvem, implante um serviço de nuvem (suporte estendido) e marque-o como permutable VIP com outro serviço de nuvem (suporte estendido)  

- O rótulo DNS (serviço de nomes de domínio) é opcional para um serviço de nuvem (suporte estendido). No Azure Resource Manager, o rótulo DNS é uma propriedade do recurso de IP público associado ao serviço de nuvem. 


<sup>*</sup> A permuta de VIP para serviços de nuvem (suporte estendido) não está disponível durante a visualização pública.  

## <a name="migration-to-azure-resource-manager"></a>Migração para Azure Resource Manager

Serviços de nuvem (suporte estendido) fornece dois caminhos para migrar do [Azure Service Manager](/powershell/azure/servicemanagement/overview) para [Azure Resource Manager](../azure-resource-manager/management/overview.md). 
1) Os clientes implantam serviços de nuvem diretamente no Azure Resource Manager e, em seguida, excluem o serviço de nuvem antigo no Service Manager do Azure. 
2) A migração in-loco oferece suporte à capacidade de migrar serviços de nuvem (clássicos) com mínimo de sem tempo de inatividade para serviços de nuvem (suporte estendido). 

### <a name="additional-migration-options"></a>Opções de migração adicionais

Ao avaliar os planos de migração dos serviços de nuvem (clássicos) para os serviços de nuvem (suporte estendido), talvez você queira investigar os serviços adicionais do Azure, como: [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/overview.md), [serviço de aplicativo](../app-service/overview.md), [serviço kubernetes do Azure](../aks/intro-kubernetes.md)e [Service Fabric do Azure](../service-fabric/service-fabric-overview.md). Esses serviços continuarão a recursos adicionais, enquanto os serviços de nuvem (suporte estendido) manterão principalmente a paridade de recursos com os serviços de nuvem (clássicos). 

Dependendo do aplicativo, os serviços de nuvem (suporte estendido) podem exigir substancialmente menos esforço para se mover para Azure Resource Manager em comparação com outras opções. Se seu aplicativo não estiver em evolução, os serviços de nuvem (suporte estendido) são uma opção viável a considerar, pois ele fornece um caminho de migração rápida. Por outro lado, se seu aplicativo estiver evoluindo continuamente e precisar de um conjunto de recursos mais moderno, explore outros serviços do Azure para atender melhor aos seus requisitos atuais e futuros. 

## <a name="next-steps"></a>Próximas etapas
- Examine os [pré-requisitos de implantação](deploy-prerequisite.md) para serviços de nuvem (suporte estendido).
- Implante um Serviço de Nuvem (suporte estendido) usando o [portal do Azure](deploy-portal.md), o [PowerShell](deploy-powershell.md), o [Modelo](deploy-template.md) ou o [Visual Studio](deploy-visual-studio.md).
- Examine as [perguntas frequentes](faq.md) sobre os Serviços de Nuvem (suporte estendido).