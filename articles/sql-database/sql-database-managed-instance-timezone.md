---
title: Fusos horários instância gerenciada do banco de dados do SQL do Azure | Microsoft Docs"
description: Saiba mais sobre as especificidades de fuso horário do banco de dados de instância gerenciada do SQL
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 07/05/2019
ms.openlocfilehash: 05ec49c98c5bcfe40346550f5570c03a8fb3f881
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657986"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Fusos horários no banco de dados de instância gerenciada do SQL

Tempo Universal Coordenado (UTC) é o fuso horário recomendado para a camada de dados de soluções de nuvem. Instância de gerenciada do banco de dados de SQL do Azure também oferece uma opção do fuso horário para atender às necessidades dos aplicativos existentes que armazenam valores de data e hora e chamam funções de data e hora com um contexto implícito de um fuso horário específico.

Funções T-SQL, como [getDate ()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) ou código CLR observar o fuso horário definido na instância de nível. Trabalhos do SQL Server Agent também seguem programações de acordo com o fuso horário da instância.

  >[!NOTE]
  > A instância gerenciada é a opção de implantação somente de banco de dados SQL que dá suporte à configuração de fuso horário. Outras opções de implantação sempre siga UTC.
Use [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) no únicos e em pool bancos de dados SQL se você precisar interpretar as informações de data e hora em um fuso horário de não-UTC.

## <a name="supported-time-zones"></a>Fusos horários com suporte

Um conjunto de fusos horários com suporte é herdado do sistema operacional subjacente da instância gerenciada. Ela é atualizada regularmente para obter novas definições de fuso horário e refletir as alterações aos existentes.

[Fuso horário de verão/hora altera a política](https://aka.ms/time) garante a precisão de histórico de 2010 para frente.

Uma lista com os nomes dos fusos horários com suporte é exposta por meio de [time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) exibição do sistema.

## <a name="set-a-time-zone"></a>Definir um fuso horário

Um fuso horário de uma instância gerenciada pode ser definido durante a criação de instância somente. O fuso horário padrão é UTC.

  >[!NOTE]
  > O fuso horário de uma instância gerenciada existente não pode ser alterado.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Definir o fuso horário por meio do portal do Azure

Quando você inserir parâmetros para uma nova instância, selecione um fuso horário na lista dos fusos horários com suporte.
  
![Definir um fuso horário durante a criação da instância](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

Especifica a propriedade timezoneId em seu [modelo do Resource Manager](https://aka.ms/sql-mi-create-arm-posh) para definir o fuso horário durante a criação de instância.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

Uma lista de valores com suporte para a propriedade timezoneId é no final deste artigo.

Se não for especificado, o fuso horário é definido como UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Verifique o fuso horário de uma instância

O [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) função retorna um nome de exibição do fuso horário da instância.

## <a name="cross-feature-considerations"></a>Considerações de entre recursos

### <a name="restore-and-import"></a>Importação e restauração

Você pode restaurar um arquivo de backup ou importar dados em uma instância gerenciada de uma instância ou um servidor com as configurações de fuso horário diferente. Certifique-se de fazer isso com cuidado. Analise o comportamento do aplicativo e os resultados das consultas e relatórios, assim como quando você transfere dados entre duas instâncias do SQL Server com as configurações de fuso horário diferente.

### <a name="point-in-time-restore"></a>Restauração pontual

<del>Quando você executa uma restauração point-in-time, o tempo de restauração para é interpretado como hora UTC. Essa configuração evita qualquer ambiguidade devido ao horário de verão e suas alterações em potencial.<del>

 >[!WARNING]
  > Comportamento atual não está alinhada com a instrução acima e tempo de restauração para é interpretado de acordo com o fuso horário da instância gerenciada do código-fonte em que os backups automáticos de banco de dados são obtidos do. Estamos trabalhando para corrigir esse comportamento para interpretar determinado ponto no tempo como hora UTC. Ver [problemas conhecidos](sql-database-managed-instance-timezone.md#known-issues) para obter mais detalhes.

### <a name="auto-failover-groups"></a>Grupos de failover automático

Usando o mesmo fuso horário em uma instância primária e secundária em um grupo de failover não é imposta, mas é altamente recomendável.

  >[!WARNING]
  > É altamente recomendável que você use o mesmo fuso horário para a instância primária e secundária em um grupo de failover. Devido a algumas situações raras, manter o mesmo fuso horário entre as instâncias principais e secundárias não é imposta. É importante entender que, no caso de failover manual ou automática, a instância secundária manterão seu fuso horário original.

## <a name="limitations"></a>Limitações

- O fuso horário da instância gerenciada existente não pode ser alterado.
- Processos externos, iniciados a partir de trabalhos do SQL Server Agent não observam o fuso horário da instância.

## <a name="known-issues"></a>Problemas conhecidos

Quando o point-in-time restore (PITR) operação é executada, o tempo de restauração para é interpretado de acordo com o fuso horário definido na instância gerenciada em que os backups automáticos de banco de dados são obtidos do, mesmo que a página do portal para PITR sugere que o tempo é interpretado como UTC.

Exemplo:

Digamos que essa instância em que os backups automáticos são feitos de tem conjunto de fuso horário da hora padrão Oriental (UTC-5).
Página do portal para restauração point-in-time sugere que a hora em que você está escolhendo para restaurar a é a hora UTC:

![PITR com a hora local usando o portal](media/sql-database-managed-instance-timezone/02-pitr-with-nonutc-timezone.png)

No entanto, o tempo de restauração para realmente é interpretado como horário padrão Oriental e neste exemplo específico, banco de dados será restaurado para o estado 9 horário padrão do Leste AM e não a hora UTC.

Se você quiser fazer a restauração point-in-time para um ponto específico no horário UTC, primeiro calcular tempo equivalente no fuso horário da instância de origem e usar o momento no portal ou no script do PowerShell/CLI.

## <a name="list-of-supported-time-zones"></a>Lista dos fusos horários com suporte

| **ID do fuso horário** | **Nome de exibição do fuso horário** |
| --- | --- |
| Hora oficial do Meridiano de data | (UTC-12:00) International Date Line West |
| UTC-11 | (UTC-11:00) Tempo Universal Coordenado-11 |
| Horário padrão das Aleutas | (UTC-10:00) Ilhas Aleutas |
| Hora oficial do Havaí | (UTC-10:00) Havaí |
| Marquesas Standard Time | (UTC-09:30) Ilhas Marquesas |
| Alaskan Standard Time | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Tempo Universal Coordenado-09 |
| Hora de padrão do Pacífico (México) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Tempo Universal Coordenado-08 |
| Hora oficial do Pacífico | (UTC-08:00) Hora do Pacífico (EUA e Canadá) |
| Horário padrão das Montanhas | (UTC-07:00) Arizona |
| Hora padrão das Montanhas (México) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Hora oficial das Montanhas | (UTC-07:00) Hora das Montanhas (EUA e Canadá) |
| Hora oficial da América Central | (UTC-06:00) América Central |
| Hora Oficial Central | (UTC-06:00) Hora Central (EUA e Canadá) |
| Horário padrão da ilha da Páscoa | (UTC-06:00) Ilha de Páscoa |
| Hora padrão de central (México) | (UTC-06:00) Guadalajara, México, Monterrey |
| Hora padrão Central do Canadá | (UTC-06:00) Saskatchewan |
| Hora oficial de Bogotá | (UTC-05:00) Bogotá, Lima, Quito, Rio Branco |
| Hora de padrão Oriental (México) | (UTC-05:00) Chetumal |
| Hora Padrão Oriental | (UTC-05:00) Hora do Leste (EUA e Canadá) |
| Horário padrão do Haiti | (UTC-05:00) Haiti |
| Horário padrão de Cuba | (UTC-05:00) Havana |
| Hora de padrão do Leste dos EUA | (UTC-05:00) Indiana (Leste) |
| Ilhas Turcos e Caicos padrão tempo | (UTC-05:00) Ilhas Turcos e Caicos |
| Horário padrão do Paraguai | (UTC-04:00) Assunção |
| Hora oficial do Atlântico | (UTC-04:00) Horário do Atlântico (Canadá) |
| Venezuela Standard Time | (UTC-04:00) Caracas |
| Hora oficial do Brasil Central | (UTC-04:00) Cuiabá |
| Hora oficial de Caracas | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Hora oficial do Pacífico | (UTC-04:00) Santiago |
| Hora padrão de Newfoundland | (UTC-03:30) Terra Nova |
| Horário padrão de Tocantins | (UTC-03:00) Araguaina |
| E. Hora oficial da América do Sul | (UTC-03:00) Brasília |
| Hora padrão | (UTC-03:00) Caiena, Fortaleza |
| Horário padrão da Argentina | (UTC-03:00) Cidade de Buenos Aires |
| Hora oficial da Groelândia | (UTC-03:00) Groenlândia |
| Horário padrão de Montevidéu | (UTC-03:00) Montevidéu |
| Hora oficial do Magalhães | (UTC-03:00) Punta Arenas |
| Horário padrão de Saint Pierre | (UTC-03:00) São Pedro e Miquelon |
| Hora padrão da Bahia | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Tempo Universal Coordenado-02 |
| Hora do Atlântico padrão | (UTC-02:00) Atlântico Central – Antiga |
| Hora oficial dos Açores | (UTC-01:00) Açores |
| Cape Verde Standard Time | (UTC-01:00) Cabo Verde |
| UTC | (UTC) Tempo Universal Coordenado |
| Hora oficial GMT | (UTC+00:00) Dublin, Edimburgo, Lisboa, Londres |
| Greenwich Standard Time | (UTC+00:00) Monróvia, Reykjavík |
| W. Hora oficial da Europa | (UTC+01:00) Amsterdã, Berlim, Berna, Roma, Estocolmo, Viena |
| Hora oficial da Europa Central | (UTC+01:00) Belgrado, Bratislava, Budapeste, Liubliana, Praga |
| Oficial românica | (UTC+01:00) Bruxelas, Copenhague, Madri, Paris |
| Horário padrão do Marrocos | (UTC + 01:00) Casablanca |
| Hora oficial de são Tomé | (UTC + 01:00) São Tomé |
| Hora padrão da Europa Central | (UTC+01:00) Sarajevo, Skopje, Varsóvia, Zagreb |
| W. Hora oficial da África Central | (UTC+01:00) Centro-oeste da África |
| Jordan Standard Time | (UTC+02:00) Amã |
| Hora padrão | (UTC+02:00) Atenas, Bucareste |
| Hora de padrão do Oriente Médio | (UTC+02:00) Beirute |
| Hora oficial do Egito | (UTC+02:00) Cairo |
| E. Hora oficial da Europa | (UTC+02:00) Chisinau |
| Hora padrão da Síria | (UTC+02:00) Damasco |
| Horário padrão de West Bank | (UTC+02:00) Gaza, Hebron |
| Hora oficial da África do Sul | (UTC+02:00) Harare, Pretória |
| Hora oficial FLE | (UTC+02:00) Helsinque, Kiev, Riga, Sófia, Tallinn, Vilnius |
| Israel Standard Time | (UTC+02:00) Jerusalém |
| Hora padrão de Caliningrado | (UTC+02:00) Kaliningrado |
| Hora padrão do Sudão | (UTC + 02:00) Cartum |
| Hora padrão da Líbia | (UTC+02:00) Trípoli |
| Horário padrão da Namíbia | (UTC+02:00) Windhoek |
| Hora oficial Arábica | (UTC+03:00) Bagdá |
| Hora padrão da Turquia | (UTC + 03:00) Istambul |
| Hora oficial árabe | (UTC+03:00) Kuwait, Riad |
| Hora padrão de Belarus | (UTC+03:00) Minsk |
| Hora oficial russa | (UTC + 03:00) Moscou, são Petersburgo |
| E. Hora oficial da África | (UTC+03:00) Nairóbi |
| Hora oficial do Irã | (UTC+03:30) Teerã |
| Hora oficial árabe | (UTC+04:00) Abu Dhabi, Mascate |
| Astrakhan Standard Time | (UTC+04:00) Astrakhan, Ulyanovsk |
| Horário padrão do Azerbaijão | (UTC+04:00) Baku |
| Fuso horário de Rússia 3 | (UTC+04:00) Ijevsk, Samara |
| Horário padrão de Maurício | (UTC+04:00) Port Louis |
| Hora padrão de Saratov | (UTC+04:00) Saratov |
| Horário padrão da Geórgia | (UTC+04:00) Tbilisi |
| Hora oficial de Volgogrado | (UTC+04:00) Volgograd |
| Caucasus Standard Time | (UTC+04:00) Ierevan |
| Hora padrão do Afeganistão | (UTC+04:30) Cabul |
| Hora de oficial do Oeste da Ásia | (UTC+05:00) Ashgabat, Tashkent |
| Hora oficial de Ecaterimburgo | (UTC+05:00) Ecaterimburgo |
| Horário padrão do Paquistão | (UTC+05:00) Islamabad, Karachi |
| Hora oficial da Índia | (UTC+05:30) Chennai, Kolkata (Calcutá), Mumbai, Nova Délhi |
| Sri Lanka Standard Time | (UTC+05:30) Sri Jayawardenepura |
| Hora oficial do Nepal | (UTC+05:45) Katmandu |
| Hora padrão | (UTC+06:00) Astana |
| Bangladesh Standard Time | (UTC+06:00) Dacca |
| Horário padrão de Omsk | (UTC+06:00) Omsk |
| Myanmar Standard Time | (UTC+06:30) Yangon (Rangoon) |
| Hora oficial de Sudeste da Ásia | (UTC+07:00) Bancoc, Hanói, Jacarta |
| Horário padrão de Altai | (UTC+07:00) Barnaul, Gorno-Altaysk |
| W. Horário padrão da Mongólia | (UTC+07:00) Hovd |
| Hora oficial da Ásia do Norte | (UTC+07:00) Krasnoyarsk |
| N. Hora padrão | (UTC+07:00) Novosibirsk |
| Horário padrão de Tomsk | (UTC+07:00) Tomsk |
| Hora oficial da China | (UTC+08:00) Beijing, Chongqing, Hong Kong, Urumqui |
| Hora oficial do nordeste da Ásia | (UTC+08:00) Irkutsk |
| Hora oficial de Cingapura | (UTC+08:00) Kuala Lumpur, Singapura |
| W. Hora oficial Austrália | (UTC+08:00) Perth |
| Hora oficial de Taipei | (UTC+08:00) Taipé |
| Horário padrão de Ulan | (UTC+08:00) Ulan Bator |
| Hora oficial Austrália Ocidental | (UTC+08:45) Eucla |
| Horário padrão de Transbaikal | (UTC+09:00) Chita |
| Hora oficial de Tóquio | (UTC+09:00) Osaka, Sapporo, Tóquio |
| Hora oficial da Coreia do Norte | (UTC + GMT+09:00) Pyongyang |
| Hora oficial da Coreia | (UTC+09:00) Seul |
| Hora oficial de Yakutsk | (UTC+09:00) Yakutsk |
| Cen. Hora oficial Austrália | (UTC+09:30) Adelaide |
| Hora oficial Austrália Central | (UTC+09:30) Darwin |
| E. Hora oficial Austrália | (UTC+10:00) Brisbane |
| Hora oficial Austrália Oriental | (UTC+10:00) Camberra, Melbourne, Sidney |
| Hora padrão do Oeste do Pacífico | (UTC+10:00) Guam, Port Moresby |
| Hora oficial da Tasmânia | (UTC+10:00) Hobart |
| Hora oficial de Vladivostok | (UTC+10:00) Vladivostok |
| Hora oficial do Lord Howe | (UTC+10:30) Ilha Lord Howe |
| Horário padrão de Bougainville | (UTC+11:00) Ilha Bougainville |
| Fuso horário de Rússia 10 | (UTC+11:00) Chokurdakh |
| Hora padrão de Magadã | (UTC+11:00) Magadan |
| Horário padrão de Norfolk | (UTC+11:00) Ilha Norfolk |
| Horário padrão de Sakhalin | (UTC+11:00) Sakhalin |
| Hora padrão Pacífico Central | (UTC+11:00) Ilhas Salomão, Nova Caledônia |
| Fuso horário de Rússia 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Hora padrão da Nova Zelândia | (UTC+12:00) Auckland, Wellington |
| UTC+12 | (UTC+12:00) Tempo Universal Coordenado+12 |
| Hora padrão de Fiji | (UTC+12:00) Fiji |
| Kamchatka Standard Time | (UTC+12:00) Petropavlovsk-Kamchatsky – Antiga |
| Hora oficial das Ilhas Chatham | (UTC+12:45) Ilhas Chatham |
| UTC+13 | (UTC + 13:00) Coordenada Tempo Universal Coordenado+13 |
| Hora oficial de Tonga | (UTC+13:00) Nuku'alofa |
| Hora oficial de Samoa | (UTC+13:00) Samoa |
| Hora oficial das Ilhas linha | (UTC+14:00) Ilha de Kiritimati |

## <a name="see-also"></a>Consulte também 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
