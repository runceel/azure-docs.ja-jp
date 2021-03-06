---
title: 暗号化されたストレージ アカウントをデプロイするための Azure Resource Manager テンプレートの作成 | Microsoft Docs
description: Visual Studio Code を使用して、暗号化されたストレージ アカウントをデプロイするためのテンプレートを作成します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 08/27/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 57d5f7039831c9fd617926f20f3ff001b22ef314
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "43097887"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>チュートリアル: 暗号化されたストレージ アカウントをデプロイするための Azure Resource Manager テンプレートの作成

Azure Resource Manager テンプレートを完成させる情報を調べる方法について説明します。

このチュートリアルでは、Azure クイック スタート テンプレートからのベース テンプレートを使用して Azure Storage アカウントを作成します。  テンプレート リファレンス ドキュメントを使用し、基本テンプレートをカスタマイズして暗号化されたストレージ アカウントを作成します。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * テンプレートの理解
> * テンプレートの編集
> * テンプレートのデプロイ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [Visual Studio Code](https://code.visualstudio.com/)。
* Resource Manager ツール拡張機能。 インストールするには、[Resource Manager ツール拡張機能のインストール](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)に関するページをご覧ください。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

このクイック スタートで使用されるテンプレートは、[Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) と呼ばれます。 テンプレートにより、Azure ストレージ アカウント リソースが定義されます。

1. Visual Studio Code から、**[ファイル]**>**[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。
4. **[ファイル]**>**[名前を付けて保存]** を選択し、ファイルを **azuredeploy.json** としてご自身のローカル コンピューターに保存します。

## <a name="understand-the-format"></a>形式の理解

VS Code から、テンプレートをルート レベルに折りたたみます。 次の要素を含む最も単純な構造があります。

![Resource Manager テンプレートの最も単純な構造](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* **$schema**: テンプレート言語のバージョンが記述されている JSON スキーマ ファイルの場所を指定します。
* **contentVersion**: この要素に値を指定して、テンプレートの大きな変更を記述します。
* **parameters**: リソースのデプロイをカスタマイズするためにデプロイを実行するときに提供される値を指定します。
* **variables**: テンプレート言語式を簡略化するためにテンプレート内で JSON フラグメントとして使用される値を指定します。
* **resources**: リソース グループ内でデプロイまたは更新されるリソースの種類を指定します。
* **outputs**: デプロイ後に返される値を指定します。

## <a name="use-parameters-in-template"></a>テンプレートでのパラメーターの使用

特定の環境に合った値をパラメーターに渡すことで、デプロイをカスタマイズすることができます。 ストレージ アカウントの値を設定するときに、テンプレートで定義されているパラメーターを使用します。

![Resource Manager テンプレートのパラメーター](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-parameters.png)

このテンプレートでは、2 つのパラメーターが定義されます。 location.defaultValue 内でテンプレート関数が使用されていることにご注意ください。

```json
"defaultValue": "[resourceGroup().location]",
```

resourceGroup() 関数は、現在のリソース グループを表すオブジェクトを返します。 テンプレート関数の一覧については、「[Azure Resource Manager テンプレートの関数](./resource-group-template-functions.md)」をご覧ください。

テンプレートで定義されたパラメーターを使用するには:

```json
"location": "[parameters('location')]",
"name": "[parameters('storageAccountType')]"
```

## <a name="use-variables-in-template"></a>テンプレートでの変数の使用

変数では、テンプレート内で使用できる値を作成できます。 変数は、テンプレートの複雑さを軽減するのに役立ちます。

![Resource Manager テンプレートの変数](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

このテンプレートでは、1 つの変数 *storageAccountName* を定義します。 定義では、2 つのテンプレート関数が使用されます。

- **concat()**: 文字列を連結します。 詳しくは、「[concat](./resource-group-template-functions-string.md#concat)」をご覧ください。
- **uniqueString()**: パラメーターとして渡された値に基づいて、決定論的ハッシュ文字列を作成します。 各 Azure ストレージ アカウントには、すべての Azure にわたって一意の名前が必要です。 この関数は、一意の文字列を提供します。 その他の文字列関数については、[文字列関数](./resource-group-template-functions-string.md)に関する記事をご覧ください。

テンプレートで定義された変数を使用するには:

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>テンプレートの編集

このチュートリアルの目的は、暗号化されたストレージ アカウントを作成するテンプレートを定義することです。  サンプル テンプレートは、基本的な暗号化されていないストレージ アカウントのみを作成します。 暗号化関連の構成を検索するには、Azure Storage アカウントのテンプレート リファレンスを使用できます。

1. [Azure テンプレート](https://docs.microsoft.com/azure/templates/)を参照します。
2. 左側の目次から、**[参照]**->**[ストレージ]**->**[ストレージ アカウント]** を選択します。 **[タイトルでフィルター]** フィールドに「**storage**」と入力することもできます。  ページには、ストレージ アカウント情報を定義するためのスキーマが含まれています。
3. 暗号化関連の情報を調べます。  
4. ストレージ アカウント リソース定義のプロパティ要素の内部に、次の json を追加します。

    ```json
    "encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true
            }
        }
    }
    ```
    この部分により、BLOB ストレージ サービスの暗号化関数が有効になります。

Visual Studio code で、最終的なリソース要素が次のようになるようにテンプレートを変更します。

![Resource Manager テンプレートの暗号化されたストレージ アカウントの resources](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

デプロイ手順については、Visual Studio Code のクイック スタートの「[テンプレートのデプロイ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template)」セクションを参照してください。

次のスクリーンショットは、新しく作成されたストレージ アカウントを一覧表示するための CLI コマンドを示しています。これは、BLOB ストレージに対して暗号化が有効になっていることを示しています。

![Azure Resource Manager の暗号化されたストレージ アカウント](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、テンプレート参照を使用して既存のテンプレートをカスタマイズする方法について説明しました。 このチュートリアルで使用したテンプレートに含まれる Azure リソースは 1 つだけです。  次のチュートリアルでは、複数のリソースを含むテンプレートを開発します。 一部のリソースには依存リソースがあります。

> [!div class="nextstepaction"]
> [複数のリソースの作成](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
