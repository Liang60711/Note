## dao 和 repository 的差別
> https://stackoverflow.com/questions/8550124/what-is-the-difference-between-dao-and-repository-patterns

> https://ithelp.ithome.com.tw/articles/10225087

* `DAO` (data access object) is an abstraction of data persistence.
* `Repository` is an abstraction of a collection of objects.

* DAO 不能使用 Repository，但 Repository 可以使用 DAO，可以簡單認為：Repository = DAO + domain model