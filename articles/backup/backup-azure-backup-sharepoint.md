---
title: Fazer backup de um farm do SharePoint no Azure com o DPM
description: Este artigo fornece uma visão geral da proteção do servidor do DPM/Backup do Azure de um farm do SharePoint no Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 7661d64e487c8b8badca240852d17bcf736ba8cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91254424"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Fazer backup de um farm do SharePoint no Azure com o DPM

Faça backup de um farm do SharePoint para o Microsoft Azure usando o DPM (System Center Data Protection Manager) da mesma maneira que o backup de outras fontes de dados. O Backup do Azure fornece flexibilidade no agendamento de backup para criar pontos de backup diariamente, semanalmente, mensalmente ou anualmente e fornece opções de política de retenção para diversos pontos de backup. O DPM fornece a capacidade de armazenar cópias de disco locais para obter RTOs (Objetivos de Tempo de Recuperação) rápidos e armazenar cópias no Azure para uma retenção econômica e de longo prazo.

Fazer backup do SharePoint no Azure com o DPM é um processo muito semelhante para fazer backup do SharePoint no DPM localmente. Neste artigo, serão observadas considerações específicas para o Azure.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Versões do SharePoint com suporte e cenários de proteção relacionados

Para obter uma lista das versões do SharePoint com suporte e das versões do DPM necessárias para fazer backup, confira [Em que o DPM pode fazer backup?](/system-center/dpm/dpm-protection-matrix#applications-backup)

## <a name="before-you-start"></a>Antes de começar

Há alguns elementos que você precisa confirmar antes de fazer o backup de um farm do SharePoint para o Azure.

### <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, verifique se você atende a todos os [pré-requisitos para usar o Backup do Microsoft Azure](backup-azure-dpm-introduction.md#prerequisites-and-limitations) para proteger as cargas de trabalho. Algumas tarefas para os pré-requisitos incluem: criar um cofre de backup, baixar as credenciais do cofre, instalar o Agente de Backup do Azure e registrar o DPM/Servidor de Backup do Azure com o cofre.

Os pré-requisitos e as limitações adicionais podem ser encontrados no artigo [fazer backup do SharePoint com o DPM](/system-center/dpm/back-up-sharepoint#prerequisites-and-limitations) .

## <a name="configure-backup"></a>Configurar backup

Para fazer backup do farm do SharePoint, configure a proteção do SharePoint usando o ConfigureSharePoint.exe e, então, crie um grupo de proteção no DPM. Para obter instruções, consulte [Configurar backup](/system-center/dpm/back-up-sharepoint#configure-backup) na documentação do DPM.

## <a name="monitoring"></a>Monitoramento

Para monitorar o trabalho de backup, siga as instruções em [monitoramento backup do DPM](/system-center/dpm/back-up-sharepoint#monitoring)

## <a name="restore-sharepoint-data"></a>Restaurar dados do SharePoint

Para saber como restaurar um item do SharePoint de um disco com o DPM, consulte [restaurar dados do SharePoint](/system-center/dpm/back-up-sharepoint#restore-sharepoint-data).

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Restaurar um banco de dados do SharePoint do Azure usando o DPM

1. Para recuperar um banco de dados de conteúdo do SharePoint, navegue pelos vários pontos de recuperação (conforme mostrado acima) e selecione o ponto de recuperação que você deseja recuperar.

    ![Proteção do SharePoint do DPM8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Clique duas vezes no ponto de recuperação do SharePoint para mostrar as informações de catálogo do SharePoint disponíveis.

   > [!NOTE]
   > Como o farm do SharePoint está protegido para retenção de longo prazo no Azure, não há nenhuma informação de catálogo (metadados) disponível no servidor DPM. Desta forma, sempre que um banco de dados de conteúdo do SharePoint pontual precisar ser recuperado, será necessário recatalogar o farm do SharePoint.
   >
   >
3. Selecione **recatalogar**.

    ![Proteção do SharePoint do DPM10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    A janela de status **Recatalogação de Nuvem** é exibida.

    ![Proteção do SharePoint do DPM11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Após a catalogação ser concluída, o status é alterado para *Êxito*. Selecione **Fechar**.

    ![Proteção do SharePoint do DPM12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Selecione o objeto do SharePoint mostrado na guia **recuperação** do DPM para obter a estrutura do banco de dados de conteúdo. Clique com o botão direito do mouse no item e selecione **recuperar**.

    ![Proteção do SharePoint do DPM13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Nesse ponto, siga as etapas de recuperação descritas anteriormente neste artigo para recuperar um banco de dados de conteúdo do SharePoint do disco.

## <a name="switching-the-front-end-web-server"></a>Alternando o servidor Web front-end

Se você tiver mais de um servidor Web front-end e quiser alternar o servidor que o DPM usa para proteger o farm, siga as instruções em [alternando o servidor web Front-End](/system-center/dpm/back-up-sharepoint#switching-the-front-end-web-server).

## <a name="next-steps"></a>Próximas etapas

* [Servidor de Backup do Azure e o DPM-perguntas frequentes](backup-azure-dpm-azure-server-faq.md)
* [Solucionar Problemas do System Center Data Protection Manager](backup-azure-scdpm-troubleshooting.md)
