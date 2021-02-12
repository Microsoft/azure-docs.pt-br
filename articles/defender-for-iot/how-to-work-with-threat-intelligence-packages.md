---
title: Atualizar dados de inteligência contra ameaças
description: O pacote de dados de inteligência contra ameaças é fornecido com cada nova versão do defender para IoT ou, se necessário, entre as versões.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: ddf2eb2fab6281e76bac0a958024b639d18a7ccc
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838024"
---
# <a name="threat-intelligence-research-and-packages"></a>Pesquisa e pacotes de inteligência contra ameaças

As equipes de segurança da Microsoft executam a inteligência de ameaças e a pesquisa de vulnerabilidades proprietárias do ICS. Essas equipes incluem o MSTIC (Microsoft Threat Intelligence Center), o DART (equipe de detecção e resposta da Microsoft), o DCU (unidade de crimes digitais) e a seção 52 (especialistas em domínio IoT/OT/ICS que acompanham os dias úteis específicos do ICS, malwares de engenharia reversa, campanhas e adversários).

As equipes fornecem detecção de segurança, análise e resposta para a Microsoft:

- Infraestrutura e serviços de nuvem.
- Produtos e dispositivos tradicionais.
- Recursos corporativos internos.

As equipes de segurança têm a vantagem de:

- Proteção contra ameaças conhecidas e relevantes.
- Informações que ajudam você a fazer a triagem e a priorizar.
- Uma compreensão do contexto completo das ameaças antes que elas sejam afetadas.
- Dados mais relevantes, precisos e acionáveis.

Essa inteligência adiciona informações contextuais para enriquecer a análise de plataforma da Microsoft e dá suporte aos serviços gerenciados da empresa para a investigação de violação e resposta a incidentes. Os pacotes de inteligência contra ameaças contêm assinaturas (incluindo assinaturas de malware), CVEs e outros conteúdos de segurança.

Você pode baixar pacotes da página **atualizações** no portal do Azure defender para IOT.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Baixe atualizações por meio do portal do Azure defender para IoT.":::

## <a name="update-threat-intelligence-data"></a>Atualizar dados de inteligência contra ameaças

Os pacotes de inteligência contra ameaças são fornecidos com cada nova atualização de versão do defender for IoT ou, se necessário, entre as versões.

Os pacotes que você baixa do portal do defender para IoT podem ser carregados manualmente para sensores individuais. Se o console de gerenciamento local gerencia seus sensores, você pode baixar pacotes de inteligência contra ameaças para o console de gerenciamento e enviá-los por push para vários sensores simultaneamente.

Para atualizar um pacote em um único sensor:

1. Vá para a página de **atualizações** do Azure defender para IOT.

2. Baixe e salve o pacote de **inteligência contra ameaças** .

3. Entre no console do sensor.

4. No menu lateral, selecione **configurações do sistema**.

5. Selecione **dados de inteligência contra ameaças** e, em seguida, selecione **Atualizar**.

6. Carregue o novo pacote.

Para atualizar um pacote em vários sensores simultaneamente:

1. Vá para a página de **atualizações** do Azure defender para IOT.

2. Baixe e salve o pacote de **inteligência contra ameaças** .

3. Entre no console de gerenciamento do.

4. No menu lateral, selecione **configurações do sistema**.

5. Na seção **configuração do mecanismo do sensor** , selecione os sensores que devem receber os pacotes atualizados.  

6. Na seção **selecionar dados de inteligência contra ameaças** , selecione o sinal de adição ( **+** ).

7. Carregue o pacote.

## <a name="see-also"></a>Consulte também

[Versões de atualização](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
