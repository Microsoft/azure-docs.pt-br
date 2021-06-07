---
title: Novidades do Azure Defender para IoT
description: Este artigo traz as novidades da última versão do Defender para IoT.
ms.topic: overview
ms.date: 03/14/2021
ms.openlocfilehash: 7d8c4ebfc55c80878b780fe9f663aaec62a75f9d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382905"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>Quais são as novidades do Azure Defender para IoT?

Este artigo lista os novos recursos e os aprimoramentos de recursos do Defender para IoT.

Os recursos indicados estão em VERSÃO PRÉVIA. Os [termos suplementares de versão prévia do Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.
## <a name="march-2021"></a>Março de 2021

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>Sensor: aprimoramento das regras de alerta personalizadas (versão prévia pública)

Agora você pode criar regras de alerta personalizadas com base no dia, no grupo de dias e no período em que a atividade de rede foi detectada.  Trabalhar com condições de regra de dia e hora é útil, por exemplo, nos casos em que a severidade do alerta é derivada da hora em que o evento de alerta ocorre. Por exemplo, crie uma regra personalizada que dispara um alerta de severidade alta quando a atividade de rede é detectada em um fim de semana ou à noite.

Esse recurso está disponível no sensor com o lançamento da versão 10.2.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>Console de gerenciamento local: alertas de exportação (versão prévia pública)

As informações de alerta já podem ser exportadas para um arquivo .csv no console de gerenciamento local. Você pode exportar as informações de todos os alertas detectados ou exportar as informações com base na exibição filtrada.

Esse recurso está disponível no console de gerenciamento local com o lançamento da versão 10.2.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Adição de um segundo adaptador de rede ao console de gerenciamento local (versão prévia pública)

Agora você pode aprimorar a segurança da sua implantação adicionando um segundo adaptador de rede ao console de gerenciamento local. Esse recurso permite que o gerenciamento local tenha os sensores conectados em uma rede segura, permitindo que os usuários acessem o console de gerenciamento local por meio de um segundo adaptador de rede separado.

Esse recurso está disponível no console de gerenciamento local com o lançamento da versão 10.2.
### <a name="device-builder---new-micro-agent-public-preview"></a>Construtor de dispositivos: novo microagente (versão prévia pública)

Um novo módulo do construtor de dispositivos está disponível. O módulo, chamado de microagente, permite:

- **Integração ao Hub IoT do Azure e ao Azure Defender para IoT**: incorpore uma segurança de ponto de extremidade mais forte diretamente nos seus dispositivos IoT integrando-a com a opção de monitoramento fornecida pelo Hub IoT do Azure e pelo Azure Defender para IoT.
- **Opções de implantação flexíveis com suporte para sistemas operacionais de IoT padrão**: podem ser implantadas como um pacote binário ou como um código-fonte modificável, com suporte para sistemas operacionais de IoT padrão como o Linux e o Azure RTOS.
- **Requisitos mínimos de recursos sem dependências de kernel do sistema operacional**: volume pequeno, baixo consumo de CPU e nenhuma dependência de kernel do sistema operacional.
- **Gerenciamento da postura de segurança**: monitore de maneira proativa a postura de segurança dos seus dispositivos IoT.
- **Detecção contínua e em tempo real de ameaças de IoT/OT**: detecte ameaças como botnets, tentativas de ataque de força bruta, criptomineradores e atividades de rede suspeita

A documentação preterida do microagente do Defender para IoT será movida para a pasta *Solução baseada em agente para criadores de dispositivos > Clássica*.

Esse conjunto de recursos está disponível com a versão de nuvem da versão prévia pública atual.

## <a name="january-2021"></a>Janeiro de 2021

- [Segurança](#security)
- [Integração](#onboarding)
- [Usabilidade](#usability)
- [Outras atualizações](#other-updates)
### <a name="security"></a>Segurança

Foram feitos aprimoramentos de recuperação de senha e certificado nesta versão.

#### <a name="certificates"></a>Certificados
  
Esta versão permite:

- Carregar certificados SSL diretamente nos sensores e nos consoles de gerenciamento locais.
- Executar a validação entre o console de gerenciamento local e os sensores conectados e entre um console de gerenciamento e um console de gerenciamento de alta disponibilidade. A validação é baseada em datas de validade, autenticidade da AC raiz e listas de certificados revogados.  Se a validação falhar, a sessão não continuará.

Para atualizações:

- Não há nenhuma alteração no certificado SSL ou na funcionalidade de validação durante a atualização.
- Após a atualização, os usuários administrativos do console de gerenciamento local e do sensor podem substituir os certificados SSL ou ativar a validação do certificado SSL na janela Configurações do Sistema, Certificado SSL.  

Para instalações novas:

- Durante o primeiro logon, os usuários precisam usar um certificado SSL (recomendado) ou um certificado autoassinado gerado localmente (não recomendado)
- A validação de certificado é ativada por padrão para instalações novas.

#### <a name="password-recovery"></a>Recuperação de senha
  
Os usuários administrativos do sensor e do console de gerenciamento local já podem recuperar senhas no portal do Azure Defender para IoT. Anteriormente, a recuperação de senha exigia a intervenção da equipe de suporte.

### <a name="onboarding"></a>Integração

#### <a name="on-premises-management-console---committed-devices"></a>Console de gerenciamento local: dispositivos confirmados

Após a entrada inicial no console de gerenciamento local, agora, os usuários precisam carregar um arquivo de ativação. O arquivo contém o número agregado de dispositivos a serem monitorados na rede organizacional. Esse número é conhecido como o número de dispositivos confirmados.
Os dispositivos confirmados são definidos durante o processo de integração no portal do Azure Defender para IoT, no qual o arquivo de ativação é gerado.
Os usuários novatos e os usuários que estão fazendo a atualização devem carregar o arquivo de ativação.
Após a ativação inicial, o número de dispositivos detectados na rede pode exceder o número de dispositivos confirmados. Esse evento poderá acontecer, por exemplo, se você conectar mais sensores ao console de gerenciamento. Se houver uma discrepância entre o número de dispositivos detectados e o número de dispositivos confirmados, um aviso será exibido no console de gerenciamento. Caso isso ocorra, carregue um novo arquivo de ativação.

#### <a name="pricing-page-options"></a>Opções da página Preços

A página Preços permite que você integre novas assinaturas ao Azure Defender para IoT e defina os dispositivos confirmados na sua rede.  
Além disso, a página Preços agora permite que você gerencie as assinaturas existentes associadas a um sensor e atualize o compromisso do dispositivo.

#### <a name="view-and-manage-onboarded-sensors"></a>Exibir e gerenciar os sensores integrados

Uma nova página Site e Sensores do portal permite:

- Adicionar informações descritivas sobre o sensor. Por exemplo, uma zona associada ao sensor ou marcas de texto livre.
- Exibir e filtrar informações do sensor. Por exemplo, veja detalhes sobre os sensores que estão conectados à nuvem ou são gerenciados localmente ou veja informações sobre os sensores de uma zona específica.  

### <a name="usability"></a>Usabilidade

#### <a name="azure-sentinel-new-connector-page"></a>Nova página do conector do Azure Sentinel

A página do conector de dados do Azure Defender para IoT no Azure Sentinel foi reformulada. O conector de dados agora é baseado em assinaturas em vez de Hubs IoT, permitindo que os clientes gerenciem melhor a conexão de configuração com o Azure Sentinel.

#### <a name="azure-portal-permission-updates"></a>atualizações de permissão do portal do Azure  

O suporte ao Leitor de segurança e ao Administrador de segurança foi adicionado.

### <a name="other-updates"></a>Outras atualizações

#### <a name="access-group---zone-permissions"></a>Grupo de acesso: permissões de zona
  
As regras do grupo de acesso do console de gerenciamento local não incluirão a opção de permitir acesso a uma zona específica. Não há nenhuma alteração na definição de regras que usam sites, regiões e unidades de negócios.   Após a atualização, os grupos de acesso que continham regras que permitiam o acesso a zonas específicas serão modificados para permitir o acesso ao site pai, incluindo todas as zonas.

#### <a name="terminology-changes"></a>Alterações de terminologia

O termo "ativo" foi renomeado como "dispositivo" no sensor e no console de gerenciamento local, em relatórios e em outras interfaces da solução.
Nos alertas do console de gerenciamento local e do sensor, o termo "Gerenciar este Evento" foi renomeado como "Etapas de Correção".

## <a name="next-steps"></a>Próximas etapas

[Introdução ao Defender para IoT](getting-started.md)
