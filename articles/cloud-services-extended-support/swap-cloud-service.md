---
title: Trocar/alternar entre dois serviços de nuvem do Azure (suporte estendido)
description: Trocar/alternar entre dois serviços de nuvem do Azure (suporte estendido)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 6f96656af9afd9874cc6273a9cea9ed43e8c69cc
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294191"
---
# <a name="swapswitch-between-two-azure-cloud-services-extended-support"></a>Trocar/alternar entre dois serviços de nuvem do Azure (suporte estendido)
Com os serviços de nuvem (suporte estendido), você pode trocar entre duas implantações de serviço de nuvem independentes. Ao contrário dos serviços de nuvem (clássicos), o conceito de slots não existe com o modelo do Azure Resource Manager. Ao decidir implantar uma nova versão de um serviço de nuvem (suporte estendido), você pode torná-lo "intercambiável" com outro serviço de nuvem existente (suporte estendido), permitindo que você prepare e teste sua nova versão usando essa implantação. Um serviço de nuvem pode se tornar "intercambiável" com outro serviço de nuvem somente no momento da implantação do segundo serviço de nuvem (do par). Ao usar o método de implantação baseado em modelo do ARM, isso é feito definindo a propriedade SwappableCloudService dentro do Perfil de Rede do objeto de Serviço de Nuvem como a ID do serviço de nuvem emparelhado. 

```
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
```
> [!Note] 
> Não é possível trocar entre um serviço de nuvem (clássico) e um serviço de nuvem (suporte estendido)

Use **Trocar** para alternar as URLs pelas quais os dois serviços de nuvem são endereçados, na prática promovendo um novo serviço de nuvem (preparado) para o lançamento para produção.
É possível permutar implantações na página Serviços de Nuvem ou no painel.

1. No [portal do Azure](https://portal.azure.com), selecione o serviço de nuvem que você deseja atualizar. Esta etapa abrirá a folha da instância do serviço de nuvem.
2. Na folha, selecione **Trocar**
   :::image type="content" source="media/swap-cloud-service-1.png" alt-text="A imagem mostra a opção Trocar o serviço de nuvem":::
   
3. O seguinte prompt de confirmação é aberto
   
   :::image type="content" source="media/swap-cloud-service-2.png" alt-text="A imagem mostra a troca do serviço de nuvem":::
   
4. Após verificar as informações da implantação, selecione OK para trocar as implantações.
A troca acontece rapidamente, pois a única coisa alterada são os VIPs (endereços IP virtuais) para os dois serviços de nuvem.

Para economizar custos de computação, você pode excluir um dos serviços de nuvem (designado como um ambiente de preparo para a implantação do aplicativo) depois de verificar se o serviço de nuvem trocado está funcionando conforme o esperado.

A API REST para executar uma "troca" entre duas implantações de suporte estendido de serviços de nuvem está abaixo:
```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```
```
{
  "frontendIPConfigurations": [
    {
    "id": "#LBFE1#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP2#"
    }
      }
    },
   {
    "id": "#LBFE2#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP1#"
     }
       }
    }
  ]
 }
```
## <a name="common-questions-about-swapping-deployments"></a>Perguntas comuns sobre troca de implantações

### <a name="what-are-the-prerequisites-for-swapping-between-two-cloud-services"></a>Quais são os pré-requisitos para trocar entre dois serviços de nuvem?
Há dois pré-requisitos principais para uma troca bem-sucedida do serviço de nuvem (suporte estendido):
* Se você quiser usar um endereço IP estático/reservado para um dos serviços de nuvem trocáveis, o outro serviço de nuvem também deverá usar um IP reservado. Caso contrário, a troca falhará.
* Todas as instâncias de suas funções devem estar em execução para que você possa executar a troca. Você pode verificar o status de suas instâncias na folha Visão geral do Portal do Azure. Como alternativa, você pode usar o comando Get-AzRole no Windows PowerShell.

As atualizações do SO convidado e as operações de recuperação de serviço também podem fazer com que as trocas de implantação falhem. Para saber mais, confira Solucionar problemas de implantação do serviço de nuvem.

### <a name="can-i-perform-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Posso executar uma troca de VIP em paralelo com outra operação de mutação?
Não. A troca de VIP é uma alteração somente de rede que precisa ser concluída antes que qualquer outra operação de computação seja executada nos serviços de nuvem. Executar uma operação de atualização, exclusão ou dimensionamento automático nos serviços de nuvem enquanto uma troca de VIP está em andamento ou disparar uma troca de VIP enquanto outra operação de computação está em andamento pode deixar o serviço de nuvem em um estado indesejado do qual a recuperação pode não ser possível. 

### <a name="does-a-swap-incur-downtime-for-my-application-how-should-i-handle-it"></a>Uma troca incorre em tempo de inatividade para meu aplicativo? Como devo lidar com isso?
Conforme descrito na seção anterior, uma troca de serviço de nuvem normalmente é rápida, pois é apenas uma alteração de configuração no Azure Load Balancer. Em alguns casos, ela pode levar 10 segundos ou mais e resultar em falhas de conexão transitórias. Para limitar o impacto sobre os clientes, considere a implementação da lógica de repetição do cliente.

## <a name="next-steps"></a>Próximas etapas 
- Examine os [pré-requisitos de implantação](deploy-prerequisite.md) para os Serviços de Nuvem (suporte estendido).
- Examine as [perguntas frequentes](faq.md) sobre os Serviços de Nuvem (suporte estendido).
- Implante um Serviço de Nuvem (suporte estendido) usando o [portal do Azure](deploy-portal.md), o [PowerShell](deploy-powershell.md), o [Modelo](deploy-template.md) ou o [Visual Studio](deploy-visual-studio.md).
