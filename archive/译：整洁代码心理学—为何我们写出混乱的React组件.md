**编者注：**==这篇文章探讨了开发者为何明知整洁代码重要却仍写出混乱 React 组件的心理原因==。1) ==认知负荷陷阱==让我们在压力下选择快速方案而非最佳实践；2) ==沉没成本谬误==使我们不愿重构已有代码；3) ==复杂性偏见==导致过早优化和过度设计；4) ==决策疲劳==限制了我们持续做出最佳编码决策的能力。文章提出了渐进式开发、心理安全环境和"童子军规则"等实用策略来改善代码质量。

我们都知道应该写整洁代码。我们读过相关书籍，参加过讲座，也认同各种原则。但不知为何，我们仍然会写出混乱的 React 组件。原因不在于我们的技术能力，而在于我们的心理。

## 认知负荷陷阱

考虑这个常见场景：

```tsx
const UserDashboard = () => {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  const [filter, setFilter] = useState("");
  const [sortBy, setSortBy] = useState("name");
  const [page, setPage] = useState(1);
  const [totalPages, setTotalPages] = useState(1);
  useEffect(() => {
    fetchUsers();
  }, [filter, sortBy, page]);
  const fetchUsers = async () => {
    try {
      setLoading(true);
      const response = await fetch(
        `/api/users?filter=${filter}&sort=${sortBy}&page=${page}`
      );
      const data = await response.json();
      setUsers(data.users);
      setTotalPages(data.totalPages);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };
  const handleFilterChange = (e) => setFilter(e.target.value);
  const handleSortChange = (e) => setSortBy(e.target.value);
  const handlePageChange = (newPage) => setPage(newPage);
  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage error={error} />;
  return (
    <div>
      <FilterBar
        filter={filter}
        onFilterChange={handleFilterChange}
        sortBy={sortBy}
        onSortChange={handleSortChange}
      />
      <UserList users={users} />
      <Pagination
        currentPage={page}
        totalPages={totalPages}
        onPageChange={handlePageChange}
      />
    </div>
  );
};
```

这个组件不算糟糕，但也不够好。它承担了太多职责，处理了太多关注点，维护起来会很困难。然而，这正是我们在压力下或试图快速推进时会写出的那种组件。

## 为何我们写出混乱代码

### 1. 计划谬误

我们总是低估任务所需时间。这导致：

- 赶工期
- 走捷径
- 跳过重构
- 忽视最佳实践

### 2. 沉没成本谬误

一旦写出代码，我们就因以下原因不愿修改它：

- 已经投入了时间
- 对自己的解决方案有情感依赖
- 害怕破坏现有功能

### 3. 复杂性偏见

我们常常：

- 过度复杂化简单解决方案
- 添加未来可能需要的功能
- 过早创建抽象
- 为可能永远不会出现的边缘情况编写代码

### 4. 决策疲劳与认知负荷

神经科学研究表明我们大脑的决策能力有限。Diederich 和 Trueblood(2018)的研究显示：

- 开发者连续编码 2 小时后错误率增加 30%
- 组件中每增加一个状态变量，认知负荷增加 37%
- 复杂组件会触发类似多任务处理的"神经切换成本"

这解释了为何我们常常：

- 选择快速方案而非适当抽象
- 复制代码而非重构现有逻辑
- 留下 TODO 注释而非立即解决问题

Sweller(1988)的==认知负荷理论==表明，工作记忆只能同时保存 4±1 个信息块。当我们的组件管理多个关注点(数据获取、状态管理、UI 渲染)时，就会超出这个限制，代码质量就会下降。

## 打破循环

### 1. 从小处着手并迭代

与其写上面那种庞然大物，我们可以从以下开始：

```tsx
const UserDashboard = () => {
  const { users, loading, error } = useUsers();
  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage error={error} />;
  return <UserList users={users} />;
};
```

然后根据需要逐步添加功能：

```tsx
const UserDashboard = () => {
  const { users, loading, error } = useUsers();
  const { filter, setFilter } = useFilter();
  const { sortBy, setSortBy } = useSort();
  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage error={error} />;
  return (
    <div>
      <FilterBar
        filter={filter}
        onFilterChange={setFilter}
        sortBy={sortBy}
        onSortChange={setSortBy}
      />{" "}
      <UserList users={users} />{" "}
    </div>
  );
};
```

### 2. 创造心理安全感

- 留出重构时间
- 允许承认错误
- 鼓励代码审查
- 表彰整洁代码范例

### 3. 使用"童子军规则"

让代码比你发现时更整洁。这意味着：

- 看到小问题就修复
- 逐步重构
- 随时记录
- 与团队分享知识

## 实用策略

### 1. 五分钟规则

写任何代码前先问：

- 最简单的可行方案是什么？
- 我能在 5 分钟内解决这个问题吗？
- 最少需要做什么？

### 2. "代码审查"测试

提交代码前问：

- 我会自豪地在代码审查中展示这个吗？
- 这是解决问题最整洁的方式吗？
- 怎样才能让这段代码更好？

### 3. "未来的我"测试

考虑：

- 未来的我能理解这段代码吗？
- 未来的我能轻松修改这段代码吗？
- 未来的我会感谢现在的我写了这段代码吗？

还有：

- 我能用一句话说明这个组件的职责吗？
- 添加新功能需要修改超过 3 个文件吗？
- 有任何"我当时在想什么？"的代码模式吗？

## 结论

写整洁代码不仅是技术能力问题，更是理解我们的心理偏见并努力克服它们。通过认识这些模式并实施这些策略，我们可以写出更好的代码，创建更易维护的应用。

记住：整洁代码不是追求完美，而是做出小而持续的改进，并意识到我们天生倾向于走捷径。

## 延伸阅读

### 书籍与概述

- [整洁代码](https://amzn.to/4jDiUcA) Robert C. Martin 著
- [计算机编程心理学](https://amzn.to/435qWEa) Gerald M. Weinberg 著
- [思考，快与慢](https://amzn.to/4iShzNQ) Daniel Kahneman 著
- [认知负荷](https://en.wikipedia.org/wiki/Cognitive_load) (维基百科概述)
- [开发者生产力的科学](https://queue.acm.org/detail.cfm?id=3595878) (ACM Queue)


[英文原文](https://cekrem.github.io/posts/psychology-of-clean-code/)