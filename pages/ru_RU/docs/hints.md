---
title: Подсказки
layout: страница
---

GORM обеспечивает поддержку оптимизатора/индексов/комментариев

https://github.com/go-gorm/hints

## Подсказки оптимизатора

```go
import "gorm.io/hints"

DB.Clauses(hints.New("hint")).Find(&User{})
// SELECT * /*+ hint */ FROM `users`
```

## Подсказки индексирования

```go
import "gorm.io/hints"

DB.Clauses(hints.UseIndex("idx_user_name")).Find(&User{})
// SELECT * FROM `users` USE INDEX (`idx_user_name`)

DB.Clauses(hints.ForceIndex("idx_user_name", "idx_user_id").ForJoin()).Find(&User{})
// SELECT * FROM `users` FORCE INDEX FOR JOIN (`idx_user_name`,`idx_user_id`)"

DB.Clauses(
    hints.ForceIndex("idx_user_name", "idx_user_id").ForOrderBy(),
    hints.IgnoreIndex("idx_user_name").ForGroupBy(),
).Find(&User{})
// SELECT * FROM `users` FORCE INDEX FOR ORDER BY (`idx_user_name`,`idx_user_id`) IGNORE INDEX FOR GROUP BY (`idx_user_name`)"
```

## Подсказки комментарии

```go
import "gorm.io/hints"

DB.Clauses(hints.Comment("select", "master")).Find(&User{})
// SELECT /*master*/ * FROM `users`;

DB.Clauses(hints.CommentBefore("insert", "node2")).Create(&user)
// /*node2*/ INSERT INTO `users` ...;

DB.Clauses(hints.CommentAfter("select", "node2")).Create(&user)
// /*node2*/ INSERT INTO `users` ...;

DB.Clauses(hints.CommentAfter("where", "hint")).Find(&User{}, "id = ?", 1)
// SELECT * FROM `users` WHERE id = ? /* hint */
```
