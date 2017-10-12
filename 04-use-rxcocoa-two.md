# 善于使用 RxCocoa（二）
使用 RxSwift 进行 iOS 开发，一个难以避开的话题即是 RxSwift 如何与 UITableView 以及 UICollectionView 配合。值得庆贺的是，RxCocoa 已经给我们
提供了方案，并且在大多数情况下，它可以为我们减少很多不必要的代码书写。

在本文中，为了方便起见，仅以 UITableView 举例。

## 基本用法
使用 RxCocoa 来构造 UITableView，你不需要再设置 delegate 和 dataSource。

例如，你只需要这样就可以成功地在 UITableView 上展示数据：

```swift
    lazy var tableView: UITableView = {
        let tv = UITableView(frame: .zero, style: .plain)
        tv.register(cellType: UITableViewCell.self)
        return tv
    }()

    override func viewDidLoad() {
        super.viewDidLoad()
        
        setupUI() // 进行了界面的布局
        
        // 其中 fetchGoals 返回了一个 Observable<[CustomType]>
        viewModel.fetchGoals()
            .bind(to: tableView.rx.items) {tv, row, element in
                let cell: UITableViewCell = tv.dequeueReusableCell(for: IndexPath(row: row, section: 0))
                cell.textLabel?.text = element.title
                return cell
            }
            .addDisposableTo(bag)

        // cell 上的点击事件
        tableView.rx.modelSelected(CustomType.self).subscribe(onNext: { (customItem) in
            // Handle with custom item
        }).addDisposableTo(bag)
    }
```

Then you are all set。

## 进阶

RxCocoa 目前只能处理单个 Section 的情况。如果你在寻求多个 Section 的方案，那么你需要 [RxDataSources](https://github.com/RxSwiftCommunity/RxDataSources)。

RxDataSources 是 RxSwiftCommunity 下的开源项目，它提供了对 UITableView 和 UICollectionView 更高级的支持。具体用法也很简单，在这就不具体介绍了，可以看它们的 README。