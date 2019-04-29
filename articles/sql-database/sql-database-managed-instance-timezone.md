---
title: Fuso horário de instância de gerenciada do banco de dados SQL do Azure | Microsoft Docs"
description: Saiba mais sobre as especificações de fuso horário do banco de dados de instância gerenciada do SQL
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 04/10/2019
ms.openlocfilehash: 23314e97051da95ab164baeab6e9d089f486351a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487386"
---
# <a name="time-zone-in-azure-sql-database-managed-instance-preview"></a>Fuso horário na instância gerenciada do banco de dados SQL do Azure (visualização)

Enquanto usar Tempo Universal Coordenado (UTC) é uma prática recomendada para a camada de dados de soluções de nuvem, banco de dados de instância gerenciada do SQL oferece uma opção do fuso horário para atender às necessidades dos aplicativos existentes que armazenam valores de data e hora e data da chamada e funções de tempo com um contexto implícito de um fuso horário específico.

Funções T-SQL, como [getDate ()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) ou código CLR observar o fuso horário definido na instância de nível. Trabalhos do SQL Agent também seguem a agenda de acordo com o fuso horário da instância.

  >[!NOTE]
  > A instância gerenciada é a opção de implantação somente de banco de dados SQL que dá suporte à configuração de fuso horário. Outras opções de implantação sempre siga UTC.
Use [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) no únicos e em pool bancos de dados SQL se você precisar interpretar as informações de data e hora no fuso horário não UTC.

## <a name="supported-time-zones"></a>Fusos horários com suporte

Um conjunto de fusos horários com suporte é herdado do sistema operacional subjacente da instância gerenciada e está sendo atualizada regularmente para obter novas definições de fuso horário e refletir as alterações aos existentes.

Uma lista com os nomes dos fusos horários com suporte é exposta por meio de [time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) exibição do sistema.

## <a name="setting-time-zone"></a>Configurando fuso horário

Um fuso horário de instância gerenciada pode ser definido durante a criação de instância somente. O fuso horário padrão é o tempo Universal Coordenado (UTC).

  >[!NOTE]
  > O fuso horário de uma instância gerenciada existente não pode ser alterado.

### <a name="setting-the-time-zone-through-azure-portal"></a>Configurando o fuso horário por meio do portal do Azure

Ao inserir parâmetros para a nova instância, selecione um fuso horário na lista dos fusos horários com suporte:
  
![Configurando o fuso horário durante a criação de instância](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

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

Lista de valores com suporte para a propriedade timezoneId pode ser encontrada no final deste artigo.

Se não for especificado, o fuso horário será definido como UTC.

## <a name="checking-the-time-zone-of-instance"></a>Verificando o fuso horário da instância

[CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) função retorna um nome de exibição do fuso horário da instância.

## <a name="cross-feature-considerations"></a>Considerações de entre recursos

### <a name="restore-and-import"></a>Importação e restauração

Você pode restaurar o arquivo de backup ou importar dados para a instância gerenciada de uma instância ou um servidor com as configurações de fuso horário diferente. No entanto, certifique-se de fazer isso com cuidado e para analisar o comportamento do aplicativo e os resultados das consultas e relatórios, assim como ao transferir dados entre duas instâncias do SQL Server com as configurações de fuso horário diferente.

### <a name="point-in-time-restore"></a>Restauração pontual

Ao executar a restauração point-in-time, o tempo de restauração para é interpretado como hora UTC para evitar qualquer ambiguidade devido ao horário de verão e suas alterações em potencial.

### <a name="auto-failover-groups"></a>Grupos de failover automático

Usando o mesmo fuso horário entre a instância primária e secundária no failover de grupo não é imposto, mas é altamente recomendável.
  >[!IMPORTANT]
  > Enquanto há cenários válidos para ter o fuso horário diferente em secundário geográfico instância usado para escala de leitura somente, observe que, no caso de failover manual ou automática para a instância secundária ele manterá seu fuso horário original.

## <a name="limitations"></a>Limitações

- Fuso horário da instância gerenciada existente não pode ser alterado.
- Processos externos, iniciados a partir de trabalhos do SQL Agent não estão sujeitas a fuso horário da instância.
- A instância gerenciada do native [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance) cmdlet do PowerShell não suporte à passagem do fuso horário parâmetro ainda. Wrapper de PowerShell de uso com [modelo do Resource Manager](https://aka.ms/sql-mi-create-arm-posh) em vez disso.
- Comando da CLI [az sql mi criar](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-create) não suporte o parâmetro de fuso horário.

## <a name="list-of-supported-time-zones"></a>Lista dos fusos horários com suporte

| **ID do fuso horário** | **Nome de exibição do fuso horário** |
| --- | --- |
| Hora Oficial do Meridiano de Data | (UTC-12:00) International Date Line West |
| UTC-11 | (UTC-11:00) Tempo Universal Coordenado-11 |
| Horário Padrão das Aleutas | (UTC-10:00) Ilhas Aleutas |
| Hora Oficial do Havaí | (UTC-10:00) Havaí |
| Horário Padrão de Marquesas | (UTC-09:30) Ilhas Marquesas |
| Hora Oficial do Alasca | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Tempo Universal Coordenado-09 |
| Horário Padrão do Pacífico (México) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Tempo Universal Coordenado-08 |
| Hora Oficial do Pacífico | (UTC-08:00) Hora do Pacífico (EUA e Canadá) |
| Hora Padrão das Montanhas dos EUA | (UTC-07:00) Arizona |
| Hora Padrão das Montanhas (México) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Hora Oficial das Montanhas | (UTC-07:00) Hora das Montanhas (EUA e Canadá) |
| Hora Oficial da América Central | (UTC-06:00) América Central |
| Hora Oficial Central | (UTC-06:00) Hora Central (EUA e Canadá) |
| Horário Padrão da Ilha da Páscoa | (UTC-06:00) Ilha de Páscoa |
| Hora Padrão Central (México) | (UTC-06:00) Guadalajara, México, Monterrey |
| Hora Oficial Canadá Central | (UTC-06:00) Saskatchewan |
| Hora Padrão do Pacífico na América do Sul | (UTC-05:00) Bogotá, Lima, Quito, Rio Branco |
| Hora Padrão Oriental (México) | (UTC-05:00) Chetumal |
| Hora Oficial do Leste dos EUA | (UTC-05:00) Hora do Leste (EUA e Canadá) |
| Horário Padrão do Haiti | (UTC-05:00) Haiti |
| Horário Padrão de Cuba | (UTC-05:00) Havana |
| Hora Padrão do Leste dos EUA | (UTC-05:00) Indiana (Leste) |
| Ilhas Turcos e Caicos padrão tempo | (UTC-05:00) Ilhas Turks e Caicos |
| Horário Padrão do Paraguai | (UTC-04:00) Assunção |
| Hora Oficial do Atlântico | (UTC-04:00) Horário do Atlântico (Canadá) |
| Horário Padrão da Venezuela | (UTC-04:00) Caracas |
| Horário Padrão do Brasil Central | (UTC-04:00) Cuiabá |
| Hora Padrão do Oeste da América do Sul | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Hora Padrão do Pacífico SA | (UTC-04:00) Santiago |
| Hora Padrão de Newfoundland | (UTC-03:30) Terra Nova |
| Horário Padrão de Tocantins | (UTC-03:00) Araguaina |
| E. Hora oficial da América do Sul | (UTC-03:00) Brasília |
| Hora Padrão do Leste da América do Sul | (UTC-03:00) Caiena, Fortaleza |
| Horário Padrão da Argentina | (UTC-03:00) Cidade de Buenos Aires |
| Hora Oficial da Groelândia | (UTC-03:00) Groenlândia |
| Horário Padrão de Montevidéu | (UTC-03:00) Montevidéu |
| Hora Padrão de Magallanes | (UTC-03:00) Punta Arenas |
| Horário Padrão de Saint Pierre | (UTC-03:00) São Pedro e Miquelon |
| Hora Padrão da Bahia | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Tempo Universal Coordenado-02 |
| Hora Oficial do Atlântico Central | (UTC-02:00) Atlântico Central – Antiga |
| Hora Oficial dos Açores | (UTC-01:00) Açores |
| Cape Verde Standard Time | (UTC-01:00) Cabo Verde |
| UTC | (UTC) Tempo Universal Coordenado |
| Hora Oficial GMT | (UTC+00:00) Dublin, Edimburgo, Lisboa, Londres |
| Hora de Greenwich | (UTC+00:00) Monróvia, Reykjavík |
| W. Hora oficial da Europa | (UTC+01:00) Amsterdã, Berlim, Berna, Roma, Estocolmo, Viena |
| Hora Oficial da Europa Central | (UTC+01:00) Belgrado, Bratislava, Budapeste, Liubliana, Praga |
| Hora Oficial Românica | (UTC+01:00) Bruxelas, Copenhague, Madri, Paris |
| Horário Padrão do Marrocos | (UTC+01:00) Casablanca |
| Hora Oficial de São Tomé | (UTC+01:00) São Tomé |
| Hora Oficial da Europa Central | (UTC+01:00) Sarajevo, Skopje, Varsóvia, Zagreb |
| W. Hora oficial da África Central | (UTC+01:00) Centro-oeste da África |
| Horário Padrão da Jordânia | (UTC+02:00) Amã |
| Hora Oficial GTB | (UTC+02:00) Atenas, Bucareste |
| Horário Padrão do Oriente Médio | (UTC+02:00) Beirute |
| Hora Oficial do Egito | (UTC+02:00) Cairo |
| E. Hora oficial da Europa | (UTC+02:00) Chisinau |
| Hora Padrão da Síria | (UTC+02:00) Damasco |
| Horário padrão de West Bank | (UTC+02:00) Gaza, Hebron |
| Hora Oficial da África do Sul | (UTC+02:00) Harare, Pretória |
| Hora Oficial FLE | (UTC+02:00) Helsinque, Kiev, Riga, Sófia, Tallinn, Vilnius |
| Israel Standard Time | (UTC+02:00) Jerusalém |
| Hora padrão de Caliningrado | (UTC+02:00) Kaliningrado |
| Hora Padrão do Sudão | (UTC+02:00) Cartum |
| Hora Padrão da Líbia | (UTC+02:00) Trípoli |
| Horário Padrão da Namíbia | (UTC+02:00) Windhoek |
| Hora Oficial Arábica | (UTC+03:00) Bagdá |
| Hora Padrão da Turquia | (UTC+03:00) Istambul |
| Hora Oficial Árabe | (UTC+03:00) Kuwait, Riad |
| Hora Padrão de Belarus | (UTC+03:00) Minsk |
| Hora oficial russa | (UTC+03:00) Moscou, São Petersburgo |
| E. Hora oficial da África | (UTC+03:00) Nairóbi |
| Hora Oficial do Irã | (UTC+03:30) Teerã |
| Hora Oficial Árabe | (UTC+04:00) Abu Dhabi, Mascate |
| Horário Padrão de Astrakhan | (UTC+04:00) Astrakhan, Ulyanovsk |
| Horário Padrão do Azerbaijão | (UTC+04:00) Baku |
| Fuso horário de Rússia 3 | (UTC+04:00) Ijevsk, Samara |
| Horário Padrão de Maurício | (UTC+04:00) Port Louis |
| Hora Padrão de Saratov | (UTC+04:00) Saratov |
| Horário Padrão da Geórgia | (UTC+04:00) Tbilisi |
| Hora Oficial de Volgogrado | (UTC+04:00) Volgogrado |
| Hora Oficial do Cáucaso | (UTC+04:00) Ierevan |
| Hora Padrão do Afeganistão | (UTC+04:30) Cabul |
| Hora Oficial do Oeste da Ásia | (UTC+05:00) Ashgabat, Tashkent |
| Hora oficial de Ecaterimburgo | (UTC+05:00) Ecaterimburgo |
| Horário Padrão do Paquistão | (UTC+05:00) Islamabad, Karachi |
| Hora Oficial da Índia | (UTC+05:30) Chennai, Kolkata (Calcutá), Mumbai, Nova Délhi |
| Hora Oficial do Sri Lanka | (UTC+05:30) Sri Jayawardenepura |
| Hora Oficial do Nepal | (UTC+05:45) Katmandu |
| Hora Oficial da Ásia Central | (UTC+06:00) Astana |
| Hora Padrão de Bangladesh | (UTC+06:00) Dacca |
| Horário Padrão de Omsk | (UTC+06:00) Omsk |
| Hora Oficial de Myanmar | (UTC+06:30) Yangon (Rangoon) |
| Hora Padrão do Sudeste Asiático | (UTC+07:00) Bancoc, Hanói, Jacarta |
| Horário Padrão de Altai | (UTC+07:00) Barnaul, Gorno-Altaysk |
| W. Horário padrão da Mongólia | (UTC+07:00) Hovd |
| Hora oficial da Ásia do Norte | (UTC+07:00) Krasnoyarsk |
| N. Hora Oficial da Ásia Central | (UTC+07:00) Novosibirsk |
| Horário Padrão de Tomsk | (UTC+07:00) Tomsk |
| Hora Oficial da China | (UTC+08:00) Beijing, Chongqing, Hong Kong, Urumqui |
| Hora oficial do nordeste da Ásia | (UTC+08:00) Irkutsk |
| Hora oficial de Cingapura | (UTC+08:00) Kuala Lumpur, Singapura |
| W. Hora oficial Austrália | (UTC+08:00) Perth |
| Hora Oficial de Taipei | (UTC+08:00) Taipé |
| Hora Padrão de Ulan Bator | (UTC+08:00) Ulan Bator |
| Horário Padrão da Austrália Ocidental | (UTC+08:45) Eucla |
| Horário Padrão de Transbaikal | (UTC+09:00) Chita |
| Hora Oficial de Tóquio | (UTC+09:00) Osaka, Sapporo, Tóquio |
| Hora Padrão da Coreia do Norte | (UTC + 09:00) Pyongyang |
| Hora Oficial da Coreia | (UTC+09:00) Seul |
| Hora oficial de Yakutsk | (UTC+09:00) Yakutsk |
| Cen. Hora oficial Austrália | (UTC+09:30) Adelaide |
| Hora Padrão da Austrália Central | (UTC+09:30) Darwin |
| E. Hora oficial Austrália | (UTC+10:00) Brisbane |
| Hora Padrão da Austrália Oriental | (UTC+10:00) Camberra, Melbourne, Sidney |
| Hora Oficial do Oeste do Pacífico | (UTC+10:00) Guam, Port Moresby |
| Hora Oficial da Tasmânia | (UTC+10:00) Hobart |
| Hora oficial de Vladivostok | (UTC+10:00) Vladivostok |
| Horário Padrão de Lord Howe | (UTC+10:30) Ilha Lord Howe |
| Horário Padrão de Bougainville | (UTC+11:00) Ilha Bougainville |
| Fuso horário de Rússia 10 | (UTC+11:00) Chokurdakh |
| Hora Padrão de Magadã | (UTC+11:00) Magadan |
| Horário Padrão de Norfolk | (UTC+11:00) Ilha Norfolk |
| Horário Padrão de Sakhalin | (UTC+11:00) Sakhalin |
| Hora Oficial do Pacífico Central | (UTC+11:00) Ilhas Salomão, Nova Caledônia |
| Fuso horário de Rússia 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Hora Oficial da Nova Zelândia | (UTC+12:00) Auckland, Wellington |
| UTC+12 | (UTC+12:00) Tempo Universal Coordenado+12 |
| Hora Padrão de Fiji | (UTC+12:00) Fiji |
| Horário Padrão de Kamchatka | (UTC+12:00) Petropavlovsk-Kamchatsky – Antiga |
| Horário Padrão das Ilhas Chatham | (UTC+12:45) Ilhas Chatham |
| UTC+13 | (UTC+13:00) Tempo Universal Coordenado+13 |
| Hora Oficial de Tonga | (UTC+13:00) Nuku'alofa |
| Hora Oficial de Samoa | (UTC+13:00) Samoa |
| Hora Padrão das Ilhas Line | (UTC+14:00) Ilha de Kiritimati |

## <a name="see-also"></a>Consulte também

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)