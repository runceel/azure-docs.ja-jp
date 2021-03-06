---
title: ローカルで Azure Data Lake Analytics コードをデバッグする
description: Azure Data Lake Tools for Visual Studio を使用してローカル ワークステーション上で U-SQL ジョブをデバッグする方法について説明します。
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 9e73fc4db1404842e6d3878d88d8f02511587bc9
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "43044979"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>ローカルで Azure Data Lake Analytics コードをデバッグする

Azure Data Lake Tools for Visual Studio を使用すると、Azure Data Lake Analytics サービスで実行するときと同じように、自分のローカルのワークステーションで Azure Data Lake Analytics コードを実行およびデバッグすることができます。

[ローカル コンピューターで U-SQL スクリプトを実行する方法](data-lake-analytics-data-lake-tools-local-run.md)について説明します。

## <a name="debug-scripts-and-c-assemblies-locally"></a>スクリプトと C# アセンブリをローカルでデバッグする

Azure Data Lake Analytics サービスに提出し、登録しなくても C# アセンブリをデバッグできます。 分離コード ファイルと参照 C# プロジェクトの両方にブレークポイントを設定できます。

### <a name="debug-local-code-in-a-code-behind-file"></a>分離コード ファイルのローカル コードをデバッグする

1. 分離コード ファイルにブレークポイントを設定します。
2. **F5** キーを選択して、スクリプトをローカルでデバッグします。

> [!NOTE]
   > 次の手順は、Visual Studio 2015 でのみ機能します。 Visual Studio の以前のバージョンでは、**PDB** ファイルを手動で追加する必要がある場合があります。  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>参照先の C# プロジェクトのローカル コードをデバッグする

1. C# アセンブリ プロジェクトを作成してビルドし、出力 **DLL** ファイルを生成します。
2. U-SQL ステートメントを使用して **DLL** ファイルを登録します。

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. C# コードにブレークポイントを設定します。
4. **F5** キーを選択し、C# **DLL** ファイルをローカルで参照してスクリプトをデバッグします。


## <a name="next-steps"></a>次の手順

- より複雑なクエリの例については、[Azure Data Lake Analytics を使用した Web サイトのログの分析](data-lake-analytics-analyze-weblogs.md)に関するページをご覧ください。
- ジョブの詳細を表示する方法については、「[Azure Data Lake Analytics ジョブに対するジョブ ブラウザーとジョブ ビューの使用](data-lake-analytics-data-lake-tools-view-jobs.md)」を参照してください。
- 頂点実行ビューの使用方法については、「[Data Lake Tools for Visual Studio での頂点実行ビューの使用](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)」を参照してください。
