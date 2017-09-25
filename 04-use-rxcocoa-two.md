# UITableView CheatSheet

## RxCocoa
在 iOS 项目中，使用 RxSwift 的一个优点是可以简化代理模式的代码。而最常见的使用代理模式的地方就是 UITableView 以及 UICollectionView。

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

# RxDataSource
当你需要处理多个 section 的时候，RxCocoa 可能就满足不了你的需求了。RxDataSource 应运而生。