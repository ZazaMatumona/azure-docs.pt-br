---
title: Solução de problemas de conectividade
description: Solução de problemas de conectividade no pool de SQL do Synapse.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 33cc0c33f90d68ac3396f1366d4ff8437563eda1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213883"
---
# <a name="troubleshooting-connectivity-issues-in-synapse-sql-pool"></a>Solução de problemas de conectividade no pool de SQL do Synapse

Este artigo lista técnicas comuns de solução de problemas de conexão com o banco de dados do pool de SQL.

## <a name="check-service-availability"></a>Verificação da disponibilidade do serviço

Verifique se o serviço está disponível. No portal do Azure, vá até o pool de SQL que você está tentando conectar. No painel do sumário à esquerda, clique em **Diagnosticar e resolver problemas**.

![Selecione Integridade do recurso](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

O status do pool de SQL será mostrado aqui. Se o serviço não estiver sendo exibido como **Disponível**, verifique outras etapas.

![Serviço Disponível](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Se a opção Integridade do recurso mostrar que a instância do pool de SQL estiver pausada ou em dimensionamento, siga as orientações para retomar a instância.

![Serviço em pausa](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Informações adicionais sobre a Integridade do recurso podem ser encontradas aqui.

## <a name="check-for-paused-or-scaling-operation"></a>Verificar se a operação está pausada ou em dimensionamento

Verifique o portal para ver se a instância do pool de SQL está pausada ou em dimensionamento.

![Serviço pausado](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Se você vir que o serviço está pausado ou em dimensionamento, verifique se ele não está durante o agendamento de manutenção. No portal de *Visão geral* do pool de SQL, você verá o agendamento de manutenção escolhido.

![Visão geral do agendamento de manutenção](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Caso contrário, peça ao administrador de TI para verificar se essa manutenção não é um evento agendado. Para retomar a instância do pool de SQL, siga [estas etapas](pause-and-resume-compute-portal.md).

## <a name="check-your-firewall-settings"></a>Verificar as configurações de firewall

O banco de dados do pool de SQL se comunica pela porta 1433.  Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 talvez não seja permitido pelo firewall da rede. Nesse caso, você não pode se conectar ao [servidor lógico](../../azure-sql/database/logical-servers.md) , a menos que o departamento de ti Abra a porta 1433. Outras informações sobre as configurações de firewall podem ser encontradas [aqui](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Verificar as configurações do ponto de extremidade de serviço/rede virtual

Se estiver recebendo os erros 40914 e 40615, veja a [descrição e a resolução do erro aqui](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Verificar se os drivers estão atualizados

### <a name="software"></a>Software

Verifique se você está usando as ferramentas mais recentes para se conectar ao pool de SQL:

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Drivers

Verifique se está usando as versões mais recentes do driver.  O uso de uma versão mais antiga dos drivers pode resultar em comportamentos inesperados, pois os drivers mais antigos podem não dar suporte aos novos recursos.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>Verificar a cadeia de conexão

Verifique se as cadeias de conexão estão configuradas corretamente.  Veja abaixo alguns exemplos.  Você pode encontrar informações adicionais sobre [cadeias de conexão aqui](sql-data-warehouse-connection-strings.md).

Cadeia de conexão do ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Cadeia de conexão ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Cadeia de conexão PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Cadeia de conexão JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Problemas de conexão intermitentes

Verifique se você está com uma carga pesada sobre o servidor, com um grande número de solicitações enfileiradas. Talvez seja necessário escalar verticalmente seu pool de SQL para obter recursos adicionais.

## <a name="common-error-messages"></a>Mensagens de erro comuns

Para saber sobre os erros 40914 e 40615, veja a [descrição e a resolução do erro aqui](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Ainda está tendo problemas de conectividade?

Crie um [tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md) para que a equipe de engenharia possa ajudá-lo.
