---
title: O que há de novo no Azure defender para IoT
description: Este artigo permite que você saiba o que há de novo na versão mais recente do defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2021
ms.author: shhazam
ms.openlocfilehash: f15c4ed6bb7da5cd33eca96c171bfada68092b35
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820663"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>O que há de novo no Azure defender para IoT?

Este artigo lista os novos recursos e aprimoramentos de recurso do defender para IoT.

Os recursos indicados estão em versão prévia. Os [termos suplementares de versão prévia do Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.
## <a name="february-2021"></a>Fevereiro de 2021

### <a name="sensor---enhanced-custom-alert-rules"></a>Sensor-regras de alerta personalizadas aprimoradas

Agora você pode criar regras de alerta personalizadas com base no dia, grupo de dias e a atividade de rede de período de tempo foi detectada.  Trabalhar com condições de regra de dia e hora é útil, por exemplo, nos casos em que a severidade do alerta é derivada pela hora em que o evento de alerta ocorre. Por exemplo, crie uma regra personalizada que dispara um alerta de severidade alta quando a atividade de rede é detectada em um fim de semana ou à noite.

Esse recurso está disponível no sensor com o lançamento da versão 10,1.

### <a name="on-premises-management-console---export-alerts"></a>Console de gerenciamento local – alertas de exportação

As informações de alerta agora podem ser exportadas para um arquivo. csv do console de gerenciamento local. Você pode exportar informações de todos os alertas detectados ou exportar informações com base na exibição filtrada.

Esse recurso está disponível no console de gerenciamento local com o lançamento da versão 10,1.
### <a name="device-builder---new-micro-agent-public-preview"></a>Device Builder-novo micro Agent (visualização pública)

Um novo módulo do Device Builder está disponível. O módulo, chamado de micro agente, permite:

- **Integração com o Hub IOT do Azure e Azure defender para IOT** -crie segurança de ponto de extremidade mais forte diretamente em seus dispositivos IOT integrando-o com a opção de monitoramento fornecida pelo Hub IOT do Azure e Azure defender para IOT.
- **Opções de implantação flexíveis com suporte para sistemas operacionais de IOT padrão** – podem ser implantadas como um pacote binário ou como código-fonte modificável, com suporte para sistemas operacionais de IOT padrão como Linux e RTOs do Azure.
- **Requisitos mínimos de recursos sem dependências de kernel do sistema operacional** -espaço pequeno, baixo consumo de CPU e nenhuma dependência de kernel do sistema operacional.
- **Gerenciamento de postura de segurança** – monitore proativamente a postura de segurança de seus dispositivos IOT.
- **Detecção de ameaças de IOT/OT contínuas e em tempo real** – Detecte ameaças como botnets, tentativas de força bruta, criptografia mineradores e atividade de rede suspeita

A documentação do módulo de segurança preterida será movida para a *solução baseada em agente para criadores de dispositivos>pasta clássica* .

Esse conjunto de recursos está disponível com a versão de nuvem de visualização pública atual.

## <a name="january-2021"></a>Janeiro de 2021

- [Segurança](#security)
- [Integração](#onboarding)
- [Usabilidade](#usability)
- [Outras atualizações](#other-updates)
### <a name="security"></a>Segurança

Foram feitos aprimoramentos de recuperação de senha e certificado para esta versão.

#### <a name="certificates"></a>Certificados
  
Esta versão permite que você:

- Carregue certificados SSL diretamente para os sensores e consoles de gerenciamento locais.
- Execute a validação entre o console de gerenciamento local e os sensores conectados e entre um console de gerenciamento e um console de gerenciamento de alta disponibilidade. A validação é baseada em datas de expiração, autenticidade da CA raiz e listas de certificados revogados.  Se a validação falhar, a sessão não continuará.

Para atualizações:

- Não há nenhuma alteração no certificado SSL ou na funcionalidade de validação durante a atualização.
- Após a atualização, os usuários administrativos do console de gerenciamento local e do sensor podem substituir os certificados SSL ou ativar a validação do certificado SSL na janela Configurações do sistema, certificado SSL.  

Para instalações novas:

- Durante o primeiro logon, os usuários precisam usar um certificado SSL (recomendado) ou um certificado autoassinado gerado localmente (não recomendado)
- A validação de certificado é ativada por padrão para instalações novas.

#### <a name="password-recovery"></a>Recuperação de senha
  
Os usuários administrativos do sensor e do console de gerenciamento local agora podem recuperar senhas do Azure defender para o portal de IoT. Antes, a recuperação de senha exigia a intervenção da equipe de suporte.

### <a name="onboarding"></a>Integração

#### <a name="on-premises-management-console---committed-devices"></a>Console de gerenciamento local-dispositivos confirmados

Seguindo a entrada inicial do console de gerenciamento local, agora os usuários são solicitados a carregar um arquivo de ativação. O arquivo contém o número agregado de dispositivos a serem monitorados na rede organizacional. Esse número é referido como o número de dispositivos confirmados.
Os dispositivos confirmados são definidos durante o processo de integração no Azure defender para o portal de IoT, no qual o arquivo de ativação é gerado.
Os usuários de primeira vez e os usuários que estão atualizando são necessários para carregar o arquivo de ativação.
Após a ativação inicial, o número de dispositivos detectados na rede pode exceder o número de dispositivos confirmados. Esse evento pode acontecer, por exemplo, se você conectar mais sensores ao console de gerenciamento. Se houver uma discrepância entre o número de dispositivos detectados e o número de dispositivos confirmados, um aviso aparecerá no console de gerenciamento. Se esse evento ocorrer, você deverá carregar um novo arquivo de ativação.

#### <a name="pricing-page-options"></a>Opções de página de preços

A página de preços permite que você integre novas assinaturas ao Azure defender para IoT e defina os dispositivos confirmados em sua rede.  
Além disso, a página de preços agora permite que você gerencie as assinaturas existentes associadas a um sensor e atualize o compromisso do dispositivo.

#### <a name="view-and-manage-onboarded-sensors"></a>Exibir e gerenciar sensores integrados

Uma nova página do portal de sensores e sites permite que você:

- Adicione informações descritivas sobre o sensor. Por exemplo, uma zona associada ao sensor ou marcas de texto livre.
- Exiba e filtre informações do sensor. Por exemplo, exiba detalhes sobre os sensores que estão conectados à nuvem ou localmente gerenciados ou exibam informações sobre sensores em uma zona específica.  

### <a name="usability"></a>Usabilidade

#### <a name="azure-sentinel-new-connector-page"></a>Página novo conector do Azure Sentinel

A página do conector de dados do Azure defender para IoT no Azure Sentinel foi reformulada. O conector de dados agora é baseado em assinaturas em vez de hubs IoT; permitir que os clientes gerenciem melhor sua conexão de configuração para o Azure Sentinel.

#### <a name="azure-portal-permission-updates"></a>portal do Azure atualizações de permissão  

O leitor de segurança e o suporte ao administrador de segurança foram adicionados.

### <a name="other-updates"></a>Outras atualizações

#### <a name="access-group---zone-permissions"></a>Permissões de zona de grupo de acesso
  
As regras de grupo de acesso do console de gerenciamento local não incluirão a opção de conceder acesso a uma zona específica. Não há nenhuma alteração na definição de regras que usam sites, regiões e unidades de negócios.   Após a atualização, os grupos de acesso que continham regras que permitem o acesso a zonas específicas serão modificados para permitir o acesso ao site pai, incluindo todas as suas zonas.

#### <a name="terminology-changes"></a>Alterações de terminologia

O termo ativo foi renomeado como dispositivo no sensor e no console de gerenciamento local, em relatórios e em outras interfaces de solução.
Nos alertas do console de gerenciamento local e do sensor, o termo gerenciar esse evento tem sido chamado de etapas de correção.

## <a name="next-steps"></a>Próximas etapas

[Introdução ao defender para IoT](getting-started.md)
