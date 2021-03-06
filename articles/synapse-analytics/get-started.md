---
title: 'Tutorial: Introdução ao Azure Synapse Analytics'
description: Neste tutorial, você aprenderá as etapas básicas para configurar e usar o Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 7affc5aad89fd79e6ba6480f7bf10d37f90dc5e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075868"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Introdução ao Azure Synapse Analytics

Este tutorial é um guia passo a passo das principais áreas de recursos do Azure Synapse Analytics. O tutorial é o ponto de partida ideal para alguém que queira um tour guiado pelos principais cenários do Azure Synapse Analytics. Depois de seguir as etapas no tutorial, você terá um workspace do Synapse totalmente funcional em que poderá começar a analisar dados usando o SQL, SQL sob demanda e Apache Spark.

Você aprenderá a:
* Provisionar um workspace do Synapse em uma assinatura do Azure
* Configurar o controle de acesso em uma conta do ADLSGEN2 para que ele funcione diretamente com o workspace do Synapse
* Carregar os dados de exemplo NYCTaxi no workspace do Synapse para que ele possa ser usado pelo SQL, SQL sob demanda e Spark
* Editar e executar scripts SQL e Notebooks Synapse usando o Synapse Studio
* Consultar tabelas SQL e tabelas do Spark
* Carregar dados de tabelas SQL em dataframes do Spark
* Carregar dados em tabelas SQL de dataframes do Spark
* Explorar o conteúdo de uma conta do ADLSGEN2
* Analisar os arquivos de dados parquet em contas do ADLSGEN2 usando o Spark e o SQL sob demanda 
* Criar um pipeline de dados que executa automaticamente um notebook Synapse a cada hora

Siga as etapas *na ordem* mostrada abaixo e você fará um tour por muitas funcionalidades e aprenderá a exercitar os principais recursos.

* [ETAPA 1 – Criar e configurar um workspace do Synapse](get-started-create-workspace.md)
* [ETAPA 2 – Analisar usando um Pool de SQL](get-started-analyze-sql-pool.md)
* [ETAPA 3 – Analisar usando Spark](get-started-analyze-spark.md)
* [ETAPA 4 – Analisar usando SQL sob demanda](get-started-analyze-sql-on-demand.md)
* [ETAPA 5 – Analisar dados em uma conta de armazenamento](get-started-analyze-storage.md)
* [ETAPA 6 – Orquestrar com pipelines](get-started-pipelines.md)
* [ETAPA 7 – Visualizar dados com o Power BI](get-started-visualize-power-bi.md)
