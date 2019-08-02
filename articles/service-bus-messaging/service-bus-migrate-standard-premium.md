---
title: Migrar namespaces padrão de barramento de serviço do Azure existentes para a camada premium | Microsoft Docs
description: Guia para permitir a migração de namespaces standard do barramento de serviço do Azure existentes para premium
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: darosa
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 57ab281e8d07537c22bd3cf60306dfb1c7e81541
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566071"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrar namespaces padrão de barramento de serviço do Azure existentes para a camada premium
Anteriormente, o barramento de serviço do Azure oferecidos namespaces apenas na camada standard. Namespaces são instalações de multilocatário que são otimizadas para ambientes de desenvolvedor e baixa taxa de transferência. A camada premium oferece recursos dedicados por namespace para latência previsível e maior taxa de transferência a um preço fixo. A camada premium é otimizada para ambientes de produção que exigem recursos empresarias adicionais e alta taxa de transferência.

Este artigo descreve como migrar namespaces existentes da camada standard para a camada premium.  

>[!WARNING]
> Migração destina-se de namespaces de barramento de serviço padrão para serem atualizados para a camada premium. A ferramenta de migração não oferece suporte a downgrade.

Alguns dos pontos a serem observados: 
- Essa migração deve acontecer em vigor, o que significa que os aplicativos de remetente e receptor **não requer alterações no código ou configuração**. A cadeia de caracteres de conexão existente vai apontar automaticamente para o novo namespace premium.
- O **premium** namespace deve ter **nenhuma entidade** nele para a migração seja bem-sucedida. 
- Todos os **entidades** no namespace padrão são **copiado** ao namespace premium durante o processo de migração. 
- Suporta migração **1.000 entidades por unidade de mensagens** na camada premium. Para identificar quantas unidades de sistema de mensagens, você precisa, comece com o número de entidades que você tem em seu namespace padrão atual. 
- Você não pode migrar diretamente de **camada básica** à **camada premier**, mas você pode fazer isso indiretamente através da migração do básico ao padrão primeiro e, em seguida, o Standard para premium na próxima etapa.

## <a name="migration-steps"></a>Etapas da migração
Algumas condições são associadas com o processo de migração. Familiarize-se com as seguintes etapas para reduzir a possibilidade de erros. Estas etapas descrevem o processo de migração e os detalhes passo a passo estão listados nas seções a seguir.

1. Crie um novo namespace premium.
1. Emparelhe os namespaces standard e premium uns aos outros.
1. Entidades de sincronização (com cópia) do padrão para o namespace premium.
1. Confirme a migração.
1. Drenar entidades no namespace padrão usando o nome após a migração do namespace.
1. Exclua o namespace padrão.

>[!IMPORTANT]
> Após a migração foi confirmada, acessar o namespace padrão antigo e drenar as filas e assinaturas. Depois que as mensagens tem sido esvaziadas, pode ser enviadas para o novo namespace premium a serem processadas pelos aplicativos do receptor. Depois de tem sido esvaziadas a filas e assinaturas, é recomendável que você exclua o antigo namespace padrão.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrar usando a CLI do Azure ou o PowerShell

Para migrar seu namespace de barramento de serviço standard para premium usando a CLI do Azure ou a ferramenta do PowerShell, siga estas etapas.

1. Crie um novo namespace premium do barramento de serviço. Você pode fazer referência a [Azure Resource Manager templates](service-bus-resource-manager-namespace.md) ou [usar o portal do Azure](service-bus-create-namespace-portal.md). Verifique se você selecionou **premium** para o **serviceBusSku** parâmetro.

1. Defina as seguintes variáveis de ambiente para simplificar os comandos de migração.
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Após a migração alias/nome (post_migration_dns_name) será ser usado para acessar a namespace padrão após a migração antigo. Use isto para drenar as filas e as assinaturas e, em seguida, exclua o namespace.

1. Emparelhe os namespaces standard e premium e inicie a sincronização usando o comando a seguir:

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. Verifique o status da migração, usando o seguinte comando:
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    A migração é considerada concluída quando você vê os seguintes valores:
    * MigrationState = "Ativo"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Êxito"

    Esse comando também exibe a configuração de migração. Verifique se que os valores estão definidos corretamente. Verifique também o namespace premium no portal para garantir que todas as filas e tópicos foram criados e se eles correspondem à que existia no namespace padrão.

1. Confirme a migração, executando o seguinte comando completo:
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrar usando o portal do Azure

Migração usando o portal do Azure tem o mesmo fluxo lógico de migração usando os comandos. Siga estas etapas para migrar usando o portal do Azure.

1. Sobre o **navegação** menu no painel esquerdo, selecione **migrar para o premium**. Clique o **começar** botão para continuar para a próxima página.
    ![Página de aterrissagem de migração][]

1. Concluída **instalação**.
   ![Namespace de instalação][]
   1. Crie e atribua o namespace para migrar o namespace padrão existente para premium.
        ![Configurar o namespace – criar um namespace premium][]
   1. Escolha uma **nome após a migração**. Você usará esse nome para acessar o namespace padrão após a migração for concluída.
        ![Configurar o namespace – escolher o nome da migração de postagem][]
   1. Selecione **'Próximo'** para continuar.
1. Entidades de sincronização entre os namespaces standard e premium.
    ![Configurar o namespace - entidades de sincronização - iniciar][]

   1. Selecione **Iniciar sincronização** para iniciar a sincronização de entidades.
   1. Selecione **Sim** na caixa de diálogo para confirmar e iniciar a sincronização.
   1. Aguarde até que a sincronização seja concluída. O status está disponível na barra de status.
        ![Namespace - entidades de sincronização - progresso da instalação][]
        >[!IMPORTANT]
        > Se você precisar anular a migração por qualquer motivo, examine o fluxo de anular a seção de perguntas Frequentes deste documento.
   1. Depois que a sincronização seja concluída, selecione **próxima** na parte inferior da página.

1. Examine as alterações na página de resumo. Selecione **concluir a migração** para alternar os namespaces e concluir a migração.
    ![Alternar o namespace - opção de menu][] a página de confirmação é exibida quando a migração for concluída.
    ![Namespace de switch - êxito][]

## <a name="caveats"></a>Limitações

Alguns dos recursos fornecidos pela camada Standard do barramento de serviço do Azure não são suportados pela camada Premium do barramento de serviço do Azure. Esses são por design, pois a camada premium oferece recursos dedicados a taxa de transferência previsível e a latência.

Aqui está uma lista de recursos sem suporte Premium e seu tamanho máximo- 

### <a name="express-entities"></a>Entidades expressas

   Não há suporte para as entidades expressas não confirmadas quaisquer dados de mensagem para o armazenamento no Premium. Recursos dedicados fornecido melhoria da taxa de transferência significativo, garantindo que dados sejam persistidos, como é esperado a partir de qualquer sistema corporativo de mensagens.
   
   Durante a migração, qualquer uma das suas entidades expressas em seu namespace padrão será criado no namespace Premium como uma entidade não express.
   
   Se você utilizar os modelos do Azure Resource Manager (ARM), verifique se que você remova o sinalizador 'enableExpress' da configuração de implantação para que os fluxos de trabalho automatizados executados sem erros.

### <a name="partitioned-entities"></a>Entidades particionadas

   Entidades particionadas tinham suporte na camada Standard para fornecer melhor disponibilidade em uma configuração de vários locatário. Com o provisionamento de recursos dedicados disponíveis por namespace na camada Premium, isso não é mais necessário.
   
   Durante a migração, qualquer entidade particionada no namespace padrão é criada no namespace Premium como uma entidade não particionada.
   
   Se seu modelo ARM define enablePartitioning como 'true' para uma fila ou um tópico específico, em seguida, ele será ignorado pelo agente.

## <a name="faqs"></a>Perguntas frequentes

### <a name="what-happens-when-the-migration-is-committed"></a>O que acontece quando a migração é confirmada?

Depois que a migração for confirmada, a cadeia de caracteres de conexão apontada para o namespace padrão apontará para o namespace premium.

Os aplicativos de remetente e destinatário serão desconectar o Namespace padrão e reconectar-se automaticamente ao namespace premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>O que fazer devo fazer após a conclusão do padrão para a migração de premium?

O padrão para a migração premium garante que os metadados de entidade como tópicos, assinaturas e filtros são copiados do namespace padrão para o namespace premium. Os dados da mensagem que foram confirmados para o namespace padrão não sejam copiados do namespace padrão para o namespace premium.

O namespace padrão pode ter algumas mensagens que foram enviadas e confirmadas enquanto a migração estava em andamento. Esvaziar manualmente essas mensagens do Namespace padrão e manualmente enviá-los para o Namespace premium. Para esvaziar manualmente as mensagens, use um aplicativo de console ou um script que descarrega as entidades de namespace padrão usando o nome DNS de migração de postagem que você especificou nos comandos de migração. Envie essas mensagens ao namespace premium para que eles podem ser processados pelos destinatários.

Depois que as mensagens tem sido esvaziadas, exclua o namespace padrão.

>[!IMPORTANT]
> Depois que as mensagens do namespace padrão tem sido esvaziadas, exclua o namespace padrão. Isso é importante porque a cadeia de caracteres de conexão que referenciada inicialmente para o namespace padrão agora refere-se ao namespace premium. Você não precisa mais o Namespace padrão. Excluir o namespace padrão que você migrou ajuda a reduzir a confusão posterior.

### <a name="how-much-downtime-do-i-expect"></a>Quanto tempo de inatividade esperado?
O processo de migração destina-se para reduzir o tempo de inatividade esperado para os aplicativos. Tempo de inatividade é reduzido por meio da cadeia de conexão usadas pelos aplicativos de remetente e destinatário para apontar para o novo namespace premium.

O tempo de inatividade que está ocorrendo pelo aplicativo é limitado ao tempo que leva para atualizar a entrada DNS para apontar para o namespace premium. Tempo de inatividade é de aproximadamente 5 minutos.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>É necessário fazer alterações de configuração ao fazer a migração?
Não, não há nenhuma alteração de código ou a configuração necessária para fazer a migração. A cadeia de caracteres de conexão que os aplicativos de remetente e receptor usam para acessar o Namespace padrão é mapeada automaticamente para atuar como um alias para o namespace premium.

### <a name="what-happens-when-i-abort-the-migration"></a>O que acontece quando eu anular a migração?
A migração pode ser anulada usando o `Abort` de comando ou usando o portal do Azure. 

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Portal do Azure

![Anular fluxo – anular sync][]
![anular fluxo - abort completa][]

Quando o processo de migração é anulado, ele anula o processo de copiar as entidades (tópicos, assinaturas e filtros) de padrão para o namespace premium e interrompe o emparelhamento.

A cadeia de caracteres de conexão não será atualizada para apontar para o namespace premium. Os aplicativos existentes continuam a funcionar como ocorria antes de iniciar a migração.

No entanto, ele não exclui as entidades no namespace premium ou exclua o namespace premium. Exclua as entidades manualmente se você decidiu não prosseguir com a migração.

>[!IMPORTANT]
> Se você optar por anular a migração, exclua o Namespace que você tinha provisionado para a migração para que você não seja cobrado pelos recursos premium.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Não quero ter esvaziar as mensagens. O que devo fazer?

Pode haver mensagens que são enviadas pelos aplicativos do remetente e confirmadas para o armazenamento em que o Namespace padrão, enquanto a migração está ocorrendo e apenas antes que a migração seja confirmada.

Durante a migração, a mensagem real/carga de dados não sejam copiada do padrão para o namespace premium. As mensagens têm sejam descarregadas manualmente e, em seguida, enviado para o namespace premium.

No entanto, se você pode migrar durante uma janela de manutenção do sistema/manutenção planejada, e você não deseja esvaziar manualmente e enviar as mensagens, siga estas etapas:

1. Pare os aplicativos do remetente. Os aplicativos de receptor processará as mensagens que estão atualmente no namespace padrão e serão drenar a fila.
1. Depois que as filas e assinaturas no Namespace padrão estiverem vazias, siga o procedimento descrito anteriormente para executar a migração do padrão para o namespace premium.
1. Após a migração for concluída, você pode reiniciar os aplicativos do remetente.
1. Os remetentes e receptores agora se conecta automaticamente ao namespace premium.

    >[!NOTE]
    > Não é necessário parar os aplicativos do receptor para a migração.
    >
    > Após a migração for concluída, os aplicativos de receptor serão desconectar do namespace padrão e conectar-se automaticamente ao namespace premium.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [diferenças entre o sistema de mensagens standard e premium](./service-bus-premium-messaging.md).
* Saiba mais sobre o [aspectos da recuperação de desastre em área geográfica e de alta disponibilidade para o barramento de serviço premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Página de aterrissagem de migração]: ./media/service-bus-standard-premium-migration/1.png
[Namespace de instalação]: ./media/service-bus-standard-premium-migration/2.png
[Configurar o namespace – criar um namespace premium]: ./media/service-bus-standard-premium-migration/3.png
[Configurar o namespace – escolher o nome da migração de postagem]: ./media/service-bus-standard-premium-migration/4.png
[Configurar o namespace - entidades de sincronização - iniciar]: ./media/service-bus-standard-premium-migration/5.png
[Namespace - entidades de sincronização - progresso da instalação]: ./media/service-bus-standard-premium-migration/8.png
[Namespace de switch - opção de menu]: ./media/service-bus-standard-premium-migration/9.png
[Namespace de switch - êxito]: ./media/service-bus-standard-premium-migration/12.png

[Anular fluxo – cancelar sincronização]: ./media/service-bus-standard-premium-migration/abort1.png
[Anular fluxo - abort completa]: ./media/service-bus-standard-premium-migration/abort3.png
