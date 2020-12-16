<!-- TEMPLATE FILE - DO NOT ADD METADATA -->
<!-- markdownlint-disable MD026 MD041 -->
データ分類によって、組織のデータを判定して価値を割り当てることができます。これは、ガバナンスの一般的な開始点となります。 データ分類プロセスでは、リスクを識別するために、機密性とビジネスへの影響によってデータを分類します。 データを分類する際、機密情報や重要なデータを盗難や損失から保護する方法でデータを管理できます。

## <a name="understand-data-risks-then-manage-them"></a>データのリスクを理解して管理する

リスクを管理する前に、リスクについて理解しておく必要があります。 データ侵害の責任の場合は、データの分類から理解を始めます。 データ分類は、デジタル資産内のすべての資産にメタデータ特性を関連付けるプロセスです。それにより、その資産に関連付けられているデータの種類を識別します。

クラウドへの移行やデプロイの潜在的な候補として識別されるすべての資産について、データ分類、ビジネス上の重要度、および請求責任を記録するためのメタデータを文書化する必要があります。 分類のこれら 3 つのポイントが、リスクの理解と軽減に大いに役立ちます。

## <a name="classifications-microsoft-uses"></a>Microsoft が使用する分類

Microsoft が使用する分類の一覧を以下に示します。 業界や既存のセキュリティ要件に応じて、データ分類の標準は、組織内に既に存在する場合があります。 標準が存在しない場合は、このサンプル分類を使用して、ご自身のデジタル資産とリスク プロファイルの理解を深めることができます。

- **ビジネス以外:** Microsoft に属していない私生活のデータ。
- **パブリック:** 自由に利用でき、公開することが承認されているビジネス データ。
- **全般:** 一般の人々を対象としていないビジネス データ。
- **機密:** 過剰に共有された場合に Microsoft に損害を与える可能性のあるビジネス データ。
- **極秘:** 過剰に共有された場合に Microsoft に広範な損害を与える可能性のあるビジネス データ。

## <a name="tagging-data-classification-in-azure"></a>Azure でのデータ分類のタグ付け

リソース タグはメタデータ保存のための優れたアプローチであり、これらのタグを使用して、デプロイされるリソースにデータ分類情報を適用できます。 分類によるクラウド資産のタグ付けは正式なデータ分類プロセスの代わりにはなりませんが、リソースの管理とポリシーの適用を行うための有益なツールを提供します。 [Azure Information Protection](/azure/information-protection/what-is-information-protection) は、存在する場所 (オンプレミス、Azure など) に関係なく、データ自体の分類に役立つ優れたソリューションです。 全体的な分類戦略の一部として検討してください。

## <a name="take-action"></a>アクションの実行

定義済みのデータ分類を使用して資産を定義およびタグ付けすることで、アクションを実行します。

- ポートフォリオ全体にタグを適用する例については、[実行可能なガバナンス ガイドのいずれかを選択](../docs/govern/guides/index.md)してください。
- [推奨される名前付けおよびタグ付け規則](../docs/ready/azure-best-practices/resource-tagging.md)に関する記事を確認して、より包括的なタグ付けの標準を定義します。
- Azure でのリソースのタグ付けの詳細については、「[タグを使用して Azure リソースと管理階層を整理する](/azure/azure-resource-manager/management/tag-resources)」を参照してください。