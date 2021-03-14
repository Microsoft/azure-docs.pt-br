---
title: 'Erro de solução de problemas: Azure Functions Runtime está inacessível'
description: Saiba como solucionar o problema de uma conta de armazenamento inválida.
ms.topic: article
ms.date: 09/05/2018
ms.openlocfilehash: a62001cedd695badc72eb76c93ea9c3cb4507403
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499621"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Erro de solução de problemas: "Azure Functions Runtime está inacessível"

Este artigo ajuda você a solucionar problemas da seguinte cadeia de caracteres de erro que aparece no portal do Azure:

> "Erro: Azure Functions Runtime está inacessível. Clique aqui para obter detalhes sobre a configuração de armazenamento. "

Esse problema ocorre quando o tempo de execução do Functions não pode iniciar. O motivo mais comum para isso é que o aplicativo de funções perdeu o acesso à sua conta de armazenamento. Para obter mais informações, consulte [requisitos da conta de armazenamento](storage-considerations.md#storage-account-requirements).

O restante deste artigo ajuda você a solucionar problemas específicos desse erro, incluindo como identificar e resolver cada caso.

## <a name="storage-account-was-deleted"></a>A conta de armazenamento foi excluída

Cada aplicativo de funções exige uma conta de armazenamento para ser operado. Se essa conta for excluída, suas funções não funcionarão.

Comece pesquisando o nome da conta de armazenamento nas configurações do aplicativo. `AzureWebJobsStorage`Or ou `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` contém o nome da sua conta de armazenamento como parte de uma cadeia de conexão. Para obter mais informações, consulte [referência de configurações de aplicativo para Azure Functions](./functions-app-settings.md#azurewebjobsstorage).

Pesquise sua conta de armazenamento no portal do Azure para ver se ela ainda existe. Se ele tiver sido excluído, recrie a conta de armazenamento e substitua as cadeias de conexão de armazenamento. O código de função é perdido e você precisa reimplantá-lo.

## <a name="storage-account-application-settings-were-deleted"></a>As configurações do aplicativo da conta de armazenamento foram excluídas

Na etapa anterior, se você não encontrar uma cadeia de conexão da conta de armazenamento, ela provavelmente foi excluída ou substituída. A exclusão de configurações do aplicativo geralmente ocorre quando você está usando slots de implantação ou Azure Resource Manager scripts para definir as configurações do aplicativo.

### <a name="required-application-settings"></a>Configurações de aplicativo obrigatórias

* Obrigatório:
    * [`AzureWebJobsStorage`](./functions-app-settings.md#azurewebjobsstorage)
* Necessário para funções de plano Premium:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](./functions-app-settings.md)
    * [`WEBSITE_CONTENTSHARE`](./functions-app-settings.md)

Para obter mais informações, consulte [referência de configurações de aplicativo para Azure Functions](./functions-app-settings.md).

### <a name="guidance"></a>Diretrizes

* Não Verifique a **configuração de slot** para nenhuma dessas configurações. Se você trocar os slots de implantação, o aplicativo de funções será interrompido.
* Não modifique essas configurações como parte das implantações automatizadas.
* Essas configurações precisam ser fornecidas e estarem válidas no momento da criação. Uma implantação automatizada que não contém essas configurações resulta em um aplicativo de funções que não será executado, mesmo que as configurações sejam adicionadas posteriormente.

## <a name="storage-account-credentials-are-invalid"></a>As credenciais da conta de armazenamento são inválidas

As cadeias de conexão da conta de armazenamento abordadas anteriormente devem ser atualizadas se você regenerar as chaves de armazenamento. Para obter mais informações sobre o gerenciamento de chaves de armazenamento, consulte [criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md).

## <a name="storage-account-is-inaccessible"></a>A conta de armazenamento está inacessível

Seu aplicativo de funções deve ser capaz de acessar a conta de armazenamento. Os problemas comuns que bloqueiam o acesso de um aplicativo de funções a uma conta de armazenamento são:

* O aplicativo de funções é implantado em seu Ambiente do Serviço de Aplicativo (ASE) sem as regras de rede corretas para permitir o tráfego de e para a conta de armazenamento.

* O firewall da conta de armazenamento está habilitado e não está configurado para permitir o tráfego de e para funções. Para saber mais, consulte [Configurar Redes Virtuais e Firewalls de Armazenamento do Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="daily-execution-quota-is-full"></a>A cota de execução diária está cheia

Se você tiver uma cota de execução diária configurada, seu aplicativo de funções estará temporariamente desabilitado, o que faz com que muitos dos controles do portal fiquem indisponíveis. 

Para verificar a cota no [portal do Azure](https://portal.azure.com), selecione **recursos de plataforma**  >  **aplicativo de funções configurações** em seu aplicativo de funções. Se você estiver acima da **cota de uso diário** que você definiu, a seguinte mensagem será exibida:

  > "O Aplicativo de funções atingiu a cota de uso diário e foi interrompido até o próximo período de 24 horas."

Para resolver esse problema, remova ou aumente a cota diária e reinicie o aplicativo. Caso contrário, a execução do seu aplicativo será bloqueada até o dia seguinte.

## <a name="app-is-behind-a-firewall"></a>O aplicativo está protegido por um firewall

Seu aplicativo de funções pode estar inacessível por qualquer um dos seguintes motivos:

* Seu aplicativo de funções está hospedado em um [ambiente do serviço de aplicativo com balanceamento de carga internamente](../app-service/environment/create-ilb-ase.md) e está configurado para bloquear o tráfego de Internet de entrada.

* Seu aplicativo de funções tem [restrições de IP de entrada](functions-networking-options.md#inbound-access-restrictions) que são configuradas para bloquear o acesso à Internet. 

O portal do Azure faz chamadas diretamente para o aplicativo em execução para buscar a lista de funções e faz chamadas HTTP para o ponto de extremidade kudu. As configurações de nível de plataforma na guia **recursos da plataforma** ainda estão disponíveis.

Para verificar sua configuração do ASE:
1. Vá para o grupo de segurança de rede (NSG) da sub-rede onde reside o ASE.
1. Valide as regras de entrada para permitir o tráfego proveniente do IP público do computador em que você está acessando o aplicativo. 
   
Você também pode usar o portal de um computador que está conectado à rede virtual que está executando seu aplicativo ou a uma máquina virtual que está sendo executada em sua rede virtual. 

Para obter mais informações sobre a configuração de regra de entrada, consulte a seção "grupos de segurança de rede" de [considerações de rede para um ambiente do serviço de aplicativo](../app-service/environment/network-info.md#network-security-groups).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como monitorar seus aplicativos de funções:

> [!div class="nextstepaction"]
> [Monitorar Azure Functions](functions-monitoring.md)
