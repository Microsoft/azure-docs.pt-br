---
title: Autogerenciamento do Azure para máquinas virtuais
description: Saiba mais sobre o Azure autogerenci para máquinas virtuais.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 7772d57937393da1c48fa2658818d8a1a2b28a1f
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550777"
---
# <a name="azure-automanage-for-virtual-machines"></a>Autogerenciamento do Azure para máquinas virtuais

Este artigo aborda informações sobre o autogerenciamento do Azure para máquinas virtuais, que têm os seguintes benefícios:

- Integre de forma inteligente máquinas virtuais para selecionar as práticas recomendadas serviços do Azure
- Configura automaticamente cada serviço por práticas recomendadas do Azure
- Monitora descompasso e corrige quando detectado
- Fornece uma experiência simples (apontar, clicar, definir, esquecer)


## <a name="overview"></a>Visão geral

O autogerenciamento do Azure para máquinas virtuais é um serviço que elimina a necessidade de descobrir, saber como integrar e como configurar determinados serviços no Azure que beneficiariam sua máquina virtual. Esses serviços ajudam a aprimorar a confiabilidade, a segurança e o gerenciamento de máquinas virtuais e são considerados serviços de práticas recomendadas do Azure, como o [azure gerenciamento de atualizações](../automation/update-management/overview.md) e o [backup do Azure](../backup/backup-overview.md) , apenas para citar alguns.

Depois de integrar suas máquinas virtuais ao gerenciamento automático do Azure, ele configura automaticamente cada serviço de prática recomendada para suas configurações recomendadas. As práticas recomendadas são diferentes para cada um dos serviços. Um exemplo pode ser o backup do Azure, em que a prática recomendada pode ser fazer backup da máquina virtual uma vez por dia e ter um período de retenção de seis meses.

O gerenciamento automático do Azure também monitora automaticamente a descompasso e corrige quando detectado. Isso significa que, se a sua máquina virtual estiver integrada ao autogerenciamento do Azure, não apenas a configuraremos por práticas recomendadas do Azure, mas Monitoraremos seu computador para garantir que ele continue em conformidade com essas práticas recomendadas em todo o seu ciclo de vida. Se sua máquina virtual for descompasso ou desviar dessas práticas, corrigiremos-a e puxaremos seu computador de volta para o estado desejado.

Por fim, a experiência é incrivelmente simples.


## <a name="prerequisites"></a>Pré-requisitos

Há vários pré-requisitos a serem considerados antes de tentar habilitar o autogerenciamento do Azure em suas máquinas virtuais.

- Somente VMs do Windows Server
- As VMs devem estar em uma região com suporte (consulte o parágrafo abaixo)
- O usuário deve ter as permissões corretas (consulte o parágrafo abaixo)
- O autogerenci não dá suporte a assinaturas de área restrita no momento

Também é importante observar que o autogerenci só dá suporte a VMs do Windows localizadas nas seguintes regiões: Europa Ocidental, leste dos EUA, oeste dos EUA 2, Canadá central, Oeste EUA Central, leste do Japão.

Você deve ter a função de **colaborador** no grupo de recursos que contém suas VMs para habilitar o autogerenciamento em VMs usando uma conta de autogerenciamento existente. Se você estiver habilitando o autogerenciamento com uma nova conta de autogerenciamento, precisará das seguintes permissões em sua assinatura: função de **proprietário** ou **colaborador** junto com as funções de **administrador de acesso do usuário** .

> [!NOTE]
> Se você quiser usar o autogerenci em uma VM que esteja conectada a um espaço de trabalho em uma assinatura diferente, deverá ter as permissões descritas acima em cada assinatura.

## <a name="participating-services"></a>Serviços participantes

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="Integração inteligente de serviços.":::

Consulte [gerenciamento automático do Azure para máquinas virtuais práticas recomendadas](virtual-machines-best-practices.md) para obter a lista completa de serviços do Azure participantes, bem como seus perfis de configuração com suporte.

 Nós iremos integrar automaticamente a esses serviços participantes. Eles são essenciais para nossas práticas recomendadas white paper, que você pode encontrar em nossa [estrutura de adoção de nuvem](/azure/cloud-adoption-framework/manage/azure-server-management).

Para todos esses serviços, faremos a integração automática, o autoconfigure, o monitor para descompasso e os mediais se o descompasso for detectado.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Habilitando o autogerenciamento para VMs no portal do Azure

No portal do Azure, você pode habilitar o autogerenci em uma máquina virtual existente ou ao criar uma nova máquina virtual. Para etapas concisas para esse processo, confira o guia de [início rápido para máquinas virtuais](quick-create-virtual-machines-portal.md).

Se for a primeira vez que você habilita o autogerenci para sua VM, você poderá pesquisar no portal do Azure para **autogerenciar – práticas recomendadas da máquina virtual do Azure**. Clique em **habilitar na VM existente**, selecione as VMs que você gostaria de carregar, clique em **selecionar**, em **habilitar** e pronto.

A única vez que você pode precisar interagir com essa VM para gerenciar esses serviços está no evento que tentamos corrigir sua VM, mas não conseguiu fazer isso. Se corrigirmos com êxito sua VM, vamos colocá-la de volta em conformidade sem até mesmo alertá-lo.


## <a name="configuration-profiles"></a>Perfis de configuração

Quando você está habilitando o autogerenciamento para sua máquina virtual, um perfil de configuração é necessário. Os perfis de configuração são a base desse serviço. Eles definem exatamente quais serviços nós integram seus computadores e até certo ponto que a configuração desses serviços seria.

### <a name="default-configuration-profiles"></a>Perfis de configuração padrão

Há dois perfis de configuração disponíveis no momento.

- **Práticas recomendadas da máquina virtual do Azure-** perfil de configuração de desenvolvimento/teste foi projetado para máquinas de desenvolvimento/teste.
- **Práticas recomendadas da máquina virtual do Azure-** o perfil de configuração de produção é para produção.

O motivo para esse diferenciador é porque certos serviços são recomendados com base na carga de trabalho em execução. Por exemplo, em uma máquina de produção, iremos integrar automaticamente você ao backup do Azure. No entanto, para uma máquina de desenvolvimento/teste, um serviço de backup seria um custo desnecessário, uma vez que os computadores de desenvolvimento/teste normalmente são menos um impacto nos negócios.

### <a name="customizing-a-configuration-profile-using-preferences"></a>Personalizando um perfil de configuração usando preferências

Além dos serviços padrão que integramos você a, permitimos que você configure um determinado subconjunto de preferências. Essas preferências são permitidas dentro de uma variedade de opções de configuração que não violam nossas práticas recomendadas. Por exemplo, no caso do backup do Azure, permitiremos que você defina a frequência do backup e o dia da semana em que ele ocorre. No entanto, *não* vamos permitir que você desative completamente o backup do Azure.

> [!NOTE]
> No perfil de configuração de desenvolvimento/teste, não faremos backup da VM.

Você pode ajustar as configurações de um perfil de configuração padrão por meio de preferências. Saiba como criar uma preferência [aqui](virtual-machines-custom-preferences.md).

> [!NOTE]
> Você não pode alterar o perfil de configuração em sua VM enquanto o autogerenci está habilitado. Será necessário desabilitar o autogerenci para essa VM e reabilitar o autogerenci com o perfil de configuração desejado e as preferências.


## <a name="automanage-account"></a>Autogerenciar conta

A conta de autogerenciamento é o contexto de segurança ou a identidade sob a qual ocorrem as operações automatizadas. Normalmente, a opção autogerenciar conta é desnecessária para você selecionar, mas se houvesse um cenário de delegação em que você quisesse dividir o gerenciamento automatizado de seus recursos (talvez entre dois administradores do sistema), essa opção permite que você defina uma identidade do Azure para cada um desses administradores.

Na experiência de portal do Azure, quando você está habilitando o autogerenciamento em suas VMs, há uma lista suspensa avançada na folha **habilitar a prática recomendada de VM do Azure** que permite atribuir ou criar manualmente a conta de autogerenciamento.

A conta de autogerenciamento receberá as funções de **colaborador de política de recurso** e **colaborador** para as assinaturas que contêm o (s) computador (es) que você carregará para autogerenciar. Você pode usar a mesma conta de autogerenciar em máquinas em várias assinaturas, o que concederá a você permissões de colaborador de conta de recurso e **colaborador** de **política de recursos** em todas as assinaturas.

Se sua VM estiver conectada a um espaço de trabalho Log Analytics em outra assinatura, a conta de autogerenciamento receberá o **colaborador de política de recurso** e **colaborador** nessa outra assinatura também.

Se você estiver habilitando o autogerenciamento com uma nova conta de autogerenciamento, precisará das seguintes permissões em sua assinatura: função de **proprietário** ou **colaborador** junto com as funções de **administrador de acesso do usuário** .

Se você estiver habilitando o autogerenciamento com uma conta de autogerenciamento existente, precisará ter a função de **colaborador** no grupo de recursos que contém suas VMs.

> [!NOTE]
> Quando você desabilita as práticas recomendadas de gerenciamento autogerencio, as permissões da conta de autogerenciar em todas as assinaturas associadas permanecerão. Remova manualmente as permissões acessando a página IAM da assinatura ou exclua a conta de autogerenciamento. A conta de autogerenciamento não poderá ser excluída se ainda estiver gerenciando qualquer computador.


## <a name="status-of-vms"></a>Status das VMs

Na portal do Azure, vá para a página **autogerenciar – práticas recomendadas da máquina virtual do Azure** que lista todas as VMs gerenciadas automaticamente. Aqui, você verá o status geral de cada máquina virtual.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Lista de máquinas virtuais configuradas.":::

Para cada VM listada, os seguintes detalhes são exibidos: nome, perfil de configuração, preferência de configuração, status, conta, assinatura e grupo de recursos.

A coluna **status** pode exibir os seguintes Estados:
- *Em andamento* -a VM acabou de ser habilitada e está sendo configurada
- *Configurado* -a VM está configurada e nenhum descompasso foi detectado
- *Falha* -a VM foi descartada e não foi possível corrigi-la
- *Pendente* -a VM não está em execução no momento e o autogerenci tentará carregar ou corrigir a VM quando ela estiver em execução

Se você vir o **status** como *com falha*, poderá solucionar problemas de implantação por meio do grupo de recursos em que sua VM está localizada. Vá para **grupos de recursos**, selecione o grupo de recursos, clique em **implantações** e veja o status com *falha* , junto com os detalhes do erro.


## <a name="disabling-automanage-for-vms"></a>Desabilitando o autogerenci para VMs

Você pode decidir um dia para desabilitar o autogerenciamento em determinadas VMs. Por exemplo, seu computador está executando alguma carga de trabalho segura muito sensível e você precisa bloqueá-lo ainda mais além do que o Azure teria feito naturalmente, portanto, você precisa configurar o computador fora das práticas recomendadas do Azure.

Para fazer isso na portal do Azure, vá para a página **autogerenciar – práticas recomendadas da máquina virtual do Azure** que lista todas as suas VMs gerenciadas automaticamente. Marque a caixa de seleção ao lado da máquina virtual que você deseja desabilitar em autogerenciar e clique no botão **desabilitar autogerenciamento** .

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Desabilitar o Gerenciamento Automático em uma máquina virtual.":::

Leia atentamente as mensagens no pop-up resultante antes de concordar em **Desabilitar**.

> [!NOTE]
> A desabilitação do autogerenciamento em uma VM resulta no seguinte comportamento:
>
> - A configuração da VM e os serviços que ela está integrado não são alterados.
> - Quaisquer encargos incorridos por esses serviços permanecem faturáveis e continuam a ser incorridos.
> - Qualquer comportamento de autogerenciamento é interrompido imediatamente.


Primeiro, nós não desativamos a máquina virtual de nenhum dos serviços que a integramos e configuramos. Portanto, qualquer cobrança incorrida por esses serviços continuará a permanecer faturável. Se necessário, você precisará fazer a integração. Qualquer comportamento de autogerenciamento será interrompido imediatamente. Por exemplo, não Monitoraremos mais a VM para descompasso.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu que o autogerenci para máquinas virtuais fornece um meio para o qual você pode eliminar a necessidade de conhecer, integrar e configurar os serviços do Azure de práticas recomendadas. Além disso, se um computador que você tiver integrado para autogerenciar para máquinas virtuais se desconectar dos perfis de configuração configurados, nós o colocaremos automaticamente em conformidade.

Tente habilitar o autogerenci para máquinas virtuais no portal do Azure.

> [!div class="nextstepaction"]
> [Habilitar o autogerenci para máquinas virtuais no portal do Azure](quick-create-virtual-machines-portal.md)