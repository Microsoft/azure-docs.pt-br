---
title: Azure Defender para Armazenamento – Benefícios e recursos
description: Saiba mais sobre os benefícios e os recursos do Azure Defender para Armazenamento.
author: memildin
ms.author: memildin
ms.date: 02/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: eb1635cec2b0bcf7f2c13101b2aeab25a869dc66
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558544"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Introdução ao Azure Defender para Armazenamento

O **Azure Defender para Armazenamento** é uma camada nativa do Azure de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar suas contas de armazenamento. Ele utiliza funcionalidades avançadas da IA de segurança e [Inteligência contra Ameaças da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684) para fornecer recomendações e alertas de segurança contextuais.

Os alertas de segurança são acionados quando ocorrem anomalias na atividade. Esses alertas de segurança são integrados à Central de Segurança do Azure e são enviados por email para administradores de assinatura, com detalhes da atividade suspeita e recomendações sobre como investigar e corrigir as ameaças.

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|O **Azure Defender para Armazenamento** é cobrado conforme mostrado na [página de preços](security-center-pricing.md)|
|Tipos de armazenamento protegidos:|[Armazenamento de Blobs](https://azure.microsoft.com/services/storage/blobs/)<br>[Arquivos do Azure](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Gov dos EUA<br>![Não](./media/icons/no-icon.png) Governo da China e outros governos|
|||


## <a name="what-are-the-benefits-of-azure-defender-for-storage"></a>Quais são os benefícios do Azure Defender para Armazenamento?

O Azure Defender para Armazenamento oferece:

- **Segurança nativa do Azure** – com a habilitação de um clique, o Defender para Armazenamento protege os dados armazenados no blob do Azure, nos Arquivos do Azure e nos Data Lakes. Como um serviço nativo do Azure, o Defender para Armazenamento proporciona segurança centralizada em todos os ativos de dados gerenciados pelo Azure e é integrado a outros serviços de segurança do Azure, como o Azure Sentinel.
- **Pacote de detecção avançada** – Da plataforma de inteligência contra ameaças da Microsoft, as detecções no Defender para Armazenamento abrangem as principais ameaças de armazenamento, como acesso anônimo, credenciais comprometidas, engenharia social, abuso de privilégio e conteúdo mal-intencionado.
- **Resposta em escala** – As ferramentas de automação da Central de Segurança facilitam a prevenção e a resposta a ameaças identificadas. Saiba mais em [Automatizar respostas para os gatilhos da Central de Segurança](workflow-automation.md).

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="Visão geral de alto nível dos recursos do Azure Defender para Armazenamento":::


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Quais tipos de alertas são fornecidos pelo Azure Defender para Armazenamento?

Os alertas de segurança são disparados quando há:

- **Padrões de acesso suspeitos** – como acesso bem-sucedido de um nó de saída do Tor ou de um IP considerado suspeito pela Inteligência contra Ameaças da Microsoft
- **Atividades suspeitas** – como extração de dados anormais ou alteração incomum de permissões de acesso
- **Upload de conteúdo mal-intencionado** – como arquivos de malware em potencial (com base na análise de reputação de hash) ou hospedagem de conteúdo de phishing

Um alerta inclui detalhes sobre o incidente que o disparou, bem como recomendações sobre como investigar e corrigir as ameaças. Os alertas podem ser exportados para o Azure Sentinel ou qualquer outro SIEM de terceiros ou qualquer outra ferramenta externa.

> [!TIP]
> É uma melhor prática [configurar o Azure Defender para Armazenamento](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center) no nível da assinatura, mas você também pode [configurá-lo em contas de armazenamento individuais](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>O que é a análise de reputação de hash para malware?

Para determinar se um arquivo carregado é suspeito, o Azure defender para Armazenamento usa a análise de reputação de hash compatível com a [Inteligência contra Ameaças da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). As ferramentas de proteção contra ameaças não verificam os arquivos carregados. Em vez disso, examinam os logs de armazenamento e comparam os hashes dos arquivos carregados recentemente com aqueles de vírus conhecidos, cavalos de Tróia, spywares e ransomwares. 

Quando há a suspeita de que um arquivo contém malware, a Central de Segurança exibe um alerta e pode, opcionalmente, enviar um email ao proprietário do armazenamento solicitando a aprovação para excluir o arquivo suspeito. Para configurar essa remoção automática de arquivos indicados pela análise de reputação de hash como possíveis portadores de malware, implante uma [automação de fluxo de trabalho para disparar com alertas que contenham "Possível upload de malware em uma conta de armazenamento"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Para habilitar as funcionalidades de proteção contra ameaças da Central de Segurança, você precisará habilitar o Azure Defender na assinatura que contém as cargas de trabalho aplicáveis.
>
> Você pode habilitar o **Azure Defender para Armazenamento** no nível de assinatura ou no nível de recurso.

## <a name="trigger-a-test-alert-for-azure-defender-for-storage"></a>Disparar um alerta de teste para o Azure Defender para Armazenamento

Para testar os alertas de segurança do Azure Defender para Armazenamento em seu ambiente, gere o alerta "Acessar de um nó de saída do Tor para uma conta de armazenamento" com as seguintes etapas:

1. Abra uma conta de armazenamento com o Azure Defender para Armazenamento habilitado.
1. Na barra lateral, selecione "Contêineres" e abra um contêiner existente ou crie um.

    :::image type="content" source="media/defender-for-storage-introduction/opening-storage-container.png" alt-text="Abrindo um contêiner de blob de uma conta de Armazenamento do Azure" lightbox="media/defender-for-storage-introduction/opening-storage-container.png":::

1. Carregue um arquivo nesse contêiner.

    > [!CAUTION]
    > Não carregue um arquivo que contém dados confidenciais.

1. Use o menu de contexto no arquivo carregado para selecionar "Gerar SAS".

    :::image type="content" source="media/defender-for-storage-introduction/generate-sas.png" alt-text="A opção de gerar SAS para um arquivo em um contêiner de blob":::

1. Deixe as opções padrão e selecione **Gerar token SAS e URL**.

1. Copie a URL SAS gerada.

1. No computador local, abra o navegador Tor.

    > [!TIP]
    > Você pode baixar o Tor no site do Projeto Tor [https://www.torproject.org/download/](https://www.torproject.org/download/).

1. No navegador Tor, navegue até a URL SAS.

1. Baixe o arquivo carregado na etapa 3.

    Dentro de duas horas, você receberá o seguinte alerta de segurança da Central de Segurança:

    :::image type="content" source="media/defender-for-storage-introduction/tor-access-alert-storage.png" alt-text="Alerta de segurança sobre o acesso de um nó de saída do Tor":::

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure Defender para Armazenamento.

Para obter material relacionado, consulte os seguintes artigos: 

- Seja um alerta for gerado pela Central de Segurança ou recebido pela Central de Segurança de um produto de segurança diferente, você poderá exportá-lo. Para exportar seus alertas para o Azure Sentinel, qualquer SIEM de terceiros ou qualquer outra ferramenta externa, siga as instruções em [Exportar alertas para um SIEM](continuous-export.md).
- [Como habilitar o Defender Avançado para Armazenamento](../storage/common/azure-defender-storage-configure.md)
- [A lista de alertas do Azure Defender para Armazenamento](alerts-reference.md#alerts-azurestorage)
- [Funcionalidades da Inteligência contra Ameaças da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)