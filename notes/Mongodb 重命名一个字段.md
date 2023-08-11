Tags: #mongodb

```
db.students.updateMany(
	{},
	{ $rename: {"nickname": "alias", "cell": "mobile"} }
)
```